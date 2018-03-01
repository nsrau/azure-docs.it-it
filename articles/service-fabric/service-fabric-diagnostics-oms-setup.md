---
title: Azure Service Fabric - Configurazione del monitoraggio con Log Analytics di OMS | Microsoft Docs
description: Informazioni su come configurare Operations Management Suite (OMS) per visualizzare e analizzare gli eventi per il monitoraggio dei cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/17/2017
ms.author: dekapur
ms.openlocfilehash: 288c7482058cd9f824b6001bb9ad36d1a5e0f8bf
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="set-up-operations-management-suite-log-analytics-for-a-cluster"></a>Configurare Log Analytics di Operations Management Suite per un cluster

È possibile configurare un'area di lavoro di Operations Management Suite (OMS) tramite Azure Resource Manager, PowerShell o Azure Marketplace. Quando si gestisce un modello di Resource Manager aggiornato della distribuzione, per un utilizzo futuro, usare lo stesso modello per configurare l'ambiente di OMS. La distribuzione tramite Marketplace è più semplice se è già disponibile un cluster distribuito, con il servizio di diagnostica abilitato. Se non si ha accesso a livello di sottoscrizione nell'account in cui si sta distribuendo OMS, eseguire la distribuzione usando PowerShell o il modello di Resource Manager.

> [!NOTE]
> Per configurare OMS per il monitoraggio del cluster, è necessario che il servizio di diagnostica sia abilitato affinché sia possibile visualizzare gli eventi a livello di cluster o di piattaforma.

## <a name="deploy-oms-by-using-azure-marketplace"></a>Distribuire OMS tramite Azure Marketplace

Se si intende aggiungere un'area di lavoro di OMS dopo aver distribuito un cluster, andare al portale di Azure Marketplace e cercare **Analisi Service Fabric**:

1. Selezionare **Nuovo** nel menu di spostamento a sinistra. 

2. Cercare **Analisi Service Fabric**. Selezionare la risorsa visualizzata.

3. Selezionare **Create**.

    ![Analisi SF OMS in Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Nella finestra di creazione di Analisi Service Fabric scegliere **Selezionare un'area di lavoro** per il campo **Area di lavoro di OMS** e quindi su **Crea una nuova area di lavoro**. Compilare le voci necessarie. L'unico requisito è che la sottoscrizione per il cluster di Service Fabric e l'area di lavoro di OMS sia la stessa. Dopo aver convalidato le voci, verrà avviata la distribuzione dell'area di lavoro di OMS. La distribuzione richiede solo alcuni minuti.

5. Al termine, selezionare di nuovo **Crea** nella parte inferiore della finestra di creazione di Analisi Service Fabric. Assicurarsi che la nuova area di lavoro sia visualizzata in **Area di lavoro OMS**. Questa azione aggiunge la soluzione all'area di lavoro creata.

Se si usa Windows, continuare con i passaggi seguenti per connettere OMS all'account di archiviazione in cui vengono archiviati gli eventi cluster. 

>[!NOTE]
>L'abilitazione di questa esperienza per i cluster Linux non è ancora disponibile. 

### <a name="add-the-oms-agent-to-your-cluster"></a>Aggiungere l'agente di OMS al cluster 

1. L'area di lavoro deve essere connessa ai dati di diagnostica provenienti dal cluster. Andare al gruppo di risorse in cui è stata creata la soluzione Analisi Service Fabric. Selezionare **ServiceFabric\<nameOfOMSWorkspace\>** e andare alla relativa pagina di panoramica. Da qui, è possibile modificare le impostazioni della soluzione, le impostazioni dell'area di lavoro e accedere al portale OMS.

2. Nel menu di spostamento a sinistra in **Origini dati dell'area di lavoro** selezionare **Log account di archiviazione**.

3. Nella pagina **Log account di archiviazione** selezionare **Aggiungi** nella parte superiore per aggiungere i log del cluster all'area di lavoro.

4. Selezionare **Account di archiviazione** per aggiungere l'account appropriato creato nel cluster. Se è stato usato il nome predefinito, l'account di archiviazione è **sfdg\<resourceGroupName\>**. È anche possibile verificare con il modello di Azure Resource Manager usato per distribuire il cluster, controllando il valore usato per **applicationDiagnosticsStorageAccountName**. Se il nome non viene visualizzato, scorrere verso il basso e selezionare **Carica altro**. Selezionare il nome dell'account di archiviazione.

5. Specificare il Tipo di dati. Impostarlo su **Eventi di Service Fabric**.

6. Assicurarsi che il valore di Origine sia impostato automaticamente su **WADServiceFabric\*EventTable**.

7. Selezionare **OK** per connettere l'area di lavoro ai log del cluster.

    ![Aggiungere i log dell'account di archiviazione a OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

L'account viene ora visualizzato come parte dei log degli account di archiviazione nelle origini dati dell'area di lavoro.

La soluzione Analisi Service Fabric è stata aggiunta in un'area di lavoro di OMS Log Analytics, che è ora connessa correttamente alla piattaforma del cluster e alla tabella del registro applicazioni. È possibile aggiungere altre origini all'area di lavoro nello stesso modo.


## <a name="deploy-oms-by-using-a-resource-manager-template"></a>Distribuire OMS con un modello di Resource Manager

Quando si distribuisce un cluster con un modello di Resource Manager, il modello crea una nuova area di lavoro OMS, aggiunge la soluzione Service Fabric all'area di lavoro e la configura per leggere i dati dalle tabelle di archiviazione appropriate.

È possibile usare e modificare [questo modello di esempio](https://azure.microsoft.com/resources/templates/service-fabric-oms/) per soddisfare i requisiti. I modelli che offrono varie opzioni per configurare un'area di lavoro di OMS sono disponibili nella pagina [Modelli di Service Fabric e OMS](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS).

Apportare le modifiche seguenti:
1. Aggiungere `omsWorkspaceName` e `omsRegion` ai paramatri aggiungendo il frammento di codice seguente ai parametri definiti nel file *template.json*. È possibile modificare i valori predefiniti nel modo desiderato. Aggiungere anche due nuovi parametri al file *parameters.json* per definire i relativi valori per la distribuzione di risorse:
    
    ```json
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "sfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
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
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
    ```

    I valori `omsRegion` devono essere conformi a un set specifico di valori. Scegliere il valore più vicino alla distribuzione del cluster.

2. Se i registri applicazioni vengono inviati a OMS, assicurarsi prima che `applicationDiagnosticsStorageAccountType` e `applicationDiagnosticsStorageAccountName` siano inclusi come parametri nel modello. Se non sono inclusi, aggiungerli alla sezione delle variabili e modificare i relativi valori in base alle esigenze. È anche possibile includerli come parametri seguendo il formato precedente.

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]"
    ```

3. Aggiunta della soluzione OMS Service Fabric alle variabili del modello:

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

## <a name="deploy-oms-by-using-azure-powershell"></a>Distribuire OMS usando Azure PowerShell

È anche possibile distribuire la risorsa Log Analytics di OMS tramite PowerShell usando il comando `New-AzureRmOperationalInsightsWorkspace`. Per usare questo metodo, assicurarsi che sia installato [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1). Usare questo script per creare una nuova area di lavoro di Log Analytics di OMS e aggiungervi la soluzione Service Fabric: 

```PowerShell

$SubscriptionName = "<Name of your subscription>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<OMS Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Login-AzureRmAccount
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

Al termine, seguire i passaggi nella sezione precedente per connettere OMS Log Analytics all'account di archiviazione appropriato.

È anche possibile aggiungere altre soluzioni o apportare altre modifiche all'area di lavoro di OMS usando PowerShell. Per altre informazioni, vedere [Gestire Log Analytics con PowerShell](../log-analytics/log-analytics-powershell-workspace-configuration.md).

## <a name="next-steps"></a>Passaggi successivi
* [Distribuire l'agente di OMS](service-fabric-diagnostics-oms-agent.md) sui nodi per raccogliere i contatori delle prestazioni, le statistiche Docker e registri per i contenitori
* Acquisire familiarità con le funzionalità di [ricerca log e query](../log-analytics/log-analytics-log-searches.md) incluse in Log Analytics
* [Usare Progettazione viste per creare viste personalizzate in Log Analytics](../log-analytics/log-analytics-view-designer.md)
