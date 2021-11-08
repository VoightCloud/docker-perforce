FROM voight/perforce-server
MAINTAINER Jeff Voight <jeff.voight@gmail.com>

RUN curl -sSL ftp://ftp.perforce.com/perforce/tools/sampledepot.tar.gz > sampledepot.tar.gz

ADD ./run.sh  /

CMD ["/run.sh"]
