FROM evarga/jenkins-slave
RUN apt-get update
RUN apt-get install -y openjdk-8-jdk git curl

#installation manuelle de maven

RUN wget http://mirrors.ircam.fr/pub/apache/maven/maven-3/3.5.4/binaries/apache-maven-3.5.4-bin.tar.gz
RUN mv apache-maven-3.5.4-bin.tar.gz /opt
RUN tar -xvf /opt/apache-maven-3.5.4-bin;tar.gz -C /opt
RUN mv /opt/apache-maven-3.5.4 /opt/apache-maven
RUN updates-alternatives --install /usr/bin/mvn maven /opt/apache-maven/bin/mvn 1001
RUN update-ca-alternatives --fresh
RUN mvn --version
RUN java -version
