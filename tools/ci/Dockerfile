FROM bcosbuild:latest as builder
LABEL maintainer service@fisco.com.cn

WORKDIR /

ARG SOURCE_BRANCH
ENV DEBIAN_FRONTEND=noninteractive \
    SOURCE=${SOURCE_BRANCH:-master}

RUN git clone https://oauth2:15cu-VPcCbmEjzW9hgCx@git.iflytek.com/TC_FLYBC/FISCO-BCOS.git && cd FISCO-BCOS \
    && git checkout ${SOURCE} && cp -r /deps ./deps && cmake . && make -j2 && strip bin/fisco-bcos

FROM ubuntu:18.04
LABEL maintainer service@fisco.com.cn

RUN apt-get -q update && apt-get install -qy --no-install-recommends libssl-dev zlib1g-dev \
    && ln -fs /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && apt-get install -qy --no-install-recommends tzdata \
    && dpkg-reconfigure --frontend noninteractive tzdata \
    && rm -rf /var/lib/apt/lists/*

COPY --from=builder /FISCO-BCOS/bin/fisco-bcos /usr/local/bin/

EXPOSE 30300 20200 8545

ENTRYPOINT ["/usr/local/bin/fisco-bcos"]
CMD ["--version"]

