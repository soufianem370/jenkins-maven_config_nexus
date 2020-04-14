# integrate nexus to maven/jenkins

install jenkins(in your instance jenkins install git and maven

watch this videos for more details: https://www.youtube.com/watch?v=qbO4MTESiJQ

## pour installer nexus with chart helm
docker file avec la dernière version

```
FROM maven:3-jdk-8 AS helmkar

ADD https://github.com/sonatype-nexus-community/nexus-repository-helm/archive/master.zip nexus-repository-helm.zip
RUN unzip nexus-repository-helm.zip && cd nexus-repository-helm-master && mvn -PbuildKar clean install

FROM sonatype/nexus3
COPY --from=helmkar nexus-repository-helm-master/target/*.kar $NEXUS_HOME/deploy
```
build and tag image nexus
```
docker build -f nexus-helm.Dockerfile -t nexus3-helm .
docker run --name nexus-helm -d -p 8081:8081 nexus3-helm
```
install nexus with helm

```
helm install nexus stable/sonatype-nexus
```
