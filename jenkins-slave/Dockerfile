#FROM registry.access.redhat.com/rhel7/rhel
FROM rhel7.2

ENV JENKINS_SWARM_VERSION 2.0
ENV JNLP_SLAVE_VERSION 2.52
ENV HOME /opt/jenkins-slave
ENV MAVEN_VERSION 3.3.3
ENV JAVA_HOME /usr/lib/jvm/java

RUN yum --disablerepo=* --enablerepo=rhel-7-server-rpms install yum-utils
RUN yum-config-manager --enable rhel-7-server-rpms rhel-7-server-extras-rpms rhel-7-server-ose-3.1-rpms

RUN yum-config-manager --disable epel  && \
    INSTALL_PKGS="rsync gettext git tar zip unzip nss_wrapper java-1.8.0-openjdk atomic-openshift-clients" && \
    yum install -y $INSTALL_PKGS && \
    rpm -V $INSTALL_PKGS && \
    yum clean all
    
RUN yum install -y git tar zip unzip which java-1.8.0-openjdk-devel && \
    yum clean all && \
    #export INSTALL_PKGS="nss_wrapper gettext origin-clients" && \
    #yum -y --setopt=tsflags=nodocs install epel-release rhel-7-server-ose-3.1-rpms && \
    #yum install -y --setopt=tsflags=nodocs install $INSTALL_PKGS && \
    #rpm -V $INSTALL_PKGS && \
    #yum clean all && \    
    useradd -u 1003 -r -m -d ${HOME} -s /sbin/nologin -c "Jenkins Slave" jenkins-slave && \
    #adduser --uid 1003 --gid 0 -c "Jenkins Slave" jenkins-slave  && \
    mkdir -p /opt/jenkins-slave/bin /var/lib/jenkins && \
    curl -fsSL http://archive.apache.org/dist/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz | tar xzf - -C /usr/share \
      && mv /usr/share/apache-maven-$MAVEN_VERSION /usr/share/maven \
      && ln -s /usr/share/maven/bin/mvn /usr/bin/mvn

RUN git config --global url."https://".insteadOf git://
RUN git config --global user.email "mukul37@gmail.com" 
RUN git config --global user.name "Mukul"

# Copy script
COPY jenkins-slave.sh /opt/jenkins-slave/bin/

# Download plugin and modify permissions
RUN curl --create-dirs -sSLo /opt/jenkins-slave/bin/swarm-client-$JENKINS_SWARM_VERSION-jar-with-dependencies.jar http://maven.jenkins-ci.org/content/repositories/releases/org/jenkins-ci/plugins/swarm-client/$JENKINS_SWARM_VERSION/swarm-client-$JENKINS_SWARM_VERSION-jar-with-dependencies.jar \
  && curl --create-dirs -sSLo /opt/jenkins-slave/bin/slave.jar http://repo.jenkins-ci.org/public/org/jenkins-ci/main/remoting/$JNLP_SLAVE_VERSION/remoting-$JNLP_SLAVE_VERSION.jar \
  && chmod -R 775 /opt/jenkins-slave /var/lib/jenkins && \
  chown -R jenkins-slave:root /opt/jenkins-slave /var/lib/jenkins

WORKDIR /var/lib/jenkins

VOLUME /var/lib/jenkins

USER 1003

ENTRYPOINT ["/opt/jenkins-slave/bin/jenkins-slave.sh"]
