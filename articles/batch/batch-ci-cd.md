---
title: Usare le pipeline di Azure per compilare e distribuire soluzioni HPC - Azure Batch | Microsoft Docs
description: Informazioni su come distribuire una pipeline di compilazione/versione per un'applicazione HPC in esecuzione in Azure Batch.
author: christianreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.openlocfilehash: 5b7c44d3ea3394ff728adfb9d9fd72293138fb2e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59494645"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Usare le pipeline di Azure per compilare e distribuire soluzioni HPC

Servizi di Azure DevOps forniscono una gamma di strumenti usati dai team di sviluppo durante la creazione di un'applicazione personalizzata. Gli strumenti forniti da Azure DevOps possono tradurre in compilazione e test di soluzioni di calcolo ad alte prestazioni automatizzati. Questo articolo viene illustrato come configurare l'integrazione continua (CI) e la distribuzione continua (CD) tramite che le pipeline di Azure per un high performance compute soluzione distribuita in Azure Batch.

Le pipeline di Azure fornisce una gamma di processi di integrazione continua/distribuzione moderni per la creazione, distribuzione, test e software di monitoraggio. Questi processi accelera la distribuzione di software, consentendo di concentrarsi sul codice invece del supporto tecnico dell'infrastruttura e le operazioni.

## <a name="create-an-azure-pipeline"></a>Creare una Pipeline di Azure

In questo esempio, si crea una build e release pipeline per distribuire un'infrastruttura di Azure Batch e rilasciare un pacchetto di applicazione. Supponendo che il codice viene sviluppato in locale, questo è il flusso di distribuzione generale:

![Diagramma che mostra il flusso di distribuzione nella nostra Pipeline](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Configurazione

Per seguire la procedura descritta in questo articolo, sono necessari un'organizzazione di Azure DevOps e un progetto team.

* [Creazione di un'organizzazione di DevOps di Azure](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Creare un progetto in Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>Controllo del codice sorgente per l'ambiente

Controllo del codice sorgente consente ai team di tenere traccia delle modifiche apportate alla codebase e controllare le versioni precedenti del codice.

In genere, controllo del codice sorgente è considerato in stretta associazione con il codice del software. Per quanto riguarda l'infrastruttura sottostante? Questo argomento per infrastruttura come codice, in cui si userà i modelli di Azure Resource Manager o altre alternative open source per definire in modo dichiarativo l'infrastruttura sottostante.

In questo esempio si basa principalmente su un numero di modelli di Resource Manager (documenti JSON) e i file binari esistenti. È possibile copiare questi esempi nel repository e li invierà a DevOps di Azure.

La struttura di base di codice usata in questo esempio è simile a quanto segue:

* Un' **i modelli arm** cartella, contenente un numero di modelli di Azure Resource Manager. I modelli sono illustrati in questo articolo.
* Oggetto **dell'applicazione client** cartella, ovvero una copia delle [Azure .NET File di elaborazione Batch con l'applicazione ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) esempio. Ciò non è necessaria per questo articolo.
* Un' **applicazione hpc** cartella, ovvero il Windows 64-bit della versione [ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).
* Oggetto **pipeline** cartella. Contiene un file YAML del nostro processo di compilazione la struttura. Questo argomento viene discusso in questo articolo.

In questa sezione presuppone che si abbia familiarità con modelli di Resource Manager la progettazione e controllo di versione. Se non si ha familiarità con questi concetti, vedere le pagine seguenti per altre informazioni.

* [Che cos'è il codice sorgente?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)

#### <a name="azure-resource-manager-templates"></a>Modelli di Gestione risorse di Azure

In questo esempio si basa su più modelli di Resource Manager per distribuire la nostra soluzione. A tale scopo, utilizziamo un numero di modelli di funzionalità (simili a moduli o unità) che implementano una parte specifica della funzionalità. È anche possibile usare un modello di soluzione end-to-end che è responsabile per riportare questi sottostanti insieme di funzionalità. Esistono un paio di vantaggi di questo approccio:

* I modelli di funzionalità sottostante possono essere singolarmente unit testata.
* I modelli di funzionalità sottostante possono essere definiti come standard all'interno di un'organizzazione ed essere riusati in più soluzioni.

In questo esempio è presente un modello di soluzione end-to-end (Deployment) che distribuisce tre modelli. I modelli sottostanti sono modelli di funzionalità, responsabili della distribuzione di un aspetto specifico della soluzione.

![Esempio di struttura del modello collegati usando i modelli di Azure Resource Manager](media/batch-ci-cd/ARMTemplateHierarchy.png)

Il primo modello che verrà esaminato è per un Account di archiviazione di Azure. La soluzione richiede un account di archiviazione per distribuire l'applicazione per l'Account Batch. È importante essere consapevoli della presenza di [Guida di riferimento di modello di Resource Manager per i tipi di risorsa Microsoft. Storage](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) durante la creazione di modelli di Resource Manager per gli account di archiviazione.

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

Successivamente, si esaminerà il modello di Account di Azure Batch. L'Account di Batch di Azure funge da piattaforma per l'esecuzione di numerose applicazioni in pool (raggruppamenti di computer). È importante essere consapevoli della presenza di [Guida di riferimento di modello di Resource Manager per i tipi di risorse di batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) durante la creazione di modelli di Resource Manager per gli account Batch.

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

Il modello seguente illustra un esempio di creazione di un Pool di Batch di Azure (macchine back-end per elaborare le nostre applicazioni). È importante essere consapevoli della presenza di [Guida di riferimento di modello di Resource Manager per i tipi di risorse di batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) durante la creazione di modelli di Resource Manager per i pool di Account Batch.

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

Infine, è disponibile un modello che funge da simile a un agente di orchestrazione. Questo modello è responsabile della distribuzione di modelli di funzionalità.

È inoltre possibile scoprire informazioni sul [creazione di modelli di Azure Resource Manager collegati](../azure-resource-manager/resource-manager-tutorial-create-linked-templates.md) in un articolo separato.

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

L'infrastruttura e il software può essere definite come codice e condivideranno il percorso nello stesso repository.

Per questa soluzione, di ffmpeg viene utilizzato come il pacchetto dell'applicazione. È possibile scaricare il pacchetto ffmpeg [qui](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).

![Struttura di Git Repository di esempio](media/batch-ci-cd/git-repository.jpg)

Esistono quattro sezioni principali a questo repository:

* Il **i modelli arm** cartella che archivia la nostra infrastruttura come codice
* Il **applicazione hpc** cartella che contiene i file binari per ffmpeg
* Il **pipeline** cartella che contiene la definizione per la nostra pipeline di compilazione.
* **Facoltativo**: Il **dell'applicazione client** cartella sarebbe per memorizzare il codice per applicazioni .NET. Non useremo queste nell'esempio, ma in un progetto, è preferibile per l'esecuzione dell'applicazione Batch HPC tramite un'applicazione client.

> [!NOTE]
> Questo è solo un esempio di una struttura di una codebase. Questo approccio viene usato per dimostrare che l'applicazione, infrastruttura e codice della pipeline vengono archiviati nello stesso repository.

Ora che il codice sorgente è configurato, è possibile iniziare la prima compilazione.

## <a name="continuous-integration"></a>Integrazione continua

[Le pipeline di Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops), all'interno di servizi di Azure DevOps, consente di implementare una pipeline di compilazione, test e distribuzione per le applicazioni.

In questa fase della pipeline, in genere vengono eseguiti test per convalidare il codice e sviluppare i componenti appropriati del software. Il numero e tipi di test e le altre attività che si esegue verranno variano a seconda della compilazione più ampia e strategia di rilascio.

## <a name="preparing-the-hpc-application"></a>Preparazione dell'applicazione HPC

In questo esempio, esamineremo le **applicazione hpc** cartella. Il **applicazione hpc** cartella è il software di ffmpeg che verrà eseguito da all'interno dell'account Azure Batch.

1. Passare alla sezione le compilazioni delle pipeline di Azure dell'organizzazione di Azure DevOps. Creare un **nuova pipeline**.

    ![Creare una nuova Pipeline di compilazione](media/batch-ci-cd/new-build-pipeline.jpg)

1. Sono disponibili due opzioni per creare una pipeline di compilazione:

    a. [Usando la finestra di progettazione visiva](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Per usarlo, fare clic su "Usare la finestra di progettazione visiva" sul **nuova pipeline** pagina.

    b. [Usa il linguaggio YAML Compila](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). È possibile creare una nuova pipeline YAML scegliendo l'opzione di GitHub nella nuova pagina della pipeline o archivi di Azure. In alternativa, è possibile archiviare l'esempio seguente nel controllo del codice sorgente e fare riferimento a un file YAML esistente facendo clic nella finestra di progettazione visiva e quindi utilizzando il modello YAML.

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

1. Dopo la compilazione è configurata in base alle esigenze, selezionare **Salva e accoda**. Se hai attivato l'integrazione continua (nelle **trigger** sezione), la compilazione attiverà automaticamente quando viene effettuato un nuovo commit nel repository, che soddisfano le condizioni impostato nella compilazione.

    ![Esempio di una Pipeline di compilazione esistente](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Visualizzare aggiornamenti in tempo reale sullo stato di avanzamento della compilazione in DevOps di Azure, passare al **compilazione** sezione della pipeline di Azure. Selezionare la build appropriata dalla definizione di compilazione.

    ![Visualizza output in tempo reale dalla compilazione](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Se si usa un'applicazione client per eseguire l'applicazione di Batch HPC, è necessario creare una definizione di compilazione separato per l'applicazione. È possibile trovare una serie di guide alle procedure nel [pipeline di Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) documentazione.

## <a name="continuous-deployment"></a>Distribuzione continua

Le pipeline di Azure può essere usata anche per distribuire l'applicazione e l'infrastruttura sottostante. [Pipeline di versione](https://docs.microsoft.com/azure/devops/pipelines/release/what-is-release-management?view=azure-devops) è il componente che abilita la distribuzione continua e automatizza il processo di rilascio.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Distribuzione dell'applicazione e l'infrastruttura sottostante

Esistono una serie di passaggi necessari per distribuire l'infrastruttura. Come abbiamo utilizzato [modelli collegati](../azure-resource-manager/resource-group-linked-templates.md), tali modelli dovrà essere accessibile da un endpoint pubblico (HTTP o HTTPS). Potrebbe trattarsi di un repository in GitHub, o un Account di archiviazione Blob di Azure o un'altra posizione di archiviazione. Gli elementi di modello è stato caricato possono rimanere protette, come possono essere contenuti in una modalità privata ma accessibili tramite una forma di token di firma di accesso condiviso. Nell'esempio seguente viene illustrato come distribuire un'infrastruttura con i modelli da un blob di archiviazione di Azure.

1. Creare un **nuova definizione di versione**e selezionare una definizione vuota. È quindi necessario rinominare l'ambiente appena creato con un nome rilevante per la pipeline.

    ![Pipeline di rilascio iniziale](media/batch-ci-cd/Release-0.jpg)

1. Creare una dipendenza alla pipeline di compilazione per ottenere l'output per la nostra applicazione HPC.

    > [!NOTE]
    > Si noti ancora una volta, il **Alias di origine**, come questo valore sarà necessario quando le attività vengono create all'interno della definizione di versione.

    ![Creare un collegamento dell'artefatto per il HPCApplicationPackage nella pipeline di compilazione appropriato](media/batch-ci-cd/Release-1.jpg)

1. Creare un collegamento a un altro elemento, questa volta, un repository di Azure. Questa è necessaria per accedere ai modelli di Resource Manager archiviati nel repository. Come modelli di Resource Manager non richiedono la compilazione, non è necessario eseguirne il push tramite una pipeline di compilazione.

    > [!NOTE]
    > Si noti ancora una volta, il **Alias di origine**, come questo valore sarà necessario quando le attività vengono create all'interno della definizione di versione.

    ![Creare un collegamento dell'artefatto per il repository di Azure](media/batch-ci-cd/Release-2.jpg)

1. Passare il **variabili** sezione. È consigliabile creare una serie di variabili nella pipeline, in modo che non si inserendo le stesse informazioni in più attività. Queste sono le variabili utilizzate in questo esempio, e il relativo impatto della distribuzione.

    * **applicationStorageAccountName**: Nome dell'Account di archiviazione per contenere i file binari dell'applicazione HPC
    * **batchAccountApplicationName**: Nome dell'applicazione nell'Account Azure Batch
    * **batchAccountName**: Nome dell'Account Azure Batch
    * **batchAccountPoolName**: Nome del pool di macchine virtuali che esegue l'elaborazione
    * **batchApplicationId**: ID univoco per l'applicazione di Azure Batch
    * **batchApplicationVersion**: Versione semantica dell'applicazione batch (vale a dire, i file binari ffmpeg)
    * **location**: Percorso per le risorse di Azure da distribuire
    * **resourceGroupName**: Nome del gruppo di risorse da creare, e in cui verranno distribuite le risorse
    * **storageAccountName**: Nome dell'Account di archiviazione per contenere i modelli di Resource Manager collegati

    ![Esempio di variabili impostate per il rilascio di pipeline di Azure](media/batch-ci-cd/Release-4.jpg)

1. Passare alle attività per l'ambiente di sviluppo. Nel seguente snapshot, si può vedere sei attività. Queste attività verranno: scaricare i file ZIP ffmpeg, distribuire un account di archiviazione per ospitare i modelli di Resource Manager nidificati, copiare tali modelli di Resource Manager per l'account di archiviazione, distribuire l'account batch e le dipendenze necessarie, creare un'applicazione in l'Account di Azure Batch e caricare il pacchetto dell'applicazione nell'Account di Azure Batch.

    ![Esempio di attività utilizzate per rilasciare l'applicazione HPC in Azure Batch](media/batch-ci-cd/Release-3.jpg)

1. Aggiungere il **Download dell'artefatto di Pipeline (anteprima)** attività e impostare le proprietà seguenti:
    * **Nome visualizzato:** Scaricare il pacchetto dell'applicazione all'agente
    * **Il nome dell'artefatto da scaricare:** applicazione hpc
    * **Percorso per il download di**: $(System.DefaultWorkingDirectory)

1. Creare un Account di archiviazione per archiviare gli elementi. Un account di archiviazione esistente dalla soluzione potrebbe essere utilizzato, ma per l'esempio autonoma e l'isolamento del contenuto, siamo in un account di archiviazione dedicato per i nostri elementi (in particolare i modelli di Resource Manager).

    Aggiungere il **distribuzione gruppo di risorse di Azure** attività e impostare le proprietà seguenti:
    * **Nome visualizzato:** Distribuire Account di archiviazione per i modelli di Resource Manager
    * **Sottoscrizione di Azure:** Selezionare la sottoscrizione Azure appropriata
    * **Azione**: Creare o aggiornare un gruppo di risorse
    * **Gruppo di risorse**: $ (resourcegroupname)
    * **Percorso**: $(location)
    * **Template**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.json
    * **Esegui override dei parametri di modello**: - accountName $(storageAccountName)

1. Caricare gli artefatti dal controllo del codice sorgente nell'Account di archiviazione. È un'attività della Pipeline di Azure per eseguire questa operazione. Come parte di questa attività, l'URL di contenitore di Account di archiviazione e il Token di firma di accesso condiviso può da includere nell'output a una variabile nelle pipeline di Azure. Ciò significa che questa possa essere riutilizzata in tutta questa fase agente.

    Aggiungere il **copia dei File di Azure** attività e impostare le proprietà seguenti:
    * **Source:** $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/
    * **Tipo di connessione Azure**: Azure Resource Manager
    * **Sottoscrizione di Azure:** Selezionare la sottoscrizione Azure appropriata
    * **Tipo di destinazione**: BLOB Azure
    * **Account di archiviazione di Resource Manager**: $(storageAccountName)
    * **Nome del contenitore**: i modelli
    * **URI contenitore di archiviazione**: templateContainerUri
    * **Token di firma di accesso condiviso contenitore di archiviazione**: templateContainerSasToken

1. Distribuire il modello dell'agente di orchestrazione. È importante ricordare il modello dell'agente di orchestrazione in precedenza, si noterà che sono parametri per l'URL del contenitore Account di archiviazione, oltre al token di firma di accesso condiviso. Si noterà che le variabili necessarie nel modello di Resource Manager vengono mantenute sia nella sezione delle variabili della definizione di versione, o sono state impostate da un'altra attività di pipeline di Azure (ad esempio, da parte dell'attività di copia dei Blob di Azure).

    Aggiungere il **distribuzione gruppo di risorse di Azure** attività e impostare le proprietà seguenti:
    * **Nome visualizzato:** Distribuire Azure Batch
    * **Sottoscrizione di Azure:** Selezionare la sottoscrizione Azure appropriata
    * **Azione**: Creare o aggiornare un gruppo di risorse
    * **Gruppo di risorse**: $ (resourcegroupname)
    * **Percorso**: $(location)
    * **Template**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.json
    * **Esegui override dei parametri di modello**: ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Una procedura comune consiste nell'usare le attività di Azure Key Vault. Se l'entità servizio (connessione alla sottoscrizione di Azure) è un criteri di accesso appropriati impostata, può scaricare i segreti da Azure Key Vault e utilizzabile come variabili nella pipeline. Il nome della chiave privata verrà impostato con il valore associato. Ad esempio, può fare riferimento una chiave privata di sshPassword con $(sshPassword) nella definizione di versione.

1. I passaggi successivi chiamare il comando di Azure. Il primo viene utilizzato per creare un'applicazione in Azure Batch. e caricare i pacchetti associati.

    Aggiungere il **CLI Azure** attività e impostare le proprietà seguenti:
    * **Nome visualizzato:** Creare l'applicazione nell'Account di Azure Batch
    * **Sottoscrizione di Azure:** Selezionare la sottoscrizione Azure appropriata
    * **Percorso dello script**: Script inline
    * **Script inline**: ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. Il secondo passaggio viene usato per caricare i pacchetti associati all'applicazione. In questo caso, i file di ffmpeg.

    Aggiungere il **CLI Azure** attività e impostare le proprietà seguenti:
    * **Nome visualizzato:** Carica pacchetto all'Account Azure Batch
    * **Sottoscrizione di Azure:** Selezionare la sottoscrizione Azure appropriata
    * **Percorso dello script**: Script inline
    * **Script inline**: ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Il numero di versione del pacchetto dell'applicazione è impostato su una variabile. Questo risulta utile se sovrascrivere le versioni precedenti del pacchetto adatta a te e se si desidera controllare manualmente il numero di versione del pacchetto eseguito il push in Azure Batch.

1. Creare un nuovo rilascio selezionando **versione > creare un nuovo rilascio**. Una volta attivato, selezionare il collegamento per la nuova versione per visualizzare lo stato.

1. È possibile visualizzare l'output in tempo reale dall'agente selezionando il **registri** pulsante sotto l'ambiente.

    ![Visualizzare lo stato del rilascio](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>L'ambiente di test

Dopo aver configurato l'ambiente, verificare che i test seguenti possono essere sia stata completata.

Connettersi al nuovo Account Batch di Azure, tramite la CLI di Azure da un prompt dei comandi di PowerShell.

* Accedere al proprio account Azure con `az login` e seguire le istruzioni per l'autenticazione.
* Ora eseguire l'autenticazione dell'account Batch: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Elencare le applicazioni disponibili

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Selezionare che il pool è valido

```azurecli
az batch pool list
```

Prendere nota del valore di `currentDedicatedNodes` dall'output di questo comando. Questo valore viene regolato del test successivo.

#### <a name="resize-the-pool"></a>Ridimensionare il pool

Ridimensionare il pool in modo che sono disponibili per i processi e attività test di nodi di calcolo, controllare con il comando di elenco di pool per visualizzare lo stato corrente fino a quando il ridimensionamento è stata completata e non vi sono nodi disponibili

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Passaggi successivi

Oltre a questo articolo, sono disponibili due esercitazioni che usano ffmpeg, usando .NET e Python. Vedere queste esercitazioni per altre informazioni su come interagire con un account Batch tramite una semplice applicazione.

* [Eseguire un carico di lavoro parallelo con Azure Batch usando l'API Python](tutorial-parallel-python.md)
* [Eseguire un carico di lavoro parallelo con Azure Batch usando l'API .NET](tutorial-parallel-dotnet.md)
