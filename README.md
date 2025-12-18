# Haskell Dev Base Image

A pre-built, feature-rich Docker image designed to speed up Haskell development environments. This image serves as the **base layer** for other Haskell projects, eliminating the need to compile GHC, Stack, and system tools from scratch every time you start a new project.

**Docker Image:** `ghcr.io/junghunleephd/hs-webapp-env:latest`

## 🚀 Why this exists
Building a robust Haskell development environment (Devcontainer) from scratch takes a long time because it involves downloading GHC, compiling Stack, and installing system dependencies.

By using this image as your base, your dev containers will start in **seconds instead of minutes**.

---

## 🛠️ What's Included
This image is built on top of `haskell:9.4` (Debian Buster) and includes:

### Haskell Toolchain
* **GHC 9.4.8**: The compiler.
* **Stack**: The build tool.
* **GHCup**: The installer/manager for Haskell tools.
* **HLS (Haskell Language Server)**: Pre-installed for VS Code IntelliSense.

### System Tools
* `zlib1g-dev`, `libssl-dev`: Critical libraries for web servers (Scotty) and networking.
* `curl`, `git`, `sudo`, `build-essential`.

### Developer Experience
* **Zsh**: Set as the default shell.
* **Starship**: A fast, modern shell prompt (pre-configured).
* **Just**: A modern command runner (alternative to Make).
* **Non-root user**: Configured with a `vscode` user (UID 1000) for seamless Docker integration.

---

## 📦 How to Use

### 1. In a VS Code Devcontainer
In your **other** Haskell projects (the web apps), configure your `.devcontainer/devcontainer.json` to use this image directly:

```json
{
  "name": "My Haskell Project",
  "image": "ghcr.io/<YOUR_GITHUB_USERNAME>/haskell-dev-base:latest",
  "remoteUser": "vscode",
  "customizations": {
    "vscode": {
      "extensions": [
        "haskell.haskell",
        "justusadam.language-haskell"
      ]
    }
  }
}
```

### 2. In a Production Dockerfile

You can use this image as a "Builder" stage to speed up your CI/CD pipelines:

```Dockerfile
# Use the cached base image for building
FROM ghcr.io/<YOUR_GITHUB_USERNAME>/haskell-dev-base:latest AS builder

WORKDIR /opt/build
COPY . .
RUN stack install --system-ghc

# Use a tiny runner for the final image
FROM debian:buster-slim
COPY --from=builder /root/.local/bin/my-app /opt/app/my-app
CMD ["/opt/app/my-app"]
```

---

## **🔄 Automation**

This repository uses **GitHub Actions** to automatically build and push the image to the GitHub Container Registry (GHCR).

- **Trigger:** Pushes to the `main` branch (only if `Dockerfile` changes).


- **Registry:** `ghcr.io`


- **Permissions:** The package must be set to **Public** in the GitHub Package settings to be usable by other repositories.