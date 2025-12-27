# Stage 1: Build
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

# Copy solution and project files first for layer caching
COPY Surging.sln ./
COPY Surging.ApiGateway/Surging.ApiGateway.csproj ./Surging.ApiGateway/
COPY Surging.Core/Surging.Core.ApiGateWay/Surging.Core.ApiGateWay.csproj ./Surging.Core/Surging.Core.ApiGateWay/
COPY Surging.Core/Surging.Core.Caching/Surging.Core.Caching.csproj ./Surging.Core/Surging.Core.Caching/
COPY Surging.Core/Surging.Core.Codec.MessagePack/Surging.Core.Codec.MessagePack.csproj ./Surging.Core/Surging.Core.Codec.MessagePack/
COPY Surging.Core/Surging.Core.Codec.ProtoBuffer/Surging.Core.Codec.ProtoBuffer.csproj ./Surging.Core/Surging.Core.Codec.ProtoBuffer/
COPY Surging.Core/Surging.Core.Common/Surging.Core.Common.csproj ./Surging.Core/Surging.Core.Common/
COPY Surging.Core/Surging.Core.Consul/Surging.Core.Consul.csproj ./Surging.Core/Surging.Core.Consul/
COPY Surging.Core/Surging.Core.CPlatform/Surging.Core.CPlatform.csproj ./Surging.Core/Surging.Core.CPlatform/
COPY Surging.Core/Surging.Core.DotNetty/Surging.Core.DotNetty.csproj ./Surging.Core/Surging.Core.DotNetty/
COPY Surging.Core/Surging.Core.ProxyGenerator/Surging.Core.ProxyGenerator.csproj ./Surging.Core/Surging.Core.ProxyGenerator/
COPY Surging.Core/Surging.Core.Zookeeper/Surging.Core.Zookeeper.csproj ./Surging.Core/Surging.Core.Zookeeper/
COPY Surging.Apm/Surging.Apm.Skywalking/Surging.Apm.Skywalking.csproj ./Surging.Apm/Surging.Apm.Skywalking/
COPY DotNetty/ ./DotNetty/
COPY DotNetty.Codecs/ ./DotNetty.Codecs/

# Restore dependencies
RUN dotnet restore Surging.ApiGateway/Surging.ApiGateway.csproj

# Copy all source files
COPY . .

# Build and publish
WORKDIR /src/Surging.ApiGateway
RUN dotnet publish -c Release -o /app/publish --no-restore

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
