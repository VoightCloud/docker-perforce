FROM voight/perforce-base
MAINTAINER Jeff Voight <jeff.voight@gmail.com>

ARG P4_VERSION=19.1

RUN yum clean all --enablerepo='*' \
    && yum clean metadata --enablerepo='*' \
    && yum install --enablerepo=perforce -y helix-p4d-20${P4_VERSION} helix-cli-20${P4_VERSION} \
    && yum clean all --enablerepo='*' \
    && rm -rf /var/cache/yum


EXPOSE 1666
ENV NAME p4depot
ENV P4CONFIG .p4config
ENV DATAVOLUME /data
ENV P4PORT 1666
ENV P4USER p4admin
ENV P4PASSWD "pass12349ers!"

VOLUME ["$DATAVOLUME"]

ADD ./p4-users.txt /root/
ADD ./p4-groups.txt /root/
ADD ./p4-protect.txt /root/
ADD ./setup-perforce.sh /usr/local/bin/
ADD ./run.sh  /

CMD ["/run.sh"]
