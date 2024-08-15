FROM amd64/ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive
WORKDIR /var/task

RUN set -ex \
    && apt-get update \
    && apt-get install -y curl ffmpeg gzip python3 python3-pip unzip xz-utils \
    && apt-get clean \
    && pip install awslambdaric \
    && rm -rf /var/lib/apt/lists/* \
    && mkdir -p /opt/voicevox

WORKDIR /opt/voicevox

# download large files in parallel for caching
RUN curl -L -O https://github.com/VOICEVOX/voicevox_core/releases/download/0.14.3/voicevox_core-linux-x64-cpu-0.14.3.zip 

RUN curl -L -o open_jtalk_dic_utf_8-1.11.tar.gz \
        https://sourceforge.net/projects/open-jtalk/files/Dictionary/open_jtalk_dic-1.11/open_jtalk_dic_utf_8-1.11.tar.gz/download

RUN curl -L -O https://github.com/VOICEVOX/voicevox_core/releases/download/0.14.3/voicevox_core-0.14.3+cpu-cp38-abi3-linux_x86_64.whl

# install voicevox_core
RUN set -ex \
    && unzip voicevox_core-linux-x64-cpu-0.14.3.zip && rm -f voicevox_core-linux-x64-cpu-0.14.3.zip \
    && tar xzf open_jtalk_dic_utf_8-1.11.tar.gz && rm -f open_jtalk_dic_utf_8-1.11.tar.gz \
    && pip install voicevox_core-0.14.3+cpu-cp38-abi3-linux_x86_64.whl \
    && rm -f voicevox_core-0.14.3+cpu-cp38-abi3-linux_x86_64.whl

WORKDIR /var/task

ENV LD_LIBRARY_PATH /opt/voicevox/voicevox_core-linux-x64-cpu-0.14.3
ENV OPEN_JTALK_DICT_DIR /opt/voicevox/open_jtalk_dic_utf_8-1.11

COPY requirements.txt ./
RUN pip install -r requirements.txt
COPY app.py ./

ENTRYPOINT ["/usr/bin/python3", "-m", "awslambdaric"]
CMD ["app.lambda_handler"]
