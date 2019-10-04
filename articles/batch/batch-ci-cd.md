---
title: Usare Azure Pipelines per compilare e distribuire soluzioni HPC-Azure Batch | Microsoft Docs
description: Informazioni su come distribuire una pipeline di compilazione/versione per un'applicazione HPC in esecuzione in Azure Batch.
author: christianreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.service: batch
ms.openlocfilehash: 47665171ee5ae137e0503b3e5fa1d369aeabb356
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840064"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Usare Azure Pipelines per compilare e distribuire soluzioni HPC

I servizi DevOps di Azure offrono una gamma di strumenti usati dai team di sviluppo per la creazione di un'applicazione personalizzata. Gli strumenti forniti da Azure DevOps possono tradursi in creazione e test automatici di soluzioni di calcolo ad alte prestazioni. Questo articolo illustra come configurare un'integrazione continua (CI) e la distribuzione continua (CD) usando Azure Pipelines per una soluzione di calcolo ad alte prestazioni distribuita su Azure Batch.

Azure Pipelines offre una gamma di moderni processi di integrazione continua/recapito continuo per la compilazione, la distribuzione, il test e il monitoraggio del software. Questi processi accelerano la distribuzione del software, consentendo di concentrarsi sul codice anziché supportare l'infrastruttura e le operazioni.

## <a name="create-an-azure-pipeline"></a>Creare una pipeline di Azure

In questo esempio verrà creata una pipeline di compilazione e di rilascio per distribuire un'infrastruttura Azure Batch e rilasciare un pacchetto di applicazione. Supponendo che il codice venga sviluppato localmente, questo è il flusso di distribuzione generale:

![Diagramma che mostra il flusso di distribuzione nella pipeline](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Configurazione

Per seguire la procedura descritta in questo articolo, sono necessari un'organizzazione DevOps di Azure e un progetto team.

* [Creare un'organizzazione di Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Creare un progetto in Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>Controllo del codice sorgente per l'ambiente

Il controllo del codice sorgente consente ai team di tenere traccia delle modifiche apportate alla codebase e controllare le versioni precedenti del codice.

In genere, il controllo del codice sorgente è pensato in mano al codice software. Informazioni sull'infrastruttura sottostante Questo ci porta all'infrastruttura come codice, in cui verrà usato Azure Resource Manager modelli o altre alternative open source per definire in modo dichiarativo l'infrastruttura sottostante.

Questo esempio si basa principalmente su una serie di modelli di Gestione risorse (documenti JSON) e binari esistenti. È possibile copiare questi esempi nel repository ed eseguirne il push in Azure DevOps.

La struttura codebase utilizzata in questo esempio è simile alla seguente:

* Una cartella **ARM-templates** , che contiene un numero di modelli di Azure Resource Manager. I modelli sono illustrati in questo articolo.
* Una cartella **client-Application** , che è una copia dell'esempio di [elaborazione di file .NET Azure batch con ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) . Questa operazione non è necessaria per questo articolo.
* Una cartella **HPC-Application** , che è la versione di Windows a 64 bit di [ffmpeg 3,4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).
* Una cartella Pipelines. Contiene un file YAML che illustra il processo di compilazione. Questo argomento viene illustrato nell'articolo.

Questa sezione presuppone che l'utente abbia familiarità con il controllo della versione e la progettazione di modelli Gestione risorse. Se non si ha familiarità con questi concetti, vedere le pagine seguenti per altre informazioni.

* [Che cos'è il controllo del codice sorgente?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)

#### <a name="azure-resource-manager-templates"></a>Modelli di Gestione risorse di Azure

Questo esempio utilizza più modelli di Gestione risorse per distribuire la soluzione. A tale scopo, viene usato un certo numero di modelli di funzionalità (simili a unità o moduli) che implementano una parte specifica della funzionalità. Viene anche usato un modello di soluzione end-to-end che è responsabile di riunire le funzionalità sottostanti. Questo approccio presenta alcuni vantaggi:

* I modelli di funzionalità sottostanti possono essere unit test singolarmente.
* I modelli di funzionalità sottostanti possono essere definiti come standard all'interno di un'organizzazione e riutilizzati in più soluzioni.

Per questo esempio è disponibile un modello di soluzione end-to-end (Deployment. Json) che distribuisce tre modelli. I modelli sottostanti sono modelli di funzionalità, responsabili della distribuzione di un aspetto specifico della soluzione.

![Esempio di struttura del modello collegato con Azure Resource Manager modelli](media/batch-ci-cd/ARMTemplateHierarchy.png)

Il primo modello che verrà esaminato è per un account di archiviazione di Azure. La soluzione richiede un account di archiviazione per distribuire l'applicazione nell'account batch. Quando si compilano modelli di Gestione risorse per gli account di archiviazione, è opportuno tenere presente la [Guida di riferimento del modello Gestione risorse per i tipi di risorse Microsoft. storage](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) .

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

Verrà ora esaminato il modello di account Azure Batch. L'account Azure Batch funge da piattaforma per eseguire numerose applicazioni tra pool (raggruppamenti di computer). Quando si compilano modelli di Gestione risorse per gli account batch, è opportuno tenere presente la [Guida di riferimento del modello Gestione risorse per i tipi di risorse Microsoft. batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) .

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

Il modello successivo mostra un esempio di creazione di un pool di Azure Batch (computer back-end per l'elaborazione delle applicazioni). Quando si compilano modelli di Gestione risorse per i pool di account batch, è opportuno tenere presente la [Guida di riferimento del modello Gestione risorse per i tipi di risorse Microsoft. batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) .

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

Infine, abbiamo un modello che funziona in modo simile a un agente di orchestrazione. Questo modello è responsabile della distribuzione dei modelli di funzionalità.

È anche possibile trovare altre informazioni sulla [creazione di modelli di Azure Resource Manager collegati](../azure-resource-manager/resource-manager-tutorial-create-linked-templates.md) in un articolo separato.

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

#### <a name="the-hpc-solution"></a>Soluzione HPC

L'infrastruttura e il software possono essere definiti come codice e posizionati nello stesso repository.

Per questa soluzione, ffmpeg viene usato come pacchetto dell'applicazione. Il pacchetto ffmpeg può essere scaricato [qui](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).

![Esempio di struttura del repository git](media/batch-ci-cd/git-repository.jpg)

Il repository contiene quattro sezioni principali:

* La cartella **ARM-templates** che archivia l'infrastruttura come codice
* La cartella **HPC-Application** che contiene i file binari per FFmpeg
* Cartella **Pipelines** che contiene la definizione per la pipeline di compilazione.
* **Facoltativo**: Cartella dell' **applicazione client** che archivia il codice per l'applicazione .NET. Questa operazione non viene utilizzata nell'esempio, ma nel progetto, potrebbe essere necessario eseguire esecuzioni dell'applicazione HPC batch tramite un'applicazione client.

> [!NOTE]
> Questo è solo un esempio di una struttura a una codebase. Questo approccio viene usato ai fini della dimostrazione che l'applicazione, l'infrastruttura e il codice della pipeline sono archiviati nello stesso repository.

Ora che il codice sorgente è configurato, è possibile iniziare la prima compilazione.

## <a name="continuous-integration"></a>Integrazione continua

[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops), all'interno di Azure DevOps Services, consente di implementare una pipeline di compilazione, test e distribuzione per le applicazioni.

In questa fase della pipeline, i test vengono in genere eseguiti per convalidare il codice e compilare i componenti del software appropriati. Il numero e i tipi di test e le eventuali attività aggiuntive eseguite dipendono dalla strategia di compilazione e versione più ampia.

## <a name="preparing-the-hpc-application"></a>Preparazione dell'applicazione HPC

In questo esempio si incentrerà sulla cartella **HPC-Application** . La cartella **HPC-Application** è il software FFmpeg che verrà eseguito dall'interno dell'account Azure batch.

1. Passare alla sezione compilazioni di Azure Pipelines nell'organizzazione di Azure DevOps. Creare una **nuova pipeline**.

    ![Creare una nuova pipeline di compilazione](media/batch-ci-cd/new-build-pipeline.jpg)

1. Sono disponibili due opzioni per creare una pipeline di compilazione:

    a. [Utilizzando la finestra di progettazione visiva](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Per usarlo, fare clic su "usa la finestra di progettazione visiva" nella pagina **nuova pipeline** .

    b. [Uso](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops)delle compilazioni YAML. È possibile creare una nuova pipeline YAML facendo clic sull'opzione Azure Repos o GitHub nella pagina nuova pipeline. In alternativa, è possibile archiviare l'esempio seguente nel controllo del codice sorgente e fare riferimento a un file YAML esistente facendo clic su finestra di progettazione visiva e quindi usando il modello YAML.

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

1. Una volta configurata la compilazione in base alle esigenze, selezionare **salva & coda**. Se è abilitata l'integrazione continua (nella sezione **trigger** ), la compilazione viene attivata automaticamente quando viene eseguito un nuovo commit nel repository, rispettando le condizioni impostate nella compilazione.

    ![Esempio di una pipeline di compilazione esistente](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Visualizzare gli aggiornamenti dinamici sullo stato di avanzamento della compilazione in Azure DevOps passando alla sezione **Build** di Azure Pipelines. Selezionare la compilazione appropriata dalla definizione di compilazione.

    ![Visualizzare gli output Live dalla compilazione](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Se si usa un'applicazione client per eseguire l'applicazione batch HPC, è necessario creare una definizione di compilazione separata per l'applicazione. È possibile trovare una serie di guide pratiche nella documentazione di [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) .

## <a name="continuous-deployment"></a>Distribuzione continua

Azure Pipelines anche usato per distribuire l'applicazione e l'infrastruttura sottostante. [Pipeline di versione](https://docs.microsoft.com/azure/devops/pipelines/release) è il componente che consente la distribuzione continua e automatizza il processo di rilascio.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Distribuzione dell'applicazione e dell'infrastruttura sottostante

Per la distribuzione dell'infrastruttura sono necessari alcuni passaggi. Poiché sono stati usati [modelli collegati](../azure-resource-manager/resource-group-linked-templates.md), questi modelli dovranno essere accessibili da un endpoint pubblico (http o HTTPS). Potrebbe trattarsi di un repository in GitHub o di un account di archiviazione BLOB di Azure o di un altro percorso di archiviazione. Gli elementi del modello caricati possono rimanere protetti, perché possono essere mantenuti in modalità privata, ma a cui si accede tramite un token di firma di accesso condiviso (SAS). L'esempio seguente illustra come distribuire un'infrastruttura con i modelli da un BLOB di archiviazione di Azure.

1. Creare una **nuova definizione di versione**e selezionare una definizione vuota. È quindi necessario rinominare l'ambiente appena creato in un elemento pertinente per la pipeline.

    ![Pipeline di versione iniziale](media/batch-ci-cd/Release-0.jpg)

1. Creare una dipendenza dalla pipeline di compilazione per ottenere l'output per l'applicazione HPC.

    > [!NOTE]
    > Ancora una volta, annotare l' **alias di origine**, che sarà necessario quando le attività vengono create all'interno della definizione di versione.

    ![Creare un collegamento artefatto a HPCApplicationPackage nella pipeline di compilazione appropriata](media/batch-ci-cd/Release-1.jpg)

1. Creare un collegamento a un altro artefatto, questa volta un repository di Azure. Questa operazione è necessaria per accedere ai modelli di Gestione risorse archiviati nel repository. Poiché i modelli di Gestione risorse non richiedono la compilazione, non è necessario eseguirne il push tramite una pipeline di compilazione.

    > [!NOTE]
    > Ancora una volta, annotare l' **alias di origine**, che sarà necessario quando le attività vengono create all'interno della definizione di versione.

    ![Creare un collegamento dell'artefatto al Azure Repos](media/batch-ci-cd/Release-2.jpg)

1. Passare alla sezione **variables** . È consigliabile creare una serie di variabili nella pipeline, in modo che non si inserisca le stesse informazioni in più attività. Queste sono le variabili usate in questo esempio e il modo in cui incidono sulla distribuzione.

    * **applicationStorageAccountName**: Nome dell'account di archiviazione in cui conservare i file binari dell'applicazione HPC
    * **batchAccountApplicationName**: Nome dell'applicazione nell'account di Azure Batch
    * **batchAccountName**: Nome dell'account di Azure Batch
    * **batchAccountPoolName**: Nome del pool di macchine virtuali che esegue l'elaborazione
    * **batchApplicationId**: ID univoco per l'applicazione Azure Batch
    * **batchApplicationVersion**: Versione semantica dell'applicazione batch (ovvero i file binari ffmpeg)
    * **location**: Percorso per le risorse di Azure da distribuire
    * **resourceGroupName**: Nome del gruppo di risorse da creare e in cui verranno distribuite le risorse
    * **storageAccountName**: Nome dell'account di archiviazione in cui conservare i modelli di Gestione risorse collegati

    ![Esempio di variabili impostate per la versione Azure Pipelines](media/batch-ci-cd/Release-4.jpg)

1. Passare alle attività per l'ambiente di sviluppo. Nello snapshot seguente è possibile visualizzare sei attività. Queste attività consentono di scaricare i file ffmpeg compressi, distribuire un account di archiviazione per ospitare i modelli annidati di Gestione risorse, copiare i modelli di Gestione risorse nell'account di archiviazione, distribuire l'account batch e le dipendenze necessarie, creare un'applicazione in l'account Azure Batch e caricare il pacchetto dell'applicazione nell'account di Azure Batch.

    ![Esempio di attività usate per rilasciare l'applicazione HPC a Azure Batch](media/batch-ci-cd/Release-3.jpg)

1. Aggiungere l'attività **Scarica elemento della pipeline (anteprima)** e impostare le proprietà seguenti:
    * **Nome visualizzato:** Scarica pacchetto dell'applicazione nell'agente
    * **Nome dell'artefatto da scaricare:** HPC-Application
    * **Percorso in cui eseguire il download**: $ (System. DefaultWorkingDirectory)

1. Creare un account di archiviazione per archiviare gli artefatti. È possibile usare un account di archiviazione esistente dalla soluzione, ma per l'esempio autonomo e l'isolamento del contenuto, si sta creando un account di archiviazione dedicato per gli artefatti (in particolare i modelli di Gestione risorse).

    Aggiungere l'attività di **distribuzione gruppo di risorse di Azure** e impostare le proprietà seguenti:
    * **Nome visualizzato:** Distribuire un account di archiviazione per i modelli di Gestione risorse
    * **Sottoscrizione di Azure:** Selezionare la sottoscrizione di Azure appropriata
    * **Azione**: Creare o aggiornare un gruppo di risorse
    * **Gruppo di risorse**: $ (resourceGroupName)
    * **Percorso**: $ (percorso)
    * **Modello**: $ (System. ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /ARM-templates/StorageAccount.JSON
    * **Esegui override dei parametri del modello**:-AccountName $ (storageAccountName)

1. Caricare gli artefatti dal controllo del codice sorgente nell'account di archiviazione. Per eseguire questa operazione, è disponibile un'attività della pipeline di Azure. Come parte di questa attività, l'URL del contenitore dell'account di archiviazione e il token di firma di accesso condiviso possono essere restituiti a una variabile in Azure Pipelines. Ciò significa che può essere riutilizzato in questa fase dell'agente.

    Aggiungere l'attività **copia file di Azure** e impostare le proprietà seguenti:
    * **Origine:** $ (System. ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /ARM-Templates/
    * **Tipo di connessione di Azure**: Azure Resource Manager
    * **Sottoscrizione di Azure:** Selezionare la sottoscrizione di Azure appropriata
    * **Tipo di destinazione**: BLOB di Azure
    * **Account di archiviazione RM**: $ (storageAccountName)
    * **Nome contenitore**: modelli
    * **URI del contenitore di archiviazione**: templateContainerUri
    * **Token SAS del contenitore di archiviazione**: templateContainerSasToken

1. Distribuire il modello dell'agente di orchestrazione. Ricordare il modello dell'agente di orchestrazione precedente. si noterà che sono presenti parametri per l'URL del contenitore dell'account di archiviazione, oltre al token SAS. Si noti che le variabili necessarie nel modello di Gestione risorse sono contenute nella sezione Variables della definizione di versione o sono state impostate da un'altra attività Azure Pipelines (ad esempio, parte dell'attività di copia BLOB di Azure).

    Aggiungere l'attività di **distribuzione gruppo di risorse di Azure** e impostare le proprietà seguenti:
    * **Nome visualizzato:** Distribuisci Azure Batch
    * **Sottoscrizione di Azure:** Selezionare la sottoscrizione di Azure appropriata
    * **Azione**: Creare o aggiornare un gruppo di risorse
    * **Gruppo di risorse**: $ (resourceGroupName)
    * **Percorso**: $ (percorso)
    * **Modello**: $ (System. ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /ARM-templates/Deployment.JSON
    * **Esegui override dei parametri del modello**:```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Una procedura comune consiste nell'usare Azure Key Vault attività. Se l'entità servizio (connessione alla sottoscrizione di Azure) ha un set di criteri di accesso appropriato, può scaricare i segreti da un Azure Key Vault e usarli come variabili nella pipeline. Il nome del segreto verrà impostato con il valore associato. È ad esempio possibile fare riferimento a un segreto di sshPassword con $ (sshPassword) nella definizione di versione.

1. I passaggi successivi chiamano l'interfaccia della riga di comando di Azure. Il primo viene usato per creare un'applicazione in Azure Batch. e caricare i pacchetti associati.

    Aggiungere l'attività dell'interfaccia della riga di comando di **Azure** e impostare le proprietà seguenti:
    * **Nome visualizzato:** Crea applicazione nell'account Azure Batch
    * **Sottoscrizione di Azure:** Selezionare la sottoscrizione di Azure appropriata
    * **Percorso script**: Script inline
    * **Script inline**:```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. Il secondo passaggio viene usato per caricare i pacchetti associati nell'applicazione. In questo caso, i file ffmpeg.

    Aggiungere l'attività dell'interfaccia della riga di comando di **Azure** e impostare le proprietà seguenti:
    * **Nome visualizzato:** Carica il pacchetto nell'account Azure Batch
    * **Sottoscrizione di Azure:** Selezionare la sottoscrizione di Azure appropriata
    * **Percorso script**: Script inline
    * **Script inline**:```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Il numero di versione del pacchetto dell'applicazione è impostato su una variabile. Questa operazione è utile se la sovrascrittura delle versioni precedenti del pacchetto funziona correttamente e se si desidera controllare manualmente il numero di versione del pacchetto di cui è stato eseguito il push in Azure Batch.

1. Creare una nuova versione selezionando **rilascia > creare una nuova versione**. Una volta attivato, selezionare il collegamento alla nuova versione per visualizzare lo stato.

1. È possibile visualizzare l'output Live dall'agente selezionando il pulsante **log** sotto l'ambiente.

    ![Visualizza lo stato della versione](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Test dell'ambiente

Una volta configurato l'ambiente, verificare che sia possibile completare correttamente i test seguenti.

Connettersi al nuovo account di Azure Batch usando l'interfaccia della riga di comando di Azure da un prompt dei comandi di PowerShell.

* Accedere al proprio account Azure con `az login` e seguire le istruzioni per l'autenticazione.
* Ora autenticare l'account batch:`az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Elencare le applicazioni disponibili

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Verificare che il pool sia valido

```azurecli
az batch pool list
```

Si noti il valore `currentDedicatedNodes` di dall'output di questo comando. Questo valore viene regolato nel test successivo.

#### <a name="resize-the-pool"></a>Ridimensionare il pool

Ridimensionare il pool in modo che siano disponibili nodi di calcolo per il processo e il test delle attività, verificare con il comando elenco pool per visualizzare lo stato corrente finché il ridimensionamento non è stato completato e sono disponibili nodi

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Passaggi successivi

Oltre a questo articolo, sono disponibili due esercitazioni che usano ffmpeg, usando .NET e Python. Per altre informazioni su come interagire con un account batch tramite una semplice applicazione, vedere queste esercitazioni.

* [Eseguire un carico di lavoro parallelo con Azure Batch usando l'API Python](tutorial-parallel-python.md)
* [Eseguire un carico di lavoro parallelo con Azure Batch usando l'API .NET](tutorial-parallel-dotnet.md)
