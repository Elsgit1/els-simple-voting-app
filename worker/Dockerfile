# because of dotnet, we always build on amd64, and target platforms in cli
# dotnet doesn't support QEMU for building or running. 
# (errors common in arm/v7 32bit) https://github.com/dotnet/dotnet-docker/issues/1537
# https://hub.docker.com/_/microsoft-dotnet
# hadolint ignore=DL3029
# to build for a different platform than your host, use --platform=<platform>
# for example, if you were on Intel (amd64) and wanted to build for ARM, you would use:
# docker buildx build --platform "linux/arm64/v8" .
# Use the official .NET SDK image for the build stage
FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build

# Set environment variables to reduce image size
ENV DOTNET_CLI_TELEMETRY_OPTOUT=1 \
    DOTNET_SKIP_FIRST_TIME_EXPERIENCE=1 \
    DOTNET_NOLOGO=true

# Set the working directory
WORKDIR /src

# Copy the .csproj file and restore dependencies
COPY *.csproj ./
RUN dotnet restore --no-cache

# Copy the remaining application files
COPY . .

# Build the application in Release mode
RUN dotnet publish -c Release -o /app --no-restore

# Use a lightweight runtime image for the final stage
FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS runtime

# Set the working directory
WORKDIR /app

# Copy the build output from the previous stage
COPY --from=build /app ./

# # Set up a non-root user for better security
# RUN adduser --disabled-password appuser
# USER appuser

# Specify the entry point for the application
ENTRYPOINT ["dotnet", "Worker.dll"]
