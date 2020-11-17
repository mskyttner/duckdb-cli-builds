FROM alpine

RUN apk update -qq && apk upgrade && apk add --no-cache \
	build-base \
	cmake clang clang-dev make gcc g++ libc-dev linux-headers \
	ninja \
	unzip \
	curl

WORKDIR /build

RUN curl -fL https://github.com/cwida/duckdb/archive/v0.2.2.tar.gz -o duckdb.tgz && \
	tar xfz duckdb.tgz --strip-components=1 && \
	rm duckdb.tgz

RUN cmake . -G Ninja

RUN N_CPU=$((`nproc --all`-1)) && \
	echo "Number of CPUs: $N_CPU" && \
	ninja -j $N_CPU

FROM alpine

# add system library required for duckdb cli to run
RUN apk update -qq && apk add --no-cache libstdc++

WORKDIR /usr/local/bin
COPY --from=0 /build/duckdb .
RUN chmod +x duckdb

WORKDIR /data
VOLUME ["/data"]

CMD ["duckdb --help"]

