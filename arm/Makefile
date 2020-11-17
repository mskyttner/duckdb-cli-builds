#! make

init:
	docker run --privileged --rm tonistiigi/binfmt --install all
	docker run --rm --privileged multiarch/qemu-user-static --reset -p yes
	sudo systemctl restart docker
	export DOCKER_CLI_EXPERIMENTAL=enabled
	docker buildx install
	docker buildx create --use

build:
	docker buildx build -t mskyttner/duckdb --push --platform=linux/arm/v6,linux/arm/v7,linux/amd64 .

run:
	docker run --rm -it mskyttner/duckdb ash
