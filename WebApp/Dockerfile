# Base image for running the application
# This sets the working directory inside the container to /app. All subsequent commands will run relative to this directory
# EXPOSE 80 This informs Docker that the container will listen on port 80 at runtime. It�s a documentation feature and doesn�t actually publish the port; it just indicates that port 80 should be accessible.
FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base
WORKDIR /app
EXPOSE 80

# Image for building the service project
# COPY ["WebApp.csproj", "."] This copies the WebApp.csproj file from your local machine into the container�s /src directory. Only the .csproj file is copied here to optimize the layer cache, as restoring dependencies doesn�t require the full source code.
# COPY . . This copies all files from the current directory on your local machine into the container�s current directory (/src). This includes the source code and any other necessary files.
# This command builds the project using the configuration specified in the BUILD_CONFIGURATION argument (default is Release). The output binaries are placed in the /app/build directory.
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["WebApp.csproj", "./"]
RUN dotnet restore "WebApp.csproj"
COPY . ./
WORKDIR "/src"
RUN dotnet build "WebApp.csproj" -c $BUILD_CONFIGURATION -o /app/build

# Image for publishing the service project
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "WebApp.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# Final image for running the application
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "WebApp.dll"]
