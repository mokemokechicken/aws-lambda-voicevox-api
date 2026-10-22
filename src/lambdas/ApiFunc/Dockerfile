FROM --platform=linux/amd64 python:3.11.17-bookworm@sha256:a54ac40b50750eb18d584a0cccf902330c2708cc4248b1be3d5e2be7d07f2cad

ENV DEBIAN_FRONTEND=noninteractive
WORKDIR /var/task
COPY requirements.txt /tmp/requirements.txt
ENV SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt

RUN set -ex \
    && apt-get update \
    && apt-get install -y ca-certificates=20250419~deb12u2 curl ffmpeg gzip unzip xz-utils \
    && test "$(dpkg-query -W -f='${Version}' ca-certificates)" = "20250419~deb12u2" \
    && update-ca-certificates \
    && python3.11 -m pip install --require-hashes -r /tmp/requirements.txt \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
    && mkdir -p /opt/voicevox

WORKDIR /opt/voicevox

# download large files in parallel for caching
RUN curl -fL --retry 3 -o voicevox_core-linux-x64-cpu-0.14.3.zip https://github.com/VOICEVOX/voicevox_core/releases/download/0.14.3/voicevox_core-linux-x64-cpu-0.14.3.zip \
    && echo '7cb696d32b93f3594faa49f5c9396af4ffa1a40a34ca596db6998def9c6c1508  voicevox_core-linux-x64-cpu-0.14.3.zip' | sha256sum --check -

RUN curl -fL --retry 3 -o open_jtalk_dic_utf_8-1.11.tar.gz \
        https://sourceforge.net/projects/open-jtalk/files/Dictionary/open_jtalk_dic-1.11/open_jtalk_dic_utf_8-1.11.tar.gz/download \
    && echo '33e9cd251bc41aa2bd7ca36f57abbf61eae3543ca25ca892ae345e394cb10549  open_jtalk_dic_utf_8-1.11.tar.gz' | sha256sum --check -

RUN curl -fL --retry 3 -o voicevox_core-0.14.3+cpu-cp38-abi3-linux_x86_64.whl https://github.com/VOICEVOX/voicevox_core/releases/download/0.14.3/voicevox_core-0.14.3+cpu-cp38-abi3-linux_x86_64.whl \
    && echo '12421247abed95a26b96ffa000437e3b07876f0f5d864186361234169c2cfe7a  voicevox_core-0.14.3+cpu-cp38-abi3-linux_x86_64.whl' | sha256sum --check -

# install voicevox_core
RUN set -ex \
    && unzip voicevox_core-linux-x64-cpu-0.14.3.zip && rm -f voicevox_core-linux-x64-cpu-0.14.3.zip \
    && tar xzf open_jtalk_dic_utf_8-1.11.tar.gz && rm -f open_jtalk_dic_utf_8-1.11.tar.gz \
    && python3.11 -m pip install --no-deps --no-index voicevox_core-0.14.3+cpu-cp38-abi3-linux_x86_64.whl \
    && rm -f voicevox_core-0.14.3+cpu-cp38-abi3-linux_x86_64.whl

WORKDIR /var/task

ENV LD_LIBRARY_PATH /opt/voicevox/voicevox_core-linux-x64-cpu-0.14.3
ENV OPEN_JTALK_DICT_DIR /opt/voicevox/open_jtalk_dic_utf_8-1.11

COPY app.py ./

ENTRYPOINT ["/usr/local/bin/python3.11", "-m", "awslambdaric"]
CMD ["app.lambda_handler"]
