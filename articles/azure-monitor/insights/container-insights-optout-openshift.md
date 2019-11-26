---
title: Come arrestare il monitoraggio del cluster Azure Red Hat OpenShift | Microsoft Docs
description: Questo articolo descrive come arrestare il monitoraggio del cluster Azure Red Hat OpenShift con monitoraggio di Azure per i contenitori.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/21/2019
ms.openlocfilehash: f769749532a05260bf3c2c9f99483c5607d985a6
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384331"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-cluster-with-azure-monitor-for-containers"></a>Come arrestare il monitoraggio del cluster Azure Red Hat OpenShift con monitoraggio di Azure per i contenitori

Dopo aver abilitato il monitoraggio del cluster Azure Red Hat OpenShift, è possibile arrestare il monitoraggio del cluster se si decide che non si vuole più monitorarlo. Questo articolo illustra come eseguire questa operazione usando i modelli di Azure Resource Manager forniti.  

## <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager

Sono disponibili due modelli di Azure Resource Manager per poter rimuovere le risorse della soluzione in modo coerente e ripetuto nel gruppo di risorse. Uno è un modello JSON che specifica la configurazione per arrestare il monitoraggio e l'altro contiene i valori dei parametri configurati per specificare l'ID risorsa cluster OpenShift e l'area di Azure in cui è distribuito il cluster. 

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.65 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-template"></a>Creare il modello

1. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aroResourceId": {
           "type": "string",
           "metadata": {
             "description": "ARO Cluster Resource ID"
          }
        },
        "aroResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the aro cluster resource e.g. westcentralus"
          }
        }
      },
      "resources": [
        {
           "name": "[split(parameters('aroResourceId'),'/')[8]]",
           "type": "Microsoft.ContainerService/openShiftManagedClusters",
           "location": "[parameters('aroResourceLocation')]",
           "apiVersion": "2019-09-30-preview",
           "properties": {
             "mode": "Incremental",
             "id": "[parameters('aroResourceId')]",
             "monitorProfile": {
               "workspaceResourceID": null,
               "enabled": false
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
        "aroResourceId": {
          "value": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.ContainerService/openShiftManagedClusters/<clusterName>"
        },
        "aroResourceLocation": {
          "value": "<azure region of the cluster e.g. westcentralus>"
        }
      }
    }
    ```

4. Modificare i valori per **aroResourceId** e **aroResourceLocation** usando i valori del cluster OpenShift, che è possibile trovare nella pagina **proprietà** del cluster selezionato.

    ![Pagina delle proprietà del contenitore](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Salvare il file con il nome **OptOutParam.json** in una cartella locale.

6. A questo punto è possibile distribuire il modello. 

### <a name="remove-the-solution-using-azure-cli"></a>Rimuovere la soluzione tramite l'interfaccia della riga di comando di Azure

Eseguire il comando seguente con l'interfaccia della riga di comando di Azure in Linux per rimuovere la soluzione e pulire la configurazione nel cluster.

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

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Eseguire i comandi di PowerShell riportati di seguito nella cartella che contiene il modello per rimuovere la soluzione e pulire la configurazione dal cluster.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine, viene restituito un messaggio simile al seguente che include il risultato:

```powershell
ProvisioningState       : Succeeded
```

## <a name="next-steps"></a>Passaggi successivi

Se l'area di lavoro è stata creata solo per supportare il monitoraggio del cluster e non è più richiesta, è necessario eliminarla manualmente. Se non si ha familiarità con la modalità di eliminazione di un'area di lavoro, vedere [eliminare un'area di lavoro di Azure log Analytics](../../log-analytics/log-analytics-manage-del-workspace.md). 
