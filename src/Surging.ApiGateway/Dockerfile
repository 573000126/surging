# Stage 1: Build
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

# Copy entire source (simpler approach for complex dependencies)
COPY . .

# Restore all dependencies
RUN dotnet restore Surging.ApiGateway/Surging.ApiGateway.csproj

# Build and publish
WORKDIR /src/Surging.ApiGateway
RUN dotnet publish -c Release -o /app/publish

# Stage 2: Runtime
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS runtime
WORKDIR /app

# Copy published app
COPY --from=build /app/publish .
COPY --from=build /src/Surging.ApiGateway/Configs ./Configs/

# Environment variables with defaults
ENV ASPNETCORE_URLS=http://+:729
ENV Register_Conn=consul:8500

# Expose port
EXPOSE 729

ENTRYPOINT ["dotnet", "Surging.ApiGateway.dll"]
