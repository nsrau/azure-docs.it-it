---
title: Usare Azure Pipelines per compilare e distribuire soluzioni HPC
description: Informazioni su come distribuire una pipeline di compilazione/versione per un'applicazione HPC in esecuzione in Azure Batch.
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: how-to
ms.openlocfilehash: 2ad148579daa30d62da01aded0a01ace56f3dcbc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760564"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Usare Azure Pipelines per compilare e distribuire soluzioni HPC

Azure DevOps Services offre una gamma di strumenti usati dai team di sviluppo per la creazione di un'applicazione personalizzata. Gli strumenti forniti da Azure DevOps possono essere utilizzati per attività automatiche di compilazione ed esecuzione di test di soluzioni di calcolo con prestazioni elevate. Questo articolo illustra come configurare un'integrazione continua (CI) e una distribuzione continua (CD) usando Azure Pipelines per una soluzione di calcolo con prestazioni elevate distribuita in Azure Batch.

Azure Pipelines offre una gamma di processi moderni di integrazione/distribuzione continua per attività di compilazione, distribuzione, testing e monitoraggio del software. Questi processi accelerano la distribuzione del software, consentendo di concentrarsi sul codice anziché supportare l'infrastruttura e le operazioni.

## <a name="create-an-azure-pipeline"></a>Creare una pipeline di Azure

In questo esempio verrà creata una pipeline di compilazione e versione per distribuire un'infrastruttura di Azure Batch e rilasciare un pacchetto dell'applicazione. Supponendo che il codice venga sviluppato localmente, questo è il flusso di distribuzione generale:

![Diagramma che mostra il flusso di distribuzione nella pipeline](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Configurazione

Per seguire la procedura descritta in questo articolo, sono necessari un'organizzazione di Azure DevOps e un progetto team.

* [Creare un'organizzazione di Azure DevOps](/azure/devops/organizations/accounts/create-organization)
* [Creare un progetto in Azure DevOps](/azure/devops/organizations/projects/create-project)

### <a name="source-control-for-your-environment"></a>Controllo del codice sorgente per l'ambiente

Il controllo del codice sorgente consente ai team di tenere traccia delle modifiche apportate alla codebase e controllare le versioni precedenti del codice.

In genere, il controllo del codice sorgente è pensato in stretta associazione con il codice software. Mentre, per quanto riguarda l'infrastruttura, tenere presente l'infrastruttura come codice, in cui verrà usato un modello di Azure Resource Manager o altre alternative open source per definire in modo dichiarativo l'infrastruttura sottostante.

Questo esempio si basa principalmente su una serie di modelli di Resource Manager (documenti JSON) e file binari esistenti. È possibile copiare questi esempi nel repository ed eseguirne il push in Azure DevOps.

La struttura della codebase usata in questo esempio è simile alla seguente:

* Una cartella **arm-templates**, che contiene diversi modelli di Azure Resource Manager. I modelli sono descritti in questo articolo.
* Una cartella **client-application**, che è una copia dell'esempio sull'[elaborazione di file di .NET con Azure Batch tramite ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial). Questa cartella non è necessaria per questo articolo.
* Una cartella **HPC-Application** , che è la versione di Windows a 64 bit di [ffmpeg 4.3.1](https://www.gyan.dev/ffmpeg/builds/packages/ffmpeg-4.3.1-2020-09-21-full_build.zip).
* Una cartella **pipelines**, che contiene un file YAML che illustra il processo di compilazione. Questo aspetto viene trattato nell'articolo.

Questa sezione presuppone che l'utente abbia familiarità con il controllo della versione e la progettazione di modelli di Resource Manager. Se non si ha familiarità con questi concetti, vedere le pagine seguenti per altre informazioni.

* [What is source control?](/azure/devops/user-guide/source-control) (Informazioni sul codice sorgente)
* [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Modelli di Gestione risorse di Azure

Questo esempio utilizza vari modelli di Resource Manager per distribuire la soluzione. A tale scopo, viene usato un certo numero di modelli di funzionalità (simili a unità o moduli) che implementano una parte specifica della funzionalità. Viene anche usato un modello di soluzione end-to-end responsabile di riunire tali funzionalità sottostanti. Questo approccio presenta alcuni vantaggi:

* I modelli di funzionalità sottostanti possono eseguire lo unit test singolarmente.
* I modelli di funzionalità sottostanti possono essere definiti come standard all'interno di un'organizzazione e riutilizzati in più soluzioni.

Per questo esempio, è disponibile un modello di soluzione end-to-end (deployment.json) che distribuisce tre modelli. I modelli sottostanti sono modelli di funzionalità, responsabili della distribuzione di un aspetto specifico della soluzione.

![Esempio di una struttura di modelli collegati con i modelli di Azure Resource Manager](media/batch-ci-cd/ARMTemplateHierarchy.png)

Il primo modello che verrà esaminato è per un account di Archiviazione di Azure. La soluzione richiede un account di archiviazione per distribuire l'applicazione nell'account di Azure Batch. Per la compilazione di modelli di Resource Manager per gli account di Archiviazione di Azure, è opportuno tenere presente la [guida di riferimento del modello di Resource Manager per i tipi di risorse Microsoft.Storage](/azure/templates/microsoft.storage/allversions).

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

Successivamente, verrà esaminato il modello dell'account di Azure Batch. L'account di Azure Batch funge da piattaforma per eseguire numerose applicazioni tra pool (raggruppamenti di computer). Per la compilazione di modelli di Resource Manager per gli account di Azure Batch, è opportuno tenere presente la [guida di riferimento del modello di Resource Manager per i tipi di risorse Microsoft.Batch](/azure/templates/microsoft.batch/allversions).

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

Il modello successivo illustra un esempio di creazione di un pool di Azure Batch (computer back-end per l'elaborazione delle applicazioni). Per la compilazione di modelli di Resource Manager per i pool degli account di Azure Batch, è opportuno tenere presente la [guida di riferimento del modello di Resource Manager per i tipi di risorse Microsoft.Batch](/azure/templates/microsoft.batch/allversions).

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

Infine, è presente un modello che funziona in modo simile a un agente di orchestrazione. Questo modello è responsabile della distribuzione dei modelli di funzionalità.

Sono inoltre disponibili altre informazioni sulla [creazione di modelli di Azure Resource Manager collegati](../azure-resource-manager/templates/deployment-tutorial-linked-template.md) in un articolo separato.

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

Per questa soluzione, ffmpeg viene usato come pacchetto dell'applicazione. È possibile scaricare il pacchetto ffmpeg [qui](https://www.videohelp.com/software?d=ffmpeg-3.3.4-win64-static.zip).

![Esempio di struttura del repository Git](media/batch-ci-cd/git-repository.jpg)

Il repository contiene quattro sezioni principali:

* La cartella **arm-templates**, che archivia l'infrastruttura come codice
* La cartella **hpc-application**, che contiene i file binari per ffmpeg
* La cartella **pipelines**, che contiene la definizione per la pipeline di compilazione.
* **Facoltativo**: La cartella **client-application**, che archivia il codice per l'applicazione .NET. Questa cartella non viene usata nell'esempio, ma nel progetto possono essere necessarie esecuzioni dell'applicazione Batch di HPC tramite un'applicazione client.

> [!NOTE]
> Questo è solo un esempio di struttura per una codebase. Questo approccio viene usato ai fini della dimostrazione che l'applicazione, l'infrastruttura e il codice della pipeline sono archiviati nello stesso repository.

Ora che il codice sorgente è configurato, è possibile iniziare la prima compilazione.

## <a name="continuous-integration"></a>Integrazione continua

[Azure Pipelines](/azure/devops/pipelines/get-started/), all'interno di Azure DevOps Services, consente di implementare una pipeline di compilazione, test e distribuzione per le applicazioni.

In questa fase della pipeline, i test vengono in genere eseguiti per convalidare il codice e compilare i componenti del software appropriati. Il numero e i tipi di test e le eventuali attività aggiuntive eseguite dipendono dalla strategia di compilazione e versione più ampia.

## <a name="preparing-the-hpc-application"></a>Preparazione dell'applicazione HPC

In questo esempio si prenderà in esame la cartella **hpc-application**. La cartella **hpc-application** è il software ffmpeg che verrà eseguito dall'account di Azure Batch.

1. Passare alla sezione Compilazioni di Azure Pipelines nell'organizzazione di Azure DevOps. Creare una **nuova pipeline**.

    ![Creazione di una nuova pipeline di compilazione](media/batch-ci-cd/new-build-pipeline.jpg)

1. Per creare una pipeline di compilazione, sono disponibili due opzioni:

    a. [Uso della finestra di progettazione grafica](/azure/devops/pipelines/get-started-designer). A tale scopo, fare clic su "Usa la finestra di progettazione grafica" nella pagina **Nuova pipeline**.

    b. [Uso delle compilazioni YAML](/azure/devops/pipelines/get-started-yaml). È possibile creare una nuova pipeline YAML facendo clic sull'opzione Azure Repos o GitHub nella pagina Nuova pipeline. In alternativa, è possibile archiviare l'esempio seguente nel controllo del codice sorgente e fare riferimento a un file YAML esistente facendo clic sulla finestra di progettazione grafica e quindi usando il modello di YAML.

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

1. Una volta configurata la compilazione in base alle esigenze, selezionare **Salva e accoda**. Se è abilitata l'integrazione continua (nella sezione **Trigger**), la compilazione viene attivata automaticamente quando viene eseguito un nuovo commit nel repository, in base alle condizioni impostate nella compilazione.

    ![Esempio di una pipeline di compilazione esistente](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Visualizzare gli aggiornamenti dinamici sullo stato di avanzamento della compilazione in Azure DevOps passando alla sezione **Build** di Azure Pipelines. Selezionare la compilazione appropriata dalla definizione di compilazione.

    ![Visualizzazione degli output live dalla compilazione](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Se si usa un'applicazione client per eseguire l'applicazione di Azure Batch per HPC, è necessario creare una definizione di compilazione separata per tale applicazione. Per le guide pratiche, vedere la documentazione di [Azure Pipelines](/azure/devops/pipelines/get-started/index).

## <a name="continuous-deployment"></a>Distribuzione continua

Azure Pipelines viene usato anche per distribuire l'applicazione e l'infrastruttura sottostante. [Pipeline di versione](/azure/devops/pipelines/release) è il componente che consente la distribuzione continua e automatizza il processo di rilascio.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Distribuzione dell'applicazione e dell'infrastruttura sottostante

Per la distribuzione dell'infrastruttura sono necessari alcuni passaggi. Poiché sono stati usati [modelli collegati](../azure-resource-manager/templates/linked-templates.md), è necessario che i modelli siano accessibili da un endpoint pubblico (HTTP o HTTPS). Potrebbe trattarsi di un repository in GitHub o di un account di Archiviazione BLOB di Azure o di un'altra posizione di archiviazione. Gli artefatti del modello caricati possono rimanere protetti, in quanto possono essere mantenuti in modalità privata, ma essere comunque accessibili tramite un token di firma di accesso condiviso (SAS). L'esempio seguente illustra come distribuire un'infrastruttura con modelli da un BLOB di Archiviazione di Azure.

1. Creare una **nuova definizione di versione** e selezionare una definizione vuota. A questo punto, è necessario rinominare l'ambiente appena creato in modo pertinente per la pipeline.

    ![Pipeline di versione iniziale](media/batch-ci-cd/Release-0.jpg)

1. Creare una dipendenza nella pipeline di compilazione per ottenere l'output per l'applicazione HPC.

    > [!NOTE]
    > Ancora una volta, annotare l'**alias di origine**, che sarà necessario quando le attività verranno create all'interno della definizione di versione.

    ![Creazione di un collegamento dell'artefatto a HPCApplicationPackage nella pipeline di compilazione appropriata](media/batch-ci-cd/Release-1.jpg)

1. Creare un collegamento a un altro artefatto. In questo caso, un repository di Azure. Questa operazione è necessaria per accedere ai modelli di Resource Manager archiviati nel repository. Poiché i modelli di Resource Manager non richiedono la compilazione, non è necessario eseguirne il push tramite una pipeline di compilazione.

    > [!NOTE]
    > Ancora una volta, annotare l'**alias di origine**, che sarà necessario quando le attività verranno create all'interno della definizione di versione.

    ![Creazione di un collegamento dell'artefatto ad Azure Repos](media/batch-ci-cd/Release-2.jpg)

1. Passare alla sezione **Variabili**. È consigliabile creare una serie di variabili nella pipeline, in modo che non si inseriscano le stesse informazioni in più attività. Queste sono le variabili usate in questo esempio e il modo in cui incidono sulla distribuzione.

    * **applicationStorageAccountName**: nome dell'account di archiviazione in cui conservare i file binari dell'applicazione HPC
    * **batchAccountApplicationName**: nome dell'applicazione nell'account di Azure Batch
    * **batchAccountName**: nome dell'account di Azure Batch
    * **batchAccountPoolName**: nome del pool di macchine virtuali che esegue l'elaborazione
    * **batchApplicationId**: ID univoco per l'applicazione Azure Batch
    * **batchApplicationVersion**: versione semantica dell'applicazione batch (ovvero i file binari ffmpeg)
    * **location**: percorso per le risorse di Azure da distribuire
    * **resourceGroupName**: nome del gruppo di risorse da creare e in cui verranno distribuite le risorse
    * **StorageAccountName**: nome dell'account di archiviazione in cui conservare i modelli di Resource Manager collegati

    ![Esempio di variabili impostate per la versione di Azure Pipelines](media/batch-ci-cd/Release-4.jpg)

1. Passare alle attività per l'ambiente di sviluppo. Nello snapshot seguente è possibile visualizzare sei attività. Queste attività consentono di scaricare i file ffmpeg compressi, distribuire un account di archiviazione per ospitare i modelli annidati di Resource Manager, copiare i modelli di Resource Manager nell'account di archiviazione, distribuire l'account di Azure Batch e le dipendenze necessarie, creare un'applicazione nell'account di Azure Batch e caricare il pacchetto dell'applicazione nell'account di Azure Batch.

    ![Esempio di attività usate per rilasciare l'applicazione HPC ad Azure Batch](media/batch-ci-cd/Release-3.jpg)

1. Aggiungere l'attività **Scarica l'artefatto della pipeline (anteprima)** e impostare le proprietà seguenti:
    * **Nome visualizzato:** scaricare il pacchetto dell'applicazione nell'agente
    * **Nome dell'artefatto da scaricare:** hpc-application
    * **Percorso di download:** $(System.DefaultWorkingDirectory)

1. Creare un account di archiviazione per archiviare gli artefatti. È possibile usare un account di archiviazione esistente dalla soluzione, ma per l'esempio autonomo e l'isolamento del contenuto, si sta creando un account di archiviazione dedicato per gli artefatti (in particolare i modelli di Resource Manager).

    Aggiungere l'attività **Distribuzione gruppo di risorse di Azure** e impostare le proprietà seguenti:
    * **Nome visualizzato:** account di archiviazione per la distribuzione per i modelli di Resource Manager
    * **Sottoscrizione di Azure:** selezionare la sottoscrizione di Azure appropriata
    * **Azione**: Creare o aggiornare un gruppo di risorse
    * **Gruppo di risorse**: $(resourceGroupName)
    * **Posizione**: $(location)
    * **Modello**: $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/storageAccount.json
    * **Override dei parametri del modello**: -accountName $(storageAccountName)

1. Caricare gli artefatti dal controllo del codice sorgente nell'account di archiviazione. Per eseguire questa operazione, è disponibile un'attività della pipeline di Azure. Come parte di questa attività, l'URL del contenitore dell'account di archiviazione e il token di firma di accesso condiviso possono essere restituiti a una variabile in Azure Pipelines. Ciò significa che possono essere riutilizzati in questa fase dell'agente.

    Aggiungere l'attività **Copia dei file di Azure** e impostare le proprietà seguenti:
    * **Origine:** $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/
    * **Tipo di connessione ad Azure:** Azure Resource Manager
    * **Sottoscrizione di Azure:** selezionare la sottoscrizione di Azure appropriata
    * **Tipo di destinazione:** BLOB Azure
    * **Account di archiviazione di Resource Manager**: $(storageAccountName)
    * **Nome del contenitore**: templates
    * **URI del contenitore di archiviazione**: templateContainerUri
    * **Token SAS del contenitore di archiviazione**: templateContainerSasToken

1. Distribuire il modello dell'agente di orchestrazione. Richiamare il modello dell'agente di orchestrazione dal passaggio precedente. Si noterà che sono presenti parametri per l'URL del contenitore dell'account di archiviazione, oltre al token di firma di accesso condiviso. Tenere presente che le variabili necessarie nel modello di Resource Manager sono contenute nella sezione Variabili della definizione di versione o sono state impostate da un'altra attività di Azure Pipelines (ad esempio, parte dell'attività di copia del BLOB di Azure).

    Aggiungere l'attività **Distribuzione gruppo di risorse di Azure** e impostare le proprietà seguenti:
    * **Nome visualizzato**: distribuire Azure Batch
    * **Sottoscrizione di Azure**: selezionare la sottoscrizione di Azure appropriata
    * **Azione**: Creare o aggiornare un gruppo di risorse
    * **Gruppo di risorse**: $(resourceGroupName)
    * **Posizione**: $(location)
    * **Modello**: $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/deployment.json
    * **Eseguire l'override dei parametri del modello**: ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Una procedura comune consiste nell'usare le attività di Azure Key Vault. Se l'entità servizio (connessione alla sottoscrizione di Azure) ha un set di criteri di accesso appropriato, può scaricare i segreti da un'istanza di Azure Key Vault e usarli come variabili nella pipeline. Il nome del segreto verrà impostato con il valore associato. È ad esempio possibile fare riferimento a un segreto di sshPassword con $(sshPassword) nella definizione di versione.

1. I passaggi successivi chiamano l'interfaccia della riga di comando di Azure. Il primo viene usato per creare un'applicazione in Azure Batch e caricare i pacchetti associati.

    Aggiungere l'attività **Interfaccia della riga di comando di Azure** e impostare le proprietà seguenti:
    * **Nome visualizzato**: creare l'applicazione nell'account di Azure Batch
    * **Sottoscrizione di Azure**: selezionare la sottoscrizione di Azure appropriata
    * **Percorso dello script**: script inline
    * **Script inline**: ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. Il secondo passaggio viene usato per caricare nell'applicazione i pacchetti associati. In questo caso, i file ffmpeg.

    Aggiungere l'attività **Interfaccia della riga di comando di Azure** e impostare le proprietà seguenti:
    * **Nome visualizzato**: caricare il pacchetto nell'account di Azure Batch
    * **Sottoscrizione di Azure**: selezionare la sottoscrizione di Azure appropriata
    * **Percorso dello script**: script inline
    * **Script inline**: ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > il numero di versione del pacchetto dell'applicazione è impostato su una variabile. Questo aspetto è utile se la sovrascrittura delle versioni precedenti del pacchetto è conforme alle proprie esigenze e se si vuole controllare manualmente il numero di versione del pacchetto di cui è stato eseguito il push in Azure Batch.

1. Creare una nuova versione selezionando **Versione > Crea nuova versione**. Una volta attivato, selezionare il collegamento alla nuova versione per visualizzare lo stato.

1. È possibile visualizzare l'output live dell'agente selezionando il pulsante **Log** nell'area dell'ambiente.

    ![Visualizzazione dello stato della versione](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Testare l'ambiente

Dopo aver configurato l'ambiente, verificare che sia possibile completare correttamente i test seguenti.

Connettersi al nuovo account di Azure Batch usando l'interfaccia della riga di comando di Azure da un prompt dei comandi di PowerShell.

* Accedere all'account Azure con `az login` e seguire le istruzioni per l'autenticazione.
* Ora autenticare l'account Batch: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Elencare le applicazioni disponibili

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Verificare che il pool sia valido

```azurecli
az batch pool list
```

Si noti il valore di `currentDedicatedNodes` dall'output di questo comando. Questo valore viene regolato nel test successivo.

#### <a name="resize-the-pool"></a>Ridimensionare il pool

Ridimensionare il pool in modo che siano disponibili nodi di calcolo per il test delle attività e del processo, usare il comando dell'elenco di pool per visualizzare lo stato corrente finché il ridimensionamento non è stato completato e sono disponibili nodi.

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Passaggi successivi

Oltre a questo articolo, sono disponibili due esercitazioni che usano ffmpeg, con .NET e Python. Per altre informazioni su come interagire con un account Batch tramite una semplice applicazione, vedere queste esercitazioni.

* [Eseguire un carico di lavoro parallelo con Azure Batch usando l'API Python](tutorial-parallel-python.md)
* [Eseguire un carico di lavoro parallelo con Azure Batch usando l'API .NET](tutorial-parallel-dotnet.md)
