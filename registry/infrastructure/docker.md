# Docker & Containerization Guidelines

These rules dictate how Dockerfiles and docker-compose files must be written. The absolute priority is to generate images that are secure, lightweight, and production-ready.

## 1. Multi-Stage Builds (Strict Requirement)
* **Separation of Build and Runtime:** Every Dockerfile MUST use multi-stage builds. 
* **The Build Stage:** Use a heavy, fully-featured SDK image (e.g., `node:18`, `golang:1.21`, `mcr.microsoft.com/dotnet/sdk`) to compile the code and install dependencies.
* **The Production Stage:** Copy only the compiled artifacts (and production dependencies) into a minimal base image (e.g., `alpine`, `distroless`, or the framework's specific minimal runtime) to drastically reduce the final image size and attack surface.

## 2. Principle of Least Privilege (No Root)
* **Never Run as Root:** The application inside the container must NEVER run as the root user. 
* **Explicit User:** Always create a dedicated, unprivileged system user and group within the Dockerfile, and switch to it using the `USER` instruction before defining the `CMD` or `ENTRYPOINT`.

## 3. Caching & Layer Optimization
* **Order of Operations:** Order commands to maximize Docker layer caching. Copy package dependency files (`package.json`, `Cargo.toml`, `requirements.txt`) and run the install command BEFORE copying the rest of the application source code. This prevents reinstalling dependencies every time a single line of code changes.

## 4. Configuration
* **No `latest` Tags:** Never use the `:latest` tag for base images. Always pin to a specific, immutable version tag (e.g., `node:18.17.0-alpine`) to guarantee reproducible builds.