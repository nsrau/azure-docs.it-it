---
title: Distribuire le risorse con i modelli e l'API REST
description: Usare Azure Resource Manager e l'API REST di Resource Manager per distribuire risorse in Azure. Le risorse sono definite in un modello di Resource Manager.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: d1c8a365153007d3337d922bc163ba3767eeddc9
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675409"
---
# <a name="deploy-resources-with-arm-templates-and-azure-resource-manager-rest-api"></a>Distribuire le risorse con i modelli ARM e Azure Resource Manager API REST

Questo articolo illustra come usare l'API REST di Azure Resource Manager con modelli di Azure Resource Manager (modelli ARM) per distribuire le risorse in Azure.

È possibile includere il modello nel corpo della richiesta o come collegamento a un file. Quando si usa un file, può trattarsi di un file locale oppure di un file esterno disponibile tramite un URI. Quando il modello si trova in un account di archiviazione, è possibile limitare l'accesso al modello e fornire un token di firma di accesso condiviso in fase di distribuzione.

## <a name="deployment-scope"></a>Ambito della distribuzione

La distribuzione può essere destinata a un gruppo di risorse, una sottoscrizione di Azure, un gruppo di gestione o un tenant. A seconda dell'ambito della distribuzione, vengono usati comandi diversi.

* Per distribuire un **gruppo di risorse** , usare [Distribuzioni - Creare](/rest/api/resources/deployments/createorupdate). La richiesta viene inviata a:

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-06-01
  ```

* Per distribuire una **sottoscrizione** , usare [Distribuzioni - Creare nell'ambito della sottoscrizione](/rest/api/resources/deployments/createorupdateatsubscriptionscope). La richiesta viene inviata a:

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-06-01
  ```

  Per altre informazioni sulle distribuzioni a livello di sottoscrizione, vedere [Creare gruppi di risorse e risorse a livello di sottoscrizione](deploy-to-subscription.md).

* Per eseguire la distribuzione in un **gruppo di gestione** , usare [Distribuzioni - Creare nell'ambito del gruppo di gestione](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). La richiesta viene inviata a:

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-06-01
  ```

  Per altre informazioni sulle distribuzioni a livello di gruppo di gestione, vedere [Creare risorse a livello di gruppo di gestione](deploy-to-management-group.md).

* Per eseguire la distribuzione in un **tenant** , usare [Distribuzioni - Creare o aggiornare nell'ambito del tenant](/rest/api/resources/deployments/createorupdateattenantscope). La richiesta viene inviata a:

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-06-01
  ```

  Per altre informazioni sulle distribuzioni a livello di tenant, vedere [Creare risorse a livello di tenant](deploy-to-tenant.md).

Gli esempi contenuti in questo articolo usano distribuzioni di gruppi di risorse.

## <a name="deploy-with-the-rest-api"></a>Distribuire con l'API REST

1. Impostare [parametri e intestazioni comuni](/rest/api/azure/), tra cui i token di autenticazione.

1. Se si esegue la distribuzione in un gruppo di risorse che non esiste, creare il gruppo di risorse. Specificare l'ID sottoscrizione, il nome del nuovo gruppo di risorse e il percorso per la soluzione. Per ulteriori informazioni, vedere [Create a resource group](/rest/api/resources/resourcegroups/createorupdate) (Creare un gruppo di risorse).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2020-06-01
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

1. Prima di distribuire il modello, è possibile visualizzare in anteprima le modifiche che il modello apporterà all'ambiente. Usare l' [operazione](template-deploy-what-if.md) di simulazione per verificare che il modello apporti le modifiche previste. Cosa-se anche il modello viene convalidato per gli errori.

1. Per distribuire un modello, specificare l'ID della sottoscrizione, il nome del gruppo di risorse e il nome della distribuzione nell'URI della richiesta.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-10-01
   ```

   Nel corpo della richiesta specificare un collegamento al modello e al file dei parametri. Per altre informazioni sul file dei parametri, vedere [Creare il file di parametri di Resource Manager](parameter-files.md).

   Si noti che la **modalità** è impostata su **Incrementale** . Per eseguire una distribuzione completa, impostare **mode** su **Complete** (Completo). Quando si utilizza la modalità di completamento, fare attenzione a non eliminare inavvertitamente le risorse non presenti nel modello.

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

    Se si vuole registrare il contenuto della risposta, richiedere il contenuto, o entrambi, includere `debugSetting` nella richiesta.

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

    È possibile impostare l'account di archiviazione per l'utilizzzo di un token di firma di accesso condiviso (SAS). Per altre informazioni, vedere [Delegating Access with a Shared Access Signature](/rest/api/storageservices/delegating-access-with-a-shared-access-signature) (Delega dell'accesso con una firma di accesso condiviso).

    Se è necessario fornire un valore sensibile per un parametro (ad esempio una password), aggiungere tale valore a un insieme di credenziali delle chiavi. Recuperare l'insieme di credenziali delle chiavi durante la distribuzione, come illustrato nell'esempio precedente. Per ulteriori informazioni, vedere [Passare valori protetti durante la distribuzione](key-vault-parameter.md).

1. Anziché eseguire il collegamento ai file per il modello e i parametri, è possibile includerli nel corpo della richiesta. L'esempio seguente illustra il corpo della richiesta con il modello e i parametri inline:

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
            "apiVersion": "2018-02-01",
            "name": "[variables('storageAccountName')]",
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

1. Per ottenere lo stato della distribuzione del modello, usare [Distribuzione - Ottenere](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
   ```

## <a name="deployment-name"></a>Nome distribuzione

È possibile assegnare un nome alla distribuzione, ad esempio `ExampleDeployment` .

Ogni volta che si esegue una distribuzione, viene aggiunta una voce alla cronologia di distribuzione del gruppo di risorse con il nome della distribuzione. Se si esegue un'altra distribuzione e si assegna lo stesso nome, la voce precedente viene sostituita con la distribuzione corrente. Se si desidera mantenere voci univoche nella cronologia di distribuzione, assegnare a ogni distribuzione un nome univoco.

Per creare un nome univoco, è possibile assegnare un numero casuale. In alternativa, aggiungere un valore di data.

Se si eseguono distribuzioni simultanee nello stesso gruppo di risorse con lo stesso nome di distribuzione, solo l'ultima distribuzione viene completata. Tutte le distribuzioni con lo stesso nome che non sono state completate vengono sostituite dall'ultima distribuzione. Ad esempio, se si esegue una distribuzione denominata `newStorage` che distribuisce un account di archiviazione denominato `storage1` e allo stesso tempo esegue un'altra distribuzione denominata `newStorage` che distribuisce un account di archiviazione denominato `storage2` , si distribuisce un solo account di archiviazione. L'account di archiviazione risultante è denominato `storage2` .

Tuttavia, se si esegue una distribuzione denominata `newStorage` che distribuisce un account di archiviazione denominato `storage1` e immediatamente dopo aver completato l'esecuzione di un'altra distribuzione denominata `newStorage` che distribuisce un account di archiviazione denominato `storage2` , si avranno due account di archiviazione. Uno è denominato `storage1` e l'altro è denominato `storage2` . Tuttavia, è presente una sola voce nella cronologia di distribuzione.

Quando si specifica un nome univoco per ogni distribuzione, è possibile eseguirli simultaneamente senza conflitti. Se si esegue una distribuzione denominata `newStorage1` che distribuisce un account di archiviazione denominato `storage1` e allo stesso tempo esegue un'altra distribuzione denominata `newStorage2` che distribuisce un account di archiviazione denominato `storage2` , si avranno due account di archiviazione e due voci nella cronologia di distribuzione.

Per evitare conflitti con le distribuzioni simultanee e per garantire voci univoche nella cronologia di distribuzione, assegnare a ogni distribuzione un nome univoco.

## <a name="next-steps"></a>Passaggi successivi

- Per eseguire il rollback a una distribuzione corretta quando viene restituito un errore, vedere [Rollback alla distribuzione corretta in caso di errore](rollback-on-error.md).
- Per specificare la modalità di gestione delle risorse che sono presenti nel gruppo, ma non sono definite nel modello, vedere [Modalità di distribuzione di Azure Resource Manager](deployment-modes.md).
- Per altre informazioni sulla gestione delle operazioni REST asincrone, vedere [Track asynchronous Azure operations](../management/async-operations.md) (Tenere traccia delle operazioni asincrone di Azure).
- Per altre informazioni sui modelli, vedere [Comprendere la struttura e la sintassi dei modelli ARM](template-syntax.md).

