FROM navikey/raspbian-buster

# this base is built from ci here: https://github.com/navikey/raspbian-buster/blob/master/.github/workflows/docker-image.yml
# runs on linux/arm,linux/arm/v6,linux/arm/v7

RUN apt-get update -y && apt-get install -y \
	unzip \
	build-essential \
	curl \
	ninja-build \
	cmake

WORKDIR /opt

RUN curl -fL https://github.com/cwida/duckdb/archive/v0.2.2.tar.gz -o duckdb.tgz && \
	tar xfz duckdb.tgz --strip-components=1 && \
	rm duckdb.tgz

RUN N_CPU=$((`nproc --all`-0)) && \
	echo "Number of CPUs: $N_CPU" && \
	cmake -G "Unix Makefiles" -DCMAKE_INSTALL_PREFIX=/usr -DBUILD_PARQUET_EXTENSION=TRUE -DCMAKE_BUILD_TYPE=Release -DBUILD_UNITTESTS=0 -DDISABLE_UNITY=1 . && \
	make -j$N_CPU && \
	make install DESTDIR=/usr/local/bin

FROM navikey/raspbian-buster

WORKDIR /usr/local/bin
COPY --from=0 /usr/local/bin/duckdb .
RUN chmod +x duckdb

WORKDIR /data
VOLUME ["/data"]

CMD ["duckdb --help"]
