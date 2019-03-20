---
title: Integrazione e recapito continui in Azure Data Factory | Microsoft Docs
description: Informazioni su come usare l'integrazione e il recapito continui per spostare le pipeline di Data Factory da un ambiente (sviluppo, test, produzione) a un altro.
services: data-factory
documentationcenter: ''
author: gauravmalhot
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: gamal
manager: craigg
ms.openlocfilehash: 5f5a9ef689fefd5683f7b6f1ebc9b2193ce020e4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995781"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Integrazione e recapito continui (CI/CD) in Azure Data Factory

L'integrazione continua è la procedura che permette di testare ogni modifica apportata alla base di codici automaticamente e quanto prima possibile. Il recapito continuo segue i test effettuati durante l'integrazione continua ed esegue il push delle modifiche in un sistema di gestione temporanea o di produzione.

In Azure Data Factory per integrazione e recapito continui si intende lo spostamento delle pipeline di Data Factory da un ambiente (sviluppo, test, produzione) a un altro. Per eseguire l'integrazione e il recapito continui, è possibile usare l'integrazione dell'interfaccia utente di Data Factory con i modelli di Azure Resource Manager. L'interfaccia utente di Data Factory può generare un modello di Resource Manager quando si selezionano le opzioni di **Modello ARM**. Quando si seleziona **Export ARM template** (Esporta modello di Azure Resource Manager), il portale genera il modello di Resource Manager per la data factory e un file di configurazione che include tutte le stringhe di connessioni e altri parametri. È quindi necessario creare un file di configurazione per ogni ambiente (sviluppo, test, produzione). Il file di modello di Resource Manager principale rimane lo stesso per tutti gli ambienti.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per un'introduzione di nove minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

## <a name="create-a-resource-manager-template-for-each-environment"></a>Creare un modello di Resource Manager per ogni ambiente
Selezionare **Export ARM template** (Esporta modello di Azure Resource Manager) per esportare il modello di Resource Manager per la data factory nell'ambiente di sviluppo.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

Passare quindi alla data factory di test e alla data factory di produzione e selezionare **Import ARM template** (Importa modello di Azure Resource Manager).

![](media/continuous-integration-deployment/continuous-integration-image2.png)

Questa azione consente di passare al portale di Azure, in cui è possibile importare il modello esportato. Selezionare **Creare un modello personalizzato nell'editor** e quindi **Carica file** e selezionare il modello di Resource Manager generato. Specificare le impostazioni e la data factory. L'intera pipeline viene importata nell'ambiente di produzione.

![](media/continuous-integration-deployment/continuous-integration-image3.png)

![](media/continuous-integration-deployment/continuous-integration-image4.png)

Selezionare **Carica file** per selezionare il modello di Resource Manager esportato e specificare tutti i valori di configurazione (ad esempio, i servizi collegati).

![](media/continuous-integration-deployment/continuous-integration-image5.png)

**Stringhe di connessione**. È possibile trovare le informazioni necessarie per creare stringhe di connessione negli articoli relativi ai singoli connettori. Ad esempio, per il database SQL di Azure, vedere [Copiare dati da o nel database SQL di Azure tramite Azure Data Factory](connector-azure-sql-database.md). Per verificare la stringa di connessione corretta, ad esempio per un servizio collegato, è possibile anche aprire la visualizzazione codice per la risorsa nell'interfaccia utente di Data Factory. Nella vista codice, tuttavia, la password o la porzione di chiave della stringa di connessione vengono rimosse. Per aprire la vista codice, selezionare l'icona evidenziata nello screenshot seguente.

![Aprire la vista codice per visualizzare la stringa di connessione](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="continuous-integration-lifecycle"></a>Ciclo di vita di integrazione continua
Ecco l'intero ciclo di vita per l'integrazione e il recapito continui che è possibile usare dopo aver abilitato l'integrazione GIT per Azure Repos nell'interfaccia utente di Data Factory:

1.  Configurare una data factory di sviluppo con Azure Repos in cui tutti gli sviluppatori possono creare risorse di Data Factory, ad esempio pipeline, set di dati e così via.

1.  Gli sviluppatori possono quindi modificare le risorse, ad esempio le pipeline. Quando apportano le modifiche, possono selezionare **Debug** per vedere come viene eseguita la pipeline con le modifiche più recenti.

1.  Quando gli sviluppatori si considerano soddisfatti delle modifiche, possono creare una richiesta pull dal proprio ramo al ramo master (o al ramo di collaborazione) per far esaminare le modifiche ai colleghi.

1.  Dopo che le modifiche sono state inserite nel ramo master, possono eseguire la pubblicazione nella factory di sviluppo selezionando **Pubblica**.

1.  Quando il team è pronto per alzare di livello le modifiche alla factory di test e alla factory di produzione, può esportare il modello di Resource Manager dal ramo master o da qualsiasi altro ramo qualora il ramo master supporti la data factory di sviluppo in tempo reale.

1.  Il modello di Resource Manager esportato può essere distribuito con file dei parametri diversi nella factory di test e nella factory di produzione.

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Automatizzare l'integrazione continua con le versioni di Azure Pipelines

Ecco i passaggi necessari per configurare una versione di Azure Pipelines per poter automatizzare la distribuzione di una data factory in più ambienti.

![Diagramma dell'integrazione continua con Azure Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Requisiti

-   Una sottoscrizione di Azure collegata a Team Foundation Server o ad Azure Repos con l' [*endpoint di servizio di Azure Resource Manager*](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

-   Una data factory configurata con l'integrazione GIT per Azure Repos.

-   Un'istanza di  [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)  contenente i segreti.

### <a name="set-up-an-azure-pipelines-release"></a>Configurare una versione di Azure Pipelines

1.  Passare alla pagina di Azure Repos nello stesso progetto di quello configurato con la data factory.

1.  Fare clic sul menu in alto **Azure Pipelines** &gt; **Versioni** &gt; **Crea definizione di versione**.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Selezionare il modello **processo vuoto**.

1.  Immettere il nome dell'ambiente.

1.  Aggiungere un elemento Git e selezionare lo stesso repository configurato con la data factory. Scegliere `adf_publish` come ramo predefinito con la versione predefinita più recente.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Aggiungere un'attività di distribuzione di Azure Resource Manager:

    a.  Creare la nuova attività, cercare **Distribuzione gruppo di risorse di Azure** e aggiungerla.

    b.  Nell'attività Distribuzione scegliere la sottoscrizione, il gruppo di risorse e la località per la data factory di destinazione e fornire le credenziali, se necessario.

    c.  Selezionare l'azione **Create or update resource group** (Creare o aggiornare un gruppo di risorse).

    d.  Selezionare **...** nel campo **Modello**. Cercare il modello di Resource Manager (*ARMTemplateForFactory.json*) creato dall'azione di pubblicazione nel portale. Cercare questo file nella cartella `<FactoryName>` del ramo `adf_publish`.

    e.  Eseguire la stessa operazione per il file dei parametri. Scegliere il file corretto a seconda che sia stata creata una copia o si stia usando il file predefinito *ARMTemplateParametersForFactory.json*.

    f.  Selezionare **...** accanto al campo **Esegui override dei parametri del modello** e immettere le informazioni per la data factory di destinazione. Per le credenziali provenienti dall'insieme di credenziali delle chiavi, usare lo stesso nome per il segreto nel formato seguente: supponendo che il nome del segreto sia `cred1`, immettere `"$(cred1)"` (tra virgolette).

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    g. Selezionare la modalità di distribuzione **incrementale**.

    > [!WARNING]
    > Se si seleziona la modalità di distribuzione **completa**, le risorse esistenti potrebbero essere eliminate, incluse tutte le risorse nel gruppo di risorse di destinazione che non sono definite nel modello di Resource Manager.

1.  Salvare la pipeline di versione.

1.  Creare una nuova versione da questa pipeline di versione.

    ![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="optional---get-the-secrets-from-azure-key-vault"></a>Facoltativo: ottenere i segreti da Azure Key Vault

Se si hanno segreti da passare in un modello di Azure Resource Manager, è consigliabile usare Azure Key Vault con la versione di Azure Pipelines.

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

### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Concedere le autorizzazioni all'agente di Azure Pipelines
L'attività di Azure Key Vault potrebbe non riuscire il runtime di integrazione fIntegration con un errore di accesso negato. Scaricare i log per la versione e individuare il file `.ps1` con il comando per concedere le autorizzazioni all'agente di Azure Pipelines. È possibile eseguire il comando direttamente oppure è possibile copiare l'ID dell'entità di sicurezza dal file e aggiungere manualmente il criterio di accesso nel portale di Azure. *Get* e *List* sono le autorizzazioni minime necessarie.

### <a name="update-active-triggers"></a>Aggiornare i trigger attivi
La distribuzione può non riuscire se si prova ad aggiornare i trigger attivi. Per aggiornare i trigger attivi, è necessario arrestarli e avviarli manualmente dopo la distribuzione. A questo scopo è possibile aggiungere un'attività Azure PowerShell, come illustrato nell'esempio seguente:

1.  Nella scheda Attività della versione cercare **Azure PowerShell** e aggiungerla.

1.  Scegliere **Azure Resource Manager** come tipo di connessione e selezionare la sottoscrizione.

1.  Scegliere **Script inline** come tipo di script e quindi fornire il codice. L'esempio seguente arresta i trigger:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

È possibile seguire una procedura simile e usare un codice simile (con la funzione `Start-AzDataFactoryV2Trigger`) per riavviare i trigger dopo la distribuzione.

> [!IMPORTANT]
> Negli scenari di integrazione e recapito continui, il tipo di runtime di integrazione in diversi ambienti deve essere lo stesso. Ad esempio, se si dispone di un runtime di integrazione *self-hosted* nell'ambiente di sviluppo, il runtime di integrazione stesso deve essere di tipo *self-hosted* in altri ambienti, come quelli di test e produzione. Analogamente, se si condividono i runtime di integrazione tra più fasi, è necessario configurarli come *self-hosted collegati* in tutti gli ambienti, ad esempio quelli di sviluppo, test e produzione.

## <a name="sample-deployment-template"></a>Esempio di modello di distribuzione

Ecco un modello di distribuzione di esempio che è possibile importare in Azure Pipelines.

```json
{
    "source": 2,
    "id": 1,
    "revision": 51,
    "name": "Data Factory Prod Deployment",
    "description": null,
    "createdBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "createdOn": "2018-03-01T22:57:25.660Z",
    "modifiedBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "modifiedOn": "2018-03-14T17:58:11.643Z",
    "isDeleted": false,
    "path": "\\",
    "variables": {},
    "variableGroups": [],
    "environments": [{
        "id": 1,
        "name": "Prod",
        "rank": 1,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserprod"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 1
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 2
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 3
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param\n(\n    [parameter(Mandatory = $false)] [String] $rootFolder=\"C:\\Users\\sampleuser\\Downloads\\arm_template\",\n    [parameter(Mandatory = $false)] [String] $armTemplate=\"$rootFolder\\arm_template.json\",\n    [parameter(Mandatory = $false)] [String] $armTemplateParameters=\"$rootFolder\\arm_template_parameters.json\",\n    [parameter(Mandatory = $false)] [String] $domain=\"microsoft.onmicrosoft.com\",\n    [parameter(Mandatory = $false)] [String] $TenantId=\"72f988bf-86f1-41af-91ab-2d7cd011db47\",\n    [parame",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": "5.*"
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "secret1",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/1"
    }, {
        "id": 2,
        "name": "Staging",
        "rank": 2,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserstaging"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 4
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 5
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 6
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "16a37943-8b58-4c2f-a3d6-052d6f032a07",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param(\n$x,\n$y,\n$z)\nwrite-host \"----------\"\nwrite-host $x\nwrite-host $y\nwrite-host $z | ConvertTo-SecureString\nwrite-host \"----------\"",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/2"
    }],
    "artifacts": [{
        "sourceId": "19749ef3-2f42-49b5-9696-f28b49faebcb:a6c88f30-5e1f-4de8-b24d-279bb209d85f",
        "type": "Git",
        "alias": "Dev",
        "definitionReference": {
            "branches": {
                "id": "adf_publish",
                "name": "adf_publish"
            },
            "checkoutSubmodules": {
                "id": "",
                "name": ""
            },
            "defaultVersionSpecific": {
                "id": "",
                "name": ""
            },
            "defaultVersionType": {
                "id": "latestFromBranchType",
                "name": "Latest from default branch"
            },
            "definition": {
                "id": "a6c88f30-5e1f-4de8-b24d-279bb209d85f",
                "name": "Dev"
            },
            "fetchDepth": {
                "id": "",
                "name": ""
            },
            "gitLfsSupport": {
                "id": "",
                "name": ""
            },
            "project": {
                "id": "19749ef3-2f42-49b5-9696-f28b49faebcb",
                "name": "Prod"
            }
        },
        "isPrimary": true
    }],
    "triggers": [{
        "schedule": {
            "jobId": "b5ef09b6-8dfd-4b91-8b48-0709e3e67b2d",
            "timeZoneId": "UTC",
            "startHours": 3,
            "startMinutes": 0,
            "daysToRelease": 31
        },
        "triggerType": 2
    }],
    "releaseNameFormat": "Release-$(rev:r)",
    "url": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1",
    "_links": {
        "self": {
            "href": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1"
        },
        "web": {
            "href": "https://sampleuser.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_release?definitionId=1"
        }
    },
    "tags": [],
    "properties": {
        "DefinitionCreationSource": {
            "$type": "System.String",
            "$value": "ReleaseNew"
        }
    }
}
```

## <a name="sample-script-to-stop-and-restart-triggers-and-clean-up"></a>Script di esempio per arrestare e riavviare i trigger ed eseguire la pulizia

Ecco uno script di esempio per arrestare i trigger prima della distribuzione e riavviarli in seguito. Lo script include anche il codice per eliminare le risorse che sono state rimosse. Per installare la versione più recente di Azure PowerShell, vedere [Installare Azure PowerShell in Windows con PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps).

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

Se si è in modalità GIT, è possibile ignorare le proprietà predefinite nel modello di Resource Manager per impostare le proprietà che sono parametri del modello e le proprietà che sono impostati come hardcoded. È possibile sostituire il modello di parametrizzazione predefinita in questi scenari:

* Usare CI/CD automatizzata e si desidera modificare alcune proprietà durante la distribuzione di Resource Manager, ma non vengono parametrizzate le proprietà per impostazione predefinita.
* La factory è talmente elevata che il modello di Resource Manager predefinito non è valido perché contiene più supera il limite massimo consentito di parametri (256).

In queste condizioni, per sostituire il modello di parametrizzazione predefinita, creare un file denominato *Gestione risorse di Azure-modelli-parametri-definition.json* nella cartella radice del repository. Il nome del file deve corrispondere esattamente. Data Factory tenta di leggere il file da qualsiasi ramo attualmente presenti nel portale di Azure Data Factory, non solo dal ramo di collaborazione. È possibile creare o modificare il file da un branch privato, in cui è possibile testare le modifiche utilizzando il **modello di esportazione ARM** nell'interfaccia utente. Quindi, è possibile unire i file nel ramo di collaborazione. Se viene trovato alcun file, viene utilizzato il modello predefinito.


### <a name="syntax-of-a-custom-parameters-file"></a>Sintassi di un file dei parametri personalizzati

Di seguito sono riportate alcune linee guida da utilizzare quando si crea il file dei parametri personalizzati. Il file è costituito da una sezione per ogni tipo di entità: trigger, pipeline, linkedservice, dataset, integrationruntime e così via.
* Immettere il percorso della proprietà sotto il tipo di entità rilevanti.
* Quando si imposta il nome della proprietà su '\*', si indica che si desidera impostare i parametri per tutte le proprietà sottostanti (solo fino al primo livello, in modo ricorsivo). È anche possibile fornire eventuali eccezioni a questa.
* Quando si imposta il valore di una proprietà come stringa, si indica che si vuole parametrizzare la proprietà. Usare il formato `<action>:<name>:<stype>`.
   *  `<action>` può essere uno dei seguenti caratteri:
      * `=` significa che mantenere il valore corrente come valore predefinito per il parametro.
      * `-` significa che non mantiene il valore predefinito per il parametro.
      * `|` è un caso speciale per i segreti da Azure Key Vault per le stringhe di connessione o le chiavi.
   * `<name>` È il nome del parametro. Se è vuota, accetta il nome della proprietà. Se il valore inizia con un `-` character, il nome è passato. Ad esempio, `AzureStorage1_properties_typeProperties_connectionString` potrebbe essere abbreviato in `AzureStorage1_connectionString`.
   * `<stype>` è il tipo del parametro. Se `<stype>` è vuoto, il tipo predefinito è `string`. Valori supportati: `string`, `bool`, `number`, `object`, e `securestring`.
* Quando si specifica una matrice nel file di definizione, si indica che la proprietà corrispondente nel modello è una matrice. Data Factory esegue l'iterazione tra tutti gli oggetti nella matrice usando la definizione specificata nell'oggetto Runtime di integrazione della matrice. Il secondo oggetto, una stringa, diventa il nome della proprietà, che viene usato come nome per il parametro per ogni iterazione.
* Non è possibile disporre di una definizione di specifiche per un'istanza di risorsa. Qualsiasi definizione si applica a tutte le risorse di quel tipo.
* Per impostazione predefinita, vengono parametrizzate tutte le stringhe sicure, ad esempio segreti di Key Vault e stringhe sicure, ad esempio le stringhe di connessione, chiavi e token.
 
## <a name="sample-parameterization-template"></a>Esempio di modello di parametrizzazione

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

### <a name="explanation"></a>Spiegazione:

#### <a name="pipelines"></a>Pipeline
    
* Qualsiasi proprietà nel percorso attività/typeProperties/waitTimeInSeconds è con parametri. Ciò significa che qualsiasi attività in una pipeline che include una proprietà a livello di codice denominata `waitTimeInSeconds` (ad esempio, il `Wait` attività) con i parametri sotto forma di numero, con un nome predefinito. Tuttavia, non avrà un valore predefinito nel modello di Resource Manager. Durante la distribuzione di Resource Manager sarà un input obbligatorio.
* Analogamente, una proprietà denominata `headers` (ad esempio, in un `Web` attività) è con parametri con tipo `object` (JObject). Ha un valore predefinito, ovvero lo stesso valore come la factory dell'origine.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Solo le proprietà e tutte le proprietà, in un percorso `typeProperties` vengono parametrizzate, con i rispettivi valori predefiniti. Ad esempio, al momento della stesura dello schema di oggi, sono disponibili due proprietà sotto **IntegrationRuntimes** proprietà del tipo: `computeProperties` e `ssisProperties`. Entrambi i tipi di proprietà vengono creati con i rispettivi valori predefiniti e i tipi (oggetto).

#### <a name="triggers"></a>Trigger

* In `typeProperties`, due proprietà vengono parametrizzate. La prima consiste `maxConcurrency`, che viene specificato ha un valore predefinito e il tipo di `string`. Include il nome del parametro predefinito di `<entityName>_properties_typeProperties_maxConcurrency`.
* Il `recurrence` proprietà inoltre dispone di parametri. Sotto di essa, tutte le proprietà in tale livello vengono specificate per aggiungere parametri come stringhe, con i valori predefiniti e i nomi dei parametri. Un'eccezione è il `interval` proprietà, che è con parametri come tipo di numero e con il nome del parametro con suffisso `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Analogamente, il `freq` proprietà è una stringa e con i parametri come stringa. Tuttavia, il `freq` proprietà con parametri senza un valore predefinito. Il nome è abbreviato e il suffisso. Ad esempio: `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Servizi collegati è univoco. Poiché i servizi collegati e set di dati possono essere di tipi diversi, è possibile fornire specifici del tipo personalizzazione. Ad esempio, si può obiettare che per tutti i servizi collegati di tipo `AzureDataLakeStore`, un modello specifico sarà applicato e per tutti gli altri utenti (tramite \*) verrà applicato un modello diverso.
* Nell'esempio precedente, il `connectionString` verrà parametrizzata proprietà come un `securestring` valore, non avrà un valore predefinito e avrà un nome di parametro abbreviata che viene aggiunto il suffisso `connectionString`.
* La proprietà `secretAccessKey`, tuttavia, si trova un' `AzureKeyVaultSecret` (ad esempio, un `AmazonS3` servizio collegato). In questo modo, viene parametrizzato automaticamente come un segreto di Azure Key Vault e vengono recuperati dall'insieme di credenziali chiave con cui è configurato nell'ambiente di produzione di origine. È anche possibile parametrizzare stesso insieme di credenziali chiave.

#### <a name="datasets"></a>Set di dati

* Anche se è disponibile per i set di dati specifici del tipo personalizzazione, configurazione può essere specificata senza che sia in modo esplicito un \*-configurazione del livello. Nell'esempio precedente, tutte le proprietà di set di dati in `typeProperties` vengono parametrizzate.

Può modificare il modello di parametrizzazione predefinita, ma si tratta del modello corrente. Ciò potrebbe risultare utile se è sufficiente aggiungere una proprietà aggiuntiva come parametro, ma anche se non si vuole perdere le parametrizzazioni esistenti ed è necessario ricrearli.


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

**Esempio**: Aggiungere un ID cluster interattivi di Databricks (da un servizio collegato Databricks) per il file dei parametri:

```
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

Se per le data factory sono state configurate l'integrazione e la distribuzione continue (CI/CD), ci si può rendere conto che, man mano che la factory si amplia, diventano evidenti i limiti dei modelli di Resource Manager, ad esempio il numero massimo di risorse o payload in ciascun modello di Resource Manager. Per scenari simili a questi, oltre a generare il modello di Resource Manager completo per una factory, Data Factory ora genera anche modelli di Resource Manager collegati. L'intero payload della factory viene pertanto suddiviso in più file e non si incorrerà più nei limiti sopra citati.

Se Git è configurato, i modelli collegati vengono generati e salvati insieme ai modelli di Resource Manager completi, nel ramo `adf_publish`, in una nuova cartella denominata `linkedTemplates`.

![Cartella dei modelli di Resource Manager collegati](media/continuous-integration-deployment/linked-resource-manager-templates.png)

I modelli di Resource Manager collegati hanno in genere un modello master e un set di modelli figlio collegati al master. Il modello padre è denominato `ArmTemplate_master.json` e i modelli di figlio vengono denominati in base allo schema `ArmTemplate_0.json`, `ArmTemplate_1.json` e così via. Per passare dall'uso del modello di Resource Manager completo all'uso dei modelli collegati, aggiornare l'attività CI/CD in modo che faccia riferimento ad `ArmTemplate_master.json` anziché ad `ArmTemplateForFactory.json` (il modello di Resource Manager completo). Resource Manager richiede anche di caricare i modelli collegati in un account di archiviazione, in modo che Azure possa accedervi durante la distribuzione. Per altre informazioni, vedere [Deploying Linked ARM Templates with VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/) (Distribuzione di modelli ARM collegati con VSTS).

Ricordarsi di aggiungere gli script di Data Factory nella pipeline CI/CD prima e dopo l'attività di distribuzione.

Se Git non è configurato, i modelli collegati sono accessibili tramite il gesto **Esporta modello ARM**.

## <a name="best-practices-for-cicd"></a>Procedure consigliate per la pipeline CI/CD

Se si usa l'integrazione di Git con data factory e si dispone di una pipeline CI/CD, che sposta le modifiche apportate dall'ambiente di sviluppo nel test e successivamente nell'ambiente di produzione, è consigliabile osservare le procedure consigliate seguenti:

-   **Integrazione di Git**. È sufficiente configurare la data factory di sviluppo con l'integrazione di Git. Le modifiche a test e produzione vengono distribuite tramite CI/CD e non necessitano dell'integrazione di Git.

-   **Script di Data Factory in CI/CD**. Prima del passaggio di distribuzione di Resource Manager in CI/CD è necessario prestare attenzione a come arrestare i trigger e ai diversi tipi di pulizia di fabbrica. È consigliabile usare [questo script](#sample-script-to-stop-and-restart-triggers-and-clean-up), che si occupa di tutte queste operazioni. Eseguire lo script una sola volta prima e una sola volta a seguito della distribuzione, usando i flag appropriati.

-   **Runtime di integrazione e condivisione**. I runtime di integrazione costituiscono uno dei componenti infrastrutturali in data factory, che subiscono modifiche con minore frequenza e sono simili in tutte le fasi di CI/CD. Di conseguenza, Data Factory prevede che l'utente abbia lo stesso nome e lo stesso tipo di runtime di integrazione in tutte le fasi di CI/CD. Per condividere i runtime di integrazione durante tutte le fasi, ad esempio il runtime di integrazione Self-Hosted, è necessario eseguire l'hosting del suddetti runtime di integrazione in una factory ternaria, al fine di contenere i runtime di integrazione condivisi. Sarà quindi possibile usarli in Sviluppo/test/produzione come un tipo di runtime di integrazione collegato.

-   **Insieme di credenziali delle chiavi**. Quando si usano i servizi collegati basati su Azure Key Vault, è possibile sfruttare ulteriormente i suoi vantaggi mantenendo potenzialmente separate l'insieme di credenziali delle chiavi per Sviluppo/test/produzione. È anche possibile configurare i livelli di autorizzazione separati per ognuno di essi. È possibile che non si voglia che i membri del team siano autorizzati ad accedere ai segreti di produzione. È anche consigliabile mantenere segreti gli stessi nomi per tutte le fasi. Se si mantengono gli stessi nomi, non sarà necessario modificare i modelli di Resource Manager in CI/CD poiché l'unico elemento che deve essere modificato è il nome dell'insieme di credenziali delle chiavi, che è uno dei parametri del modello di Resource Manager.

## <a name="unsupported-features"></a>Funzionalità non supportate

-   Non è possibile pubblicare risorse individuali, poiché le entità di data factory dipendono l'una dall'altra. Ad esempio, i trigger dipendono dalla pipeline, le pipeline dipendono dal set di dati e da altre pipeline e così via. Il rilevamento delle dipendenze in via di modifica è un'operazione complicata. Se fosse possibile selezionare le risorse da pubblicare manualmente, sarebbe possibile scegliere solo un subset dell'intera serie di modifiche, il che porterebbe a comportamenti inaspettati dopo la pubblicazione.

-   Non è possibile pubblicare da rami privati.

-   Non è possibile ospitare i progetti in Bitbucket.
