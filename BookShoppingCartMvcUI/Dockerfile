# 請參閱 https://aka.ms/customizecontainer 了解如何自訂您的偵錯容器，以及 Visual Studio 如何使用此 Dockerfile 來組建您的映像，以加快偵錯速度。

# 使用 ASP.NET Core 執行階段映像作為基礎映像
FROM mcr.microsoft.com/dotnet/aspnet:9.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

# 使用 ASP.NET Core SDK 映像來建構應用程式
FROM mcr.microsoft.com/dotnet/sdk:9.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["BookShoppingCartMvcUI/BookShoppingCartMvcUI.csproj", "./"]
RUN dotnet restore "./BookShoppingCartMvcUI/BookShoppingCartMvcUI.csproj"
COPY . .
WORKDIR "/src/BookShoppingCartMvcUI"
RUN dotnet build "./BookShoppingCartMvcUI.csproj" -c $BUILD_CONFIGURATION -o /app/build

# 從建構階段發布應用程式
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./BookShoppingCartMvcUI.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# 最終映像，基於 base 映像，並包含發布的應用程式
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "BookShoppingCartMvcUI.dll"]