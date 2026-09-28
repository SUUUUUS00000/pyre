FROM ubuntu:24.04

ARG DEBIAN_FRONTEND=noninteractive
ARG EMSDK_VERSION=latest
ARG NODE_MAJOR=22

RUN apt-get update && apt-get install -y --no-install-recommends \
        ca-certificates curl git build-essential cmake \
        python3 python3-pip xz-utils \
        openjdk-21-jdk-headless gradle \
    && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL "https://nodesource.com{NODE_MAJOR}.x" | bash - \
    && apt-get install -y --no-install-recommends nodejs \
    && rm -rf /var/lib/apt/lists/*

RUN git clone --depth 1 https://github.com /opt/emsdk \
    && cd /opt/emsdk \
    && ./emsdk install $EMSDK_VERSION \
    && ./emsdk activate $EMSDK_VERSION

RUN echo '. /opt/emsdk/emsdk_env.sh > /dev/null 2>&1' >> /etc/bash.bashrc

ENV PATH="/opt/emsdk:/opt/emsdk/upstream/emscripten:/opt/emsdk/upstream/bin:${PATH}"
ENV EMSDK="/opt/emsdk"

COPY . /work
WORKDIR /work

RUN ./decompiler-wasm/build.sh

RUN mkdir -p specs/dist && echo '{}' > specs/dist/manifest.json
RUN ln -s /work/decompiler-wasm/dist web/public/decompiler
RUN ln -s /work/specs/dist web/public/specs

WORKDIR /work/web
RUN npm install
RUN npm run build

EXPOSE 5173

CMD ["npm", "run", "preview", "--", "--host", "0.0.0.0", "--port", "5173"]
