FROM mcr.microsoft.com/dotnet/core/sdk:3.1 AS build
WORKDIR /source

ARG RUNTIME=alpine-x64

# copy csproj and restore as distinct layers
COPY *.csproj .
RUN dotnet restore -r $RUNTIME

# copy and publish app and libraries
COPY . .
RUN dotnet publish \
  --runtime $RUNTIME \
  --self-contained true \
  /p:PublishTrimmed=true \
  /p:PublishSingleFile=true \
  -c Release \
  -o /app \
  --no-restore

# final stage/image
FROM homeassistant/amd64-base:3.12
#FROM mcr.microsoft.com/dotnet/core/runtime-deps:3.1-alpine3.12

###########################################################
# BEGIN COPY & PASTE 
# https://github.com/dotnet/dotnet-docker/blob/master/src/runtime-deps/3.1/alpine3.12/amd64/Dockerfile
###########################################################

RUN apk add --no-cache \
        ca-certificates \
        \
        # .NET Core dependencies
        krb5-libs \
        libgcc \
        libintl \
        libssl1.1 \
        libstdc++ \
        zlib

ENV \
#    # Configure web servers to bind to port 80 when present
#    ASPNETCORE_URLS=http://+:80 \
    # Enable detection of running in a container
    DOTNET_RUNNING_IN_CONTAINER=true \
    # Set the invariant mode since icu_libs isn't included (see https://github.com/dotnet/announcements/issues/20)
    DOTNET_SYSTEM_GLOBALIZATION_INVARIANT=true

###########################################################
# END COPY & PASTE 
###########################################################

WORKDIR /app
COPY --from=build /app .
ENTRYPOINT ["./NetCore.Docker"]
