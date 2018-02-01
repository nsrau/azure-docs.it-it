---
title: Azure Service Fabric - Configurazione del monitoraggio con Log Analytics di OMS | Microsoft Docs
description: Informazioni su come configurare OMS per visualizzare e analizzare gli eventi per il monitoraggio dei cluster di Azure Service Fabric.
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
ms.openlocfilehash: 53b06c5a1395f34c96d4011366835a920d5c670b
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2018
---
# <a name="set-up-oms-log-analytics-for-a-cluster"></a>Configurare Log Analytics di OMS per un cluster

È possibile configurare un'area di lavoro di OMS tramite Azure Resource Manager, PowerShell o Azure Marketplace. Quando si gestisce un modello di Resource Manager aggiornato della distribuzione, per un utilizzo futuro, usare lo stesso modello per configurare l'ambiente di OMS. La distribuzione tramite Marketplace è più semplice se è già disponibile un cluster distribuito, con il servizio di diagnostica abilitato. Se non si ha accesso a livello di sottoscrizione nell'account in cui si sta distribuendo OMS, usare PowerShell o eseguire la distribuzione tramite il modello di Resource Manager.

> [!NOTE]
> È necessario che il servizio di diagnostica sia abilitato per il cluster affinché sia possibile visualizzare i cluster o affinché gli eventi a livello di piattaforma siano in grado di configurare OMS per il monitoraggio del cluster.

## <a name="deploying-oms-using-azure-marketplace"></a>Distribuzione di OMS tramite Azure Marketplace

Se si preferisce aggiungere un'area di lavoro di OMS dopo aver distribuito un cluster, passare in Azure Marketplace (nel portale) e cercare *"Analisi Service Fabric"*.

1. Fare clic su **Nuovo** nel riquadro di spostamento sinistro. 

2. Cercare *Analisi Service Fabric*. Fare clic sulla risorsa visualizzata.

3. Fare clic su **Crea**

    ![Analisi SF OMS in Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Nella finestra di creazione di Analisi Service Fabric fare clic su **Selezionare un'area di lavoro** per il campo *Area di lavoro OMS* e quindi su **Crea una nuova area di lavoro**. Compilare le voci necessarie: l'unico requisito è che la sottoscrizione per il cluster di Service Fabric e l'area di lavoro OMS devono essere gli stessi. Dopo aver convalidato le voci, verrà avviata la distribuzione dell'area di lavoro di OMS. Dovrebbero essere necessari solo alcuni minuti.

5. Al termine, fare di nuovo clic su **Crea** nella parte inferiore della finestra di creazione di Analisi Service Fabric. Assicurarsi che la nuova area di lavoro sia visualizzata in *Area di lavoro OMS*. La soluzione viene aggiunta all'area di lavoro appena creata.

Se si usa Windows, continuare con i passaggi seguenti per associare OMS all'account di archiviazione in cui vengono archiviati gli eventi cluster. Non è ancora possibile eseguire correttamente questa operazione per i cluster Linux. Nel frattempo, procedere con l'aggiunta dell'agente OMS al cluster.  

1. L'area di lavoro deve ancora essere connessa ai dati di diagnostica provenienti dal cluster. Passare al gruppo di risorse in cui è stata creata la soluzione Analisi Service Fabric. Verrà visualizzata l'indicazione *ServiceFabric (\<nomeAreaDiLavoroOMS\>)*. Fare clic sulla soluzione per passare alla relativa pagina di panoramica, da dove è possibile modificare le impostazioni della soluzione e le impostazioni dell'area di lavoro e passare al portale di OMS.

2. Nel menu di spostamento a sinistra fare clic su **Log account di archiviazione** in *Origini dati dell'area di lavoro*.

3. Nella pagina *Log account di archiviazione* fare clic su **Aggiungi** nella parte superiore per aggiungere i log del cluster all'area di lavoro.

4. Fare clic su **Account di archiviazione** per aggiungere l'account appropriato creato nel cluster. Se è stato usato il nome predefinito, il nome dell'account di archiviazione è *sfdg\<NomeGruppoDiRisorse\>*. È anche possibile verificare controllando il modello di Azure Resource Manager usato per distribuire il cluster, esaminando il valore usato per `applicationDiagnosticsStorageAccountName`. Potrebbe anche essere necessario scorrere verso il basso e fare clic su **Carica altro** se il nome non è visualizzato. Fare clic sul nome dell'account di archiviazione corretto per selezionarlo.

5. Sarà quindi necessario specificare il valore di *Tipo di dati*, che deve essere impostato su **Eventi di Service Fabric**.

6. Il valore di *Origine* dovrebbe essere impostato automaticamente su *WADServiceFabric\*EventTable*.

7. Fare clic su **OK** per connettere l'area di lavoro ai log del cluster.

    ![Aggiungere i log dell'account di archiviazione a OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

L'account dovrebbe ora essere visualizzato in *Log account di archiviazione* nelle origini dati dell'area di lavoro.

In questo modo, la soluzione Analisi Service Fabric è stata aggiunta in un'area di lavoro di OMS Log Analytics, che è ora connessa correttamente alla piattaforma del cluster e alla tabella del log dell'applicazione. È possibile aggiungere altre origini all'area di lavoro nello stesso modo.


## <a name="deploying-oms-using-a-resource-manager-template"></a>Distribuzione di OMS con il modello di Resource Manager

Quando si distribuisce un cluster con un modello di Resource Manager, il modello deve creare una nuova area di lavoro OMS, aggiungervi la soluzione Service Fabric e configurarla per leggere i dati dalle tabelle di archiviazione appropriate.

[Qui](https://azure.microsoft.com/resources/templates/service-fabric-oms/) viene illustrato un modello di esempio che è possibile usare e modificare in base alle necessità. Altri modelli che offrono varie opzioni per configurare un'area di lavoro di OMS sono disponibili nella pagina [Modelli di Service Fabric e OMS](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS).

Di seguito sono descritte le principali modifiche apportate:

1. Aggiunta di `omsWorkspaceName` e `omsRegion` ai parametri. Questo significa aggiungere il frammento di codice seguente ai parametri definiti nel file *template.json*. È possibile modificare i valori predefiniti nel modo desiderato. È anche necessario aggiungere due nuovi parametri al file *parameters.json* per definire i relativi valori per la distribuzione di risorse:
    
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

    I valori `omsRegion` devono essere conformi a un set specifico di valori. È consigliabile scegliere il valore più vicino alla distribuzione del cluster.

2. Se i registri applicazioni vengono inviati a OMS, assicurarsi che `applicationDiagnosticsStorageAccountType` e `applicationDiagnosticsStorageAccountName` siano inclusi come parametri nel modello. In caso contrario, aggiungerli alla sezione delle variabili come indicato di seguito e modificare i relativi valori in base alle esigenze. Se lo si desidera, è anche possibile includerli come parametri seguendo il formato usato in precedenza.

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]"
    ```

3. Aggiunta della soluzione OMS Service Fabric alle variabili del modello:

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
    ```

4. Aggiunta delle informazioni seguenti alla fine della sezione resources, dopo la posizione in cui viene dichiarata la risorsa del cluster Service Fabric.

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

5. Distribuzione del modello come aggiornamento di Resource Manager nel cluster. Questa operazione viene eseguita usando l'API `New-AzureRmResourceGroupDeployment` nel modulo AzureRM PowerShell. Un comando di esempio potrebbe essere:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Azure Resource Manager sarà in grado di rilevare che si tratta di un aggiornamento a una risorsa esistente. Elaborerà solo le modifiche tra il modello di distribuzione che definisce la distribuzione esistente e il nuovo modello fornito.

## <a name="deploying-oms-using-azure-powershell"></a>Distribuzione di OMS tramite Azure PowerShell

È anche possibile distribuire la risorsa Log Analytics di OMS tramite PowerShell. Questo risultato viene ottenuto usando il comando `New-AzureRmOperationalInsightsWorkspace`. A questo scopo, assicurarsi che sia installato [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1). Usare questo script per creare una nuova area di lavoro di Log Analytics di OMS e aggiungervi la soluzione Service Fabric: 

```ps

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

Al termine, se il cluster è un cluster Windows, seguire i passaggi della sezione precedente per associare Log Analytics di OMS all'account di archiviazione appropriato.

È anche possibile aggiungere altre soluzioni o apportare altre modifiche all'area di lavoro di OMS usando PowerShell. Per altre informazioni, vedere [Gestire Log Analytics con PowerShell](../log-analytics/log-analytics-powershell-workspace-configuration.md)

## <a name="next-steps"></a>Passaggi successivi
* [Distribuire l'agente di OMS](service-fabric-diagnostics-oms-agent.md) sui nodi per raccogliere i contatori delle prestazioni, le statistiche Docker e registri per i contenitori
* Acquisire familiarità con le funzionalità di [ricerca log e query](../log-analytics/log-analytics-log-searches.md) incluse in Log Analytics
* [Usare Progettazione viste per creare viste personalizzate in Log Analytics](../log-analytics/log-analytics-view-designer.md)
