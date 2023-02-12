# Build Images
    
 - Build Local Image

    `docker buildx build --load --tag scootsoftware/snapserver:latest .`

 - Enable Cross-compilation

    `docker run --rm --privileged multiarch/qemu-user-static --reset -p yes`

 - Test Build

    `docker buildx build --platform linux/arm64,linux/amd64 --tag scootsoftware/snapserver:latest .`

 - Build & Push Images

    `docker buildx build --push --platform linux/arm64,linux/amd64 --tag scootsoftware/snapserver:latest .`
