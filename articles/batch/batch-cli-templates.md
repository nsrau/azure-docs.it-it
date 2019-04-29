---
title: Eseguire processi end-to-end usando i modelli - Azure Batch | Microsoft Docs
description: Creare pool, processi e attività di Batch con file di modelli e l'interfaccia della riga di comando di Azure.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 80d2e995a18a2d6dafbb8d92fdd5996b10eab17c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60783739"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>Usare il trasferimento di file e i modelli dell'interfaccia della riga di comando di Azure Batch

Usando un'estensione Azure Batch per l'interfaccia della riga di comando di Azure è possibile eseguire processi di Batch senza scrittura del codice.

Creare e usare file modello JSON con l'interfaccia della riga di comando di Azure per creare pool, processi e attività di Batch. Usare i comandi di estensione dell'interfaccia della riga di comando per caricare facilmente i file di input dei processi nell'account di archiviazione associato all'account Batch e scaricare i file di output dei processi.

## <a name="overview"></a>Panoramica

Un'estensione dell'interfaccia della riga di comando di Azure consente a utenti non sviluppatori di usare Batch end-to-end. Con i soli comandi dell'interfaccia della riga di comando, è possibile creare un pool, caricare dati di input, creare processi e attività associate e scaricare i dati di output risultanti. Non è necessario alcun codice aggiuntivo. Eseguire i comandi dell'interfaccia della riga di comando direttamente o integrarli negli script.

I modelli di Batch sono basati sul [supporto di Batch esistente nell'interfaccia della riga di comando di Azure](batch-cli-get-started.md#json-files-for-resource-creation) per i file JSON e consentono di specificare i valori delle proprietà per la creazione di pool, processi, attività e altri elementi. I modelli di Batch aggiungono le funzionalità seguenti:

-   È possibile definire parametri. Quando viene usato il modello, per creare l'elemento vengono specificati solo i valori dei parametri, mentre gli altri valori delle proprietà dell'elemento sono specificati nel corpo del modello. Un utente che conosce Batch e le applicazioni che devono essere eseguite da Batch può creare modelli, specificando i valori delle proprietà di pool, processi e attività. Un utente che ha meno familiarità con Batch e/o con le applicazioni può specificare solo i valori per i parametri definiti.

-   Le factory delle attività di processo creano una o più attività associate a un processo, evitando di dover creare diverse definizioni di attività e semplificando così notevolmente l'invio di processi.


I processi in genere usano file di dati di input e producono file di dati di output. Un account di archiviazione è associato, per impostazione predefinita, ad ogni account Batch. È possibile trasferire i file da e verso questo account di archiviazione usando l'interfaccia della riga di comando, senza alcun codice e senza credenziali di archiviazione.

Ad esempio, [ffmpeg](https://ffmpeg.org/) è una diffusa applicazione che elabora i file audio e video. Di seguito è illustrata la procedura con l'interfaccia della riga di comando di Azure Batch per richiamare ffmpeg per la transcodifica di file video di origine in file con risoluzioni diverse.

-   Creare un modello di pool. L'utente che crea il modello sa come chiamare l'applicazione ffmpeg e conosce i relativi requisiti. Specifica i valori appropriati per sistema operativo, dimensioni VM, modalità di installazione di ffmpeg (ad esempio da un pacchetto dell'applicazione o usando uno strumento di gestione pacchetti) e altri valori delle proprietà del pool. I parametri vengono creati in modo che quando il modello viene usato, è necessario specificare solo l'ID del pool e il numero di VM.

-   Creare un modello di processo. L'utente che crea il modello sa come si deve richiamare ffmpeg per eseguire la transcodifica di un video di origine in un file con una risoluzione diversa e specifica la riga di comando dell'attività. L'utente sa inoltre che è presente una cartella contenente i file video di origine, con un'attività necessaria per ogni file di input.

-   Un utente finale con un set di file video di cui eseguire la transcodifica deve prima di tutto creare un pool usando il modello di pool, specificando solo l'ID del pool e il numero di VM necessarie. Può quindi caricare i file di origine per la transcodifica. È quindi possibile inviare un processo usando il modello di processo, specificando solo l'ID del pool e il percorso dei file di origine caricati. Viene creato il processo di batch con un'attività per ogni file di input generato. Infine, i file di output transcodificati possono essere scaricati.

## <a name="installation"></a>Installazione

Per installare l'estensione dell'interfaccia della riga di comando di Azure Batch, innanzitutto [installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli) oppure eseguire l'interfaccia in [Azure Cloud Shell](../cloud-shell/overview.md).

Installare la versione più recente dell'estensione di Batch mediante il comando dell'interfaccia della riga di comando di Azure seguente:

```azurecli
az extension add --name azure-batch-cli-extensions
```

Per altre informazioni sulle estensioni dell'interfaccia della riga di comando Batch e per altre opzioni di installazione, consultare il [repository GitHub](https://github.com/Azure/azure-batch-cli-extensions).


Per usare le funzionalità di estensione dell'interfaccia della riga di comando, è necessario un account Azure Batch e, per i comandi che trasferiscono file da e verso una risorsa di archiviazione, un account di archiviazione collegato.

Per accedere a un account Batch con l'interfaccia della riga di comando di Azure, vedere [Gestire le risorse di Batch con l'interfaccia della riga di comando di Azure](batch-cli-get-started.md).

## <a name="templates"></a>Modelli

I modelli di Azure Batch sono simili ai modelli di Azure Resource Manager per quanto riguarda funzionalità e sintassi. Si tratta di file JSON contenenti nomi e valori delle proprietà degli elementi, ma con i seguenti importanti concetti aggiuntivi:

-   **Parameters**

    -   I valori delle proprietà possono essere specificati in una sezione del corpo, in modo che quando il modello viene usato sia necessario fornire solo i valori dei parametri. È ad esempio possibile inserire la definizione completa di un pool nel corpo e definire un solo parametro come ID del pool. Per creare un pool deve quindi essere fornita solo una stringa di ID del pool.
        
    -   Il corpo del modello può essere creato da un utente che conosce Batch e le applicazioni che devono essere eseguite da Batch. Quando il modello viene usato, è necessario fornire solo i valori per i parametri definiti dall'autore. Un utente senza conoscenza approfondita di Batch e/o delle applicazioni può quindi usare i modelli.

-   **Variabili**

    -   È possibile specificare valori dei parametri semplici o complessi in un'unica posizione e usarli in una o più posizioni nel corpo del modello. Le variabili possono semplificare il modello e ridurne le dimensioni, oltre che renderne più facile la gestione, grazie alla presenza di un'unica posizione per la modifica delle proprietà.

-   **Costrutti di livello superiore**

    -   Nel modello sono disponibili alcuni costrutti di livello superiore che non sono ancora disponibili nelle API di Batch. È ad esempio possibile definire una factory di attività in un modello di processo che crea più attività per il processo usando una definizione di attività comune. Questi costrutti evitano la necessità di usare codice per creare dinamicamente più file JSON, ad esempio un file per ogni attività, oltre che di creare file di script per installare le applicazioni tramite uno strumento di gestione pacchetti.

    -   A un certo punto, questi costrutti possono essere aggiunti al servizio Batch e vengono resi disponibili nelle API di Batch, nell'interfaccia utente e in altre posizioni.

### <a name="pool-templates"></a>Modelli di pool

I modelli di pool supportano le funzionalità standard di parametri e variabili dei modelli. Supportano anche il costrutto di livello superiore seguente:

-   **Riferimenti ai pacchetti**

    -   Facoltativamente, è possibile copiare il software in nodi del pool usando strumenti di gestione dei pacchetti. Vengono specificati lo strumento di gestione dei pacchetti e l'ID del pacchetto. Dichiarando uno o più pacchetti, si evita di creare uno script che ottiene i pacchetti necessari, installare lo script ed eseguirlo in ogni nodo del pool.

Di seguito è riportato un esempio di un modello che crea un pool di macchine virtuali Linux con ffmpeg installato. Per usarlo, fornire solo una stringa di ID del pool e il numero di macchine virtuali nel pool:

```json
{
    "parameters": {
        "nodeCount": {
            "type": "int",
            "metadata": {
                "description": "The number of pool nodes"
            }
        },
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The pool ID "
            }
        }
    },
    "pool": {
        "type": "Microsoft.Batch/batchAccounts/pools",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('poolId')]",
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "Canonical",
                    "offer": "UbuntuServer",
                    "sku": "16.04-LTS",
                    "version": "latest"
                },
                "nodeAgentSKUId": "batch.node.ubuntu 16.04"
            },
            "vmSize": "STANDARD_D3_V2",
            "targetDedicatedNodes": "[parameters('nodeCount')]",
            "enableAutoScale": false,
            "maxTasksPerNode": 1,
            "packageReferences": [
                {
                    "type": "aptPackage",
                    "id": "ffmpeg"
                }
            ]
        }
    }
}
```

Se il nome del file del modello fosse _pool-ffmpeg.json_, richiamare il modello nel modo seguente:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

L'interfaccia della riga di comando richiede di specificare i valori per i parametri `poolId` e `nodeCount`. È anche possibile indicare i parametri in un file JSON. Ad esempio: 

```json
{
  "poolId": {
    "value": "mypool"
  },
  "nodeCount": {
    "value": 2
  }
}
```

Se il nome del file JSON di parametri fosse *pool-parameters.json*, richiamare il modello nel modo seguente:

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>Modelli di processo

I modelli di processo supportano le funzionalità standard di parametri e variabili dei modelli. Supportano anche il costrutto di livello superiore seguente:

-   **Factory di attività**

    -   Vengono create più attività per un processo da una definizione di attività. Sono supportati tre tipi di factory di attività: processo parametrico di organizzazione, attività per file e raccolta di attività.

Di seguito è riportato un esempio di un modello che crea un processo per eseguire la transcodifica di file video MP4 in file con una di due risoluzioni inferiori usando ffmpeg. Viene creata un'attività per ogni file video di origine. Vedere [Gruppi e trasferimenti di file](#file-groups-and-file-transfer) per altre informazioni sui gruppi di file per l'input e l'output dei processi.

```json
{
    "parameters": {
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch pool which runs the job"
            }
        },
        "jobId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch job"
            }
        },
        "resolution": {
            "type": "string",
            "defaultValue": "428x240",
            "allowedValues": [
                "428x240",
                "854x480"
            ],
            "metadata": {
                "description": "Target video resolution"
            }
        }
    },
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('jobId')]",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "[parameters('poolId')]"
            },
            "taskFactory": {
                "type": "taskPerFile",
                "source": { 
                    "fileGroup": "ffmpeg-input"
                },
                "repeatTask": {
                    "commandLine": "ffmpeg -i {fileName} -y -s [parameters('resolution')] -strict -2 {fileNameWithoutExtension}_[parameters('resolution')].mp4",
                    "resourceFiles": [
                        {
                            "blobSource": "{url}",
                            "filePath": "{fileName}"
                        }
                    ],
                    "outputFiles": [
                        {
                            "filePattern": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                            "destination": {
                                "autoStorage": {
                                    "path": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                                    "fileGroup": "ffmpeg-output"
                                }
                            },
                            "uploadOptions": {
                                "uploadCondition": "TaskSuccess"
                            }
                        }
                    ]
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Se il nome del file del modello fosse _job-ffmpeg.json_, richiamare il modello nel modo seguente:

```azurecli
az batch job create --template job-ffmpeg.json
```

Come in precedenza, l'interfaccia della riga di comando richiede di specificare i valori per i parametri. È anche possibile indicare i parametri in un file JSON.

### <a name="use-templates-in-batch-explorer"></a>Usare i modelli in Batch Explorer

È possibile caricare un modello dell'interfaccia della riga di comando Batch per l'applicazione desktop [Batch Explorer](https://github.com/Azure/BatchExplorer) (in precedenza denominata BatchLabs) per creare un pool o un processo di Batch. È anche possibile selezionare da pool e modelli di processi predefiniti nella raccolta di Batch Explorer.

Per caricare un modello:

1. In Batch Explorer selezionare **Raccolta** > **Modelli locali**.

2. Selezionare o trascinare un pool o un modello di processo locale.

3. Selezionare **Usa questo modello** e seguire le istruzioni visualizzate.

## <a name="file-groups-and-file-transfer"></a>Gruppi e trasferimenti di file

La maggior parte dei processi e delle attività richiede file di input e produce file di output. In genere, sia i file di input che i file di output devono essere trasferiti dal client al nodo o dal nodo al client. L'estensione dell'interfaccia della riga di comando di Azure Batch consente di evitare il trasferimento di file e usa l'account di archiviazione associabile a ogni account Batch.

Un gruppo di file equivale a un contenitore creato nell'account di archiviazione di Azure. Il gruppo di file può avere sottocartelle.

L'estensione dell'interfaccia della riga di comando di Batch fornisce comandi che consentono di caricare i file da un client a un gruppo di file specificato e di scaricare i file dal gruppo di file specificato a un client.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

I modelli di processo e di pool consentono di specificare che i file archiviati in gruppi di file vengano copiati nei nodi del pool o dai nodi del pool a un gruppo di file. Ad esempio, nel modello di processo specificato in precedenza, il filegroup *ffmpeg-input* viene specificato per la factory delle attività come il percorso dei file video di origine copiati nel nodo per la transcodifica. Il filegroup *ffmpeg-output* è la posizione in cui vengono copiati i file di output transcodificati dal nodo che esegue ogni attività.

## <a name="summary"></a>Riepilogo

Il supporto per i modelli e il trasferimento di file è stato attualmente aggiunto solo all'interfaccia della riga di comando di Azure. L'obiettivo è quello di allargare il numero di utenti che possono usare Batch, consentendone l'utilizzo anche a ricercatori e utenti IT che non hanno la necessità di sviluppare codice usando le API di Batch. Senza dover scrivere il codice, gli utenti che conoscono Azure Batch e le applicazioni che devono essere eseguite da Batch possono creare modelli per la creazione di pool e processi. Con i parametri dei modelli, gli utenti che non hanno una conoscenza approfondita di Batch e delle applicazioni possono usare i modelli.

Provare l'estensione Batch per l'interfaccia della riga di comando di Azure e inviare feedback o suggerimenti nella sezione Commenti dell'articolo o tramite il [repository della community di Batch](https://github.com/Azure/Batch).

## <a name="next-steps"></a>Passaggi successivi

- Documentazione dettagliata su installazione e utilizzo, esempi e codice sorgente sono disponibili nel [repository GitHub di Azure](https://github.com/Azure/azure-batch-cli-extensions).

- Altre informazioni sull'uso di [Batch Explorer](https://github.com/Azure/BatchExplorer) per creare e gestire le risorse di Batch.
