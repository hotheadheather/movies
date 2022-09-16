#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
RUN dotnet tool install -g dotnet-ef
ENV PATH $PATH:/root/.dotnet/tools
WORKDIR /src
COPY ["MvcMovie/MvcMovie.csproj", "MvcMovie/"]
RUN dotnet restore "MvcMovie/MvcMovie.csproj"

COPY . .
WORKDIR "/src/MvcMovie"
RUN dotnet build "MvcMovie.csproj" -c Release -o /app/build

#reverts migrations
RUN dotnet ef database update 0


RUN dotnet ef migrations remove

RUN dotnet ef migrations add InitialCreate
RUN dotnet ef database update

FROM build AS publish
RUN dotnet publish "MvcMovie.csproj" -c Release -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "MvcMovie.dll"]
