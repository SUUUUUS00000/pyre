FROM ubuntu:24.04

ARG DEBIAN_FRONTEND=noninteractive
ARG EMSDK_VERSION=latest
ARG NODE_MAJOR=22

RUN apt-get update && apt-get install -y --no-install-recommends \
        ca-certificates curl git build-essential cmake \
        python3 python3-pip xz-utils \
        openjdk-21-jdk-headless gradle \
    && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL "https://deb.nodesource.com/setup_${NODE_MAJOR}.x" | bash - \
    && apt-get install -y --no-install-recommends nodejs \
    && rm -rf /var/lib/apt/lists/*

RUN git clone --depth 1 https://github.com/emscripten-core/emsdk.git /opt/emsdk \
    && cd /opt/emsdk \
    && ./emsdk install $EMSDK_VERSION \
    && ./emsdk activate $EMSDK_VERSION

RUN echo '. /opt/emsdk/emsdk_env.sh > /dev/null 2>&1' >> /etc/bash.bashrc

ENV PATH="/opt/emsdk:/opt/emsdk/upstream/emscripten:/opt/emsdk/upstream/bin:${PATH}"
ENV EMSDK="/opt/emsdk"

# Автоматический перенос кода вашего форка внутрь контейнера
COPY . /work
WORKDIR /work

# Запуск компиляции C++ исходников в WebAssembly-модуль
RUN ./decompiler-wasm/build.sh

# Сборка спецификаций процессоров Ghidra
RUN ./specs/stage-specs.sh /opt

# Переход в директорию сайта, установка зависимостей и сборка
WORKDIR /work/web
RUN npm install

EXPOSE 5173

# Команда автоматического запуска веб-интерфейса в облаке
CMD ["npm", "run", "dev", "--", "--host", "0.0.0.0"]
