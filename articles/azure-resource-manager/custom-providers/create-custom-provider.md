---
title: Creare il provider di risorse
description: Descrive come creare un provider di risorse personalizzato e distribuire i relativi tipi di risorse personalizzati.
author: MSEvanhi
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: evanhi
ms.openlocfilehash: 23ae69f49d37a210dd9fa1bfeedeca6c1b461e20
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888862"
---
# <a name="quickstart-create-a-custom-provider-and-deploy-custom-resources"></a>Avvio rapido: Creare un provider personalizzato e distribuire le risorse personalizzate

Questa guida di avvio rapido descrive come creare un provider di risorse e distribuire i tipi di risorse personalizzati per tale provider di risorse. Per altre informazioni sui provider personalizzati, vedere [Azure Custom Providers Preview overview (Panoramica dell'anteprima di provider personalizzati di Azure)](overview.md).

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Per completare la procedura descritta in questa guida di avvio rapido, è necessario chiamare operazioni `REST`. Esistono [modi diversi per inviare richieste REST](/rest/api/azure/).

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Preparare l'ambiente per l'interfaccia della riga di comando di Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Gli esempi dell'interfaccia della riga di comando di Azure usano `az rest` per richieste `REST`. Per altre informazioni, vedere [az rest](/cli/azure/reference-index#az-rest).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- I comandi di PowerShell vengono eseguiti in locale con PowerShell 7 o versione successiva e i moduli di Azure PowerShell. Per altre informazioni, vedere [Installare Azure PowerShell](/powershell/azure/install-az-ps).
- Se non è già disponibile uno strumento per le operazioni `REST`, installare [ARMClient](https://github.com/projectkudu/ARMClient). Si tratta di uno strumento da riga di comando open source che semplifica la chiamata dell'API di Azure Resource Manager.
- Dopo aver installato **ARMClient**, è possibile visualizzare le informazioni sull'utilizzo da un prompt dei comandi di PowerShell digitando: `armclient.exe`. In alternativa, passare al [wiki di ARMClient](https://github.com/projectkudu/ARMClient/wiki).

---

## <a name="deploy-custom-provider"></a>Distribuire un provider personalizzato

Per configurare il provider personalizzato, distribuire un [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) alla sottoscrizione di Azure.

Dopo aver distribuito il modello, la sottoscrizione include le risorse seguenti:

- App per le funzioni con le operazioni per risorse e azioni.
- Account di archiviazione per archiviare gli utenti creati tramite il provider personalizzato.
- Provider personalizzato che definisce i tipi di risorse personalizzati e le azioni. Usa l'endpoint dell'app per le funzioni per inviare richieste.
- Risorse personalizzate dal provider personalizzato.

Per distribuire il provider personalizzato, usare l'interfaccia della riga di comando di Azure, PowerShell o il portale di Azure:

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

In questo esempio viene richiesto di specificare un gruppo di risorse, la località e il nome dell'app per le funzioni del provider. I nomi sono archiviati in variabili che vengono usate in altri comandi. I comandi [az group create](/cli/azure/group#az-group-create) e [az deployment group create](/cli/azure/deployment/group#az-deployment-group-create) distribuiscono le risorse.

```azurecli-interactive
read -p "Enter a resource group name:" rgName &&
read -p "Enter the location (i.e. eastus):" location &&
read -p "Enter the provider's function app name:" funcName &&
templateUri="https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json" &&
az group create --name $rgName --location "$location" &&
az deployment group create --resource-group $rgName --template-uri $templateUri --parameters funcName=$funcName &&
echo "Press [ENTER] to continue ..." &&
read
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

In questo esempio viene richiesto di specificare un gruppo di risorse, la località e il nome dell'app per le funzioni del provider. I nomi sono archiviati in variabili che vengono usate in altri comandi. I comandi [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) e [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) distribuiscono le risorse.

```powershell
$rgName = Read-Host -Prompt "Enter a resource group name"
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$funcName = Read-Host -Prompt "Enter the provider's function app name"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json"
New-AzResourceGroup -Name $rgName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $rgName -TemplateUri $templateUri -funcName $funcName
Read-Host -Prompt "Press [ENTER] to continue ..."
```

---

È anche possibile distribuire la soluzione dal portale di Azure. Fare clic su ogni pulsante **Distribuisci in Azure** per aprire il modello nel portale di Azure.

[![Distribuzione in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json)

## <a name="view-custom-provider-and-resource"></a>Visualizzare il provider personalizzato e le risorse

Nel portale, il provider personalizzato è un tipo di risorsa nascosta. Per verificare che il provider di risorse sia stato distribuito, passare al gruppo di risorse. Selezionare l'opzione **Mostra tipi nascosti**.

![Mostra i tipi di risorse nascosti](./media/create-custom-provider/show-hidden.png)

Per visualizzare il tipo di risorsa personalizzata che è stata distribuita, usare l'operazione `GET` sul tipo di risorsa.

```http
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
subID=$(az account show --query id --output tsv)
requestURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"
az rest --method get --uri $requestURI
```

Si riceve la risposta:

```json
{
  "value": [
    {
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole",
        "provisioningState": "Succeeded"
      },
      "resourceGroup": "<rg-name>",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

Si riceve la risposta:

```json
{
  "value": [
    {
      "properties": {
        "provisioningState": "Succeeded",
        "FullName": "Santa Claus",
        "Location": "NorthPole"
      },
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

---

## <a name="call-action"></a>Chiamare un'azione

Il provider personalizzato dispone anche di un'azione denominata `ping`. Il codice che elabora la richiesta viene implementato nell'app per le funzioni. L'azione `ping` risponde con un messaggio di saluto.

Per inviare una richiesta di `ping`, usare l'operazione `POST` sul provider personalizzato.

```http
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
pingURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"
az rest --method post --uri $pingURI
```

Si riceve la risposta:

```json
{
  "message": "hello <function-name>.azurewebsites.net",
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  }
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

Si riceve la risposta:

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

---

## <a name="create-a-resource-type"></a>Creare un tipo di risorsa

Per creare un tipo di risorsa personalizzata, è possibile distribuire la risorsa in un modello. Questo approccio è illustrato nel modello distribuito in questa guida di avvio rapido. È anche possibile inviare una richiesta `PUT` per il tipo di risorsa.

```http
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
addURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
az rest --method put --uri $addURI --body "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"
```

Si riceve la risposta:

```json
{
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "properties": {
    "FullName": "Test User",
    "Location": "Earth",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "<rg-name>",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

Si riceve la risposta:

```json
{
  "properties": {
    "provisioningState": "Succeeded",
    "FullName": "Test User",
    "Location": "Earth"
  },
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

---

## <a name="custom-resource-provider-commands"></a>Comandi del provider di risorse personalizzato

Usare i comandi di [custom-providers](/cli/azure/ext/custom-providers/custom-providers/resource-provider) per gestire il provider di risorse personalizzato.

### <a name="list-custom-resource-providers"></a>Elencare i provider di risorse personalizzati

Il comando `list` elenca tutti i provider di risorse personalizzati presenti in una sottoscrizione. Con l'impostazione predefinita vengono elencati i provider di risorse personalizzati per la sottoscrizione corrente, ma è anche possibile specificare il parametro `--subscription`. Per visualizzare l'elenco per un gruppo di risorse, usare il parametro `--resource-group`.

```azurecli-interactive
az custom-providers resource-provider list --subscription $subID
```

```json
[
  {
    "actions": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "ping",
        "routingType": "Proxy"
      }
    ],
    "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceproviders/<provider-name>",
    "location": "eastus",
    "name": "<provider-name>",
    "provisioningState": "Succeeded",
    "resourceGroup": "<rg-name>",
    "resourceTypes": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "users",
        "routingType": "Proxy, Cache"
      }
    ],
    "tags": {},
    "type": "Microsoft.CustomProviders/resourceproviders",
    "validations": null
  }
]
```

### <a name="show-the-properties"></a>Visualizzare le proprietà

Usare il comando `show` per visualizzare tutte le proprietà del provider di risorse personalizzato. Il formato dell'output è simile a quello dell'output di `list`.

```azurecli-interactive
az custom-providers resource-provider show --resource-group $rgName --name $funcName
```

### <a name="create-a-new-resource"></a>Creare una nuova risorsa

Usare il comando `create` per creare o aggiornare un provider di risorse personalizzato. In questo esempio vengono aggiornati `actions` e `resourceTypes`.

```azurecli-interactive
az custom-providers resource-provider create --resource-group $rgName --name $funcName \
--action name=ping endpoint=https://myTestSite.azurewebsites.net/api/{requestPath} routing_type=Proxy \
--resource-type name=users endpoint=https://myTestSite.azurewebsites.net/api{requestPath} routing_type="Proxy, Cache"
```

```json
"actions": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api/{requestPath}",
    "name": "ping",
    "routingType": "Proxy"
  }
],

"resourceTypes": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api{requestPath}",
    "name": "users",
    "routingType": "Proxy, Cache"
  }
],
```

### <a name="update-the-providers-tags"></a>Aggiornare i tag del provider

Il comando `update` consente di aggiornare solo i tag per un provider di risorse personalizzato. Il tag viene visualizzato nel servizio app del provider di risorse personalizzato del portale di Azure.

```azurecli-interactive
az custom-providers resource-provider update --resource-group $rgName --name $funcName --tags new=tag
```

```json
"tags": {
  "new": "tag"
},
```

### <a name="delete-a-custom-resource-provider"></a>Eliminare un provider di risorse personalizzato

Il comando `delete` chiede conferma ed elimina solo il provider di risorse personalizzato. L'account di archiviazione, il servizio app e il piano di servizio app non vengono eliminati. Dopo l'eliminazione del provider, viene nuovamente visualizzato il prompt dei comandi.

```azurecli-interactive
az custom-providers resource-provider delete --resource-group $rgName --name $funcName
```

## <a name="next-steps"></a>Passaggi successivi

Per un'introduzione ai provider personalizzati, vedere l'articolo seguente:

> [!div class="nextstepaction"]
> [Panoramica dei provider personalizzati di Azure](overview.md)
