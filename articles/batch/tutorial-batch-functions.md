---
title: Attivare un processo di Batch usando Funzioni di Azure
description: Esercitazione - Applicare il metodo OCR ai documenti digitalizzati quando vengono aggiunti a un BLOB di archiviazione
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc
ms.openlocfilehash: d5a5197227ff62ca0c610e2c4e269480690d3faf
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343103"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Esercitazione: Attivare un processo di Batch usando Funzioni di Azure

Questa esercitazione spiega come attivare un processo di Batch usando Funzioni di Azure. Verrà illustrato un esempio in cui ai documenti aggiunti a un contenitore BLOB del servizio di archiviazione di Azure viene applicato il riconoscimento ottico dei caratteri (OCR) tramite Azure Batch. Per semplificare l'elaborazione OCR, verrà configurata una funzione di Azure che esegue un processo OCR di Batch ogni volta che viene aggiunto un file nel contenitore BLOB.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Un account Azure Batch e un account di archiviazione di Azure collegato. Per altre informazioni su come creare e collegare account, vedere [Creare un account Batch](quick-create-portal.md#create-a-batch-account).
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Creare un pool e un processo di Batch usando Batch Explorer

In questa sezione si userà Batch Explorer per creare il pool e il processo di Batch per l'esecuzione delle attività OCR. 

### <a name="create-a-pool"></a>Creare un pool

1. Accedere a Batch Explorer con le credenziali di Azure.
1. Creare un pool selezionando **Pool** nella barra laterale sinistra e quindi facendo clic sul pulsante **Aggiungi** sopra il modulo di ricerca. 
    1. Scegliere un ID e un nome visualizzato. Per questo esempio si userà `ocr-pool`.
    1. Impostare il tipo di scala su **A dimensione fissa**e il numero di nodi dedicati su 3.
    1. Selezionare **Ubuntu 18.04-LTS** come sistema operativo.
    1. Scegliere `Standard_f2s_v2` come dimensione della macchina virtuale.
    1. Abilitare l'attività di avvio e aggiungere il comando `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"`. Assicurarsi di impostare l'identità dell'utente come **Task default user (Admin)** (Utente predefinito attività - Amministratore), per consentire alle attività di avvio di includere comandi con `sudo`.
    1. Selezionare **OK**.
### <a name="create-a-job"></a>Creare un processo

1. Creare un processo nel pool selezionando **Processi** nella barra laterale sinistra e quindi facendo clic sul pulsante **Aggiungi** sopra il modulo di ricerca. 
    1. Scegliere un ID e un nome visualizzato. Per questo esempio si userà `ocr-job`.
    1. Impostare il pool su `ocr-pool` o sul nome scelto per il pool.
    1. Selezionare **OK**.


## <a name="create-blob-containers"></a>Creare contenitori BLOB

Verranno ora creati i contenitori BLOB in cui archiviare i file di input e output per il processo OCR di Batch.

1. Accedere a Storage Explorer con le credenziali di Azure.
1. Usando l'account di archiviazione collegato all'account Batch, creare due contenitori BLOB (uno per i file di input e uno per i file di output) seguendo i passaggi in [Creare un contenitore BLOB](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#create-a-blob-container).

In questo esempio il contenitore di input è denominato `input` e in esso vengono inizialmente caricati per l'elaborazione tutti i documenti senza OCR. Il contenitore di output è denominato `output` e in esso il processo di Batch scrive i documenti elaborati con OCR.  
    * In questo esempio, il contenitore di input sarà denominato `input` e il contenitore di output `output`.  
    * Il contenitore di input è quello in cui vengono inizialmente caricati per l'elaborazione tutti i documenti senza OCR.  
    * Il contenitore di output è quello in cui il processo di Batch scrive i documenti elaborati con OCR.  

Creare una firma di accesso condiviso per il contenitore di output in Storage Explorer. A tale scopo, fare clic con il pulsante destro del mouse sul contenitore di output e scegliere **Get Shared Access Signature** (Ottieni firma di accesso condiviso). In **Autorizzazioni** selezionare **Scrittura**. Non sono necessarie altre autorizzazioni.  

## <a name="create-an-azure-function"></a>Creare una funzione di Azure

In questa sezione si creerà la funzione di Azure che attiva il processo OCR di Batch ogni volta che viene caricato un file nel contenitore di input.

1. Seguire i passaggi in [Creare una funzione attivata dall'archiviazione BLOB di Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-storage-blob-triggered-function) per creare una funzione.
    1. Quando viene richiesto un account di archiviazione, usare lo stesso account di archiviazione collegato all'account Batch.
    1. Per **Stack di runtime** scegliere .NET. La funzione verrà scritta in C# per sfruttare Batch .NET SDK.
1. Una volta creata la funzione attivata dal BLOB, usare [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) e [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) da GitHub nella funzione.
    * `run.csx` viene eseguito quando viene aggiunto un nuovo BLOB nel contenitore BLOB di input.
    * `function.proj` elenca le librerie esterne nel codice della funzione, ad esempio Batch .NET SDK.
1. Modificare i valori segnaposto delle variabili nella funzione `Run()` del file `run.csx` in modo da riflettere le credenziali di archiviazione e di Batch. Le credenziali dell'account di archiviazione e di Batch sono disponibili nel portale di Azure nella sezione **Chiavi** dell'account Batch.
    * Recuperare le credenziali dell'account di archiviazione e di Batch nel portale di Azure nella sezione **Chiavi** dell'account Batch. 

## <a name="trigger-the-function-and-retrieve-results"></a>Attivare la funzione e recuperare i risultati

Caricare i file digitalizzati dalla directory [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) in GitHub nel contenitore di input. Monitorare Batch Explorer per verificare che venga aggiunta un'attività a `ocr-pool` per ogni file. Dopo alcuni secondi, il file a cui è stato applicato il metodo OCR viene aggiunto al contenitore di output. Il file è quindi visibile e può essere recuperato in Storage Explorer.

È inoltre possibile controllare il file di log nella parte inferiore della finestra dell'editor Web di Funzioni di Azure dove vengono visualizzati messaggi simili ai seguenti per ogni file caricato nel contenitore di input:

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-06-21T20:02:35.129 [Information] Name of output text file: <outputTxtFile>
2019-06-21T20:02:35.130 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Per scaricare i file di output da Storage Explorer nel computer locale, selezionare prima di tutto i file desiderati e quindi selezionare **Scarica** sulla barra multifunzione superiore. 

> [!TIP]
> È possibile eseguire ricerche nei file scaricati aprendoli in un lettore PDF.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come: 

> [!div class="checklist"]
> * Usare Batch Explorer per creare pool e processi
> * Usare Storage Explorer per creare contenitori BLOB e una firma di accesso condiviso
> * Creare una funzione di Azure attivata da un BLOB
> * Caricare i file di input nella risorsa di archiviazione
> * Monitorare l'esecuzione delle attività
> * Recuperare i file di output

* Per altri esempi di uso dell'API .NET per pianificare ed elaborare i carichi di lavoro di Batch, vedere gli [esempi su GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp). 

* Per informazioni su altri trigger di Funzioni di Azure che è possibile usare per eseguire i carichi di lavoro di Batch, vedere la [documentazione di Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings).
