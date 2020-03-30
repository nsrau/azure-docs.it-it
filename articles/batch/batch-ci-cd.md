---
title: Usare le pipeline di Azure per compilare & distribuire soluzioni HPC - Azure BatchUse Azure Pipelines to build to build to deploy HPC solutions - Azure Batch
description: Informazioni su come distribuire una pipeline di compilazione/rilascio per un'applicazione HPC in esecuzione in Azure Batch.Learn how to deploy a build/release pipeline for an HPC application running on Azure Batch.
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.service: batch
ms.openlocfilehash: 50cb711dfd16c2a8718d13ba9255ace1e7e3e26d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533131"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Usare le pipeline di Azure per compilare e distribuire soluzioni HPCUse Azure Pipelines to build and deploy HPC solutions

I servizi DevOps di Azure offrono una gamma di strumenti usati dai team di sviluppo durante la creazione di un'applicazione personalizzata. Gli strumenti forniti da DevOps di Azure possono tradursi in compilazione e test automatizzati di soluzioni di elaborazione ad alte prestazioni. Questo articolo illustra come configurare un'integrazione continua (CI) e una distribuzione continua (CD) usando le pipeline di Azure per una soluzione di calcolo ad alte prestazioni distribuita in Azure Batch.This article demonstrates how to set up a continuous integration (CI) and continuous deployment (CD) using Azure Pipelines for a high performance compute solution deployed on Azure Batch.

Le pipeline di Azure offrono una gamma di processi CI/CD moderni per la creazione, la distribuzione, il test e il monitoraggio del software. Questi processi accelerano la distribuzione del software, consentendo di concentrarsi sul codice anziché supportare l'infrastruttura e le operazioni.

## <a name="create-an-azure-pipeline"></a>Creare una pipeline di AzureCreate an Azure Pipeline

In questo esempio verrà creata una pipeline di compilazione e rilascio per distribuire un'infrastruttura di Azure Batch e rilasciare un pacchetto dell'applicazione. Supponendo che il codice venga sviluppato localmente, si tratta del flusso di distribuzione generale:Assuming that the code is developed locally, this is the general deployment flow:

![Diagramma che mostra il flusso di distribuzione nella pipeline](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Configurazione

Per seguire i passaggi descritti in questo articolo, sono necessari un'organizzazione DevOps di Azure e un progetto team.

* [Creare un'organizzazione DevOps di AzureCreate an Azure DevOps Organization](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Creare un progetto in Azure DevOpsCreate a project in Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>Controllo del codice sorgente per l'ambiente

Il controllo del codice sorgente consente ai team di tenere traccia delle modifiche apportate alla codebase e di esaminare le versioni precedenti del codice.

In genere, il controllo del codice sorgente è pensato di mano nella mano con il codice software. Che ne dici dell'infrastruttura sottostante? Questo ci porta all'infrastruttura come codice, in cui useremo modelli di Azure Resource Manager o altre alternative open source per definire in modo dichiarativo l'infrastruttura sottostante.

Questo esempio si basa fortemente su una serie di modelli di Resource Manager (documenti JSON) e file binari esistenti. È possibile copiare questi esempi nel repository e eseguirne il push in DevOps di Azure.You can copy these examples into your repository and push them to Azure DevOps.

La struttura della codebase utilizzata in questo esempio è simile al seguente.

* Una cartella di modelli di arm, contenente un numero di modelli di Azure Resource Manager.An **arm-templates** folder, containing a number of Azure Resource Manager templates. I modelli sono illustrati in questo articolo.
* Una cartella **dell'applicazione client,** ovvero una copia dell'esempio [Azure Batch .NET File Processing with ffmpeg.](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) Questo non è necessario per questo articolo.
* Una cartella **hpc-application,** che è la versione windows a 64 bit di [ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).
* Una cartella **pipeline.** Questo contiene un file YAML che descrive il nostro processo di compilazione. Questo è discusso nell'articolo.

In questa sezione si presuppone che l'utente abbia familiarità con il controllo della versione e la progettazione di modelli di Resource Manager.This section assumes you are familiar with version control and designing Resource Manager templates. Se non si ha familiarità con questi concetti, vedere le pagine seguenti per ulteriori informazioni.

* [Che cos'è il controllo del codice sorgente?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Comprendere la struttura e la sintassi dei modelli di Azure Resource ManagerUnderstand the structure and syntax of Azure Resource Manager Templates](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Modelli di Gestione risorse di Azure

Questo esempio sfrutta più modelli di Resource Manager per distribuire la soluzione. A tale scopo, utilizziamo una serie di modelli di funzionalità (simile a unità o moduli) che implementano una parte specifica di funzionalità. Utilizziamo anche un modello di soluzione end-to-end responsabile dell'unione delle funzionalità sottostanti. Questo approccio offre un paio di vantaggi:There are a couple of benefits to this approach:

* I modelli di funzionalità sottostanti possono essere sottoposti a unit test singolarmente.
* I modelli di funzionalità sottostanti possono essere definiti come standard all'interno di un'organizzazione ed essere riutilizzati in più soluzioni.

Per questo esempio, è disponibile un modello di soluzione end-to-end (deployment.json) che distribuisce tre modelli. I modelli sottostanti sono modelli di funzionalità, responsabili della distribuzione di un aspetto specifico della soluzione.

![Esempio di struttura di modelli collegati con i modelli di Azure Resource ManagerExample of Linked Template Structure using Azure Resource Manager templates](media/batch-ci-cd/ARMTemplateHierarchy.png)

Il primo modello che verrà esaminato è per un account di archiviazione di Azure.The first template that we will look at is for an Azure Storage Account. Our solution requires a storage account to deploy the application to our Batch Account. È utile conoscere la guida di riferimento ai modelli di Resource Manager per i [tipi di risorse Microsoft.Storage](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) quando si creano modelli di Resource Manager per gli account di archiviazione.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

Successivamente, esamineremo il modello Account batch di Azure.Next, we will look at the Azure Batch Account template. L'account Batch di Azure funge da piattaforma per l'esecuzione di numerose applicazioni tra pool (raggruppamenti di macchine). Vale la pena di essere a conoscenza della guida di riferimento ai modelli di Resource Manager per i [tipi di risorse Microsoft.Batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) quando si creano modelli di Resource Manager per gli account batch.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

Il modello successivo mostra un esempio di creazione di un pool batch di Azure (i computer back-end per elaborare le applicazioni). È utile conoscere la guida di riferimento ai modelli di Resource Manager per i [tipi di risorse Microsoft.Batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) durante la creazione di modelli di Resource Manager per i pool di account batch.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

Infine, abbiamo un modello che agisce in modo simile a un agente di orchestrazione. Questo modello è responsabile della distribuzione dei modelli di funzionalità.

È anche possibile trovare altre informazioni sulla creazione di modelli di [Azure Resource Manager collegati](../azure-resource-manager/templates/template-tutorial-create-linked-templates.md) in un articolo separato.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

#### <a name="the-hpc-solution"></a>La soluzione HPC

L'infrastruttura e il software possono essere definiti come codice e collocati nello stesso repository.

Per questa soluzione, il ffmpeg viene utilizzato come pacchetto dell'applicazione. Il pacchetto ffmpeg può essere scaricato [qui](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).

![Esempio di struttura del repository GitExample Git Repository Structure](media/batch-ci-cd/git-repository.jpg)

Esistono quattro sezioni principali per questo repository:There are four main sections to this repository:

* La cartella **dei modelli** di arm che memorizza la nostra infrastruttura come codice
* La cartella **hpc-application** che contiene i file binari per ffmpeg
* Cartella **pipeline che** contiene la definizione per la pipeline di compilazione.
* **Facoltativo:** cartella **dell'applicazione client** in cui viene archiviato il codice per l'applicazione .NET. Non lo useremo nell'esempio, ma nel tuo progetto, potresti voler eseguire esecuzioni dell'applicazione batch HPC tramite un'applicazione client.

> [!NOTE]
> Questo è solo un esempio di una struttura a una codebase. Questo approccio viene utilizzato per dimostrare che il codice dell'applicazione, dell'infrastruttura e della pipeline viene archiviato nello stesso repository.

Ora che il codice sorgente è impostato, è possibile iniziare la prima compilazione.

## <a name="continuous-integration"></a>Integrazione continua

[Pipeline di Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops), all'interno di Servizi DevOps di Azure, consente di implementare una pipeline di compilazione, test e distribuzione per le applicazioni.

In questa fase della pipeline, i test vengono in genere eseguiti per convalidare il codice e compilare le parti appropriate del software. Il numero e i tipi di test e le eventuali attività aggiuntive eseguite dipendono dalla strategia di compilazione e rilascio più ampia.

## <a name="preparing-the-hpc-application"></a>Preparazione dell'applicazione HPC

In questo esempio, ci concentreremo sulla cartella **hpc-application.** La cartella hpc-application è il software ffmpeg che verrà eseguito dall'interno dell'account Azure Batch.The **hpc-application** folder is the ffmpeg software that will run from within the Azure Batch account.

1. Passare alla sezione Compilazioni di Azure Pipelines nell'organizzazione DevOps di Azure.Navigate to the Builds section of Azure Pipelines in your Azure DevOps organization. Creare una **nuova pipeline**.

    ![Creare una nuova pipeline di compilazioneCreate a New Build Pipeline](media/batch-ci-cd/new-build-pipeline.jpg)

1. Sono disponibili due opzioni per creare una pipeline di compilazione:You have two options to create a Build pipeline:

    a. [Utilizzo della finestra di progettazione visiva](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Per usare questa opzione, fare clic su "Usa la finestra di progettazione visiva" nella pagina **Nuova pipeline.**

    b. [Utilizzo delle compilazioni YAML](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). È possibile creare una nuova pipeline YAML facendo clic sull'opzione Azure Repos o GitHub nella pagina Nuova pipeline. In alternativa, è possibile archiviare l'esempio riportato di seguito nel controllo del codice sorgente e fare riferimento a un file YAML esistente facendo clic su Finestra di progettazione visiva e quindi utilizzando il modello YAML.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. Una volta configurata la compilazione in base alle esigenze, selezionare **Salva & coda**. Se è attivata l'integrazione continua (nella sezione **Trigger),** la compilazione verrà attivata automaticamente quando viene creato un nuovo commit nel repository, soddisfacendo le condizioni impostate nella compilazione.

    ![Esempio di pipeline di compilazione esistente](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Visualizzare gli aggiornamenti in tempo reale sullo stato della compilazione in DevOps di Azure passando alla sezione **Compilazione** di Pipeline di Azure.View live updates on the progress of your build in Azure DevOps by navigating to the Build section of Azure Pipelines. Selezionare la compilazione appropriata dalla definizione di compilazione.

    ![Visualizzare gli output in tempo reale dalla compilazione](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Se si utilizza un'applicazione client per eseguire l'applicazione batch HPC, è necessario creare una definizione di compilazione separata per tale applicazione. Nella documentazione relativa alle pipeline di [Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) è possibile trovare una serie di guide alle procedure.

## <a name="continuous-deployment"></a>Distribuzione continua

Pipeline di Azure usate anche per distribuire l'applicazione e l'infrastruttura sottostante. Le pipeline di [rilascio](https://docs.microsoft.com/azure/devops/pipelines/release) sono il componente che consente la distribuzione continua e automatizza il processo di rilascio.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Distribuzione dell'applicazione e dell'infrastruttura sottostante

La distribuzione dell'infrastruttura prevede numerosi passaggi. Poiché abbiamo usato [modelli collegati,](../azure-resource-manager/templates/linked-templates.md)tali modelli dovranno essere accessibili da un endpoint pubblico (HTTP o HTTPS). Può trattarsi di un repository in GitHub, un account di archiviazione BLOB di Azure o un altro percorso di archiviazione. Gli elementi del modello caricato possono rimanere protetti, in quanto possono essere mantenuti in modalità privata ma accessibili tramite una forma di token di firma di accesso condiviso. L'esempio seguente illustra come distribuire un'infrastruttura con modelli da un BLOB di Archiviazione di Azure.The following example demonstrates how to deploy an infrastructure with templates from an Azure Storage blob.

1. Creare una **nuova definizione**di versione e selezionare una definizione vuota. È quindi necessario rinominare l'ambiente appena creato con qualcosa di rilevante per la pipeline.

    ![Pipeline di rilascio iniziale](media/batch-ci-cd/Release-0.jpg)

1. Creare una dipendenza dalla pipeline di compilazione per ottenere l'output per l'applicazione HPC.

    > [!NOTE]
    > Ancora una volta, prendere nota di **Source Alias**, in quanto ciò sarà necessario quando le attività vengono create all'interno della definizione di rilascio.

    ![Creare un collegamento artefatto a HPCApplicationPackage nella pipeline di compilazione appropriataCreate an artifact link to the HPCApplicationPackage in the appropriate build pipeline](media/batch-ci-cd/Release-1.jpg)

1. Creare un collegamento a un altro elemento, questa volta, un repo di Azure.Create a link to another artifact, this time, an Azure Repo. Questa operazione è necessaria per accedere ai modelli di Resource Manager archiviati nel repository. Poiché i modelli di Resource Manager non richiedono la compilazione, non è necessario eseguirne il push attraverso una pipeline di compilazione.

    > [!NOTE]
    > Ancora una volta, prendere nota di **Source Alias**, in quanto ciò sarà necessario quando le attività vengono create all'interno della definizione di rilascio.

    ![Creare un collegamento di elemento ai repository di AzureCreate an artifact link to the Azure Repos](media/batch-ci-cd/Release-2.jpg)

1. Passare alla sezione delle **variabili.** È consigliabile creare un numero di variabili nella pipeline, in modo da non immettere le stesse informazioni in più attività. Queste sono le variabili utilizzate in questo esempio e il modo in cui influiscono sulla distribuzione.

    * **applicationStorageAccountName**: nome dell'account di archiviazione per contenere i file binari dell'applicazione HPC
    * **batchAccountApplicationName**: nome dell'applicazione nell'account Batch di Azure
    * **batchAccountName**: nome dell'account batch di Azure
    * **batchAccountPoolName**: nome del pool di macchine virtuali che esegue l'elaborazione
    * **batchApplicationId:** ID univoco per l'applicazione Azure Batch
    * **batchApplicationVersion**: versione semantica dell'applicazione batch, ovvero i file binari di ffmpeg
    * **location**: percorso per le risorse di Azure da distribuire
    * **resourceGroupName:** nome del gruppo di risorse da creare e la posizione in cui verranno distribuite le risorse
    * **storageAccountName**: nome dell'account di archiviazione per contenere i modelli di Resource Manager collegati

    ![Esempio di variabili impostate per la versione Azure PipelinesExample of variables set for the Azure Pipelines release](media/batch-ci-cd/Release-4.jpg)

1. Passare alle attività per l'ambiente di sviluppo. Nell'istantanea seguente, è possibile visualizzare sei attività. Queste attività: scaricheranno i file ffmpeg compressi, distribuiranno un account di archiviazione per ospitare i modelli di Resource Manager annidati, copylano i modelli di Resource Manager nell'account di archiviazione, distribuiscono l'account batch e le dipendenze necessarie, creano un'applicazione in l'account Batch di Azure e caricare il pacchetto dell'applicazione nell'account Batch di Azure.The Azure Batch Account and upload the application package to the Azure Batch Account.

    ![Esempio delle attività usate per rilasciare l'applicazione HPC in Azure BatchExample of the tasks used to release the HPC Application to Azure Batch](media/batch-ci-cd/Release-3.jpg)

1. Aggiungere l'attività **Scarica artefatto pipeline (anteprima)** e impostare le proprietà seguenti:Add the Download Pipeline Artifact (Preview) task and set the following properties:
    * **Nome visualizzato:** Scarica ApplicationPackage nell'agente
    * **Il nome dell'elemento da scaricare:** hpc-application
    * **Percorso da scaricare in**:

1. Creare un account di archiviazione per archiviare gli elementi. Un account di archiviazione esistente dalla soluzione potrebbe essere usato, ma per l'esempio indipendente e l'isolamento del contenuto, stiamo creando un account di archiviazione dedicato per gli elementi (in particolare i modelli di Resource Manager).

    Aggiungere l'attività **distribuzione del gruppo di risorse** di Azure e impostare le proprietà seguenti:Add the Azure Resource Group Deployment task and set the following properties:
    * **Nome visualizzato:** Distribuire l'account di archiviazione per i modelli di Resource ManagerDeploy Storage Account for Resource Manager templates
    * **Sottoscrizione di Azure:Azure Subscription:** Selezionare la sottoscrizione di Azure appropriataSelect the appropriate Azure Subscription
    * **Azione:** Creare o aggiornare il gruppo di risorse
    * **Gruppo di risorse**: (resourceGroupName)
    * **Ubicazione**: (posizione)
    * **Modello**: (System.ArtifactsDirectory)/**.**
    * **Parametri del modello**di override : -accountName :(storageAccountName)Override template parameters : -accountName

1. Caricare gli elementi dal controllo del codice sorgente nell'account di archiviazione. Per eseguire questa operazione è disponibile un'attività Pipeline di Azure.There is an Azure Pipeline task to perform this. Come parte di questa attività, l'URL del contenitore dell'account di archiviazione e il token di firma di accesso condiviso possono essere restituiti in una variabile nelle pipeline di Azure.As part of this task, the Storage Account Container URL and the SAS Token can be outputed to a variable in Azure Pipelines. Ciò significa che può essere riutilizzato durante questa fase dell'agente.

    Aggiungere l'attività Copia file di Azure e impostare le proprietà seguenti:Add the **Azure File Copy** task and set the following properties:
    * **Origine:** (System.ArtifactsDirectory)/**.**
    * **Tipo di connessione di Azure:** Azure Resource ManagerAzure Connection Type : Azure Resource Manager
    * **Sottoscrizione di Azure:Azure Subscription:** Selezionare la sottoscrizione di Azure appropriataSelect the appropriate Azure Subscription
    * **Tipo di destinazione:** BLOB di AzureDestination Type : Azure Blob
    * **Account di archiviazione RM**: ((nomeaccountarchivio)
    * **Nome contenitore**: modelli
    * **URI contenitore di archiviazione:** templateContainerUriStorage Container URI : templateContainerUri
    * **Token sAS del contenitore di archiviazione:** templateContainerSasTokenStorage Container SAS Token: templateContainerSasToken

1. Distribuire il modello dell'agente di orchestrazione. Richiamare il modello dell'agente di orchestrazione da prima, si noterà che erano presenti parametri per l'URL del contenitore di account di archiviazione, oltre al token di firma di accesso condiviso. Si noti che le variabili richieste nel modello di Resource Manager sono contenute nella sezione variables della definizione di versione o sono state impostate da un'altra attività Pipeline di Azure (ad esempio, parte dell'attività Copia BLOB di Azure).

    Aggiungere l'attività **distribuzione del gruppo di risorse** di Azure e impostare le proprietà seguenti:Add the Azure Resource Group Deployment task and set the following properties:
    * **Nome visualizzato:** Distribuire Azure BatchDeploy Azure Batch
    * **Sottoscrizione di Azure:Azure Subscription:** Selezionare la sottoscrizione di Azure appropriataSelect the appropriate Azure Subscription
    * **Azione:** Creare o aggiornare il gruppo di risorse
    * **Gruppo di risorse**: (resourceGroupName)
    * **Ubicazione**: (posizione)
    * **Modello**: (System.ArtifactsDirectory)/**.**
    * **Parametri del modello**di override :```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Una pratica comune consiste nell'usare le attività dell'insieme di credenziali delle chiavi di Azure.A common practice is to use Azure Key Vault tasks. Se l'entità servizio (connessione alla sottoscrizione di Azure) dispone di un set di criteri di accesso appropriato, è possibile scaricare segreti da un insieme di credenziali delle chiavi di Azure ed essere usato come variabili nella pipeline. Il nome del segreto verrà impostato con il valore associato. Ad esempio, un segreto di sshPassword potrebbe essere fatto riferimento con s (sshPassword) nella definizione di versione.

1. The next steps call the Azure CLI. Il primo viene usato per creare un'applicazione in Azure Batch.The first is used to create an application in Azure Batch. e caricare i pacchetti associati.

    Aggiungere l'attività **dell'interfaccia della riga di comando** di Azure e impostare le proprietà seguenti:Add the Azure CLI task and set the following properties:
    * **Nome visualizzato:** Creare un'applicazione nell'account Batch di AzureCreate application in Azure Batch Account
    * **Sottoscrizione di Azure:Azure Subscription:** Selezionare la sottoscrizione di Azure appropriataSelect the appropriate Azure Subscription
    * **Posizione script**: Script inline
    * **Script in linea**:```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. Il secondo passaggio viene usato per caricare i pacchetti associati nell'applicazione. Nel nostro caso, i file ffmpeg.

    Aggiungere l'attività **dell'interfaccia della riga di comando** di Azure e impostare le proprietà seguenti:Add the Azure CLI task and set the following properties:
    * **Nome visualizzato:** Caricare il pacchetto nell'account Batch di AzureUpload package to Azure Batch Account
    * **Sottoscrizione di Azure:Azure Subscription:** Selezionare la sottoscrizione di Azure appropriataSelect the appropriate Azure Subscription
    * **Posizione script**: Script inline
    * **Script in linea**:```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Il numero di versione del pacchetto dell'applicazione è impostato su una variabile. Ciò è utile se la sovrascrittura delle versioni precedenti del pacchetto funziona automaticamente e se si vuole controllare manualmente il numero di versione del pacchetto inviato ad Azure Batch.

1. Creare una nuova release selezionando **Release > Crea una nuova versione**. Una volta attivato, selezionare il collegamento alla nuova versione per visualizzare lo stato.

1. È possibile visualizzare l'output in tempo reale dall'agente selezionando il pulsante **Registri** sotto l'ambiente.

    ![Visualizzare lo stato del rilascio](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Test dell'ambiente

Una volta configurato l'ambiente, verificare che i test seguenti siano stati completati correttamente.

Connettersi al nuovo account Batch di Azure usando l'interfaccia della riga di comando di Azure da un prompt dei comandi di PowerShell.Connect to the new Azure Batch Account, using the Azure CLI from a PowerShell command prompt.

* Accedere all'account Azure `az login` con e seguire le istruzioni per l'autenticazione.
* A questo punto autenticare l'account Batch:`az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Elencare le applicazioni disponibili

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Verificare che la piscina sia valida

```azurecli
az batch pool list
```

Prendere nota `currentDedicatedNodes` del valore dell'output di questo comando. Questo valore viene regolato nel test successivo.

#### <a name="resize-the-pool"></a>Ridimensionare la piscina

Ridimensionare il pool in modo che siano disponibili nodi di calcolo per il test di processi e attività, verificare con il comando dell'elenco dei pool per visualizzare lo stato corrente fino al completamento del ridimensionamento e sono disponibili nodi

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Passaggi successivi

Oltre a questo articolo, ci sono due tutorial che utilizzano ffmpeg, utilizzando .NET e Python. Vedere queste esercitazioni per ulteriori informazioni su come interagire con un account Batch tramite una semplice applicazione.

* [Eseguire un carico di lavoro parallelo con Azure Batch usando l'API Python](tutorial-parallel-python.md)
* [Eseguire un carico di lavoro parallelo con Azure Batch usando l'API .NET](tutorial-parallel-dotnet.md)
