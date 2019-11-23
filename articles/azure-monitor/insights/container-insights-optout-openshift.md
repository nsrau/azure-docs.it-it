---
title: How to stop monitoring your Azure Red Hat OpenShift cluster | Microsoft Docs
description: This article describes how you can stop monitoring of your Azure Red Hat OpenShift cluster with Azure Monitor for containers.
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
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-cluster-with-azure-monitor-for-containers"></a>How to stop monitoring your Azure Red Hat OpenShift cluster with Azure Monitor for containers

After you enable monitoring of your Azure Red Hat OpenShift cluster, you can stop monitoring the cluster if you decide you no longer want to monitor it. This article shows how to accomplish this using the provided Azure Resource Manager templates.  

## <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager

Sono disponibili due modelli di Azure Resource Manager per poter rimuovere le risorse della soluzione in modo coerente e ripetuto nel gruppo di risorse. One is a JSON template specifying the configuration to stop monitoring and the other contains parameter values that you configure to specify the OpenShift cluster resource ID and Azure region that the cluster is deployed in. 

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. You must be running the Azure CLI version 2.0.65 or later. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

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

4. Edit the values for **aroResourceId** and **aroResourceLocation** by using the values of the OpenShift cluster, which you can find on the **Properties** page for the selected cluster.

    ![Pagina delle proprietà del contenitore](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Salvare il file con il nome **OptOutParam.json** in una cartella locale.

6. A questo punto è possibile distribuire il modello. 

### <a name="remove-the-solution-using-azure-cli"></a>Rimuovere la soluzione tramite l'interfaccia della riga di comando di Azure

Execute the following command with Azure CLI on Linux to remove the solution and clean up the configuration on your cluster.

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

Execute the following PowerShell commands in the folder containing the template to remove the solution and clean up the configuration from your cluster.    

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

Se l'area di lavoro è stata creata solo per supportare il monitoraggio del cluster e non è più richiesta, è necessario eliminarla manualmente. If you are not familiar with how to delete a workspace, see [Delete an Azure Log Analytics workspace](../../log-analytics/log-analytics-manage-del-workspace.md). 
