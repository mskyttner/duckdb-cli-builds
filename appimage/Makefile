#! make

all: build extract

init:
	docker run --privileged --rm tonistiigi/binfmt --install all
	docker run --rm --privileged multiarch/qemu-user-static --reset -p yes
	sudo systemctl restart docker
	export DOCKER_CLI_EXPERIMENTAL=enabled
	docker buildx install
	docker buildx create --use

build:
	docker build --load -t "mskyttner/duckdb:cli" .

extract:
	docker run --name duckdb mskyttner/duckdb:cli duckdb -version
	docker cp duckdb:/usr/local/bin/duckdb-x86_64.AppImage .
	docker stop duckdb
	docker rm duckdb

