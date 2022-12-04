# UBUNTU
FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS build-env
#install ldap libs
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        libldap-2.4-2 \
    && rm -rf /var/lib/apt/lists/*
