FROM ubuntu:trusty

RUN apt-get update -y && apt-get install -y \
	unzip \
	ninja-build \
	build-essential \
	cmake3 \
	curl

WORKDIR /opt

RUN curl -fL https://github.com/cwida/duckdb/archive/v0.2.2.tar.gz -o duckdb.tgz && \
	tar xfz duckdb.tgz --strip-components=1 && \
	rm duckdb.tgz

RUN curl "https://github.com/AppImage/AppImageKit/releases/download/continuous/appimagetool-x86_64.AppImage" -L --output appimagetool-x86_64.AppImage --silent && \
    chmod a+x appimagetool-x86_64.AppImage

RUN N_CPU=$((`nproc --all`-1)) && \
	echo "Number of CPUs: $N_CPU" && \
	cmake . -DCMAKE_INSTALL_PREFIX=/usr && \
	make -j$N_CPU && \
	make install DESTDIR=AppDir

FROM appimagecrafters/appimage-builder:0.7.5

WORKDIR /opt
COPY --from=0 /opt/AppDir AppDir

RUN apt-get update -y && apt-get install -y strace curl nano libfuse-dev

RUN curl "https://github.com/linuxdeploy/linuxdeploy/releases/download/continuous/linuxdeploy-x86_64.AppImage" -L --output linuxdeploy --silent && \
	chmod +x linuxdeploy

#COPY duckdb.desktop AppDir/usr/share/applications/duckdb.desktop
#COPY duckdb.png AppDir/share/icons/hicolor/48x48/apps/duckdb.png
#COPY duckdb.svg AppDir/usr/share/icons/hicolor/scalable/apps/duckdb.svg
COPY duckdb.desktop .
COPY duckdb.png .
COPY duckdb.svg .

RUN ./linuxdeploy --appimage-extract-and-run --appdir AppDir && \
	./linuxdeploy --appimage-extract-and-run -dduckdb.desktop -iduckdb.svg --appdir AppDir --output appimage

FROM debian:oldstable-slim

WORKDIR /usr/local/bin
COPY --from=1 /opt/AppDir/usr/bin/duckdb .
COPY --from=1 /opt/duckdb-x86_64.AppImage .

WORKDIR /data
VOLUME ["/data"]

CMD ["duckdb", "--help"]
