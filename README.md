# duckdb-cli-appimage

This repo contains some files for local (and cloud) builds of the [duckdb CLI tool](https://duckdb.org).

## Cloud builds

- See [this GitHub Action](.github/workflows/push-duckdb-cli-appimage.yml) for the appimage build.
- See [this GitHub Action](.github/workflows/push-duckdb-cli-arm.yml) for the arm crossbuild.

To get the resulting binaries, one has to pull the images and [extract them](appimage/Makefile).

## Local builds

Docker (and the buildx docker CLI-plugin for ARM) is required, and `make`.

- [](appimage/) contains files for building an AppImage for x64 linuxes (baked into a container image)
- [](arm/) contains files for building the duckdb CLI for arm/v6, arm/v7 (rpis etc) and linux/amd64

### ARM build

For this you need docker and a cli-plugin (that comes with Docker Desktop) called buildx. Docker Desktop is needed only if you're on MacOS or Windows. Otherwise, if using Docker Community Edition, the tools for crossbuilding can be set up like so:

```bash
# use docker and "buildx" cli plugin; it enables building for linux/arm* and some other archs:
# see https://github.com/docker/buildx
# some commands to set up buildx for a cross build
docker run --privileged --rm tonistiigi/binfmt --install all
docker run --rm --privileged multiarch/qemu-user-static --reset -p yes
sudo systemctl restart docker
export DOCKER_CLI_EXPERIMENTAL=enabled
docker buildx install
docker buildx create --use
```

The buildx setup above reports to give support for building for these archs/platforms: 

- `linux/amd64, linux/arm64, linux/riscv64, linux/ppc64le, linux/s390x, linux/386, linux/arm/v7, linux/arm/v6`

In the ./arm directory, there is a Makefile.

Use `make build` in that directory to build the multistage Dockerfile.

### AppImage build

In the ./appimage directory, there is a Makefile.

Use `make build` there, which builds the multistage Dockerfile, in three stages:

- Uses Ubunty Trusty to build the duckdb CLI tool 
- Uses another toolset for generating the AppImage
- Takes the previous build artifacts and installs it on a Debian (oldstable-slim)

Then use `make extract` to copy out the duckdb binary or the duckdb-AppImage.

The resulting duckdb-AppImage should work on many x64 linuxes.

NB: Instead of using "linuxdeploy", the "appimage-builder" could be used:

- <https://github.com/tyu1996/opera-AppImage/blob/master/opera.yml>



