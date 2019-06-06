---
title: Distribuire le risorse con i modelli e l'API REST | Microsoft Docs
description: Usare il REST API di Resource Manager e Azure Resource Manager per distribuire risorse in Azure. Le risorse sono definite in un modello di Resource Manager.
author: tfitzmac
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: tomfitz
ms.openlocfilehash: 0490cf6837cb413bc2e869424cd430fd4a824dc9
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688878"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Distribuire le risorse con i modelli e l'API REST di Resource Manager

In questo articolo viene illustrato come utilizzare l'API REST di Resource Manager con i modelli di Resource Manager per distribuire le risorse in Azure.  

È possibile includere il modello nel corpo della richiesta o come collegamento a un file. Quando si usa un file, può trattarsi di un file locale oppure di un file esterno disponibile tramite un URI. Quando il modello si trova in un account di archiviazione, è possibile limitare l'accesso al modello e fornire un token di firma di accesso condiviso in fase di distribuzione.

## <a name="deployment-scope"></a>Ambito di distribuzione

È possibile destinare la distribuzione a un gruppo di gestione, una sottoscrizione di Azure o un gruppo di risorse. Nella maggior parte dei casi, si interagirà distribuzioni a un gruppo di risorse. Usare le distribuzioni di sottoscrizione o gruppo di gestione per applicare i criteri e le assegnazioni di ruolo all'interno dell'ambito specificato. È anche possibile usare distribuzioni della sottoscrizione per creare un gruppo di risorse e distribuire le risorse ad esso. A seconda dell'ambito della distribuzione, si usano comandi diversi.

Per distribuire in un **gruppo di risorse**, usare [distribuzioni - creare](/rest/api/resources/deployments/createorupdate). La richiesta viene inviata a:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Per distribuire in un **abbonamento**, usare [distribuzioni - creazione all'ambito della sottoscrizione](/rest/api/resources/deployments/createorupdateatsubscriptionscope). La richiesta viene inviata a:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Per distribuire in un **gruppo di gestione**, usare [distribuzioni - creare in ambito di gruppo di gestione](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). La richiesta viene inviata a:

```HTTP
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Gli esempi in questo articolo usano distribuzioni di gruppi di risorse. Per altre informazioni sulle distribuzioni di sottoscrizione, vedere [creare i gruppi di risorse e le risorse a livello di sottoscrizione](deploy-to-subscription.md).

## <a name="deploy-with-the-rest-api"></a>Distribuire con l'API REST

1. Impostare [parametri e intestazioni comuni](/rest/api/azure/), tra cui i token di autenticazione.

1. Se non è già disponibile un gruppo di risorse, crearne uno. Specificare l'ID sottoscrizione, il nome del nuovo gruppo di risorse e il percorso per la soluzione. Per ulteriori informazioni, vedere [Create a resource group](/rest/api/resources/resourcegroups/createorupdate) (Creare un gruppo di risorse).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-05-01
   ```

   Con un corpo della richiesta del tipo:

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. Convalidare la distribuzione prima dell'esecuzione eseguendo l'operazione di [convalida della distribuzione di un modello](/rest/api/resources/deployments/validate) . Durante il test della distribuzione, specificare i parametri esattamente come quando si esegue la distribuzione (illustrata nel passaggio successivo).

1. Per distribuire un modello, fornire l'ID sottoscrizione, il nome del gruppo di risorse, il nome della distribuzione nell'URI della richiesta. 

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-05-01
   ```

   Nel corpo della richiesta, fornire un collegamento al file di modello e dei parametri. Si noti che la **modalità** è impostata su **Incrementale**. Per eseguire una distribuzione completa, impostare **mode** su **Complete** (Completo). Quando si utilizza la modalità di completamento, fare attenzione a non eliminare inavvertitamente le risorse non presenti nel modello.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
    }
   }
   ```

    Per registrare il contenuto della risposta, il contenuto della richiesta o entrambi, includere **debugSetting** nella richiesta.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    È possibile impostare l'account di archiviazione per l'utilizzzo di un token di firma di accesso condiviso (SAS). Per altre informazioni, vedere [Delegating Access with a Shared Access Signature](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature) (Delega dell'accesso con una firma di accesso condiviso).

1. Anziché eseguire il collegamento ai file per il modello e i parametri, è possibile includerli nel corpo della richiesta. Nell'esempio seguente illustra il corpo della richiesta con il modello e parametri inline:

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
              "Standard_LRS",
              "Standard_GRS",
              "Standard_ZRS",
              "Premium_LRS"
            ],
            "metadata": {
              "description": "Storage Account type"
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2018-02-01",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

1. Per ottenere lo stato della distribuzione del modello, usare [ottenere distribuzioni -](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="redeploy-when-deployment-fails"></a>Eseguire nuovamente la distribuzione se non è riuscita

Questa funzionalità è detta anche *Rollback in caso di errore*. Quando una distribuzione non riesce, è possibile ridistribuire automaticamente una distribuzione precedente con esito positivo dalla cronologia della distribuzione. Per specificare la ridistribuzione, usare la proprietà `onErrorDeployment` nel corpo della richiesta. Questa funzionalità è utile se si ha uno stato noto soddisfacente per la distribuzione dell'infrastruttura e vuole ripristinare questo stato. Esistono una serie di avvertenze e limitazioni:

- La ridistribuzione viene eseguita esattamente come è stato eseguito in precedenza con gli stessi parametri. È possibile modificare i parametri.
- Viene eseguita la distribuzione precedente usando il [modalità completa](./deployment-modes.md#complete-mode). Vengono eliminate tutte le risorse non incluse nella distribuzione precedente e qualsiasi risorsa di configurazioni è impostata allo stato precedente. Assicurarsi di aver compreso le [modalità di distribuzione](./deployment-modes.md).
- La ridistribuzione interessa solo le risorse, le modifiche ai dati non sono interessate.
- Questa funzionalità è supportata solo in distribuzioni con configurazione gruppo di risorse, non distribuzioni a livello di sottoscrizione. Per altre informazioni sulla distribuzione a livello di sottoscrizione, vedere [creare i gruppi di risorse e le risorse a livello di sottoscrizione](./deploy-to-subscription.md).

Per usare questa opzione, le distribuzioni devono avere nomi univoci in modo che sia possibile identificarle nella cronologia. Se non si dispone di nomi univoci, la distribuzione non riuscita corrente potrebbe sovrascrivere quella eseguita in modo corretto nella cronologia. È possibile usare questa opzione solo con le distribuzioni a livello di radice. Le distribuzioni di un modello annidato non sono disponibili per la ridistribuzione.

Per eseguire nuovamente l'ultima distribuzione con esito positivo se la distribuzione corrente non riesce, usare:

```json
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
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Per eseguire nuovamente una distribuzione specifica se la distribuzione corrente non riesce, usare:

```json
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
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
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

- Per specificare la modalità di gestione delle risorse che sono presenti nel gruppo, ma non sono definite nel modello, vedere [Modalità di distribuzione di Azure Resource Manager](deployment-modes.md).
- Per altre informazioni sulla gestione delle operazioni REST asincrone, vedere [Track asynchronous Azure operations](resource-manager-async-operations.md) (Tenere traccia delle operazioni asincrone di Azure).
- Per altre informazioni sui modelli, vedere [comprendere la struttura e sintassi di modelli Azure Resource Manager](resource-group-authoring-templates.md).

