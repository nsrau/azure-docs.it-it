## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

 Un [gruppo di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) è un contenitore logico in cui vengono gestite le risorse come le app Web e i database. È possibile distribuire, aggiornare ed eliminare le risorse in un gruppo di risorse.

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create).

```azurecli
az group create --name myResourceGroup --location westeurope
```

Per visualizzare le posizioni disponibili, usare il comando `az appservice list-locations`. In genere si creano risorse in un'area nelle vicinanze.

## <a name="create-an-azure-app-service-plan"></a>Creare un piano di servizio app di Azure

Creare un [piano di servizio app](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) "GRATUITO" con il comando [az appservice plan create](/cli/azure/appservice/plan#create).

[!INCLUDE [app-service-plan](app-service-plan.md)]

Il comando seguente consente di creare un piano di servizio app denominato `quickStartPlan` usando il piano tariffario **Gratuito**.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

Al termine della creazione del piano di servizio app, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "quickStartPlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "quickStartPlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
```

## <a name="create-a-web-app"></a>Creare un'app Web