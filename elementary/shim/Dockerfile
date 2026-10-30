FROM docker.io/library/ubuntu@sha256:008173c23f95b170204355c12626cb5a965d779a7e1283b09e9cffbb1bf33ca3
ENV DEBIAN_FRONTEND=noninteractive LC_ALL=C.UTF-8 SOURCE_DATE_EPOCH=1755116379
ADD https://snapshot.ubuntu.com/ubuntu/20260930T000000Z/pool/main/c/ca-certificates/ca-certificates_20260223_all.deb /tmp/ca-certificates.deb
RUN echo 'f7025ab9b24cd73215510931037b02d6960d89584d0d00afba81851abdbe6ef1  /tmp/ca-certificates.deb' | sha256sum -c - \
    && dpkg-deb -x /tmp/ca-certificates.deb /tmp/ca-bootstrap \
    && mkdir -p /etc/ssl/certs \
    && cat /tmp/ca-bootstrap/usr/share/ca-certificates/mozilla/*.crt > /etc/ssl/certs/ca-certificates.crt \
    && rm -rf /tmp/ca-certificates.deb /tmp/ca-bootstrap
RUN rm -f /etc/apt/sources.list /etc/apt/sources.list.d/* \
    && printf '%s\n' \
       'deb [check-valid-until=no] https://snapshot.ubuntu.com/ubuntu/20260930T000000Z noble main universe' \
       'deb [check-valid-until=no] https://snapshot.ubuntu.com/ubuntu/20260930T000000Z noble-updates main universe' \
       'deb [check-valid-until=no] https://snapshot.ubuntu.com/ubuntu/20260930T000000Z noble-security main universe' \
       > /etc/apt/sources.list \
    && apt-get update --error-on=any \
    && apt-get install -y --no-install-recommends build-essential libelf-dev \
       libefivar-dev bsdextrautils dos2unix ca-certificates curl openssl python3 git \
    && rm -rf /var/lib/apt/lists/*
RUN curl -fL --retry 3 -o /tmp/shim.tar.bz2 \
       https://github.com/rhboot/shim/releases/download/16.1/shim-16.1.tar.bz2 \
    && echo '46319cd228d8f2c06c744241c0f342412329a7c630436fce7f82cf6936b1d603  /tmp/shim.tar.bz2' | sha256sum -c - \
    && mkdir /build && tar -xf /tmp/shim.tar.bz2 -C /build --strip-components=1 \
    && rm /tmp/shim.tar.bz2
COPY elementary/signing/vale-secure-boot.der /build/vale.cer
COPY elementary/shim/sbat.vale.csv /build/data/sbat.vale.csv
WORKDIR /build
RUN make -j2 VENDOR_CERT_FILE=vale.cer 'DEFAULT_LOADER=\\systemd-bootx64.efi' \
       ENABLE_CODESIGN_EKU=1 'POST_PROCESS_PE_FLAGS=-n -x' \
    && mkdir /output \
    && cp shimx64.efi mmx64.efi fbx64.efi /output/ \
    && dpkg-query -W > /output/build-packages.txt \
    && (cd /output && sha256sum *.efi > SHA256SUMS && cat SHA256SUMS)
