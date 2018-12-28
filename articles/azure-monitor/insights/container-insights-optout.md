---
title: Come arrestare il monitoraggio del cluster del servizio Kubernetes di Azure | Microsoft Docs
description: Questo articolo descrive come interrompere il monitoraggio del cluster del servizio Kubernetes di Azure con Monitoraggio di Azure per contenitori.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2018
ms.author: magoedte
ms.openlocfilehash: f9c2324eb429c82f7e937b4f18311bf204eeb193
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408754"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Come arrestare il monitoraggio del servizio Kubernetes di Azure con Monitoraggio di Azure per contenitori

Dopo aver abilitato il monitoraggio di un cluster AKS, è possibile interrompere il monitoraggio del cluster se non lo si ritiene più necessario. Questo articolo illustra come eseguire questa operazione usando l'interfaccia della riga di comando di Azure o i modelli di Azure Resource Manager disponibili.  


## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Usare il comando [az aks disable-addons](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) per disabilitare Monitoraggio di Azure per contenitori. Il comando rimuove l'agente dai nodi del cluster, non rimuove la soluzione o i dati già raccolti e archiviati nella risorsa Log Analytics.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Per riabilitare il monitoraggio per il cluster, vedere [Abilitare il monitoraggio usando l'interfaccia della riga di comando di Azure](container-insights-onboard.md#enable-monitoring-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager
Sono disponibili due modelli di Azure Resource Manager per poter rimuovere le risorse della soluzione in modo coerente e ripetuto nel gruppo di risorse. Uno è un modello JSON che specifica la configurazione per interrompere il monitoraggio, mentre l'altro contiene i valori dei parametri da configurare per specificare l'ID risorsa del cluster AKS e il gruppo di risorse in cui è distribuito il cluster. 

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

>[!NOTE]
>Il modello deve essere distribuito nello stesso gruppo di risorse del cluster. Se si omettono altre proprietà o altri componenti aggiuntivi quando si usa questo modello, è possibile che vengano rimossi dal cluster. Ad esempio, *enableRBAC*.  
>

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È richiesta la versione 2.0.27 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-template"></a>Creare il modello

1. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Salvare il file con il nome **OptOutTemplate.json** in una cartella locale.
3. Incollare nel file la sintassi JSON seguente:

    ```json
    {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "aksResourceId": {
         "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
      },
      "aksResourceLocation": {
        "value": "<aksClusterRegion>"
        }
      }
    }
    ```

4. Sostituire i valori di **aksResourceId** e **aksResourceLocation** con i valori del cluster AKS, indicati nella pagina **Proprietà** per il cluster selezionato.

    ![Pagina delle proprietà del contenitore](media/container-insights-optout/container-properties-page.png)

    Nella pagina **Proprietà** copiare anche l'**ID risorsa dell'area di lavoro**. Questo valore è necessario se si decide di eliminare l'area di lavoro di Log Analytics in un secondo momento. Questa operazione che non viene eseguita nell'ambito di questo processo. 

5. Salvare il file con il nome **OptOutParam.json** in una cartella locale.
6. A questo punto è possibile distribuire il modello. 

### <a name="remove-the-solution-using-azure-cli"></a>Rimuovere la soluzione tramite l'interfaccia della riga di comando di Azure
Eseguire il comando seguente con l'interfaccia della riga di comando di Azure in Linux per rimuovere la soluzione ed eseguire la pulizia della configurazione nel cluster del servizio Kubernetes di Azure.

```azurecli
az login   
az account set --subscription "Subscription Name" 
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine, viene restituito un messaggio simile al seguente che include il risultato:

```azurecli
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Rimuovere la soluzione tramite PowerShell

Eseguire i comandi di PowerShell seguenti nella cartella che contiene il modello per rimuovere la soluzione ed eseguire la pulizia della configurazione nel cluster del servizio Kubernetes di Azure.    

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine, viene restituito un messaggio simile al seguente che include il risultato:

```powershell
ProvisioningState       : Succeeded
```

Se l'area di lavoro è stata creata solo per supportare il monitoraggio del cluster e non è più richiesta, è necessario eliminarla manualmente. Se non si ha familiarità con le procedure per l'eliminazione di un'area di lavoro, vedere [Eliminare un'area di lavoro di Azure Log Analytics con il portale di Azure](../../log-analytics/log-analytics-manage-del-workspace.md). Non dimenticare il valore di **ID risorsa dell'area di lavoro** che è stato copiato in precedenza nel passaggio 4, perché sarà necessario in seguito. 

