---
title: Eseguire processi di Azure Batch end-to-end senza scrivere codice (anteprima) | Microsoft Docs
description: "Creare file modello per l'interfaccia della riga di comando di Azure per creare pool, processi e attività di Batch."
services: batch
author: mscurrell
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 12/18/2017
ms.author: markscu
ms.openlocfilehash: c2d4a33cd6c4c9db608a76b24935b474b551b291
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="use-azure-batch-cli-templates-and-file-transfer-preview"></a>Usare il trasferimento di file e i modelli dell'interfaccia della riga di comando di Azure Batch (anteprima)

Usando l'interfaccia della riga di comando di Azure è possibile eseguire processi di Batch senza scrittura del codice.

Creare e usare file modello con l'interfaccia della riga di comando di Azure per creare pool, processi e attività di Batch. I file di input dei processi possono essere caricati facilmente nell'account di archiviazione associato all'account Batch. È inoltre possibile scaricare i file di output dei processi.

## <a name="overview"></a>Panoramica

Un'estensione dell'interfaccia della riga di comando di Azure consente a utenti non sviluppatori di usare Batch end-to-end. È possibile creare un pool, caricare i dati di input, creare processi e attività associate e scaricare i dati di output risultanti senza necessità di codice, usando direttamente l'interfaccia della riga di comando o integrandola in script.

I modelli di Batch sono basati sul [supporto di Batch esistente nell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/batch/batch-cli-get-started#json-files-for-resource-creation), che consente ai file JSON di specificare i valori delle proprietà per la creazione di pool, processi, attività e altri elementi. Con i modelli di Batch, vengono aggiunte le funzionalità seguenti a ciò che è possibile fare con i file JSON:

-   È possibile definire parametri. Quando viene usato il modello, per creare l'elemento vengono specificati solo i valori dei parametri, mentre gli altri valori delle proprietà dell'elemento sono specificati nel corpo del modello. Un utente che conosce Batch e le applicazioni che devono essere eseguite da Batch può creare modelli, specificando i valori delle proprietà di pool, processi e attività. Un utente che ha meno familiarità con Batch e/o con le applicazioni può specificare solo i valori per i parametri definiti.

-   Le factory delle attività di processo creano una o più attività associate a un processo, evitando di dover creare diverse definizioni di attività e semplificando così notevolmente l'invio di processi.


È necessario fornire file di dati di input per i processi e spesso vengono generati file di dati di output. Per impostazione predefinita, a ogni account Batch è associato un account di archiviazione e i file possono essere facilmente trasferiti da e verso questo account di archiviazione usando l'interfaccia della riga di comando, senza necessità di codici o credenziali di archiviazione.

Ad esempio, [ffmpeg](http://ffmpeg.org/) è una diffusa applicazione che elabora i file audio e video. L'interfaccia della riga di comando di Azure Batch può essere usata per richiamare ffmpeg per la transcodifica di file video di origine in file con risoluzioni diverse.

-   Viene creato un modello di pool. L'utente che crea il modello sa come chiamare l'applicazione ffmpeg e conosce i relativi requisiti. Specifica i valori appropriati per sistema operativo, dimensioni VM, modalità di installazione di ffmpeg (ad esempio da un pacchetto dell'applicazione o usando uno strumento di gestione pacchetti) e altri valori delle proprietà del pool. I parametri vengono creati in modo che quando il modello viene usato, è necessario specificare solo l'ID del pool e il numero di VM.

-   Viene creato un modello di processo. L'utente che crea il modello sa come si deve richiamare ffmpeg per eseguire la transcodifica di un video di origine in un file con una risoluzione diversa e specifica la riga di comando dell'attività. L'utente sa inoltre che è presente una cartella contenente i file video di origine, con un'attività necessaria per ogni file di input.

-   Un utente finale con un set di file video di cui eseguire la transcodifica deve prima di tutto creare un pool usando il modello di pool, specificando solo l'ID del pool e il numero di VM necessarie. Può quindi caricare i file di origine per la transcodifica. È quindi possibile inviare un processo usando il modello di processo, specificando solo l'ID del pool e il percorso dei file di origine caricati. Viene creato il processo di batch con un'attività per ogni file di input generato. Infine, i file di output transcodificati possono essere scaricati.

## <a name="installation"></a>Installazione

Il modello e le funzionalità di trasferimento di file richiedono l'installazione di un'estensione.

Per istruzioni su come installare l'interfaccia della riga di comando di Azure, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

Dopo avere installato l'interfaccia della riga di comando di Azure, è possibile installare la versione più recente dell'estensione di Batch mediante il comando dell'interfaccia della riga di comando seguente:

```azurecli
az extension add --source https://github.com/Azure/azure-batch-cli-extensions/releases/download/azure-batch-cli-extensions-2.0.1/azure_batch_cli_extensions-2.0.1-py2.py3-none-any.whl
```

Per ulteriori informazioni sull'estensione del Batch, vedere [Estensioni per interfaccia della riga di comando Batch di Microsoft Azure per Windows, Mac e Linux](https://github.com/Azure/azure-batch-cli-extensions#microsoft-azure-batch-cli-extensions-for-windows-mac-and-linux).

## <a name="templates"></a>Modelli

L'interfaccia della riga di comando di Azure Batch consente la creazione di elementi, come pool, processi e attività, specificando un file JSON contenente valori e nomi di proprietà. Ad esempio: 

```azurecli
az batch pool create –-json-file AppPool.json
```

I modelli di Azure Batch sono simili ai modelli di Azure Resource Manager per quanto riguarda funzionalità e sintassi. Si tratta di file JSON contenenti nomi e valori delle proprietà degli elementi, ma con i seguenti importanti concetti aggiuntivi:

-   **Parameters**

    -   I valori delle proprietà possono essere specificati in una sezione del corpo, in modo che quando il modello viene usato sia necessario fornire solo i valori dei parametri. È ad esempio possibile inserire la definizione completa di un pool nel corpo e definire un solo parametro come ID del pool. Per creare un pool deve quindi essere fornita solo una stringa di ID del pool.
        
    -   Il corpo del modello può essere creato da un utente che conosce Batch e le applicazioni che devono essere eseguite da Batch. Quando il modello viene usato, è necessario fornire solo i valori per i parametri definiti dall'autore. Un utente senza conoscenza approfondita di Batch e/o delle applicazioni può quindi usare i modelli.

-   **Variabili**

    -   È possibile specificare valori dei parametri semplici o complessi in un'unica posizione e usarli in una o più posizioni nel corpo del modello. Le variabili possono semplificare il modello e ridurne le dimensioni, oltre che rendere più facile la sua gestione, grazie alla presenza di un'unica posizione per modificare le proprietà il cui valore può cambiare.

-   **Costrutti di livello superiore**

    -   Nel modello sono disponibili alcuni costrutti di livello superiore che non sono ancora disponibili nelle API di Batch. È ad esempio possibile definire una factory di attività in un modello di processo che crea più attività per il processo usando una definizione di attività comune. Questi costrutti evitano la necessità di usare codice per creare dinamicamente più file JSON, ad esempio un file per ogni attività, oltre che creare file di script per installare le applicazioni tramite uno strumento di gestione pacchetti, ad esempio.

    -   A un certo punto, dove applicabile, questi costrutti possono essere aggiunti al servizio Batch e disponibili nelle API, nell'interfaccia utente e in altre posizioni di Batch.

### <a name="pool-templates"></a>Modelli di pool

Oltre alle funzionalità standard di parametri e variabili dei modelli, il modello di pool supporta i costrutti di livello superiore seguenti:

-   **Riferimenti ai pacchetti**

    -   Facoltativamente, è possibile copiare il software in nodi del pool usando strumenti di gestione dei pacchetti. Vengono specificati lo strumento di gestione dei pacchetti e l'ID del pacchetto. La possibilità di dichiarare uno o più pacchetti evita la necessità di creare uno script che ottiene i pacchetti necessari, installare lo script ed eseguire lo script in ogni nodo del pool.

Di seguito è riportato un esempio di un modello che crea un pool di VM Linux con l'applicazione ffmpeg installata e richiede solo una stringa di ID del pool e il numero di VM da fornire:

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
                    "sku": "16.04.0-LTS",
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

Se il nome del file del modello fosse _pool-ffmpeg.json_, sarebbe possibile richiamare il modello nel modo seguente:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

### <a name="job-templates"></a>Modelli di processo

Oltre alle funzionalità standard di parametri e variabili dei modelli, il modello di processo supporta i costrutti di livello superiore seguenti:

-   **Factory di attività**

    -   Vengono create più attività per un processo da una definizione di attività. Sono supportati tre tipi di factory di attività: processo parametrico di organizzazione, attività per file e raccolta di attività.

Di seguito è riportato un esempio di un modello che crea un processo che usa ffmpeg per eseguire la transcodifica di file video MP4 in file con una di due risoluzioni inferiori, con un'attività creata per ogni file video di origine:

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

Se il nome del file del modello fosse _job-ffmpeg.json_, sarebbe possibile richiamare il modello nel modo seguente:

```azurecli
az batch job create --template job-ffmpeg.json
```

## <a name="file-groups-and-file-transfer"></a>Gruppi e trasferimenti di file

La maggior parte dei processi e delle attività richiede file di input e produce file di output. Sia i file di input che i file di output in genere devono essere trasferiti dal client al nodo o dal nodo al client. L'estensione dell'interfaccia della riga di comando di Azure Batch consente di evitare il trasferimento di file e usa l'account di archiviazione creato per impostazione predefinita per ogni account Batch.

Un gruppo di file equivale a un contenitore creato nell'account di archiviazione di Azure. Il gruppo di file può avere sottocartelle.

L’estensione per interfaccia della riga di comando Batch fornisce comandi che consentono di caricare i file da un client a un gruppo di file specificato e di scaricare i file dal gruppo di file specificato a un client.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

I modelli di processo e di pool consentono di specificare che i file archiviati in gruppi di file vengano copiati nei nodi del pool o dai nodi del pool a un gruppo di file. Nel modello di processo illustrato in precedenza, ad esempio, viene specificato il gruppo di file "ffmpeg-input" per la factory di attività come posizione dei file video di origine copiati nel nodo per la transcodifica. Il gruppo di file "ffmpeg-output" viene usato come posizione in cui vengono copiati i file di output transcodificati dal nodo che esegue ogni attività.

## <a name="summary"></a>Summary

Il supporto per i modelli e il trasferimento di file è stato attualmente aggiunto solo all'interfaccia della riga di comando di Azure. L'obiettivo è quello di allargare il numero di utenti che possono usare Batch, consentendone l'utilizzo anche a ricercatori, utenti IT e così via che non hanno la necessità di sviluppare codice usando le API di Batch. Senza dover scrivere il codice, gli utenti che conoscono Azure Batch e le applicazioni che devono essere eseguite da Batch possono creare modelli per la creazione di pool e processi. Con i parametri dei modelli, gli utenti che non hanno una conoscenza approfondita di Batch e delle applicazioni possono usare i modelli.

Provare l'estensione Batch per l'interfaccia della riga di comando di Azure e fornire feedback o suggerimenti nella sezione Commenti dell'articolo o tramite il [forum di Azure Batch](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch).

## <a name="next-steps"></a>Passaggi successivi

- Vedere il post del blog sui modelli di Batch: [Processi in esecuzione di Azure Batch che usano l'interfaccia della riga di comando di Azure: non è richiesto alcun codice](https://azure.microsoft.com/en-us/blog/running-azure-batch-jobs-using-the-azure-cli-no-code-required/).
- Documentazione dettagliata su installazione e utilizzo, esempi e codice sorgente sono disponibili nel [repository GitHub di Azure](https://github.com/Azure/azure-batch-cli-extensions).
