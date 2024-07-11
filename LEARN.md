# Understanding Docker and Container Technology

To understand Docker, we first need to know about container technology. Containers allow developers to package an application with all its dependencies and test it across different operating systems. Essentially, if we want to test our app on different operating systems, we don't need to install those operating systems. Instead, we can simply send our container, which includes our project, and test it.

## What is Docker?

Docker is an open-source container service. It allows us to create multiple containers and work across different systems. Since Docker is open-source, you can share your app on Docker Hub, and anyone can pull your project and use it. Not only can individuals share their projects, but large companies also share their app images for public use. Examples include PostgreSQL, MySQL, and Selenium. You can check all available Docker images on [Docker Hub](https://hub.docker.com/).

## Dockerizing a .NET Project

We have learned the basics of Docker, and now I want to demonstrate how Docker works by dockerizing a test project I created. The following is a simple configuration for a Dockerfile based on .NET documentation. While this is my first attempt, and I am aware that there are more complex Dockerfile configurations, this serves as a good starting point.

### Dockerfile

```
# Use the official ASP.NET Core runtime as a base image
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 80
```
# Use the official .NET SDK as a build environment
```
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY ["DockerizeWithRider.csproj", "."]
RUN dotnet restore "./DockerizeWithRider.csproj"
COPY . .
RUN dotnet publish "DockerizeWithRider.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=build /app/publish .
ENTRYPOINT ["dotnet", "DockerizeWithRider.dll"]` 
```
### Explanation

#### Base Image

```
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 80` 
```
-   **FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base**: This sets up the runtime environment for our application. Since our project runs on .NET 8.0, we specify the runtime version.
-   **WORKDIR /app**: Sets the working directory for our project inside the container, not on the local machine.
-   **EXPOSE 80**: Informs Docker that the container will listen on port 80 at runtime. Since we created a web app, specifying the port is necessary.

#### Build Environment

```
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY ["DockerizeWithRider.csproj", "."]
RUN dotnet restore "./DockerizeWithRider.csproj"
COPY . .
RUN dotnet publish "DockerizeWithRider.csproj" -c Release -o /app/publish` 
```
-   **FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build**: This sets up the build environment, using the .NET SDK to compile our project.
-   **WORKDIR /src**: Creates a new working directory named `/src` to hold our source code and project files.
-   **COPY ["DockerizeWithRider.csproj", "."]**: Copies the project file to the `/src` folder.
-   **RUN dotnet restore "./DockerizeWithRider.csproj"**: Restores dependencies and tools for the specific project.
-   **COPY . .**: Copies all files from the current directory to the container's `/src` directory.
-   **RUN dotnet publish "DockerizeWithRider.csproj" -c Release -o /app/publish**: Publishes the application in Release configuration, outputting to `/app/publish`.

#### Final Stage
```
FROM base AS final
WORKDIR /app
COPY --from=build /app/publish .
ENTRYPOINT ["dotnet", "DockerizeWithRider.dll"]` 
```
-   **FROM base AS final**: Creates a final stage using the base image. If needed, we could add different runtimes to test our app with different .NET versions, but here we only use .NET 8.0.
-   **WORKDIR /app**: Sets the working directory to `/app`.
-   **COPY --from=build /app/publish .**: Copies the published output from the build stage to the final stage's `/app` directory.
-   **ENTRYPOINT ["dotnet", "DockerizeWithRider.dll"]**: Sets the entry point for the Docker container, executing `DockerizeWithRider.dll` to run our application.

## Conclusion

We have learned about container technologies, Docker, and how to dockerize a .NET project. While there are more complex configurations for creating an image for our application, this first attempt provided significant insights.

If you have any questions or notice any mistakes, please feel free to reach out or create an issue.
