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
ms.date: 08/14/2019
ms.openlocfilehash: 7c5c1e91e97087bf28b03629659e5194f67c22b3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680036"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Integrazione e recapito continui (CI/CD) in Azure Data Factory

## <a name="overview"></a>Panoramica

L'integrazione continua è la procedura che permette di testare ogni modifica apportata alla base di codici automaticamente e quanto prima possibile. Il recapito continuo segue i test che si verificano durante l'integrazione continua e inserisce le modifiche in un sistema di gestione temporanea o di produzione.

In Azure Data Factory, il recapito & di integrazione continua significa lo sviluppo di pipeline di Data Factory da un ambiente (sviluppo, test, produzione) a un altro. Per eseguire l'integrazione continua & il recapito, è possibile usare l'integrazione di Data Factory UX con i modelli di Azure Resource Manager. Il Data Factory UX può generare un modello di Gestione risorse dall'elenco a discesa **modello ARM** . Quando si seleziona **Export ARM template** (Esporta modello di Azure Resource Manager), il portale genera il modello di Resource Manager per la data factory e un file di configurazione che include tutte le stringhe di connessioni e altri parametri. Quindi, è necessario creare un file di configurazione per ogni ambiente (sviluppo, test, produzione). Il file di modello di Resource Manager principale rimane lo stesso per tutti gli ambienti.

Per un'introduzione di nove minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="continuous-integration-lifecycle"></a>Ciclo di vita di integrazione continua

Di seguito è riportata una panoramica di esempio dell'integrazione continua e del ciclo di vita di recapito in una data factory di Azure configurata con Azure Repos git. Per altre informazioni su come configurare un repository git, vedere [controllo del codice sorgente in Azure Data Factory](source-control.md).

1.  Un data factory di sviluppo viene creato e configurato con Azure Repos git in cui tutti gli sviluppatori hanno l'autorizzazione per creare Data Factory risorse quali pipeline e set di impostazioni.

1.  Quando gli sviluppatori apportano modifiche al Branch delle funzionalità, eseguono il debug delle esecuzioni di pipeline con le modifiche più recenti. Per ulteriori informazioni su come eseguire il debug di un'esecuzione di pipeline, vedere [sviluppo iterativo e debug con Azure Data Factory](iterative-development-debugging.md).

1.  Una volta soddisfatte le modifiche, gli sviluppatori creano una richiesta pull dal ramo della funzionalità al ramo master o di collaborazione per approvare le modifiche apportate dai peer.

1.  Dopo che la richiesta pull è stata approvata e le modifiche sono state unite nel ramo master, è possibile pubblicarle nella Factory di sviluppo.

1.  Quando il team è pronto per distribuire le modifiche alla Factory di test e quindi alla Factory di produzione, esporta il modello di Gestione risorse dal ramo master.

1.  Il modello di Gestione risorse esportato viene distribuito con file di parametri diversi nella Factory di test e nella Factory di produzione.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Creare un modello di Resource Manager per ogni ambiente

Nell'elenco a discesa **modello ARM** selezionare **Esporta modello ARM** per esportare il modello di gestione risorse per il data factory nell'ambiente di sviluppo.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

Nelle data factory di test e di produzione selezionare **Importa modello ARM**. Questa azione consente di passare al portale di Azure, in cui è possibile importare il modello esportato. Selezionare **Compila un modello personalizzato nell'editor** per aprire l'editor del modello Gestione risorse.

![](media/continuous-integration-deployment/continuous-integration-image3.png) 

Fare clic su **Carica file** e selezionare il modello di gestione risorse generato.

![](media/continuous-integration-deployment/continuous-integration-image4.png)

Nel riquadro Impostazioni, immettere i valori di configurazione, ad esempio le credenziali del servizio collegato. Al termine, fare clic su **Acquista** per distribuire il modello di gestione risorse.

![](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Stringhe di connessione

Informazioni su come configurare le stringhe di connessione sono disponibili nell'articolo di ogni connettore. Ad esempio, per il database SQL di Azure, vedere [Copiare dati da o nel database SQL di Azure tramite Azure Data Factory](connector-azure-sql-database.md). Per verificare una stringa di connessione, è possibile aprire la visualizzazione codice per la risorsa nel Data Factory UX. Nella visualizzazione codice viene rimossa la parte della password o della chiave dell'account della stringa di connessione. Per aprire la vista codice, selezionare l'icona evidenziata nello screenshot seguente.

![Aprire la vista codice per visualizzare la stringa di connessione](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Automatizzare l'integrazione continua con le versioni di Azure Pipelines

Di seguito è riportata una guida per la configurazione di una versione Azure Pipelines, che consente di automatizzare la distribuzione di un data factory in più ambienti.

![Diagramma dell'integrazione continua con Azure Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Requisiti

-   Una sottoscrizione di Azure collegata a Team Foundation Server o Azure Repos usando l' [endpoint del servizio Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

-   Data Factory configurato con Azure Repos integrazione git.

-    [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) contenente i segreti per ogni ambiente.

### <a name="set-up-an-azure-pipelines-release"></a>Configurare una versione di Azure Pipelines

1.  Nell' [esperienza utente di Azure DevOps](https://dev.azure.com/)aprire il progetto configurato con il data factory.

1.  Sul lato sinistro della pagina fare clic su **pipeline** , quindi selezionare **versioni**.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Selezionare **nuova pipeline** o se sono presenti pipeline esistenti, **nuove**, quindi **nuova pipeline di rilascio**.

1.  Selezionare il modello di **processo vuoto** .

    ![](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  Nel campo **nome fase** immettere il nome dell'ambiente.

1.  Selezionare **Aggiungi un artefatto**e selezionare lo stesso repository configurato con il data factory. Scegliere `adf_publish` come ramo predefinito con la versione predefinita più recente.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Aggiungere un'attività di distribuzione di Azure Resource Manager:

    a.  Nella visualizzazione stage fare clic sul collegamento **Visualizza attività di fase** .

    ![](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Creare una nuova attività. Cercare la **distribuzione del gruppo di risorse di Azure**e fare clic su **Aggiungi**.

    c.  Nell'attività Distribuzione scegliere la sottoscrizione, il gruppo di risorse e la località per la data factory di destinazione e fornire le credenziali, se necessario.

    d.  Nell'elenco a discesa azione selezionare **Crea o Aggiorna gruppo di risorse**.

    e.  Selezionare **...** nel campo **Modello**. Cercare il modello di Azure Resource Manager creare tramite il passaggio **Importa modello ARM** in [creare un modello di Resource Manager per ogni ambiente](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment). Cercare questo file nella cartella `<FactoryName>` del ramo `adf_publish`.

    f.  Selezionare **...** nel **campo parametri modello.** per scegliere il file dei parametri. Scegliere il file corretto a seconda che sia stata creata una copia o si stia usando il file predefinito *ARMTemplateParametersForFactory.json*.

    g.  Selezionare **...** accanto al campo **Esegui override dei parametri del modello** e immettere le informazioni per la data factory di destinazione. Per le credenziali provenienti da Key Vault, immettere il nome del segreto tra virgolette doppie. Se, ad esempio, il nome del segreto è `cred1`, immettere `"$(cred1)"`per il relativo valore.

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    h. Selezionare la modalità di distribuzione **incrementale**.

    > [!WARNING]
    > Se si seleziona la modalità di distribuzione **completa** , è possibile che vengano eliminate le risorse esistenti, incluse tutte le risorse nel gruppo di risorse di destinazione che non sono definite nel modello di gestione risorse.

1.  Salvare la pipeline di versione.

1. Per attivare una versione, fare clic su **Crea versione**

![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="get-secrets-from-azure-key-vault"></a>Ottenere i segreti da Azure Key Vault

Se si dispone di segreti per passare un modello di Azure Resource Manager, è consigliabile usare Azure Key Vault con la versione Azure Pipelines.

Esistono due modi per gestire i segreti:

1.  Aggiungere i segreti al file dei parametri. Per altre informazioni, vedere [Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione](../azure-resource-manager/resource-manager-keyvault-parameter.md).

    -   Creare una copia del file dei parametri che viene caricato nel ramo di pubblicazione e impostare i valori dei parametri che si vuole ottenere da Key Vault con il formato seguente:

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

    -   Quando si usa questo metodo, viene eseguito il pull automatico del segreto dall'insieme di credenziali delle chiavi.

    -   Il file dei parametri deve essere anche nel ramo di pubblicazione.

1.  Aggiungere un'[attività di Azure Key Vault](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) prima della distribuzione di Azure Resource Manager descritta nella sezione precedente:

    -   Selezionare la scheda **Attività**, creare una nuova attività, cercare **Azure Key Vault** e aggiungerla.

    -   Nell'attività Key Vault scegliere la sottoscrizione in cui è stato creato l'insieme di credenziali delle chiavi, fornire le credenziali se necessario e quindi scegliere l'insieme di credenziali delle chiavi.

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Concedere le autorizzazioni all'agente di Azure Pipelines

L'attività Azure Key Vault potrebbe non riuscire con un errore di accesso negato se non sono presenti le autorizzazioni appropriate. Scaricare i log per la versione e individuare il file `.ps1` con il comando per concedere le autorizzazioni all'agente di Azure Pipelines. È possibile eseguire il comando direttamente oppure è possibile copiare l'ID dell'entità di sicurezza dal file e aggiungere manualmente il criterio di accesso nel portale di Azure. **Get** ed **List** sono le autorizzazioni minime necessarie.

### <a name="update-active-triggers"></a>Aggiornare i trigger attivi

La distribuzione può non riuscire se si prova ad aggiornare i trigger attivi. Per aggiornare i trigger attivi, è necessario arrestarli e avviarli manualmente dopo la distribuzione. È possibile eseguire questa operazione tramite un'attività di Azure PowerShell.

1.  Nella scheda attività della versione aggiungere un'attività di **Azure PowerShell** .

1.  Scegliere **Azure Resource Manager** come tipo di connessione e selezionare la sottoscrizione.

1.  Scegliere **Script inline** come tipo di script e quindi fornire il codice. L'esempio seguente arresta i trigger:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

È possibile seguire una procedura simile (con la funzione `Start-AzDataFactoryV2Trigger`) per riavviare i trigger dopo la distribuzione.

> [!IMPORTANT]
> Negli scenari di integrazione e recapito continui, il tipo di runtime di integrazione in diversi ambienti deve essere lo stesso. Ad esempio, se si dispone di un runtime di integrazione *self-hosted* nell'ambiente di sviluppo, il runtime di integrazione stesso deve essere di tipo *self-hosted* in altri ambienti, come quelli di test e produzione. Analogamente, se si condividono i runtime di integrazione tra più fasi, è necessario configurarli come *self-hosted collegati* in tutti gli ambienti, ad esempio quelli di sviluppo, test e produzione.

#### <a name="sample-prepostdeployment-script"></a>Script di pre/post-distribuzione di esempio

Di seguito è riportato uno script di esempio per arrestare i trigger prima della distribuzione e riavviare i trigger in seguito. Lo script include anche il codice per eliminare le risorse che sono state rimosse. Per installare la versione più recente di Azure PowerShell, vedere [Installare Azure PowerShell in Windows con PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps).

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder,
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
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
    $pipelinesADF = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
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

    #Start Active triggers - After cleanup efforts
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

In queste condizioni, per eseguire l'override del modello di parametrizzazione predefinito, creare un file denominato *ARM-Template-Parameters-Definition. JSON* nella cartella radice del repository. Il nome del file deve corrispondere esattamente. Data Factory prova a leggere questo file da qualsiasi ramo attualmente attivo nel portale di Azure Data Factory, non solo dal ramo collaborazione. È possibile creare o modificare il file da un ramo privato, in cui è possibile testare le modifiche usando il **modello Export ARM** nell'interfaccia utente. Quindi, è possibile eseguire il merge del file nel ramo collaborazione. Se non viene trovato alcun file, viene usato il modello predefinito.


### <a name="syntax-of-a-custom-parameters-file"></a>Sintassi di un file dei parametri personalizzati

Di seguito sono riportate alcune linee guida da usare quando si crea il file di parametri personalizzati. Il file è costituito da una sezione per ogni tipo di entità: trigger, pipeline, servizio collegato, set di dati, Runtime di integrazione e così via.
* Immettere il percorso della proprietà nel tipo di entità pertinente.
* Quando si imposta un nome di proprietà su'\*'', si indica che si desidera parametrizzare tutte le proprietà al suo interno (solo per il primo livello, non in modo ricorsivo). È anche possibile fornire eventuali eccezioni a questo.
* Quando si imposta il valore di una proprietà come stringa, si indica che si vuole parametrizzare la proprietà. Usare il formato `<action>:<name>:<stype>`.
   *  `<action>` può essere uno dei seguenti caratteri:
      * `=` significa che il valore corrente viene mantenuto come valore predefinito per il parametro.
      * `-` significa che non si mantiene il valore predefinito per il parametro.
      * `|` è un caso speciale per i segreti da Azure Key Vault per le stringhe di connessione o le chiavi.
   * `<name>` è il nome del parametro. Se è vuota, prende il nome della proprietà. Se il valore inizia con un carattere `-`, il nome viene abbreviato. Ad esempio, `AzureStorage1_properties_typeProperties_connectionString` verrebbe abbreviato in `AzureStorage1_connectionString`.
   * `<stype>` è il tipo di parametro. Se `<stype>` è vuoto, il tipo predefinito è `string`. Valori supportati: `string`, `bool`, `number`, `object` e `securestring`.
* Quando si specifica una matrice nel file di definizione, si indica che la proprietà corrispondente nel modello è una matrice. Data Factory esegue l'iterazione di tutti gli oggetti nella matrice utilizzando la definizione specificata nell'oggetto Integration Runtime della matrice. Il secondo oggetto, una stringa, diventa il nome della proprietà, che viene usato come nome per il parametro per ogni iterazione.
* Non è possibile avere una definizione specifica per un'istanza di risorsa. Qualsiasi definizione viene applicata a tutte le risorse di quel tipo.
* Per impostazione predefinita, vengono parametrizzate tutte le stringhe protette, ad esempio Key Vault segreti e stringhe protette, ad esempio stringhe di connessione, chiavi e token.
 
### <a name="sample-parameterization-template"></a>Modello di parametrizzazione di esempio

Di seguito è riportato un esempio di come può essere un modello di parametrizzazione:

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
Di seguito è riportata una spiegazione del modo in cui viene costruito il modello precedente, suddiviso per tipo di risorsa.

#### <a name="pipelines"></a>Pipeline
    
* Qualsiasi proprietà nel percorso Activities/typeProperties/waitTimeInSeconds è parametrizzata. Qualsiasi attività in una pipeline con una proprietà a livello di codice denominata `waitTimeInSeconds` (ad esempio, l'attività `Wait`) viene parametrizzata come numero, con un nome predefinito. Ma non avrà un valore predefinito nel modello di Gestione risorse. Sarà un input obbligatorio durante la distribuzione del Gestione risorse.
* Analogamente, una proprietà denominata `headers` (ad esempio, in un'attività `Web`) viene parametrizzata con il tipo `object` (JObject). Ha un valore predefinito, che corrisponde al valore della factory di origine.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Tutte le proprietà sotto il percorso `typeProperties` sono parametrizzate con i rispettivi valori predefiniti. Sono ad esempio disponibili due proprietà nelle proprietà del tipo **IntegrationRuntimes** : `computeProperties` e `ssisProperties`. Entrambi i tipi di proprietà vengono creati con i rispettivi valori e tipi predefiniti (oggetto).

#### <a name="triggers"></a>Trigger

* In `typeProperties`, due proprietà sono parametrizzate. Il primo è `maxConcurrency`, che è specificato per avere un valore predefinito ed è di tipo`string`. Il nome del parametro predefinito è `<entityName>_properties_typeProperties_maxConcurrency`.
* Anche la proprietà `recurrence` è parametrizzata. Al suo interno, tutte le proprietà a tale livello vengono specificate per essere parametrizzate come stringhe, con i valori predefiniti e i nomi di parametro. Un'eccezione è rappresentata dalla proprietà `interval`, che è parametrizzata come tipo di numero e con il nome del parametro con suffisso `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Analogamente, la proprietà `freq` è una stringa e viene parametrizzata come stringa. Tuttavia, la proprietà `freq` è parametrizzata senza un valore predefinito. Il nome viene abbreviato e viene suffissato. Ad esempio, `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* I servizi collegati sono univoci. Poiché i servizi collegati e i set di impostazioni hanno un'ampia gamma di tipi, è possibile specificare una personalizzazione specifica del tipo. In questo esempio, tutti i servizi collegati di tipo `AzureDataLakeStore`, verrà applicato un modello specifico e per tutti gli altri (tramite \*) verrà applicato un modello diverso.
* La proprietà `connectionString` verrà parametrizzata come valore `securestring`, non avrà un valore predefinito e avrà un nome di parametro abbreviato con il suffisso `connectionString`.
* La proprietà `secretAccessKey` è un `AzureKeyVaultSecret` (ad esempio, in un servizio collegato `AmazonS3`). Viene automaticamente parametrizzato come segreto Azure Key Vault e recuperato dall'insieme di credenziali delle chiavi configurato. È anche possibile parametrizzare l'insieme di credenziali delle chiavi.

#### <a name="datasets"></a>Set di dati

* Sebbene la personalizzazione specifica del tipo sia disponibile per i set di data, è possibile specificare la configurazione senza una configurazione a livello di \*. Nell'esempio precedente, vengono parametrizzate tutte le proprietà del set di dati in `typeProperties`.

### <a name="default-parameterization-template"></a>Modello di parametrizzazione predefinito

Di seguito è riportato il modello di parametrizzazione predefinito corrente. Se è necessario solo aggiungere uno o pochi parametri, la modifica di questa operazione può essere utile in quanto non si perderà la struttura di parametrizzazione esistente.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
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

Di seguito è riportato un esempio di come aggiungere un singolo valore al modello di parametrizzazione predefinito. Si vuole solo aggiungere un ID cluster interattivo databricks esistente per un servizio collegato databricks al file dei parametri. Si noti che il file seguente è identico a quello del file precedente, ad eccezione di `existingClusterId` incluso nel campo proprietà di `Microsoft.DataFactory/factories/linkedServices`.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
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

Se sono state configurate l'integrazione e la distribuzione continue (CI/CD) per le Data Factory, è possibile che si verifichino i limiti del modello di Azure Resource Manager Man mano che le dimensioni della Factory aumentano. Un esempio di limite è il numero massimo di risorse in un modello di Gestione risorse. Per gestire le fabbriche di grandi dimensioni, insieme alla generazione del modello di Gestione risorse completo per una factory, Data Factory ora genera modelli di Gestione risorse collegati. Con questa funzionalità, l'intero payload della factory viene suddiviso in diversi file, in modo che non si incorrano nei limiti.

Se è stato configurato git, i modelli collegati vengono generati e salvati insieme ai modelli di Gestione risorse completi nel ramo `adf_publish` in una nuova cartella denominata `linkedTemplates`.

![Cartella dei modelli di Resource Manager collegati](media/continuous-integration-deployment/linked-resource-manager-templates.png)

I modelli di Resource Manager collegati hanno in genere un modello master e un set di modelli figlio collegati al master. Il modello padre è denominato `ArmTemplate_master.json` e i modelli di figlio vengono denominati in base allo schema `ArmTemplate_0.json`, `ArmTemplate_1.json` e così via. Per usare i modelli collegati anziché il modello di Gestione risorse completo, aggiornare l'attività CI/CD in modo che punti a `ArmTemplate_master.json` invece di `ArmTemplateForFactory.json` (modello di Gestione risorse completo). Resource Manager richiede anche di caricare i modelli collegati in un account di archiviazione, in modo che Azure possa accedervi durante la distribuzione. Per altre informazioni, vedere [Deploying Linked ARM Templates with VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/) (Distribuzione di modelli ARM collegati con VSTS).

Ricordarsi di aggiungere gli script di Data Factory nella pipeline CI/CD prima e dopo l'attività di distribuzione.

Se Git non è configurato, i modelli collegati sono accessibili tramite il gesto **Esporta modello ARM**.

## <a name="hot-fix-production-branch"></a>Ramo di produzione con correzione rapida

Se si distribuisce una factory in produzione e si comprende un bug che deve essere corretto immediatamente, ma non è possibile distribuire il ramo di collaborazione corrente, potrebbe essere necessario distribuire una correzione a caldo. Questo approccio è noto come progettazione rapida o QFE. 

1.  In Azure DevOps passare alla versione che è stata distribuita in produzione e individuare l'ultimo commit distribuito.

2.  Dal messaggio di commit, ottenere l'ID commit del ramo collaborazione.

3.  Creare un nuovo ramo di correzione a caldo dal commit.

4.  Passare al Azure Data Factory UX e passare a questo ramo.

5.  Con il Azure Data Factory UX, correggere il bug. Testare le modifiche.

6.  Una volta verificata la correzione, fare clic su **Esporta modello ARM** per ottenere il modello di gestione risorse per la correzione a caldo.

7.  Archiviare manualmente questa compilazione nel ramo adf_publish.

8.  Se la pipeline di rilascio è stata configurata per l'attivazione automatica in base alle archiviazioni di adf_publish, verrà avviata automaticamente una nuova versione. In caso contrario, accodare manualmente una versione.

9.  Distribuire la versione con correzione a caldo nelle factory di test e di produzione. Questa versione contiene il payload di produzione precedente e la correzione apportata nel passaggio 5.

10. Aggiungere le modifiche dalla correzione a caldo al ramo di sviluppo in modo che le versioni successive non vengano eseguite nello stesso bug.

## <a name="best-practices-for-cicd"></a>Procedure consigliate per la pipeline CI/CD

Se si usa l'integrazione di Git con data factory e si dispone di una pipeline CI/CD, che sposta le modifiche apportate dall'ambiente di sviluppo nel test e successivamente nell'ambiente di produzione, è consigliabile osservare le procedure consigliate seguenti:

-   **Integrazione di Git**. È necessario solo configurare il data factory di sviluppo con l'integrazione git. Le modifiche apportate ai test e alla produzione vengono distribuite tramite CI/CD e non richiedono l'integrazione di git.

-   **Script di Data Factory in CI/CD**. Prima del passaggio di distribuzione Gestione risorse in CI/CD, è necessario eseguire determinate attività, ad esempio l'arresto/avvio dei trigger e la pulizia. Si consiglia di usare gli script di PowerShell prima e dopo la distribuzione. Per altre informazioni, vedere [Update Active Triggers](#update-active-triggers). 

-   **Runtime di integrazione e condivisione**. I runtime di integrazione non cambiano spesso e sono simili in tutte le fasi dell'integrazione continua/recapito continuo. Di conseguenza, Data Factory prevede che l'utente abbia lo stesso nome e lo stesso tipo di runtime di integrazione in tutte le fasi di CI/CD. Se si intende condividere runtime di integrazione in tutte le fasi, provare a usare una factory ternaria solo per contenere i runtime di integrazione condivisi. È possibile usare questa factory condivisa in tutti gli ambienti come tipo di runtime di integrazione collegata.

-   **Insieme di credenziali delle chiavi**. Quando si usano servizi collegati basati su Azure Key Vault, è possibile sfruttarne ulteriormente i vantaggi mantenendo gli insiemi di credenziali delle chiavi distinti per ambienti diversi. È anche possibile configurare i livelli di autorizzazione separati per ognuno di essi. Ad esempio, potrebbe non essere necessario che i membri del team dispongano delle autorizzazioni per i segreti di produzione. Se si segue questo approccio, è consigliabile mantenerne gli stessi nomi in tutte le fasi. Se si mantengono gli stessi nomi, non è necessario modificare i modelli di Gestione risorse negli ambienti CI/CD, perché l'unica cosa che cambia è il nome dell'insieme di credenziali delle chiavi, che è uno dei parametri del modello di Gestione risorse.

## <a name="unsupported-features"></a>Funzionalità non supportate

- Per impostazione predefinita, ADF _non consente i_ commit con cherry-picking o la pubblicazione selettiva delle risorse. Le pubblicazioni includeranno **tutte** le modifiche apportate all'Data Factory

    - Le entità di data factory dipendono l'una dall'altra, ad esempio, i trigger dipendono da pipeline, le pipeline dipendono da set di dati e altre pipeline e così via. La pubblicazione selettiva di un subset di risorse _può_ causare comportamenti imprevisti ed errori
    - In rare occasioni in cui è richiesta la pubblicazione selettiva, è possibile prendere in considerazione una correzione a caldo. Per altre informazioni, vedere [Hot-Fix Production Branch](#hot-fix-production-branch)

-   Non è possibile pubblicare da rami privati

-   Attualmente non è possibile ospitare progetti in bitbucket
