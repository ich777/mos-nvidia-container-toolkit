FROM debian:buster-slim

RUN echo "deb http://archive.debian.org/debian/ buster main contrib non-free" > /etc/apt/sources.list && \
    echo "deb http://archive.debian.org/debian-security/ buster/updates main contrib non-free" >> /etc/apt/sources.list && \
    echo 'Acquire::Check-Valid-Until "false";' > /etc/apt/apt.conf.d/99no-check-valid-until

RUN apt-get update && \
  DEBIAN_FRONTEND=noninteractive apt-get -y install --no-install-recommends dpkg wget curl ca-certificates git build-essential \
  dh-make fakeroot devscripts lsb-release && \
  rm -rf /var/lib/apt/lists/*

ENV OS_ARCH="amd64"
ENV GOLANG_VERSION="1.22.12"
ENV BUILD_DIR=/tmp
ENV GOPATH=$BUILD_DIR/go
ENV PATH=/opt/go/bin:$PATH
ENV GPG_TTY=/dev/console
ENV PACKAGE_DIR=$BUILD_DIR/package

COPY config.toml /opt/config.toml

RUN curl https://storage.googleapis.com/golang/go${GOLANG_VERSION}.linux-${OS_ARCH}.tar.gz | tar -C /opt -xz
RUN chmod -R 777 /opt

CMD bash -c "cd $BUILD_DIR && \
  mkdir -p $GOPATH/src/github.com/NVIDIA && \
  cd $GOPATH/src/github.com/NVIDIA && \
  git clone --depth 1 --branch v${TOOLKIT_VERSION} https://github.com/NVIDIA/nvidia-container-toolkit && \
  cd $GOPATH/src/github.com/NVIDIA/nvidia-container-toolkit && \
  git checkout v${TOOLKIT_VERSION} && \
  mkdir -p $PACKAGE_DIR/usr/bin $PACKAGE_DIR/DEBIAN $PACKAGE_DIR/etc/nvidia-container-runtime $PACKAGE_DIR/usr/share/doc/nvidia-container-toolkit && \
  sed -i '/if err := updateLdCache(os\.Args); err != nil/,+3d' $GOPATH/src/github.com/NVIDIA/nvidia-container-toolkit/cmd/nvidia-cdi-hook/update-ldcache/update-ldcache.go && \
  sed -i '/func updateLdCacheHandler() {/a\\\\tvar _ = log.Printf' $GOPATH/src/github.com/NVIDIA/nvidia-container-toolkit/cmd/nvidia-cdi-hook/update-ldcache/update-ldcache.go && \
  make LIBNVIDIA_CONTAINER_VERSION=${TOOLKIT_VERSION} LIBNVIDIA_CONTAINER_TAG=${TOOLKIT_VERSION} binaries && \
  cp $GOPATH/src/github.com/NVIDIA/nvidia-container-toolkit/nvidia-container-runtime-hook $PACKAGE_DIR/usr/bin && \
  cp $GOPATH/src/github.com/NVIDIA/nvidia-container-toolkit/nvidia-container-runtime $PACKAGE_DIR/usr/bin && \
  cp $GOPATH/src/github.com/NVIDIA/nvidia-container-toolkit/nvidia-ctk $PACKAGE_DIR/usr/bin && \
  cp $GOPATH/src/github.com/NVIDIA/nvidia-container-toolkit/nvidia-cdi-hook $PACKAGE_DIR/usr/bin && \
  cp $GOPATH/src/github.com/NVIDIA/nvidia-container-toolkit/nvidia-container-runtime.cdi $PACKAGE_DIR/usr/bin && \
  cp $GOPATH/src/github.com/NVIDIA/nvidia-container-toolkit/nvidia-container-runtime.legacy $PACKAGE_DIR/usr/bin && \
  cd $PACKAGE_DIR/usr/bin && \
  ln -s nvidia-container-runtime-hook nvidia-container-toolkit && \
  cp /opt/config.toml $PACKAGE_DIR/etc/nvidia-container-runtime/config.toml && \
  cat $GOPATH/src/github.com/NVIDIA/nvidia-container-toolkit/LICENSE* >> $PACKAGE_DIR/usr/share/doc/nvidia-container-toolkit/LICENSE && \
  cd $BUILD_DIR && \
  echo \"Package: nvidia-container-toolkit\" > $PACKAGE_DIR/DEBIAN/control && \
  echo \"Version: $TOOLKIT_VERSION\" >> $PACKAGE_DIR/DEBIAN/control && \
  echo \"Architecture: amd64\" >> $PACKAGE_DIR/DEBIAN/control && \
  echo \"Maintainer: ich777\" >> $PACKAGE_DIR/DEBIAN/control && \
  echo \"Description: nvidia-container-toolkit for MOS\" >> $PACKAGE_DIR/DEBIAN/control && \
  dpkg-deb --build package $BUILD_DIR/nvidia-container-toolkit_${TOOLKIT_VERSION}-1+mos_amd64.deb && \
  md5sum $BUILD_DIR/nvidia-container-toolkit_${TOOLKIT_VERSION}-1+mos_amd64.deb | cut -d ' ' -f1 > $BUILD_DIR/nvidia-container-toolkit_${TOOLKIT_VERSION}-1+mos_amd64.deb.md5"
