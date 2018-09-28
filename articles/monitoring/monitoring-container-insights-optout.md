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
ms.date: 09/13/2018
ms.author: magoedte
ms.openlocfilehash: 2b989fbebe237e4e3746ef2f237193587173dfe4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963407"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-azure-monitor-for-containers"></a>Come arrestare il monitoraggio del servizio Kubernetes di Azure con Monitoraggio di Azure per contenitori

Se dopo aver abilitato il monitoraggio del cluster del servizio Kubernetes di Azure si decide che il monitoraggio non è più necessario, è possibile *disabilitarlo esplicitamente* usando i modelli di Azure Resource Manager con il cmdlet **New-AzureRmResourceGroupDeployment** di PowerShell oppure l'interfaccia della riga di comando di Azure. Un modello JSON specifica la configurazione per *disabilitare esplicitamente il monitoraggio*. L'altro contiene i valori dei parametri configurati per specificare l'ID risorsa del cluster AKS e il gruppo di risorse in cui è distribuito il cluster. 

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È richiesta la versione 2.0.27 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="create-template"></a>Creare il modello

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

    ![Pagina delle proprietà del contenitore](./media/monitoring-container-health/container-properties-page.png)

    Nella pagina **Proprietà** copiare anche l'**ID risorsa dell'area di lavoro**. Questo valore è necessario se si decide di eliminare l'area di lavoro di Log Analytics in un secondo momento. Questa operazione che non viene eseguita nell'ambito di questo processo. 

5. Salvare il file con il nome **OptOutParam.json** in una cartella locale.
6. A questo punto è possibile distribuire il modello. 

## <a name="remove-the-solution-using-azure-cli"></a>Rimuovere la soluzione tramite l'interfaccia della riga di comando di Azure
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

## <a name="remove-the-solution-using-powershell"></a>Rimuovere la soluzione tramite PowerShell

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

Se l'area di lavoro è stata creata solo per supportare il monitoraggio del cluster e non è più richiesta, è necessario eliminarla manualmente. Se non si ha familiarità con le procedure per l'eliminazione di un'area di lavoro, vedere [Eliminare un'area di lavoro di Azure Log Analytics con il portale di Azure](../log-analytics/log-analytics-manage-del-workspace.md). Non dimenticare il valore di **ID risorsa dell'area di lavoro** che è stato copiato in precedenza nel passaggio 4, perché sarà necessario in seguito. 

