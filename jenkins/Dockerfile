FROM registry.access.redhat.com/openshift3/jenkins-1-rhel7:1.609-15

ADD config-cloud.xml credentials.xml /opt/openshift/

RUN yum install -y --nogpgcheck --disableplugin=subscription-manager git tar zip unzip which java-1.8.0-openjdk-devel && \
    yum clean all && \
    useradd -u 1001 -r -m -d ${HOME} -s /sbin/nologin -c "Jenkins Slave" jenkins-slave && \
    mkdir -p /opt/jenkins-slave/bin /var/lib/jenkins && \
    curl -fsSL http://archive.apache.org/dist/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz | tar xzf - -C /usr/share \
      && mv /usr/share/apache-maven-$MAVEN_VERSION /usr/share/maven \
      && ln -s /usr/share/maven/bin/mvn /usr/bin/mvn

# Install additional plugins and modify files
RUN mkdir -p /opt/openshift/plugins && \
          cat /opt/openshift/credentials.xml > /opt/openshift/configuration/credentials.xml && \
          curl -sSL -f https://updates.jenkins-ci.org/download/plugins/swarm/2.0/swarm.hpi -o /opt/openshift/plugins/swarm.jpi && \
		  curl -sSL -f https://updates.jenkins-ci.org/download/plugins/durable-task/1.7/durable-task.hpi -o /opt/openshift/plugins/durable-task.jpi && \
		  curl -sSL -f https://updates.jenkins-ci.org/download/plugins/credentials/1.24/credentials.hpi -o /opt/openshift/plugins/credentials.jpi && \
		  curl -sSL -f https://updates.jenkins-ci.org/download/plugins/kubernetes/0.5/kubernetes.hpi -o /opt/openshift/plugins/kubernetes.jpi && \
		  touch /opt/openshift/plugins/credentials.jpi.pinned && \
		  sed -i "s|<slaveAgentPort>.*</slaveAgentPort>|<slaveAgentPort>50000</slaveAgentPort>|" "/opt/openshift/configuration/config.xml" && \
		  sed -i "s|<numExecutors>.*</numExecutors>|<numExecutors>0</numExecutors>|" "/opt/openshift/configuration/config.xml" && \
		  sed -i "/<slaves\/>/r /opt/openshift/config-cloud.xml" "/opt/openshift/configuration/config.xml" && \
		  chmod -R og+rw /opt/openshift/plugins && \
		  rm -rf /opt/openshift/config-cloud.xml /opt/openshift/credentials.xml