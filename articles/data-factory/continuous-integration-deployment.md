---
title: Integrazione e recapito continui in Azure Data Factory
description: Informazioni su come usare l'integrazione e il recapito continui per spostare le pipeline di Data Factory da un ambiente (sviluppo, test, produzione) a un altro.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: 7c9f22d27351b0f57c5a0158821f347073ae60b4
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187818"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Integrazione e recapito continui in Azure Data Factory

## <a name="overview"></a>Panoramica

L'integrazione continua è la pratica di testare automaticamente le modifiche apportate alla codebase e il prima possibile. Il recapito continuo segue i test che si verificano durante l'integrazione continua e inserisce le modifiche in un sistema di gestione temporanea o di produzione.

In Azure Data Factory, l'integrazione e il recapito continuo (CI/CD) implica lo sviluppo di pipeline Data Factory da un ambiente (sviluppo, test, produzione) a un altro. È possibile usare l'integrazione di Data Factory UX con i modelli Azure Resource Manager per eseguire CI/CD.

Nel Data Factory UX è possibile generare un modello di Gestione risorse dal menu a discesa **modello ARM** . Quando si seleziona **Esporta modello ARM**, il portale genera il modello di gestione risorse per il data factory e un file di configurazione che include tutte le stringhe di connessione e altri parametri. Si crea quindi un file di configurazione per ogni ambiente (sviluppo, test, produzione). Il file di modello di Resource Manager principale rimane lo stesso per tutti gli ambienti.

Per un'introduzione di nove minuti a questa funzionalità e a una dimostrazione, Guarda questo video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>Ciclo di vita CI/CD

Di seguito è riportata una panoramica di esempio del ciclo di vita CI/CD in un data factory di Azure configurato con Azure Repos git. Per altre informazioni su come configurare un repository git, vedere [controllo del codice sorgente in Azure Data Factory](source-control.md).

1.  Un data factory di sviluppo viene creato e configurato con Azure Repos git. Tutti gli sviluppatori devono disporre dell'autorizzazione per creare risorse Data Factory come pipeline e set di impostazioni.

1.  Quando gli sviluppatori apportano modifiche ai Branch delle funzionalità, eseguono il debug delle esecuzioni di pipeline con le modifiche più recenti. Per ulteriori informazioni su come eseguire il debug di un'esecuzione di pipeline, vedere [sviluppo iterativo e debug con Azure Data Factory](iterative-development-debugging.md).

1.  Una volta soddisfatte le modifiche, gli sviluppatori creano una richiesta pull dal ramo della funzionalità al ramo master o di collaborazione per approvare le modifiche apportate dai peer.

1.  Dopo che una richiesta pull è stata approvata e le modifiche sono state unite nel ramo master, è possibile pubblicare le modifiche nella Factory di sviluppo.

1.  Quando il team è pronto per distribuire le modifiche alla Factory di test e quindi alla Factory di produzione, il team esporta il modello di Gestione risorse dal ramo master.

1.  Il modello di Gestione risorse esportato viene distribuito con diversi file di parametri nella Factory di test e nella Factory di produzione.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Creare un modello di Resource Manager per ogni ambiente

1. Nell'elenco **modello ARM** selezionare **Esporta modello ARM** per esportare il modello di gestione risorse per il data factory nell'ambiente di sviluppo.

   ![Esportare un modello di Gestione risorse](media/continuous-integration-deployment/continuous-integration-image1.png)

1. Nelle data factory di test e di produzione selezionare **Importa modello ARM**. Questa azione consente di passare al portale di Azure, in cui è possibile importare il modello esportato. Selezionare **Compila un modello personalizzato nell'editor** per aprire l'editor del modello Gestione risorse.

   ![Creazione di un modello personalizzato](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Selezionare **Carica file**e quindi selezionare il modello di gestione risorse generato.

   ![Modifica del modello](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. Nella sezione Impostazioni, immettere i valori di configurazione, ad esempio le credenziali del servizio collegato. Al termine, selezionare **Acquista** per distribuire il modello di gestione risorse.

   ![Sezione Impostazioni](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Stringhe di connessione

Per informazioni su come configurare le stringhe di connessione, vedere l'articolo del connettore. Ad esempio, per il database SQL di Azure, vedere [Copiare dati da o nel database SQL di Azure tramite Azure Data Factory](connector-azure-sql-database.md). Per verificare una stringa di connessione, è possibile aprire la visualizzazione codice per la risorsa nel Data Factory UX. Nella visualizzazione codice viene rimossa la parte della password o della chiave dell'account della stringa di connessione. Per aprire la visualizzazione codice, selezionare l'icona evidenziata qui:

![Aprire la visualizzazione codice per visualizzare la stringa di connessione](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Automatizzare l'integrazione continua con Azure Pipelines versioni

Di seguito è riportata una guida per la configurazione di una versione Azure Pipelines, che consente di automatizzare la distribuzione di un data factory in più ambienti.

![Diagramma dell'integrazione continua con Azure Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Requisiti

-   Una sottoscrizione di Azure collegata a Visual Studio Team Foundation Server o Azure Repos che usa l' [endpoint del servizio Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).

-   Data factory configurato con Azure Repos integrazione git.

-   Un insieme di credenziali delle [chiavi di Azure](https://azure.microsoft.com/services/key-vault/) che contiene i segreti per ogni ambiente.

### <a name="set-up-an-azure-pipelines-release"></a>Configurare una versione di Azure Pipelines

1.  In [Azure DevOps](https://dev.azure.com/)aprire il progetto configurato con il data factory.

1.  Sul lato sinistro della pagina selezionare **pipeline**, quindi selezionare **versioni**.

    ![Seleziona pipeline, versioni](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Selezionare **nuova pipeline**o, se sono presenti pipeline esistenti, selezionare **nuovo** e quindi **nuova pipeline di rilascio**.

1.  Selezionare il modello di **processo vuoto** .

    ![Seleziona processo vuoto](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  Nella casella **nome fase** immettere il nome dell'ambiente.

1.  Selezionare **Aggiungi artefatto**, quindi selezionare il repository configurato con il data factory. Selezionare **adf_publish** per il **ramo predefinito**. Per la **versione predefinita**, selezionare **più recente dal ramo predefinito**.

    ![Aggiungere un elemento](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Aggiungere un'attività di distribuzione di Azure Resource Manager:

    a.  Nella visualizzazione stage selezionare **Visualizza attività fasi**.

    ![Visualizzazione temporanea](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Creare una nuova attività. Cercare la **distribuzione del gruppo di risorse di Azure**e quindi selezionare **Aggiungi**.

    c.  Nell'attività di distribuzione selezionare la sottoscrizione, il gruppo di risorse e il percorso per la data factory di destinazione. Fornire le credenziali, se necessario.

    d.  Nell'elenco **azione** selezionare **Crea o Aggiorna gruppo di risorse**.

    e.  Selezionare il pulsante con i puntini di sospensione ( **...** ) accanto alla casella **modello** . Cercare il modello di Azure Resource Manager creato usando il modello di **importazione ARM** nella sezione [creare un modello di gestione risorse per ogni ambiente](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment) di questo articolo. Cercare questo file nella cartella <FactoryName> del ramo di adf_publish.

    f.  Selezionare **...** accanto alla casella **parametri modello** per scegliere il file dei parametri. Il file scelto dipenderà dal fatto che sia stata creata una copia o che usi il file predefinito, ARMTemplateParametersForFactory. JSON.

    g.  Selezionare **...** accanto alla casella **Sostituisci parametri modello** e immettere le informazioni per la data factory di destinazione. Per le credenziali provenienti da Azure Key Vault, immettere il nome del segreto tra virgolette doppie. Se, ad esempio, il nome del segreto è cred1, immettere **"$ (cred1)"** per questo valore.

    h. Selezionare **incrementale** per la **modalità di distribuzione**.

    > [!WARNING]
    > Se si seleziona **completa** per la **modalità di distribuzione**, le risorse esistenti potrebbero essere eliminate, incluse tutte le risorse nel gruppo di risorse di destinazione che non sono definite nel modello di gestione risorse.

    ![Distribuzione Data Factory Prod](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Salvare la pipeline di versione.

1. Per attivare una versione, selezionare **Crea versione**.

   ![Selezionare Crea versione](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> Negli scenari CI/CD, il tipo di runtime di integrazione (IR) in ambienti diversi deve essere lo stesso. Se, ad esempio, si dispone di un runtime di integrazione self-hosted nell'ambiente di sviluppo, anche lo stesso runtime di integrazione deve essere di tipo self-hosted in altri ambienti, ad esempio test e produzione. Analogamente, se si condividono runtime di integrazione in più fasi, è necessario configurare i runtime di integrazione come self-hosted collegato in tutti gli ambienti, ad esempio sviluppo, test e produzione.

### <a name="get-secrets-from-azure-key-vault"></a>Ottenere i segreti da Azure Key Vault

Se si dispone di segreti per passare un modello di Azure Resource Manager, è consigliabile usare Azure Key Vault con la versione Azure Pipelines.

Esistono due modi per gestire i segreti:

1.  Aggiungere i segreti al file dei parametri. Per altre informazioni, vedere [Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione](../azure-resource-manager/templates/key-vault-parameter.md).

    Creare una copia del file dei parametri caricato nel ramo di pubblicazione. Impostare i valori dei parametri che si desidera ottenere da Key Vault utilizzando il formato seguente:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    Quando si usa questo metodo, viene eseguito il pull automatico del segreto dall'insieme di credenziali delle chiavi.

    Il file dei parametri deve essere anche nel ramo di pubblicazione.

-  Aggiungere un' [attività Azure Key Vault](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) prima dell'attività di distribuzione Azure Resource Manager descritta nella sezione precedente:

    1.  Nella scheda **attività** , creare una nuova attività. Cercare **Azure Key Vault** e aggiungerlo.

    1.  Nell'attività Key Vault selezionare la sottoscrizione in cui è stato creato l'insieme di credenziali delle chiavi. Fornire le credenziali, se necessario, e quindi selezionare l'insieme di credenziali delle chiavi.

    ![Aggiungere un'attività Key Vault](media/continuous-integration-deployment/continuous-integration-image8.png)

   #### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Concedere le autorizzazioni all'agente di Azure Pipelines

   L'attività Azure Key Vault potrebbe non riuscire con un errore di accesso negato se non sono impostate le autorizzazioni corrette. Scaricare i log per la versione e individuare il file con estensione ps1 che contiene il comando per concedere le autorizzazioni all'agente Azure Pipelines. È possibile eseguire il comando direttamente. In alternativa, è possibile copiare l'ID entità dal file e aggiungere manualmente i criteri di accesso nel portale di Azure. `Get` e `List` sono le autorizzazioni minime necessarie.

### <a name="update-active-triggers"></a>Aggiornare i trigger attivi

La distribuzione può non riuscire se si prova ad aggiornare i trigger attivi. Per aggiornare i trigger attivi, è necessario arrestarli manualmente e quindi riavviarli dopo la distribuzione. A tale scopo, è possibile utilizzare un'attività Azure PowerShell:

1.  Nella scheda **attività** della versione aggiungere un'attività **Azure PowerShell** . Scegliere la versione 4. * dell'attività. 

1.  Selezionare la sottoscrizione in cui si trova la factory.

1.  Selezionare **percorso file script** come tipo di script. A questo scopo, è necessario salvare lo script di PowerShell nel repository. È possibile usare lo script di PowerShell seguente per arrestare i trigger:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

È possibile completare passaggi simili (con la funzione `Start-AzDataFactoryV2Trigger`) per riavviare i trigger dopo la distribuzione.

### <a name="sample-pre--and-post-deployment-script"></a>Script di pre-distribuzione e post-distribuzione di esempio

Lo script di esempio seguente può essere usato per arrestare i trigger prima della distribuzione e riavviarli in seguito. Lo script include anche il codice per eliminare le risorse che sono state rimosse. Salvare lo script in un repository git di Azure DevOps e farvi riferimento tramite un'attività Azure PowerShell usando la versione 4. *.

Quando si esegue uno script di pre-distribuzione, è necessario specificare una variante dei seguenti parametri nel campo **argomenti script** .

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


Quando si esegue uno script di post-distribuzione, è necessario specificare una variante dei seguenti parametri nel campo **argomenti script** .

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

    ![Azure PowerShell task](media/continuous-integration-deployment/continuous-integration-image11.png)

Di seguito è riportato lo script che può essere usato per pre-e post-distribuzione. Questo account per le risorse eliminate e i riferimenti alle risorse.

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        return $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
        triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Usare i parametri personalizzati con il modello di Resource Manager

Se si è in modalità GIT, è possibile eseguire l'override delle proprietà predefinite nel modello di Gestione risorse per impostare le proprietà con parametri nel modello e le proprietà hardcoded. In questi scenari potrebbe essere necessario eseguire l'override del modello di parametrizzazione predefinito:

* Si utilizza l'integrazione continua/recapito continuo automatica e si desidera modificare alcune proprietà durante Gestione risorse distribuzione, ma le proprietà non sono parametrizzate per impostazione predefinita.
* La Factory è talmente grande che il modello di Gestione risorse predefinito non è valido perché contiene un numero di parametri massimo consentito (256).

In queste condizioni, per eseguire l'override del modello di parametrizzazione predefinito, creare un file denominato ARM-Template-Parameters-Definition. JSON nella cartella specificata come cartella radice per l'integrazione di git data factory. È necessario usare il nome esatto del file. Data Factory legge questo file da qualsiasi ramo attualmente attivo nel portale di Azure Data Factory, non solo dal ramo collaborazione. È possibile creare o modificare il file da un ramo privato, in cui è possibile testare le modifiche selezionando **Esporta modello ARM** nell'interfaccia utente. È quindi possibile unire il file nel ramo collaborazione. Se non viene trovato alcun file, viene usato il modello predefinito.

### <a name="syntax-of-a-custom-parameters-file"></a>Sintassi di un file dei parametri personalizzati

Di seguito sono riportate alcune linee guida da seguire quando si crea il file di parametri personalizzati. Il file è costituito da una sezione per ogni tipo di entità: trigger, pipeline, servizio collegato, set di dati, Runtime di integrazione e così via.
* Immettere il percorso della proprietà nel tipo di entità pertinente.
* L'impostazione di un nome di proprietà su `*` indica che si desidera parametrizzare tutte le proprietà al suo interno (solo fino al primo livello, non in modo ricorsivo). È anche possibile fornire eccezioni a questa configurazione.
* Impostando il valore di una proprietà come stringa, viene indicato che si desidera parametrizzare la proprietà. Usare il formato `<action>:<name>:<stype>`.
   *  `<action>` può essere uno di questi caratteri:
      * `=` significa che il valore corrente viene mantenuto come valore predefinito per il parametro.
      * `-` significa che non si mantiene il valore predefinito per il parametro.
      * `|` è un caso speciale per i segreti da Azure Key Vault per le stringhe di connessione o le chiavi.
   * `<name>` è il nome del parametro. Se è vuota, prende il nome della proprietà. Se il valore inizia con un carattere di `-`, il nome viene abbreviato. Ad esempio, `AzureStorage1_properties_typeProperties_connectionString` verrebbe abbreviato in `AzureStorage1_connectionString`.
   * `<stype>` è il tipo di parametro. Se `<stype>` è vuoto, il tipo predefinito è `string`. Valori supportati: `string`, `bool`, `number`, `object`e `securestring`.
* La specifica di una matrice nel file di definizione indica che la proprietà corrispondente nel modello è una matrice. Data Factory scorre tutti gli oggetti nella matrice utilizzando la definizione specificata nell'oggetto di Integration runtime della matrice. Il secondo oggetto, una stringa, diventa il nome della proprietà, che viene usato come nome per il parametro per ogni iterazione.
* Una definizione non può essere specifica di un'istanza di risorsa. Qualsiasi definizione viene applicata a tutte le risorse di quel tipo.
* Per impostazione predefinita, vengono parametrizzate tutte le stringhe protette, ad esempio Key Vault segreti e le stringhe protette, ad esempio le stringhe di connessione, le chiavi e i token.
 
### <a name="sample-parameterization-template"></a>Modello di parametrizzazione di esempio

Di seguito è riportato un esempio di come potrebbe essere un modello di parametrizzazione:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Ecco una spiegazione del modo in cui viene costruito il modello precedente, suddiviso per tipo di risorsa.

#### <a name="pipelines"></a>Pipeline
    
* Qualsiasi proprietà nel percorso `activities/typeProperties/waitTimeInSeconds` è parametrizzata. Qualsiasi attività in una pipeline che dispone di una proprietà a livello di codice denominata `waitTimeInSeconds` (ad esempio, l'attività `Wait`) viene parametrizzata come numero, con un nome predefinito. Ma non avrà un valore predefinito nel modello di Gestione risorse. Sarà un input obbligatorio durante la distribuzione del Gestione risorse.
* Analogamente, una proprietà denominata `headers` (ad esempio, in un'attività `Web`) viene parametrizzata con il tipo `object` (JObject). Ha un valore predefinito, che è lo stesso valore di quello della factory di origine.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Tutte le proprietà sotto il percorso `typeProperties` vengono parametrizzate con i rispettivi valori predefiniti. Sono ad esempio disponibili due proprietà in `IntegrationRuntimes` proprietà del tipo: `computeProperties` e `ssisProperties`. Entrambi i tipi di proprietà vengono creati con i rispettivi valori e tipi predefiniti (oggetto).

#### <a name="triggers"></a>Trigger

* In `typeProperties`, due proprietà sono parametrizzate. Il primo è `maxConcurrency`, che è specificato per avere un valore predefinito ed è di tipo`string`. Il nome del parametro predefinito è `<entityName>_properties_typeProperties_maxConcurrency`.
* Anche la proprietà `recurrence` è parametrizzata. Al suo interno, tutte le proprietà a tale livello vengono specificate per essere parametrizzate come stringhe, con i valori predefiniti e i nomi di parametro. Un'eccezione è rappresentata dalla proprietà `interval`, che è parametrizzata come tipo `number`. Il nome del parametro è con suffisso `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Analogamente, la proprietà `freq` è una stringa e viene parametrizzata come stringa. Tuttavia, la proprietà `freq` è parametrizzata senza un valore predefinito. Il nome viene abbreviato e viene suffissato. Ad esempio: `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* I servizi collegati sono univoci. Poiché i servizi collegati e i set di impostazioni hanno un'ampia gamma di tipi, è possibile specificare una personalizzazione specifica del tipo. In questo esempio, per tutti i servizi collegati di tipo `AzureDataLakeStore`, verrà applicato un modello specifico. Per tutti gli altri (tramite `*`), verrà applicato un modello diverso.
* La proprietà `connectionString` verrà parametrizzata come valore `securestring`. Non avrà un valore predefinito. Avrà un nome di parametro abbreviato con il suffisso `connectionString`.
* La proprietà `secretAccessKey` è un `AzureKeyVaultSecret`, ad esempio in un servizio collegato Amazon S3. Viene automaticamente parametrizzato come segreto Azure Key Vault e recuperato dall'insieme di credenziali delle chiavi configurato. È anche possibile parametrizzare l'insieme di credenziali delle chiavi.

#### <a name="datasets"></a>Set di dati

* Sebbene la personalizzazione specifica del tipo sia disponibile per i set di impostazioni, è possibile fornire la configurazione senza avere una configurazione a livello di \*. Nell'esempio precedente, vengono parametrizzate tutte le proprietà del set di dati in `typeProperties`.

### <a name="default-parameterization-template"></a>Modello di parametrizzazione predefinito

Di seguito è riportato il modello di parametrizzazione predefinito corrente. Se è necessario aggiungere solo alcuni parametri, la modifica diretta di questo modello potrebbe essere un'idea consigliata, perché non si perderà la struttura di parametrizzazione esistente.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
            "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

Nell'esempio seguente viene illustrato come aggiungere un singolo valore al modello di parametrizzazione predefinito. Si vuole solo aggiungere un ID cluster interattivo Azure Databricks esistente per un servizio collegato databricks al file dei parametri. Si noti che questo file corrisponde al file precedente, ad eccezione dell'aggiunta di `existingClusterId` nel campo proprietà di `Microsoft.DataFactory/factories/linkedServices`.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
            "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Modelli di Resource Manager collegati

Se è stata configurata l'integrazione continua/distribuzione continua per le Data Factory, è possibile che vengano superati i limiti del modello Azure Resource Manager Man mano che le dimensioni della Factory aumentano Ad esempio, un limite è il numero massimo di risorse in un modello di Gestione risorse. Per ospitare grandi Factory durante la generazione del modello di Gestione risorse completo per una factory, Data Factory ora genera modelli di Gestione risorse collegati. Con questa funzionalità, l'intero payload della factory viene suddiviso in diversi file in modo da non vincolare i limiti.

Se è stato configurato git, i modelli collegati vengono generati e salvati insieme ai modelli di Gestione risorse completi nel ramo di adf_publish in una nuova cartella denominata linkedTemplates:

![Cartella dei modelli di Resource Manager collegati](media/continuous-integration-deployment/linked-resource-manager-templates.png)

I modelli di Gestione risorse collegati sono in genere costituiti da un modello master e da un set di modelli figlio collegati al database master. Il modello padre è denominato ArmTemplate_master. JSON e i modelli figlio vengono denominati con il modello ArmTemplate_0. JSON, ArmTemplate_1. JSON e così via. 

Per usare i modelli collegati anziché il modello di Gestione risorse completo, aggiornare l'attività CI/CD in modo che punti a ArmTemplate_master. JSON anziché a ArmTemplateForFactory. JSON (modello di Gestione risorse completo). Gestione risorse anche necessario caricare i modelli collegati in un account di archiviazione in modo che Azure possa accedervi durante la distribuzione. Per altre informazioni, vedere [distribuzione di modelli di gestione risorse collegati con VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Ricordarsi di aggiungere gli script di Data Factory nella pipeline CI/CD prima e dopo l'attività di distribuzione.

Se Git non è configurato, è possibile accedere ai modelli collegati tramite **Esporta modello ARM** nell'elenco di **modelli ARM** .

## <a name="hotfix-production-branch"></a>Ramo di produzione hotfix

Se si distribuisce una factory in produzione e si comprende un bug che deve essere corretto immediatamente, ma non è possibile distribuire il ramo di collaborazione corrente, potrebbe essere necessario distribuire un hotfix. Questo approccio è noto come progettazione rapida o QFE.

1.  In Azure DevOps passare alla versione che è stata distribuita in produzione. Trovare l'ultimo commit distribuito.

2.  Dal messaggio di commit, ottenere l'ID commit del ramo collaborazione.

3.  Creare un nuovo ramo dell'hotfix dal commit.

4.  Passare al Azure Data Factory UX e passare al ramo hotfix.

5.  Con il Azure Data Factory UX, correggere il bug. Testare le modifiche.

6.  Dopo aver verificato la correzione, selezionare **Esporta modello ARM** per ottenere il modello di gestione risorse dell'hotfix.

7.  Controllare manualmente questa compilazione nel ramo adf_publish.

8.  Se la pipeline di rilascio è stata configurata in modo che venga attivata automaticamente in base alle archiviazioni di adf_publish, viene avviata automaticamente una nuova versione. In caso contrario, accodare manualmente una versione.

9.  Distribuire la versione hotfix nelle factory di test e di produzione. Questa versione contiene il payload di produzione precedente e la correzione apportata nel passaggio 5.

10. Aggiungere le modifiche dall'hotfix al ramo Development in modo che le versioni successive non includano lo stesso bug.

## <a name="best-practices-for-cicd"></a>Procedure consigliate per la pipeline CI/CD

Se si usa l'integrazione git con il data factory e si ha una pipeline di integrazione continua/distribuzione continua che sposta le modifiche dallo sviluppo a test e quindi alla produzione, è consigliabile eseguire le procedure consigliate seguenti:

-   **Integrazione di git**. È necessario configurare solo la data factory di sviluppo con l'integrazione git. Le modifiche apportate ai test e alla produzione vengono distribuite tramite CI/CD e non richiedono l'integrazione di git.

-   **Script di Data Factory in CI/CD**. Prima del passaggio di distribuzione Gestione risorse in CI/CD, è necessario completare determinate attività, ad esempio l'arresto e il riavvio dei trigger e l'esecuzione della pulizia. Si consiglia di usare gli script di PowerShell prima e dopo la distribuzione. Per altre informazioni, vedere [Update Active Triggers](#update-active-triggers).

-   **Runtime di integrazione e condivisione**. I runtime di integrazione non cambiano spesso e sono simili in tutte le fasi dell'integrazione continua/recapito continuo. Quindi Data Factory si prevede di avere lo stesso nome e tipo di runtime di integrazione in tutte le fasi di CI/CD. Se si desidera condividere runtime di integrazione in tutte le fasi, è consigliabile utilizzare una factory ternaria per contenere solo i runtime di integrazione condivisi. È possibile usare questa factory condivisa in tutti gli ambienti come tipo di runtime di integrazione collegata.

-   **Insieme di credenziali delle chiavi**. Quando si usano servizi collegati basati su Azure Key Vault, è possibile sfruttarli ulteriormente mantenendo insiemi di credenziali delle chiavi distinti per ambienti diversi. È anche possibile configurare livelli di autorizzazione separati per ogni insieme di credenziali delle chiavi. Ad esempio, potrebbe non essere necessario che i membri del team dispongano delle autorizzazioni per i segreti di produzione. Se si segue questo approccio, si consiglia di proteggere gli stessi nomi di segreto in tutte le fasi. Se si mantengono gli stessi nomi, non è necessario modificare i modelli di Gestione risorse negli ambienti CI/CD, perché l'unica cosa che cambia è il nome dell'insieme di credenziali delle chiavi, che è uno dei parametri del modello di Gestione risorse.

## <a name="unsupported-features"></a>Funzionalità non supportate

- Per impostazione predefinita, Data Factory non consente il cherry-picking di commit o la pubblicazione selettiva delle risorse. Le pubblicazioni includeranno tutte le modifiche apportate all'data factory.

    - Le entità di data factory dipendono l'una dall'altra. Ad esempio, i trigger dipendono dalle pipeline e le pipeline dipendono da set di impostazioni e altre pipeline. La pubblicazione selettiva di un subset di risorse può causare comportamenti imprevisti ed errori.
    - In rari casi in cui è necessaria la pubblicazione selettiva, provare a usare un hotfix. Per ulteriori informazioni, vedere la pagina relativa al [ramo di produzione degli hotfix](#hotfix-production-branch).

-   Non è possibile pubblicare da rami privati.

-   Attualmente non è possibile ospitare progetti in bitbucket.
