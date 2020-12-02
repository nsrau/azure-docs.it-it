---
title: Abilitare monitoraggio di Azure sul dispositivo GPU Pro Azure Stack Edge
description: Viene descritto come abilitare monitoraggio di Azure in un dispositivo GPU Pro Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/05/2020
ms.author: alkohli
ms.openlocfilehash: 3485c6ca5c2672fa48b6118a78600b9745994ce1
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466444"
---
# <a name="enable-azure-monitor-on-your-azure-stack-edge-pro-gpu-device"></a>Abilitare monitoraggio di Azure sul dispositivo GPU Pro Azure Stack Edge

Il monitoraggio dei contenitori sul dispositivo GPU Azure Stack Edge Pro è fondamentale, specialmente quando si eseguono più applicazioni di calcolo. Monitoraggio di Azure consente di raccogliere i log dei contenitori e le metriche di memoria e processore dal cluster Kubernetes in esecuzione nel dispositivo.

Questo articolo descrive i passaggi necessari per abilitare monitoraggio di Azure nel dispositivo e raccogliere i log del contenitore nell'area di lavoro Log Analytics. L'archivio delle metriche di monitoraggio di Azure non è attualmente supportato con il dispositivo GPU Pro Azure Stack Edge. 


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario:

- Un dispositivo Azure Stack Edge Pro. Verificare che il dispositivo sia attivato in base ai passaggi descritti in [esercitazione: attivare il dispositivo](azure-stack-edge-gpu-deploy-activate.md).
- Il passaggio di **calcolo** è stato completato in base all' [esercitazione: configurare il calcolo nel dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-deploy-configure-compute.md) sul dispositivo. Il dispositivo deve disporre di una risorsa Hub di Internet delle cose associata, un dispositivo Internet delle cose e un dispositivo IoT Edge.


## <a name="create-log-analytics-workspace"></a>Creare Log Analytics area di lavoro.

Per creare un'area di lavoro di log Analytics, seguire questa procedura. Un'area di lavoro di log Analytics è un'unità di archiviazione logica in cui i dati di log vengono raccolti e archiviati.

1. Nella portale di Azure selezionare **+ Crea una risorsa** e cercare **log Analytics area di lavoro** , quindi selezionare **Crea**. 
1. Nell' **area di lavoro crea log Analytics** configurare le impostazioni seguenti. Accettare il resto come predefinito.

    1. Nella scheda **nozioni di base** specificare la sottoscrizione, il gruppo di risorse, il nome e l'area per l'area di lavoro. 

        ![Scheda nozioni di base per area di lavoro Log Analytics](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-basics-1.png)  

    1. Nella scheda piano **tariffario** accettare il piano predefinito **con pagamento in base** al consumo.

        ![Scheda prezzi per l'area di lavoro Log Analytics](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-pricing-1.png) 

    1. Nella scheda **Verifica e crea** esaminare le informazioni per l'area di lavoro e selezionare **Crea**.

        ![Esaminare e creare per Log Analytics area di lavoro](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-review-create-1.png)

Per ulteriori informazioni, vedere la procedura dettagliata in [creare un'area di lavoro log Analytics tramite portale di Azure](../azure-monitor/learn/quick-create-workspace.md).



## <a name="enable-container-insights"></a>Abilita informazioni dettagliate sul contenitore

Per abilitare il contenitore Insights nell'area di lavoro, seguire questa procedura. 

1. Seguire i passaggi dettagliati della [soluzione come aggiungere i contenitori di monitoraggio di Azure](../azure-monitor/insights/container-insights-hybrid-setup.md#how-to-add-the-azure-monitor-containers-solution). Usare il file di modello seguente `containerSolution.json` :

    ```yml
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
            }
        ]
    }
    ```

1. Ottenere l'ID e la posizione della risorsa. Passare a `Your Log Analytics workspace > General > Properties`. Copiare le informazioni seguenti:

    - **ID risorsa** che è l'ID di risorsa di Azure completo dell'area di lavoro di Azure log Analytics. 
    - **località** che rappresenta l'area di Azure.

    ![Proprietà dell'area di lavoro Log Analytics](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-properties-1.png) 

1. Utilizzare il seguente file di parametri `containerSolutionParams.json` . Sostituire `workspaceResourceId` con l'ID risorsa e `workspaceRegion` con il percorso copiato nel passaggio precedente.

    ```yaml
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "workspaceResourceId": {
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
        },
        "workspaceRegion": {
        "value": "westus"
        }
        }
    }
    ```
    
    Di seguito è riportato un esempio di output di un'area di lavoro Log Analytics con informazioni dettagliate sul contenitore abilitate:
    
    ```powershell
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...
    MOTD: Switch to Bash from PowerShell: bash
    VERBOSE: Authenticating to Azure ...
    VERBOSE: Building your Azure drive ...
    
    PS /home/alpa> az account set -s fa68082f-8ff7-4a25-95c7-ce9da541242f
    PS /home/alpa> ls
    clouddrive  containerSolution.json
    PS /home/alpa> ls
    clouddrive  containerSolution.json  containerSolutionParams.json
    PS /home/alpa> az deployment group create --resource-group myaserg --name Testdeployment1 --template-file containerSolution.json --parameters containerSolutionParams.json
    {- Finished ..
        "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.Resources/deployments/Testdeployment1",
        "location": null,
        "name": "Testdeployment1",
        "properties": {
        "correlationId": "3a9045fe-2de0-428c-b17b-057508a8c575",
        "debugSetting": null,
        "dependencies": [],
        "duration": "PT11.1588316S",
        "error": null,
        "mode": "Incremental",
        "onErrorDeployment": null,
        "outputResources": [
            {
            "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.OperationsManagement/solutions/ContainerInsights(myaseloganalyticsws)",
            "resourceGroup": "myaserg"
            }
        ],
        "outputs": null,
        "parameters": {
            "workspaceRegion": {
            "type": "String",
            "value": "westus"
            },
            "workspaceResourceId": {
            "type": "String",
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
            }
        },
        "parametersLink": null,
        "providers": [
            {
            "id": null,
            "namespace": "Microsoft.Resources",
            "registrationPolicy": null,
            "registrationState": null,
            "resourceTypes": [
                {
                "aliases": null,
                "apiProfiles": null,
                "apiVersions": null,
                "capabilities": null,
                "defaultApiVersion": null,
                "locations": [
                    null
                ],
                "properties": null,
                "resourceType": "deployments"
                }
            ]
            }
        ],
        "provisioningState": "Succeeded",
        "templateHash": "10500027184662969395",
        "templateLink": null,
        "timestamp": "2020-11-06T22:09:56.908983+00:00",
        "validatedResources": null
        },
        "resourceGroup": "myaserg",
        "tags": null,
        "type": "Microsoft.Resources/deployments"
    }
    PS /home/alpa>
    ```

## <a name="configure-azure-monitor-on-your-device"></a>Configurare monitoraggio di Azure nel dispositivo

1. Passare alla risorsa Log Analytics appena creata e copiare l' **ID dell'area di lavoro** e la **chiave primaria** (chiave dell'area di lavoro).

    ![Gestione degli agenti nell'area di lavoro Log Analytics](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-agents-management-1.png)

    Salvare queste informazioni perché verrà usato in un passaggio successivo.

1. [Connettersi all'interfaccia di PowerShell del dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 
    
1. Usare l'ID dell'area di lavoro di log Analytics e la chiave dell'area di lavoro con il cmdlet seguente:

    `Set-HcsKubernetesAzureMonitorConfiguration -WorkspaceId <> -WorkspaceKey <>`

1. Dopo l'abilitazione di monitoraggio di Azure, verranno visualizzati i log nell'area di lavoro Log Analytics. Per visualizzare lo stato del cluster Kubernetes distribuito nel dispositivo, passare a monitoraggio di **Azure > insights > contenitori**. Per l'opzione Environment selezionare **All**. 

    ![Metriche nell'area di lavoro Log Analytics](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-metrics-1.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [monitorare i carichi di lavoro Kubernetes tramite il dashboard di Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).
