---
title: Creare un provider di risorse con anteprima dei provider personalizzati di Azure
description: Descrive come creare un provider di risorse personalizzato e distribuire i relativi tipi di risorse personalizzati.
services: managed-applications
author: MSEvanhi
ms.service: managed-applications
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: 34dd5efda2c9f6cc9a7b5ddcde06e8f7d27de901
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65157339"
---
# <a name="tutorial-create-custom-provider-and-deploy-custom-resources"></a>Esercitazione: Creare un provider personalizzato e distribuire le risorse personalizzate

Questa esercitazione illustra come creare un provider di risorse e distribuire i tipi di risorse personalizzati per tale provider di risorse. Per altre informazioni sui provider personalizzati, vedere [Azure Custom Providers Preview overview (Panoramica dell'anteprima di provider personalizzati di Azure)](custom-providers-overview.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura di questa esercitazione, è necessario chiamare le operazioni REST. Esistono [modi diversi per inviare richieste REST](/rest/api/azure/). Se non è già disponibile uno strumento per le operazioni REST, installare [ARMClient](https://github.com/projectkudu/ARMClient). Si tratta di uno strumento da riga di comando open source che semplifica la chiamata dell'API di Azure Resource Manager.

## <a name="deploy-custom-provider"></a>Distribuire un provider personalizzato

Per configurare il provider personalizzato, distribuire un [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) alla sottoscrizione di Azure.

Dopo aver distribuito il modello, la sottoscrizione include le risorse seguenti:

* App per le funzioni con le operazioni per risorse e azioni.
* Account di archiviazione per archiviare gli utenti creati tramite il provider personalizzato.
* Provider personalizzato che definisce i tipi di risorse personalizzati e le azioni. Usa l'endpoint dell'app per le funzioni per inviare richieste.
* Risorse personalizzate dal provider personalizzato.

Per distribuire il provider personalizzato con PowerShell, usare:

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

In alternativa, è possibile distribuire la soluzione con il pulsante seguente:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>Visualizzare il provider personalizzato e le risorse

Nel portale, il provider personalizzato è un tipo di risorsa nascosta. Per verificare che il provider di risorse sia stato distribuito, passare al gruppo di risorse. Selezionare l'opzione **Mostra tipi nascosti**.

![Mostra i tipi di risorse nascosti](./media/create-custom-providers/show-hidden.png)

Per visualizzare il tipo di risorsa personalizzata che è stata distribuita, usare l'operazione GET sul tipo di risorsa.

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

Con ARMClient usare:

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

## <a name="call-action"></a>Chiamare un'azione

Il provider personalizzato dispone anche di un'azione denominata **ping**. Il codice che elabora la richiesta viene implementato nell'app per le funzioni. L'azione ping risponde con un messaggio di saluto.

Per inviare una richiesta di ping, usare l'operazione POST sul provider personalizzato.

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

Con ARMClient usare:

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

## <a name="create-resource-type"></a>Creare un tipo di risorsa

Per creare un tipo di risorsa personalizzata, è possibile distribuire la risorsa in un modello. Questo approccio è illustrato nel modello distribuito in questa esercitazione. È anche possibile inviare una richiesta PUT per il tipo di risorsa.

```
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

Con ARMClient usare:

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

## <a name="next-steps"></a>Passaggi successivi

Per un'introduzione ai provider personalizzati, vedere [Azure Custom Providers Preview overview (Panoramica dell'anteprima di provider personalizzati di Azure)](custom-providers-overview.md).
