# UBUNTU
FROM mcr.microsoft.com/dotnet/core/sdk:3.1-bionic AS build-env
#install JAVA
RUN apt-get update && apt-get -y install openjdk-8-jdk
#install SonarScanner
ENV PATH "$PATH:/root/.dotnet/tools"
RUN dotnet tool install dotnet-sonarscanner --global