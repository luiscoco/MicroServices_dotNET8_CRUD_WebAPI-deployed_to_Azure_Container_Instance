# How to deploy Azure Container Instance (ACI) a .NET8 CRUD WebAPI Azure CosmosDB Microservice

## 1. Create an Azure Container Registry

We create Azure Container Registry service for uploading the .NET CRUD WebAPI docker image

![image](https://github.com/luiscoco/MicroServices_dotNET8_CRUD_WebAPI-CosmosDB-deployed_to_Azure_Container_Instance/assets/32194879/38684cb4-6405-4516-a2d5-8c2b5fd9dddb)

![image](https://github.com/luiscoco/MicroServices_dotNET8_CRUD_WebAPI-CosmosDB-deployed_to_Azure_Container_Instance/assets/32194879/a34a5018-1a6f-4dec-8182-5a763be38da0)

![image](https://github.com/luiscoco/MicroServices_dotNET8_CRUD_WebAPI-CosmosDB-deployed_to_Azure_Container_Instance/assets/32194879/e1604cbc-3bb6-43ea-b210-d6c6034c6f31)

![image](https://github.com/luiscoco/MicroServices_dotNET8_CRUD_WebAPI-CosmosDB-deployed_to_Azure_Container_Instance/assets/32194879/e0d0ab7e-ba80-4fd3-ba16-58913bc4b76e)

![image](https://github.com/luiscoco/MicroServices_dotNET8_CRUD_WebAPI-CosmosDB-deployed_to_Azure_Container_Instance/assets/32194879/bdccf3dc-114e-4758-bd1b-0c3996a91d9a)

## 2. Set the Admin User

We can enable the Admin User in the Azure Portal 

![image](https://github.com/luiscoco/MicroServices_dotNET8_CRUD_WebAPI-CosmosDB-deployed_to_Azure_Container_Instance/assets/32194879/3690cdf3-d6b3-4003-9716-6cbf0eee4a87)

Or we can enable the Admin User programmatically with Azure CLI

```
az acr update --name mymicroservicecontainer --resource-group myRG --admin-enabled true
```

Log in to Azure ACR

```
az acr login --name mymicroservicecontainer
```

## 3. Create a Dockerfile

With Visual Studio 2022 Community Edition we can automatically create the Dockerfile. 

After creating automatically the Dockerfile we **expose the port 80**

This is the Dockerfile source code

```
#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
USER app
WORKDIR /app
EXPOSE 80

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["AzureCosmosCRUDWebAPI.csproj", "."]
RUN dotnet restore "./././AzureCosmosCRUDWebAPI.csproj"
COPY . .
WORKDIR "/src/."
RUN dotnet build "./AzureCosmosCRUDWebAPI.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./AzureCosmosCRUDWebAPI.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "AzureCosmosCRUDWebAPI.dll"]
```

## 4. Create the Docker image

```
docker build -t mymicroservicecontainer.azurecr.io/mymicroservicecontainer:v1 .
```

## 5. Push the Docker image

```
docker push mymicroservicecontainer.azurecr.io/mymicroservicecontainer:v1
```

## 6. Run the container Docker image

```
docker run -p 80:8080 mymicroservicecontainer.azurecr.io/mymicroservicecontainer:v1
```

## 7. Create the Azure Container Instance (ACI) 

We copy the ACR username and password:

![image](https://github.com/luiscoco/MicroServices_dotNET8_CRUD_WebAPI-CosmosDB-deployed_to_Azure_Container_Instance/assets/32194879/2099d8be-1613-44fb-90d1-08ed846fe0b6)

**ACR username**: mymicroservicecontainer
 
**ACR password**: RtxdWayhsBSsrvs9ZEpG/cV+M9A1F7Xu5td9+S2lWp+ACRDRt6Dk

```
az container create --resource-group myRG --name mycontainerinstance --image mymicroservicecontainer.azurecr.io/mymicroservicecontainer:v1 --cpu 1 --memory 1.5 --registry-login-server mymicroservicecontainer.azurecr.io --registry-username mymicroservicecontainer --registry-password RtxdWayhsBSsrvs9ZEpG/cV+M9A1F7Xu5td9+S2lWp+ACRDRt6Dk --dns-name-label mymicroservicedns007 --ports 8080 --location westeurope
```

Also we can input the command in multiline

```
az container create --resource-group myRG ^
--name mycontainerinstance ^
--image mymicroservicecontainer.azurecr.io/mymicroservicecontainer:v1 ^
--cpu 1 ^
--memory 1.5 ^
--registry-login-server mymicroservicecontainer.azurecr.io ^
--registry-username mymicroservicecontainer ^
--registry-password RtxdWayhsBSsrvs9ZEpG/cV+M9A1F7Xu5td9+S2lWp+ACRDRt6Dk ^
--dns-name-label mymicroservicedns007 ^
--ports 8080 ^
--location westeurope
```

## 8. 



## 9. 






