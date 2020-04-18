---
title: Integrazione e distribuzione continue in Azure Data FactoryContinuous integration and delivery in Azure Data Factory
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
ms.openlocfilehash: 3e6612b30dd8ae6716c0f87687e77c5961275ea5
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606575"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Integrazione e distribuzione continue in Azure Data FactoryContinuous integration and delivery in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Panoramica

L'integrazione continua è la pratica di testare ogni modifica apportata alla codebase automaticamente e il più presto possibile.La distribuzione continua segue il test che viene eseguito durante l'integrazione continua ed esegue il push delle modifiche in un sistema di gestione temporanea o di produzione.

In Azure Data Factory l'integrazione e la distribuzione continue (CI/CD) significa spostare le pipeline di Data Factory da un ambiente (sviluppo, test, produzione) a un altro. È possibile usare l'integrazione dell'esperienza utente di Data Factory con i modelli di Azure Resource Manager per eseguire CI/CD.

Nell'esperienza utente di Data Factory è possibile generare un modello di Resource Manager dal menu a discesa **Modello ARM.** Quando si seleziona **Esporta modello ARM**, il portale genera il modello Resource Manager per la data factory e un file di configurazione che include tutte le stringhe di connessione e altri parametri. Quindi si crea un file di configurazione per ogni ambiente (sviluppo, test, produzione). Il file di modello di Resource Manager principale rimane lo stesso per tutti gli ambienti.

Per un'introduzione di nove minuti a questa funzionalità e una dimostrazione, guardare questo video:For a nine-minute introduction to this feature and a demonstration, watch this video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>Ciclo di vita di CI/CD

Di seguito è riportata una panoramica di esempio del ciclo di vita ci/CD in una data factory di Azure configurata con Azure Repos Git.Below is a sample overview of the CI/CD lifecycle in an Azure data factory that's configured with Azure Repos Git. Per altre informazioni su come configurare un repository Git, vedere [Controllo del codice sorgente in Azure Data Factory.For](source-control.md)more information on how to configure a Git repository, see Source control in Azure Data Factory .

1.  Una data factory di sviluppo viene creata e configurata con Azure Repos Git.A development data factory is created and configured with Azure Repos Git. Tutti gli sviluppatori devono disporre dell'autorizzazione per creare risorse di Data Factory come pipeline e set di dati.

1.  Come gli sviluppatori apportare modifiche nei loro rami di funzionalità, eseguire il debug delle esecuzioni della pipeline con le modifiche più recenti. Per altre informazioni su come eseguire il debug di una pipeline, vedere [Sviluppo e debug iterativi con Azure Data Factory.](iterative-development-debugging.md)

1.  Dopo che gli sviluppatori sono soddisfatti delle modifiche, creano una richiesta pull dal ramo di funzionalità al ramo di collaborazione o master per ottenere le modifiche esaminate dai peer.

1.  Dopo l'approvazione di una richiesta pull e l'unione delle modifiche nel ramo master, le modifiche possono essere pubblicate nella factory di sviluppo.

1.  Quando il team è pronto per distribuire le modifiche alla factory di test e quindi alla factory di produzione, esporta il modello Resource Manager dal ramo master.

1.  Il modello di Resource Manager esportato viene distribuito con file di parametri diversi nella factory di test e nella factory di produzione.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Creare un modello di Resource Manager per ogni ambiente

1. Nell'elenco **Modello ARM** selezionare **Esporta modello ARM** per esportare il modello resource Manager per la data factory nell'ambiente di sviluppo.

   ![Esportare un modello di Resource ManagerExport a Resource Manager template](media/continuous-integration-deployment/continuous-integration-image1.png)

1. Nelle factory di dati di test e di produzione selezionare **Importa modello ARM**. Questa azione consente di passare al portale di Azure, in cui è possibile importare il modello esportato. Selezionare **Crea modello personalizzato nell'editor** per aprire l'editor dei modelli di Resource Manager.Select Build your own template in the editor to open the Resource Manager template editor.

   ![Crea il tuo modello](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Selezionare **Carica file**e quindi selezionare il modello di Resource Manager generato. Si tratta del file **arm_template.json** che si trova nel file .zip esportato nel passaggio 1.

   ![Modifica del modello](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. Nella sezione delle impostazioni immettere i valori di configurazione, ad esempio le credenziali del servizio collegato. Al termine, selezionare Acquista per distribuire il modello di Resource Manager.When you're done, select **Purchase** to deploy the Resource Manager template.

   ![Sezione Impostazioni](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Stringhe di connessione

Per informazioni su come configurare le stringhe di connessione, vedere l'articolo del connettore. Ad esempio, per il database SQL di Azure, vedere [Copiare dati da o nel database SQL di Azure tramite Azure Data Factory](connector-azure-sql-database.md). Per verificare una stringa di connessione, è possibile aprire la visualizzazione codice per la risorsa nell'esperienza utente di Data Factory.To verify a connection string, you can open the code view for the resource in the Data Factory UX. Nella visualizzazione codice la parte relativa alla password o alla chiave dell'account della stringa di connessione viene rimossa. Per aprire la vista Codice, selezionare l'icona evidenziata qui:

![Aprire la visualizzazione codice per visualizzare la stringa di connessioneOpen code view to see the connection string](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Automatizzare l'integrazione continua usando le versioni di Azure PipelinesAutomate continuous integration by using Azure Pipelines releases

Di seguito è riportata una guida per la configurazione di una versione di Azure Pipelines, che automatizza la distribuzione di una data factory in più ambienti.

![Diagramma dell'integrazione continua con Azure Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Requisiti

-   Una sottoscrizione di Azure collegata a Visual Studio Team Foundation Server o Azure Repos che usa l'endpoint del [servizio Azure Resource Manager.](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)

-   Una data factory configurata con l'integrazione di Azure Repos Git.A data factory configured with Azure Repos Git integration.

-   Un insieme di [credenziali delle chiavi](https://azure.microsoft.com/services/key-vault/) di Azure che contiene i segreti per ogni ambiente.

### <a name="set-up-an-azure-pipelines-release"></a>Configurare una versione di Azure Pipelines

1.  In [Azure DevOps](https://dev.azure.com/)aprire il progetto configurato con la data factory.

1.  Sul lato sinistro della pagina selezionare **Pipeline**, quindi **Rilascia**.

    ![Seleziona pipeline, rilasci](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Selezionare **Nuova pipeline**oppure, se si dispone di pipeline esistenti, selezionare **Nuovo** e quindi Nuova pipeline **di rilascio**.

1.  Selezionare il modello **di processo Vuoto.**

    ![Selezionare Processo vuoto](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  Nella casella **Nome fase,** immettere il nome dell'ambiente.

1.  Selezionare **Aggiungi elemento**, quindi selezionare il repository configurato con la data factory. Selezionare **adf_publish** per il **ramo Predefinito**. Per la **versione predefinita**, selezionare Più recente dal **ramo predefinito**.

    ![Aggiungere un elemento](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Aggiungere un'attività di distribuzione di Azure Resource Manager:

    a.  Nella vista delle fasi, selezionare **Visualizza attività fase**.

    ![Vista dello stage](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Creare una nuova attività. Cercare **Distribuzione gruppo di risorse di Azure**e quindi selezionare **Aggiungi**.

    c.  Nell'attività Distribuzione selezionare la sottoscrizione, il gruppo di risorse e il percorso per la data factory di destinazione. Se necessario, fornire le credenziali.

    d.  Nell'elenco **Azione** selezionare **Crea o aggiorna gruppo di risorse.**

    e.  Selezionare il pulsante con i conielli (**...**) accanto alla casella **Modello.** Cercare il modello di Azure Resource Manager creato usando **Importa modello ARM** nella sezione Creare un modello di Resource Manager per ogni [ambiente](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment) di questo articolo. Cercare questo file <FactoryName> nella cartella del ramo adf_publish.

    f.  Selezionare **...** accanto alla casella **Parametri modello** per scegliere il file dei parametri. Il file scelto dipenderà dalla creazione di una copia o dal fatto che il file predefinito, ARMTemplateParametersForFactory.json.

    g.  Selezionare **...** accanto alla casella **Sostituisci parametri modello** e immettere le informazioni per la data factory di destinazione. Per le credenziali provenienti dall'insieme di credenziali delle chiavi di Azure, immettere il nome del segreto tra virgolette doppie. Ad esempio, se il nome del segreto è cred1, immettere **"(cred1)"** per questo valore.

    h. Selezionare **Incrementale** per la **modalità di distribuzione**.

    > [!WARNING]
    > Se si seleziona **Completa** per la **modalità di distribuzione**, le risorse esistenti potrebbero essere eliminate, incluse tutte le risorse nel gruppo di risorse di destinazione non definite nel modello di Resource Manager.

    ![Distribuzione prod di Data Factory](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Salvare la pipeline di versione.

1. Per attivare una versione, selezionare **Crea versione**.

   ![Selezionare Crea versione](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> Negli scenari CI/CD, il tipo di runtime di integrazione (IR) in ambienti diversi deve essere lo stesso. Ad esempio, se si dispone di un prodotto a motore self-hosted nell'ambiente di sviluppo, lo stesso iR deve essere di tipo self-hosted in altri ambienti, ad esempio test e produzione. Analogamente, se si condividono runtime di integrazione in più fasi, è necessario configurare i runtime di integrazione come connessi come self-hosted in tutti gli ambienti, ad esempio sviluppo, test e produzione.

### <a name="get-secrets-from-azure-key-vault"></a>Ottenere segreti dall'insieme di credenziali delle chiavi di AzureGet secrets from Azure Key Vault

Se sono segreti per passare un modello di Azure Resource Manager, è consigliabile usare l'insieme di credenziali delle chiavi di Azure con la versione Azure Pipelines.If you have secrets to pass in an Azure Resource Manager template, we recommend that you use Azure Key Vault with the Azure Pipelines release.

Esistono due modi per gestire i segreti:There are two ways to handle secrets:

1.  Aggiungere i segreti al file dei parametri. Per altre informazioni, vedere [Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione](../azure-resource-manager/templates/key-vault-parameter.md).

    Creare una copia del file dei parametri caricato nel ramo di pubblicazione. Impostare i valori dei parametri che si desidera ottenere dall'insieme di credenziali delle chiavi utilizzando il seguente formato:

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

1. Aggiungere [un'attività Dell'insieme di](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) credenziali delle chiavi di Azure prima dell'attività di distribuzione di Azure Resource Manager descritta nella sezione precedente:Add an Azure Key Vault task before the Azure Resource Manager Deployment task described in the previous section:

    1.  Nella scheda **Attività** creare una nuova attività. Cercare **L'insieme di credenziali delle** chiavi di Azure e aggiungerlo.

    1.  Nell'attività Key Vault, selezionare la sottoscrizione in cui è stato creato l'insieme di credenziali delle chiavi. Specificare le credenziali, se necessario, quindi selezionare l'insieme di credenziali delle chiavi.

    ![Aggiungere un'attività dell'insieme di credenziali delle chiaviAdd a Key Vault task](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Concedere le autorizzazioni all'agente di Azure Pipelines

L'attività Dell'insieme di credenziali delle chiavi di Azure potrebbe non riuscire con un errore di accesso negato se non sono impostate le autorizzazioni corrette. Scaricare i log per la versione e individuare il file con estensione ps1 che contiene il comando per concedere autorizzazioni all'agente Pipeline di Azure.Download the logs for the release, and locate the .ps1 file that contains the command to give permissions to the Azure Pipelines agent. È possibile eseguire il comando direttamente. In alternativa, è possibile copiare l'ID entità dal file e aggiungere manualmente i criteri di accesso nel portale di Azure.Or you can copy the principal ID from the file and add the access policy manually in the Azure portal. `Get`e `List` sono le autorizzazioni minime richieste.

### <a name="update-active-triggers"></a>Aggiornare i trigger attivi

La distribuzione può non riuscire se si prova ad aggiornare i trigger attivi. Per aggiornare i trigger attivi, è necessario arrestarli manualmente e quindi riavviarli dopo la distribuzione. È possibile eseguire questa operazione usando un'attività di Azure PowerShell:You can do this by using an Azure PowerShell task:

1.  Nella scheda Attività della versione aggiungere un'attività di **Azure PowerShell.On** the **Tasks** tab of the release, add an Azure PowerShell task. Scegliere la versione dell'attività 4. 

1.  Selezionare l'abbonamento in cui si trova la tua fabbrica.

1.  Selezionare **Percorso file script** come tipo di script. Ciò richiede il salvataggio dello script di PowerShell nel repository. Lo script di PowerShell seguente può essere usato per arrestare i trigger:The following PowerShell script can be used to stop triggers:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

È possibile completare passaggi `Start-AzDataFactoryV2Trigger` simili (con la funzione) per riavviare i trigger dopo la distribuzione.

### <a name="sample-pre--and-post-deployment-script"></a>Esempio di script pre e post-distribuzione

Lo script di esempio seguente può essere usato per arrestare i trigger prima della distribuzione e riavviarli in un secondo momento. Lo script include anche il codice per eliminare le risorse che sono state rimosse. Salvare lo script in un repository git DevOps di Azure e farvi riferimento tramite un'attività di Azure PowerShell con la versione 4.

Quando si esegue uno script pre-distribuzione, è necessario specificare una variazione dei parametri seguenti nel campo **Argomenti script.**

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


Quando si esegue uno script post-distribuzione, è necessario specificare una variazione dei parametri seguenti nel campo **Argomenti script.**

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Attività di Azure PowerShellAzure PowerShell task](media/continuous-integration-deployment/continuous-integration-image11.png)

Di seguito è riportato lo script che può essere utilizzato per la pre e post-distribuzione. Tiene conto delle risorse eliminate e dei riferimenti alle risorse.

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
        Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Disabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
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
        Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Enabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Usare i parametri personalizzati con il modello di Resource Manager

Se si è in modalità GIT, è possibile eseguire l'override delle proprietà predefinite nel modello di Resource Manager per impostare le proprietà con parametri nel modello e le proprietà hardcoded. È possibile eseguire l'override del modello di parametrizzazione predefinito in questi scenari:You might want to override the default parameterization template in these scenarios:

* Si utilizza CI/CD automatizzato e si desidera modificare alcune proprietà durante la distribuzione di Resource Manager, ma le proprietà non sono parametrizzate per impostazione predefinita.
* La factory è così grande che il modello di Resource Manager predefinito non è valido perché ha più di parametri consentiti massimi (256).

In queste condizioni, per eseguire l'override del modello di parametrizzazione predefinito, creare un file denominato **arm-template-parameters-definition.json** nella cartella specificata come cartella radice per l'integrazione git della data factory. È necessario utilizzare il nome esatto del file. Data Factory legge questo file dal ramo in cui ci si trova attualmente nel portale di Azure Data Factory, non solo dal ramo di collaborazione. È possibile creare o modificare il file da un ramo privato, in cui è possibile testare le modifiche selezionando **Esporta modello ARM** nell'interfaccia utente. È quindi possibile unire il file nel ramo di collaborazione. Se non viene trovato alcun file, viene utilizzato il modello predefinito.

> [!NOTE]
> Un modello di parametrizzazione personalizzato non modifica il limite dei parametri di modello ARM di 256.A custom parameterization template doesn't change the ARM template parameter limit of 256. Consente di scegliere e ridurre il numero di proprietà con parametri.

### <a name="syntax-of-a-custom-parameters-file"></a>Sintassi di un file dei parametri personalizzati

Di seguito sono riportate alcune linee guida da seguire quando si crea il file dei parametri personalizzati. Il file è costituito da una sezione per ogni tipo di entità: trigger, pipeline, servizio collegato, set di dati, runtime di integrazione e così via.
* Immettere il percorso della proprietà sotto il tipo di entità pertinente.
* L'impostazione `*` di un nome di proprietà su indica che si desidera parametrizzare tutte le proprietà al suo ambiente (solo fino al primo livello, non in modo ricorsivo). È inoltre possibile fornire eccezioni a questa configurazione.
* L'impostazione del valore di una proprietà come stringa indica che si desidera parametrizzare la proprietà. Utilizzare il `<action>:<name>:<stype>`formato .
   *  `<action>` può essere uno di questi caratteri:
      * `=` significa mantenere il valore corrente come valore predefinito per il parametro.
      * `-` significa che non mantenere il valore predefinito per il parametro.
      * `|` è un caso speciale per i segreti dall'insieme di credenziali delle chiavi di Azure per le stringhe di connessione o le chiavi.
   * `<name>` è il nome del parametro. Se è vuoto, accetta il nome della proprietà. Se il valore `-` inizia con un carattere, il nome viene abbreviato. Ad esempio, `AzureStorage1_properties_typeProperties_connectionString` verrebbe `AzureStorage1_connectionString`abbreviato in .
   * `<stype>` è il tipo di parametro. Se `<stype>` è vuoto, il `string`tipo predefinito è . `string`Valori supportati: `bool` `number`, `object`, `securestring`, , e .
* Specificando una matrice nel file di definizione si indica che la proprietà corrispondente nel modello è una matrice. Data Factory scorre tutti gli oggetti nella matrice utilizzando la definizione specificata nell'oggetto runtime di integrazione della matrice. Il secondo oggetto, una stringa, diventa il nome della proprietà, che viene usato come nome per il parametro per ogni iterazione.
* Una definizione non può essere specifica di un'istanza di risorsa. Qualsiasi definizione si applica a tutte le risorse di quel tipo.
* Per impostazione predefinita, tutte le stringhe sicure, ad esempio i segreti dell'insieme di credenziali delle chiavi, e le stringhe sicure, ad esempio stringhe di connessione, chiavi e token, sono parametrizzate.
 
### <a name="sample-parameterization-template"></a>Modello di parametrizzazione di esempio

Ecco un esempio di come potrebbe apparire un modello di parametrizzazione:Here's an example of what a parameterization template might look like:

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
Ecco una spiegazione di come viene costruito il modello precedente, suddiviso per tipo di risorsa.

#### <a name="pipelines"></a>Pipeline
    
* Qualsiasi proprietà nel `activities/typeProperties/waitTimeInSeconds` percorso è parametrizzata. Qualsiasi attività in una pipeline con una `waitTimeInSeconds` proprietà a `Wait` livello di codice denominata (ad esempio, l'attività) viene parametrizzata come numero, con un nome predefinito. Ma non avrà un valore predefinito nel modello di Resource Manager.But it won't have a default value in the Resource Manager template. Sarà un input obbligatorio durante la distribuzione di Resource Manager.It will be a mandatory input during the Resource Manager deployment.
* Analogamente, una `headers` proprietà chiamata (ad esempio, in `Web` `object` un'attività) è parametrizzata con tipo (JObject). Ha un valore predefinito, che è lo stesso valore di quello della factory di origine.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Tutte le proprietà `typeProperties` nel percorso sono parametrizzate con i rispettivi valori predefiniti. Ad esempio, sono disponibili `IntegrationRuntimes` due `computeProperties` proprietà `ssisProperties`nelle proprietà del tipo: e . Entrambi i tipi di proprietà vengono creati con i rispettivi valori e tipi predefiniti (Object).

#### <a name="triggers"></a>Trigger

* In `typeProperties`, due proprietà sono parametrizzate. Il primo `maxConcurrency`è , che è specificato per avere`string`un valore predefinito ed è di tipo . Ha il nome `<entityName>_properties_typeProperties_maxConcurrency`di parametro predefinito .
* Anche `recurrence` la proprietà è con parametri. Sotto di esso, tutte le proprietà a quel livello vengono specificate come stringhe, con i valori predefiniti e i nomi dei parametri. Un'eccezione `interval` è la proprietà , `number`che è parametrizzata come tipo . Il nome del parametro viene associato a un suffisso con `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Analogamente, `freq` la proprietà è una stringa e viene parametrizzata come stringa. Tuttavia, `freq` la proprietà è parametrizzata senza un valore predefinito. Il nome viene abbreviato e suffisso. Ad esempio: `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices (Servizi collegati)

* I servizi collegati sono unici. Poiché i servizi collegati e i set di dati dispongono di un'ampia gamma di tipi, è possibile fornire la personalizzazione specifica del tipo. In questo esempio, per tutti `AzureDataLakeStore`i servizi collegati di tipo , verrà applicato un modello specifico. Per tutti gli `*`altri (via ), verrà applicato un modello diverso.
* La `connectionString` proprietà verrà parametrata come `securestring` valore. Non avrà un valore predefinito. Avrà un nome di parametro abbreviato `connectionString`che è suffisso con .
* La `secretAccessKey` proprietà è `AzureKeyVaultSecret` un (ad esempio, in un servizio collegato Amazon S3). Viene automaticamente parametrizzato come segreto dell'insieme di credenziali delle chiavi di Azure e recuperato dall'insieme di credenziali delle chiavi configurato. È inoltre possibile parametrizzare l'insieme di credenziali delle chiavi stesso.

#### <a name="datasets"></a>Set di dati

* Sebbene la personalizzazione specifica del tipo sia disponibile per \*i set di dati, è possibile fornire la configurazione senza disporre esplicitamente di una configurazione a livello. Nell'esempio precedente, tutte le `typeProperties` proprietà del set di dati in sono parametrizzate.

### <a name="default-parameterization-template"></a>Modello di parametrizzazione predefinito

Di seguito è riportato il modello di parametrizzazione predefinito corrente. Se è necessario aggiungere solo alcuni parametri, la modifica diretta di questo modello potrebbe essere una buona idea perché non si perderà la struttura di parametrizzazione esistente.

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

Nell'esempio seguente viene illustrato come aggiungere un singolo valore al modello di parametrizzazione predefinito. Vogliamo solo aggiungere un ID cluster interattivo di Azure Databricks esistente per un servizio collegato Databricks al file dei parametri. Si noti che questo file è lo stesso `existingClusterId` del file `Microsoft.DataFactory/factories/linkedServices`precedente, ad eccezione dell'aggiunta di sotto il campo delle proprietà di .

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

Se hai configurato CI/CD per le data factory, potresti superare i limiti del modello di Azure Resource Manager man mano che la tua fabbrica aumenta. Ad esempio, un limite è il numero massimo di risorse in un modello di Resource Manager.For example, one limit is the maximum number of resources in a Resource Manager template. Per supportare factory di grandi dimensioni durante la generazione del modello di Resource Manager completo per una factory, Data Factory ora genera modelli di Resource Manager collegati. Con questa funzionalità, l'intero payload di fabbrica è suddiviso in diversi file in modo da non essere vincolati dai limiti.

Se Git è stato configurato, i modelli collegati vengono generati e salvati insieme ai modelli completi di Resource Manager nel ramo adf_publish in una nuova cartella denominata linkedTemplates:

![Cartella dei modelli di Resource Manager collegati](media/continuous-integration-deployment/linked-resource-manager-templates.png)

I modelli di Gestione risorse collegati sono in genere costituiti da un modello principale e da un set di modelli figlio collegati al master. Il modello padre è denominato ArmTemplate_master.json e i modelli figlio sono denominati con il modello ArmTemplate_0.json, ArmTemplate_1.json e così via. 

Per usare i modelli collegati anziché il modello completo di Resource Manager, aggiornare l'attività CI/CD in modo che punti a ArmTemplate_master.json anziché a ArmTemplateForFactory.json (il modello completo di Resource Manager). Resource Manager richiede anche il caricamento dei modelli collegati in un account di archiviazione in modo che Azure possa accedervi durante la distribuzione. Per altre info, vedere Distribuzione di modelli di [Resource Manager collegati con VSTS.](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/)

Ricordarsi di aggiungere gli script di Data Factory nella pipeline CI/CD prima e dopo l'attività di distribuzione.

Se Git non è configurato, è possibile accedere ai modelli collegati tramite **Esporta modello ARM** nell'elenco Modello **ARM.**

## <a name="hotfix-production-branch"></a>Ramo di produzione dell'hotfix

Se si distribuisce una factory nell'ambiente di produzione e si rende conto che è necessario risolvere immediatamente un bug, ma non è possibile distribuire il ramo di collaborazione corrente, potrebbe essere necessario distribuire un hotfix. Questo approccio è noto come quick-fix engineering o QFE.

1.    In DevOps di Azure passare alla versione distribuita nell'ambiente di produzione. Trovare l'ultimo commit distribuito.

2.    Dal messaggio di commit ottenere l'ID commit del ramo di collaborazione.

3.    Creare un nuovo ramo di hotfix da tale commit.

4.    Passare all'esperienza utente di Azure Data Factory e passare al ramo dell'hotfix.

5.    Utilizzando l'esperienza utente di Azure Data Factory, correggere il bug. Testare le modifiche.

6.    Dopo aver verificato la correzione, selezionare **Esporta modello ARM** per ottenere il modello di hotfix Resource Manager.

7.    Controllare manualmente questa compilazione nel ramo adf_publish.

8.    Se la pipeline di rilascio è stata configurata per l'attivazione automatica in base aadf_publish archiviazioni, verrà avviata automaticamente una nuova versione. In caso contrario, accodare manualmente una versione.

9.    Distribuire la versione dell'aggiornamento rapido per le factory di test e produzione. Questa versione contiene il payload di produzione precedente più la correzione effettuata nel passaggio 5.This release contains the previous production payload plus the fix that you made in step 5.

10.    Aggiungere le modifiche dall'hotfix al ramo di sviluppo in modo che le versioni successive non includeranno lo stesso bug.

## <a name="best-practices-for-cicd"></a>Procedure consigliate per la pipeline CI/CD

Se si usa l'integrazione Git con la data factory e si dispone di una pipeline CI/CD che sposta le modifiche dallo sviluppo al test e quindi alla produzione, è consigliabile eseguire le procedure consigliate seguenti:If you're using Git integration with your data factory and have a CI/CD pipeline that moves your changes from development into test and then to production, we recommend these best practices:

-   **Integrazione Git**. È necessario configurare solo la data factory di sviluppo con l'integrazione Git.You need to configure only your development data factory with Git integration. Le modifiche per il test e la produzione vengono distribuite tramite CI/CD e non richiedono l'integrazione Git.Changes to test and production are deployed via CI/CD and don't need Git integration.

-   **Script di Data Factory in CI/CD**. Prima del passaggio di distribuzione di Resource Manager in CI/CD, è necessario completare alcune attività, ad esempio l'arresto e il riavvio dei trigger e l'esecuzione della pulizia. È consigliabile usare gli script di PowerShell prima e dopo la distribuzione. Per ulteriori informazioni, consultate [Aggiornare i trigger attivi.](#update-active-triggers)

-   **Runtime di integrazione e condivisione**. I runtime di integrazione non cambiano spesso e sono simili in tutte le fasi del CI/CD. Quindi Data Factory prevede che tu abbia lo stesso nome e tipo di runtime di integrazione in tutte le fasi di CI/CD. Se si desidera condividere i runtime di integrazione in tutte le fasi, è consigliabile usare una factory ternaria solo per contenere i runtime di integrazione condivisi. È possibile usare questa factory condivisa in tutti gli ambienti come tipo di runtime di integrazione collegata.

-   **Key Vault**. Quando si usano servizi collegati basati su Archiviazione delle chiavi di Azure, è possibile sfruttarli ulteriormente mantenendo insiemi di credenziali delle chiavi separati per ambienti diversi. È inoltre possibile configurare livelli di autorizzazione separati per ogni insieme di credenziali delle chiavi. Ad esempio, è possibile che non si desideri che i membri del team dispongano delle autorizzazioni per i segreti di produzione. Se si segue questo approccio, è consigliabile mantenere gli stessi nomi di segreto in tutte le fasi. Se si mantengono gli stessi nomi, non è necessario modificare i modelli di Resource Manager in ambienti CI/CD perché l'unica cosa che cambia è il nome dell'insieme di credenziali delle chiavi, che è uno dei parametri del modello di Resource Manager.

## <a name="unsupported-features"></a>Funzionalità non supportate

- Per impostazione specifica, Data Factory non consente la selezione delle attività di raccolta dei commit o la pubblicazione selettiva delle risorse. Le pubblicazione includeranno tutte le modifiche apportate nella data factory.

    - Le entità factory di dati dipendono l'una dall'altra. Ad esempio, i trigger dipendono dalle pipeline e le pipeline dipendono da set di dati e altre pipeline. La pubblicazione selettiva di un sottoinsieme di risorse potrebbe causare comportamenti ed errori imprevisti.
    - In rare occasioni in cui è necessaria la pubblicazione selettiva, prendere in considerazione l'utilizzo di un hotfix. Per ulteriori informazioni, vedere [Ramo di produzione dell'hotfix](#hotfix-production-branch).

-   Non è possibile pubblicare da rami privati.

-   Al momento non è possibile ospitare progetti in Bitbucket.
