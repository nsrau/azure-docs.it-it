---
title: Distribuire le risorse con i modelli e l'API REST | Microsoft Docs
description: Utilizzare Azure Resource Manager e l'API REST di Resource Manager per distribuire una risorsa in Azure. Le risorse sono definite in un modello di Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: tomfitz
ms.openlocfilehash: 6915395abfedb75ec3ec0a5bd8f569ef2490e5be
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Distribuire le risorse con i modelli e l'API REST di Resource Manager

In questo articolo viene illustrato come utilizzare l'API REST di Resource Manager con i modelli di Resource Manager per distribuire le risorse in Azure.  

> [!TIP]
> Per informazioni su come eseguire il debug di un errore durante la distribuzione, vedere:
> 
> * [View deployment operations](resource-manager-deployment-operations.md) (Visualizzare le operazioni di distribuzione) per ottenere informazioni per la risoluzione degli errori
> * [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md) per informazioni sulla risoluzione degli errori di distribuzione più comuni
> 
> 

Il modello può essere un file locale oppure un file esterno disponibile tramite un URI. Quando il modello si trova in un account di archiviazione, è possibile limitare l'accesso al modello e fornire un token di firma di accesso condiviso in fase di distribuzione.

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy-with-the-rest-api"></a>Distribuire con l'API REST
1. Impostare [parametri e intestazioni comuni](/rest/api/azure/), tra cui i token di autenticazione.

2. Se non è già disponibile un gruppo di risorse, crearne uno. Specificare l'ID sottoscrizione, il nome del nuovo gruppo di risorse e il percorso per la soluzione. Per ulteriori informazioni, vedere [Create a resource group](/rest/api/resources/resourcegroups/createorupdate) (Creare un gruppo di risorse).

  ```HTTP
  PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
  {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
  }
  ```

3. Convalidare la distribuzione prima dell'esecuzione eseguendo l'operazione di [convalida della distribuzione di un modello](/rest/api/resources/deployments/validate) . Durante il test della distribuzione, specificare i parametri esattamente come quando si esegue la distribuzione (illustrata nel passaggio successivo).

4. Creare una distribuzione. Fornire l'ID sottoscrizione, il nome del gruppo di risorse, il nome della distribuzione e un collegamento al modello. Per informazioni sul file di modello, vedere [File di parametri](#parameter-file). Per altre informazioni sull'API REST per creare un gruppo di risorse, vedere [Create a template deployment](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_CreateOrUpdate) (Creare la distribuzione di un modello). Si noti che la **modalità** è impostata su **Incrementale**. Per eseguire una distribuzione completa, impostare **mode** su **Complete** (Completo). Quando si utilizza la modalità di completamento, fare attenzione a non eliminare inavvertitamente le risorse non presenti nel modello.

  ```HTTP
  PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
  {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      }
    }
  }
  ```

    Per registrare il contenuto della risposta, il contenuto della richiesta o entrambi, includere **debugSetting** nella richiesta.

  ```HTTP
  "debugSetting": {
    "detailLevel": "requestContent, responseContent"
  }
  ```

    È possibile impostare l'account di archiviazione per l'utilizzzo di un token di firma di accesso condiviso (SAS). Per altre informazioni, vedere [Delegating Access with a Shared Access Signature](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature) (Delega dell'accesso con una firma di accesso condiviso).

5. Ottenere lo stato della distribuzione del modello. Per altre informazioni, vedere [Get information about a template deployment](/rest/api/resources/deployments/get) (Ottenere informazioni sulla distribuzione di un modello).

  ```HTTP
  GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
  ```

## <a name="redeploy-when-deployment-fails"></a>Eseguire nuovamente la distribuzione se non è riuscita

Per le distribuzioni che non vengono eseguite in modo corretto, è possibile specificare che una distribuzione precedente nella cronologia venga eseguita nuovamente in modo automatico. Per usare questa opzione, le distribuzioni devono avere nomi univoci in modo che sia possibile identificarle nella cronologia. Se non si dispone di nomi univoci, la distribuzione non riuscita corrente potrebbe sovrascrivere quella eseguita in modo corretto nella cronologia. È possibile usare questa opzione solo con le distribuzioni a livello di radice. Le distribuzioni di un modello annidato non sono disponibili per la ridistribuzione.

Per eseguire nuovamente l'ultima distribuzione con esito positivo se la distribuzione corrente non riesce, usare:

```HTTP
"onErrorDeployment": {
  "type": "LastSuccessful",
},
```

Per eseguire nuovamente una distribuzione specifica se la distribuzione corrente non riesce, usare:

```HTTP
"onErrorDeployment": {
  "type": "SpecificDeployment",
  "deploymentName": "<deploymentname>"
}
```

La distribuzione specificata deve aver avuto esito positivo.

## <a name="parameter-file"></a>File di parametri

Se si usa un file di parametri per passare i valori dei parametri durante la distribuzione, è necessario creare un file JSON con un formato simile all'esempio seguente:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               }, 
               "secretName": "sqlAdminPassword" 
            }   
        }
   }
}
```

Le dimensioni del file di parametro non possono essere superiori a 64 KB.

Se è necessario fornire un valore sensibile per un parametro (ad esempio una password), aggiungere tale valore a un insieme di credenziali delle chiavi. Recuperare l'insieme di credenziali delle chiavi durante la distribuzione, come illustrato nell'esempio precedente. Per ulteriori informazioni, vedere [Passare valori protetti durante la distribuzione](resource-manager-keyvault-parameter.md). 

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla gestione delle operazioni REST asincrone, vedere [Track asynchronous Azure operations](resource-manager-async-operations.md) (Tenere traccia delle operazioni asincrone di Azure).
* Per un esempio di distribuzione delle risorse con la libreria client .NET, vedere [Distribuire le risorse usando le librerie .NET e un modello](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Per definire i parametri nel modello, vedere [Creazione di modelli](resource-group-authoring-templates.md#parameters).
* Per indicazioni sulla distribuzione della soluzione in ambienti diversi, vedere [Ambienti di sviluppo e test in Microsoft Azure](solution-dev-test-environments.md).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).

