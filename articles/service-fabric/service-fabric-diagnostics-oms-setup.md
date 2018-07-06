---
title: Azure Service Fabric - Configurazione del monitoraggio con Log Analytics | Microsoft Docs
description: Informazioni su come configurare Log Analytics per visualizzare e analizzare gli eventi per il monitoraggio dei cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/03/2018
ms.author: srrengar
ms.openlocfilehash: 90a28162fb1f455c154ad4d2da7beac6bc785bc7
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301037"
---
# <a name="set-up-log-analytics-for-a-cluster"></a>Configurare Log Analytics per un cluster

Log Analytics è la soluzione consigliata per monitorare gli eventi a livello di cluster. È possibile configurare l'area di lavoro di Log Analytics tramite Azure Resource Manager, PowerShell o Azure Marketplace. Quando si gestisce un modello aggiornato di Resource Manager della distribuzione per un uso futuro, usare lo stesso modello per configurare l'ambiente di Log Analytics. La distribuzione tramite Marketplace è più semplice se è già disponibile un cluster distribuito, con il servizio di diagnostica abilitato. Se non si ha accesso a livello di sottoscrizione nell'account in cui si sta eseguendo la distribuzione, eseguire la distribuzione usando PowerShell o il modello di Resource Manager.

> [!NOTE]
> Per configurare Log Analytics per il monitoraggio del cluster è necessario che il servizio di diagnostica sia abilitato affinché sia possibile visualizzare gli eventi a livello di cluster o di piattaforma. Fare riferimento alle procedure per la [configurazione della diagnostica nei cluster Windows](service-fabric-diagnostics-event-aggregation-wad.md) e la [configurazione della diagnostica nei cluster Linux](service-fabric-diagnostics-event-aggregation-lad.md) per altre informazioni.

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Distribuire un'area di lavoro di Log Analytics tramite Azure Marketplace

Se si intende aggiungere un'area di lavoro di Log Analytics dopo aver distribuito un cluster, andare al portale di Azure Marketplace e cercare **Analisi Service Fabric**. Si tratta di una soluzione personalizzata per le distribuzioni di Service Fabric con dati specifici di Service Fabric. In questo processo verranno create la soluzione (dashboard per la visualizzazione di informazioni dettagliate) e l'area di lavoro (aggregazione dei dati del cluster sottostanti).

1. Selezionare **Nuovo** nel menu di spostamento a sinistra. 

2. Cercare **Analisi Service Fabric**. Selezionare la risorsa visualizzata.

3. Selezionare **Create**.

    ![Analisi Service Fabric in Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Nella finestra di creazione di Analisi Service Fabric scegliere **Selezionare un'area di lavoro** per il campo **Area di lavoro di OMS** e quindi su **Crea una nuova area di lavoro**. Compilare le voci necessarie. L'unico requisito è che la sottoscrizione per il cluster di Service Fabric e l'area di lavoro sia la stessa. Dopo aver convalidato le voci, verrà avviata la distribuzione dell'area di lavoro. La distribuzione richiede solo alcuni minuti.

5. Al termine, selezionare di nuovo **Crea** nella parte inferiore della finestra di creazione di Analisi Service Fabric. Assicurarsi che la nuova area di lavoro sia visualizzata in **Area di lavoro OMS**. Questa azione aggiunge la soluzione all'area di lavoro creata.

Se si usa Windows, continuare con i passaggi seguenti per connettere Log Analytics all'account di archiviazione in cui vengono archiviati gli eventi cluster. 

>[!NOTE]
>L'abilitazione di questa esperienza per i cluster Linux non è ancora disponibile. 

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Connettere l'area di lavoro di Log Analytics al cluster 

1. L'area di lavoro deve essere connessa ai dati di diagnostica provenienti dal cluster. Andare al gruppo di risorse in cui è stata creata la soluzione Analisi Service Fabric. Selezionare **ServiceFabric\<NomeAreaDiLavoro\>** e andare alla relativa pagina di panoramica. Da qui è possibile modificare le impostazioni della soluzione, le impostazioni dell'area di lavoro e accedere all'area di lavoro di Log Analytics.

2. Nel menu di spostamento a sinistra in **Origini dati dell'area di lavoro** selezionare **Log account di archiviazione**.

3. Nella pagina **Log account di archiviazione** selezionare **Aggiungi** nella parte superiore per aggiungere i log del cluster all'area di lavoro.

4. Selezionare **Account di archiviazione** per aggiungere l'account appropriato creato nel cluster. Se è stato usato il nome predefinito, l'account di archiviazione è **sfdg\<resourceGroupName\>**. È anche possibile verificare con il modello di Azure Resource Manager usato per distribuire il cluster, controllando il valore usato per **applicationDiagnosticsStorageAccountName**. Se il nome non viene visualizzato, scorrere verso il basso e selezionare **Carica altro**. Selezionare il nome dell'account di archiviazione.

5. Specificare il Tipo di dati. Impostarlo su **Eventi di Service Fabric**.

6. Assicurarsi che il valore di Origine sia impostato automaticamente su **WADServiceFabric\*EventTable**.

7. Selezionare **OK** per connettere l'area di lavoro ai log del cluster.

    ![Aggiungere i log dell'account di archiviazione a Log Analytics](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

L'account viene ora visualizzato come parte dei log degli account di archiviazione nelle origini dati dell'area di lavoro.

La soluzione Analisi Service Fabric è stata aggiunta in un'area di lavoro di Log Analytics, che è ora connessa correttamente alla piattaforma del cluster e alla tabella del registro applicazioni. È possibile aggiungere altre origini all'area di lavoro nello stesso modo.


## <a name="deploy-log-analytics-by-using-a-resource-manager-template"></a>Distribuire Log Analytics con un modello di Resource Manager

Quando si distribuisce un cluster con un modello di Resource Manager, il modello crea una nuova area di lavoro di Log Analytics, aggiunge la soluzione Service Fabric all'area di lavoro e la configura per leggere i dati dalle tabelle di archiviazione appropriate.

È possibile usare e modificare [questo modello di esempio](https://github.com/krnese/azure-quickstart-templates/tree/master/service-fabric-oms) per soddisfare i requisiti.

Apportare le modifiche seguenti:
1. Aggiungere `omsWorkspaceName` e `omsRegion` ai paramatri aggiungendo il frammento di codice seguente ai parametri definiti nel file *template.json*. È possibile modificare i valori predefiniti nel modo desiderato. Aggiungere anche due nuovi parametri al file *parameters.json* per definire i relativi valori per la distribuzione di risorse:
    
    ```json
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "sfomsworkspace",
        "metadata": {
            "description": "Name of your Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your Log Analytics workspace"
        }
    }
    ```

    I valori `omsRegion` devono essere conformi a un set specifico di valori. Scegliere il valore più vicino alla distribuzione del cluster.

2. Se i registri applicazioni vengono inviati a Log Analytics, assicurarsi prima che `applicationDiagnosticsStorageAccountType` e `applicationDiagnosticsStorageAccountName` siano inclusi come parametri nel modello. Se non sono inclusi, aggiungerli alla sezione delle variabili e modificare i relativi valori in base alle esigenze. È anche possibile includerli come parametri seguendo il formato precedente.

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]"
    ```

3. Aggiungere la soluzione Service Fabric alle variabili del modello:

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
    ```

4. Aggiungere le informazioni seguenti alla fine della sezione resources, dopo la posizione in cui viene dichiarata la risorsa del cluster Service Fabric:

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', parameters('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "id": "[resourceId('Microsoft.OperationsManagement/solutions/', variables('solution'))]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
    ```
    
    > [!NOTE]
    > Se si è aggiunto `applicationDiagnosticsStorageAccountName` come variabile, assicurarsi di sostituire ogni riferimento a esso con `variables('applicationDiagnosticsStorageAccountName')` invece che con `parameters('applicationDiagnosticsStorageAccountName')`.

5. Distribuire il modello come un aggiornamento di Gestione risorse al cluster tramite l'API `New-AzureRmResourceGroupDeployment` nel modulo AzureRM PowerShell. Un comando di esempio potrebbe essere:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Azure Resource Manager rileva che questo comando è un aggiornamento a una risorsa esistente. Elabora solo le modifiche tra il modello che definisce la distribuzione esistente e il nuovo modello fornito.

## <a name="deploy-log-analytics-by-using-azure-powershell"></a>Distribuire Log Analytics tramite Azure PowerShell

È anche possibile distribuire la risorsa di Log Analytics tramite PowerShell usando il comando `New-AzureRmOperationalInsightsWorkspace`. Per usare questo metodo, assicurarsi che sia installato [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1). Usare questo script per creare una nuova area di lavoro di Log Analytics e aggiungervi la soluzione Service Fabric: 

```PowerShell

$SubscriptionName = "<Name of your subscription>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

Al termine, seguire i passaggi nella sezione precedente per connettere Log Analytics all'account di archiviazione appropriato.

È anche possibile aggiungere altre soluzioni o apportare altre modifiche all'area di lavoro di Log Analytics usando PowerShell. Per altre informazioni, vedere [Gestire Log Analytics con PowerShell](../log-analytics/log-analytics-powershell-workspace-configuration.md).

## <a name="next-steps"></a>Passaggi successivi
* [Distribuire l'agente di Log Analytics](service-fabric-diagnostics-oms-agent.md) sui nodi per raccogliere i contatori delle prestazioni, le statistiche Docker e registri per i contenitori
* Acquisire familiarità con le funzionalità di [ricerca log e query](../log-analytics/log-analytics-log-searches.md) incluse in Log Analytics
* [Usare Progettazione viste per creare viste personalizzate in Log Analytics](../log-analytics/log-analytics-view-designer.md)
