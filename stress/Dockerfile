FROM ubuntu

MAINTAINER Elastisys <techteam@elastisys.com>

RUN apt-get update && apt-get install -y stress

ENV CPU_CORES 1

CMD stress --cpu $CPU_CORES
