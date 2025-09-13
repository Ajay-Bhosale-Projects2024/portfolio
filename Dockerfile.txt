# Use official .NET runtime for production
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 8080

# Use SDK image to build the app
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

# Copy csproj and restore
COPY ["portfolio.csproj", "./"]
RUN dotnet restore "./portfolio.csproj"

# Copy everything and build
COPY . .
RUN dotnet build "portfolio.csproj" -c Release -o /app/build

# Publish the app
FROM build AS publish
RUN dotnet publish "portfolio.csproj" -c Release -o /app/publish /p:UseAppHost=false

# Final stage
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "portfolio.dll"]
