# Stage 1: Build
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /build

# Copy entire source
COPY . .

# Restore all dependencies via the solution file
RUN dotnet restore src/Surging.sln

# Build and publish
WORKDIR /build/src/Surging.ApiGateway
RUN dotnet publish -c Release -o /app/publish

# Stage 2: Runtime
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS runtime
WORKDIR /app

# Copy published app
COPY --from=build /app/publish .
COPY --from=build /build/src/Surging.ApiGateway/Configs ./Configs/

# Environment variables with defaults
ENV ASPNETCORE_URLS=http://+:729
ENV Register_Conn=consul:8500

# Expose port
EXPOSE 729

ENTRYPOINT ["dotnet", "Surging.ApiGateway.dll"]
