---
title: Driver ABFS per Azure Data Lake Storage Gen2 - Anteprima
description: Driver ABFS per file system Hadoop
services: storage
keywords: ''
author: jamesbak
manager: jahogg
ms.topic: article
ms.author: jamesbak
ms.date: 06/27/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: e92c4efba29f1c40f6d4cb155974ca3a896796e5
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114334"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Driver ABFS (Azure Blob File System): un driver di Archiviazione di Azure dedicato per Hadoop

Uno dei metodi di accesso principali per i dati archiviati nella versione di anteprima di Azure Data Lake Storage Gen2 consiste nell'uso del [file system Hadoop](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html). Azure Data Lake Storage Gen2 offre un driver associato denominato `ABFS`, ovvero Azure Blob File System. ABFS fa parte di Apache Hadoop ed è incluso in molte delle distribuzioni commerciali di Hadoop. Tramite questo driver, molti framework e applicazioni possono accedere ai dati in Data Lake Storage Gen2 senza che nel codice siano presenti riferimenti espliciti al servizio Data Lake Storage Gen2.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Funzionalità principale: driver del BLOB del servizio di archiviazione di Azure

Il driver del BLOB del servizio di archiviazione di Azure, o [driver WASB (Windows Azure Storage Blob)](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) forniva il supporto originale per i BLOB di Archiviazione di Azure. Il driver eseguiva un'attività complessa che consisteva nel mappare la semantica basata su file system, come richiesta dall'interfaccia del file system Hadoop, a quella dell'interfaccia basata sullo stile di archiviazione degli oggetti esposta da Archiviazione BLOB di Azure. Il driver supporta ancora questo modello, consentendo l'accesso ad alte prestazioni ai dati archiviati nei BLOB, ma contiene una quantità notevole di codice per eseguire il mapping ed è quindi difficile da gestire. Inoltre, alcuni comandi come [FileSystem.rename()](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) e [FileSystem.delete()](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive), quando applicati a directory, richiedono l'esecuzione di una notevole quantità di operazioni, poiché negli archivi degli oggetti non sono supportate le directory, e questo influisce spesso negativamente sulle prestazioni.

Per ovviare alle carenze di progettazione inerenti al driver WASB, nel nuovo servizio Azure Data Lake Storage è stato quindi implementato il supporto per il nuovo driver ABFS.

## <a name="the-azure-blob-file-system-driver"></a>Driver ABFS

L'[interfaccia REST di Azure Data Lake Storage](https://docs.microsoft.com/en-us/rest/api/storageservices/data-lake-storage-gen2) è stata progettata in modo da supportare la semantica basata su file system in Archiviazione BLOB di Azure. Poiché anche il file system Hadoop è stato progettato per il supporto della stessa semantica, non è più necessario che nel driver sia definito un mapping complesso. Pertanto, il driver ABFS è un semplice shim client per l'API REST.

Il driver deve tuttavia eseguire ancora alcune funzioni:

### <a name="uri-scheme-to-reference-data"></a>Schema dell'URI per il riferimento ai dati

In conformità con altre implementazioni di file system in Hadoop, il driver ABFS definisce uno specifico schema di URI in modo da distinguere con precisione i riferimenti alle risorse (directory e file). Lo schema dell'URI è illustrato in [Usare l'URI di Azure Data Lake Storage Gen2](./introduction-abfs-uri.md). L'URI ha la struttura seguente: `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Con questo formato di URI, è possibile usare gli strumenti e i framework standard di Hadoop per fare riferimento alle risorse seguenti:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data 
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/ 
```

Internamente, il driver ABFS converte in file e directory le risorse specificate nell'URI ed esegue chiamate all'API REST di Azure Data Lake Storage con tali riferimenti.

### <a name="authentication"></a>Authentication

Il driver ABFS supporta attualmente l'autenticazione della chiave condivisa per consentire all'applicazione Hadoop di accedere in modo sicuro alle risorse contenute in Data Lake Storage Gen2. La chiave viene crittografata e archiviata nella configurazione Hadoop.

### <a name="configuration"></a>Configurazione

Tutte le informazioni di configurazione per il driver ABFS sono archiviate nel file di configurazione <code>core-site.xml</code>. In tutte le distribuzioni Hadoop con [Ambari](http://ambari.apache.org/), la configurazione può essere gestita anche tramite il portale Web o l'API REST di Ambari.

I dettagli di tutte le voci di configurazione supportate sono riportati nella [documentazione ufficiale di Hadoop](http://hadoop.apache.org/docs/current/hadoop-azure/index.html).

### <a name="hadoop-documentation"></a>Documentazione di Hadoop

Informazioni dettagliate sul driver ABFS sono disponibili nella [documentazione ufficiale di Hadoop](http://hadoop.apache.org/docs/current/hadoop-azure/index.html).

## <a name="next-steps"></a>Passaggi successivi

- [Impostazione dei cluster in HDInsight](./quickstart-create-connect-hdi-cluster.md)
- [Creare un cluster di Azure Databricks](./quickstart-create-databricks-account.md)
- [Usare l'URI di Azure Data Lake Storage Gen2](./introduction-abfs-uri.md)
