---
title: Esempi di Archiviazione di Azure che usano Java | Documentazione Microsoft
description: Visualizzare, scaricare ed eseguire codici di esempio e applicazioni per l'Archiviazione di Azure. Individuare esempi introduttivi per BLOB, code, tabelle e file usando le librerie client di archiviazione Java.
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 02/13/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: b0e0588810fcac239a3647f9b750dd036341ab9f
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322228"
---
# <a name="azure-storage-samples-using-v12-java-client-libraries"></a>Esempi di Archiviazione di Azure che usano le librerie client Java v12

La tabella seguente fornisce una panoramica del repository di esempi e degli scenari presentati in ogni esempio. Fare clic sui collegamenti per visualizzare il codice di esempio corrispondente in GitHub.

> [!NOTE]
> Questi esempi usano la versione più recente della libreria Java v12 di Archiviazione di Azure. Per il codice v8 legacy, vedere [Introduzione al servizio BLOB di Azure in Java](https://github.com/Azure-Samples/storage-blob-java-getting-started) nel repository GitHub.

## <a name="blob-samples"></a>Esempi di BLOB

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="":::
      [Eseguire l'autenticazione con una credenziale della chiave condivisa](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L38)
   :::column-end:::
   :::column span="":::
      [Eseguire l'autenticazione con Identità di Azure](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/AzureIdentityExample.java#L10)
   :::column-end:::
:::row-end:::

### <a name="blob-service"></a>Servizio BLOB

:::row:::
   :::column span="":::
      [Creare un client del servizio BLOB](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L49)
   :::column-end:::
   :::column span="":::
      [Elencare i contenitori](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/ListContainersExample.java#L10)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Eliminare i contenitori](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/ListContainersExample.java#L52)
   :::column-end:::
:::row-end:::

### <a name="batching"></a>Creazione di batch

:::row:::
   :::column span="":::
      [Creare un client di batch di BLOB](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob-batch/src/samples/java/com/azure/storage/blob/batch/ReadmeSamples.java#L41)
   :::column-end:::
   :::column span="":::
      [Eliminare i BLOB in blocco](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob-batch/src/samples/java/com/azure/storage/blob/batch/ReadmeSamples.java#L45)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Impostare il livello di accesso in un batch di BLOB](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob-batch/src/samples/java/com/azure/storage/blob/batch/ReadmeSamples.java#L51)
   :::column-end:::
:::row-end:::

### <a name="container"></a>Contenitore

:::row:::
   :::column span="":::
      [Creare un client di contenitore](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L57)
   :::column-end:::
   :::column span="":::
      [Creare un contenitore](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L64)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Elencare i BLOB](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L112)
   :::column-end:::
   :::column span="":::
      [Eliminare un contenitore](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L123)
   :::column-end:::
:::row-end:::

### <a name="blob"></a>BLOB

:::row:::
   :::column span="":::
      [Caricare un BLOB](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L79)
   :::column-end:::
   :::column span="":::
      [Scaricare un BLOB](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L86)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Eliminare un BLOB](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L118)
   :::column-end:::
   :::column span="":::
      [Caricare un BLOB da un file di grandi dimensioni](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/FileTransferExample.java#L95)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Scaricare un BLOB di grandi dimensioni in un file](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/FileTransferExample.java#L100)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Risoluzione dei problemi
:::row:::
   :::column span="2":::
      [Attivare un errore reversibile con un client contenitore](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/StorageErrorHandlingExample.java#L11)
   :::column-end:::
:::row-end:::

## <a name="data-lake-storage-gen2-samples"></a>Esempi di Data Lake Storage Gen2

### <a name="data-lake-service"></a>Servizio Data Lake
:::row:::
   :::column span="":::
      [Creare un client del servizio Data Lake](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L48)
   :::column-end:::
   :::column span="":::
      [Creare un client di file system](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L57)
   :::column-end:::
:::row-end:::

### <a name="file-system"></a>File system
:::row:::
   :::column span="":::
      [Creare un file system](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L64)
   :::column-end:::
   :::column span="":::
      [Creare una directory](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Creare un file e una sottodirectory](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L73)
   :::column-end:::
   :::column span="":::
      [Creare un client di file](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L83)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Elencare i percorsi in un file system](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L131)
   :::column-end:::
   :::column span="":::
      [Eliminare un file system](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L142)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Elencare i file system in un account di archiviazione di Azure](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/ListFileSystemsExample.java#L10)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>Directory

:::row:::
   :::column span="":::
      [Creare un client di directory](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L31)
   :::column-end:::
   :::column span="":::
      [Creare una directory padre](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L37)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Creare una directory figlio](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L44)
   :::column-end:::
   :::column span="":::
      [Creare file in una directory figlio](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L52)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Ottenere le proprietà della directory](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L68)
   :::column-end:::
   :::column span="":::
      [Eliminare una directory figlio](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L83)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Eliminare una cartella padre](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L90)
   :::column-end:::
:::row-end:::

### <a name="file"></a>File

:::row:::
   :::column span="":::
      [Creare un file con un client di file](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L93)
   :::column-end:::
   :::column span="":::
      [Eliminare un file](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L137)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Impostare i controlli di accesso su un file](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/GetSetAccessControlExample.java#L82)
   :::column-end:::
   :::column span="":::
      [Ottenere i controlli di accesso su un file](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/GetSetAccessControlExample.java#L104)
   :::column-end:::
:::row-end:::


## <a name="azure-file-samples"></a>Esempi di file di Azure

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="2":::
      [Eseguire l'autenticazione con una stringa di connessione](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareServiceSample.java#L27)
   :::column-end:::
:::row-end:::

### <a name="file-service"></a>Servizio file

:::row:::
   :::column span="":::
      [Creare condivisioni file](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareServiceSample.java#L31)
   :::column-end:::
   :::column span="":::
      [Ottenere le proprietà](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareServiceSample.java#L40)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Elencare le condivisioni](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareServiceSample.java#L49)
   :::column-end:::
   :::column span="":::
      [Eliminare le condivisioni](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareServiceSample.java#L49)
   :::column-end:::
:::row-end:::

### <a name="file-share"></a>Condivisione file

:::row:::
   :::column span="":::
      [Creare un client di condivisione](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L29)
   :::column-end:::
   :::column span="":::
      [Creare una condivisione](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L40)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Creare uno snapshot di condivisione](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L55)
   :::column-end:::
   :::column span="":::
      [Creare una directory con un client di condivisione](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L63)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Ottenere le proprietà di una condivisione](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L72)
   :::column-end:::
   :::column span="":::
      [Ottenere directory radice ed elencare le directory](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L100)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Eliminare una condivisione](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L151)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>Directory

:::row:::
   :::column span="":::
      [Creare una directory padre](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L35)
   :::column-end:::
   :::column span="":::
      [Creare una directory figlio](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L42)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Creare file in una directory figlio](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L50)
   :::column-end:::
   :::column span="":::
      [Elencare directory e file](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L66)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Eliminare una cartella figlio](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L90)
   :::column-end:::
   :::column span="":::
      [Eliminare una cartella padre](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L97)
   :::column-end:::
:::row-end:::

### <a name="file"></a>File

:::row:::
   :::column span="":::
      [Creare un client di file](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/FileSample.java#L45)
   :::column-end:::
   :::column span="":::
      [Caricare un file](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/FileSample.java#L90)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Scaricare un file](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/FileSample.java#L100)
   :::column-end:::
   :::column span="":::
      [Ottenere le proprietà dei file](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/FileSample.java#L120)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Eliminare un file](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/FileSample.java#L128)
   :::column-end:::
:::row-end:::


## <a name="queue-samples"></a>Esempi di code

### <a name="authentication"></a>Authentication

:::row:::
   :::column span="2":::
      [Eseguire l'autenticazione con un token di firma di accesso condiviso](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/QueueServiceSamples.java#L17)
   :::column-end:::
:::row-end:::

### <a name="queue-service"></a>Servizio di accodamento

:::row:::
   :::column span="":::
      [Creare una coda](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/QueueServiceSamples.java#L20)
   :::column-end:::
   :::column span="":::
      [Elencare le code](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/QueueServiceSamples.java#L22)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Eliminare le code](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/QueueServiceSamples.java#L27)
   :::column-end:::
:::row-end:::

### <a name="queue"></a>Coda

:::row:::
   :::column span="":::
      [Creare un client di accodamento](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L25)
   :::column-end:::
   :::column span="":::
      [Aggiungere messaggi a una coda](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L27)
   :::column-end:::
:::row-end:::

### <a name="message"></a>Message

:::row:::
   :::column span="":::
      [Ottenere il numero di messaggi](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L34)
   :::column-end:::
   :::column span="":::
      [Visualizzare in anteprima i messaggi](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L37)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Ricevere messaggi](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L41)
   :::column-end:::
   :::column span="":::
      [Aggiornare un messaggio](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L45)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Eliminare il primo messaggio](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L50)
   :::column-end:::
   :::column span="":::
      [Cancellare tutti i messaggi](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L59)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Eliminare una coda](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L64)
   :::column-end:::
:::row-end:::

## <a name="table-samples-v11"></a>Esempi di tabelle (v11)

:::row:::
   :::column span="":::
      [Creare una tabella](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L50)
   :::column-end:::
   :::column span="":::
      [Eliminare un'entità o una tabella](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L109)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Inserire/unire/sostituire entità](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L195)
   :::column-end:::
   :::column span="":::
      [Eseguire query sulle entità](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L234)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Eseguire query sulle tabelle](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L262)
   :::column-end:::
   :::column span="":::
      [ACL/proprietà di tabella](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java#L49)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Aggiornare l'entità](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L76)
   :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Librerie di esempi di codice Azure

Per visualizzare le librerie complete Java di esempio, vedere:

* [Esempi di codice di BLOB di Azure](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)
* [Esempi di codice di Azure Data Lake](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake)
* [Esempi di codice di File di Azure](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share)
* [Esempi di codice di accodamento di Azure](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue)

È possibile esplorare e clonare il repository GitHub per ogni libreria.

## <a name="getting-started-guides"></a>Guide introduttive

Per istruzioni su come installare e iniziare a utilizzare le librerie client di Archiviazione di Azure, consultare le seguenti guide.

* [Getting Started with Azure Blob Service in Java](../blobs/storage-quickstart-blobs-java.md) (Introduzione al servizio BLOB di Azure in Java)
* [Getting Started with Azure Queue Service in Java](../queues/storage-quickstart-queues-java.md) (Introduzione al servizio di accodamento di Azure in Java)
* [Getting Started with Azure Table Service in Java](../../cosmos-db/table-storage-how-to-use-java.md) (Introduzione al servizio tabelle di Azure in Java)
* [Getting Started with Azure File Service in Java](../files/storage-java-how-to-use-file-storage.md) (Introduzione al servizio File di Azure in Java)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su esempi con altri linguaggi:

* .NET: [Esempi di Archiviazione di Azure che usano .NET](storage-samples-dotnet.md)
* Python: [Esempi di Archiviazione di Azure che usano Python](storage-samples-python.md)
* JavaScript/Node.js: [Esempi di Archiviazione di Azure che usano JavaScript](storage-samples-javascript.md)
* Tutti gli altri linguaggi: [Esempi di Archiviazione di Azure](storage-samples.md)
