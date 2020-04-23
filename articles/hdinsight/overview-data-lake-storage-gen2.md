---
title: Azure Data Lake Storage Gen2 overview in HDInsight
description: Panoramica di Data Lake Storage Gen2 in HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 677d8348cc230e4f64915ac5fc1e86b9b50ab5c3
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873342"
---
# <a name="azure-data-lake-storage-gen2-overview-in-hdinsight"></a>Azure Data Lake Storage Gen2 overview in HDInsight

Azure Data Lake Storage Gen2 usa le funzionalità di base di Azure Data Lake Storage Gen1 e le integra in Archiviazione BLOB di Azure. Queste funzionalità includono un file system compatibile con Hadoop, Azure Active Directory (Azure AD) ed elenchi di controllo di accesso (ACL) basati su POSIX. Questa combinazione consente di sfruttare le prestazioni di Azure Data Lake Storage Gen1. Anche se si usa la suddivisione in livelli e la gestione del ciclo di vita dei dati dell'archiviazione BLOB.

Per altre informazioni su Azure Data Lake Storage Gen2, consultare [Introduzione ad Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Funzionalità principali di Azure Data Lake Storage Gen2

* **Accesso compatibile con Hadoop:** In Azure Data Lake Storage Gen2 è possibile gestire e accedere ai dati come con un file system distribuito Hadoop (HDFS). Il driver del file system BLOB di Azure (ABFS) è disponibile in tutti gli ambienti Apache Hadoop, tra cui Azure HDInsight e Azure Databricks. Usarlo per accedere ai dati archiviati in Data Lake Storage Gen2.

* **Un superset di autorizzazioni POSIX:** Il modello di sicurezza per Data Lake Gen2 supporta le autorizzazioni ACL e POSIX insieme a una granularità aggiuntiva specifica di Data Lake Storage Gen2. È possibile configurare le impostazioni tramite gli strumenti di amministrazione o framework quali Apache Hive e Apache Spark.

* **Efficacia dei costi:** Data Lake Storage Gen2 offre capacità di archiviazione e transazioni a basso costo. I cicli di vita dell'archiviazione BLOB di Azure consentono di ridurre i costi regolando i tassi di fatturazione man mano che i dati si spostano nel corso del ciclo di vita.

* **Compatibilità con gli strumenti, i framework e le app** di archiviazione BLOB: Data Lake Storage Gen2 continua a funzionare con un'ampia gamma di strumenti, framework e applicazioni per l'archiviazione BLOB.

* **Driver ottimizzato:** Il driver ABFS è ottimizzato specificamente per l'analisi dei Big Data. Le API REST corrispondenti vengono rilevate tramite l'endpoint del file system distribuito (DFS), ovvero dfs.core.windows.net.

## <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Novità di Azure Data Lake Storage Gen2

### <a name="managed-identities-for-secure-file-access"></a>Identità gestite per un accesso sicuro ai file

Azure HDInsight usa identità gestite per proteggere l'accesso del cluster ai file in Azure Data Lake Storage Gen2. Le identità gestite sono una funzionalità di Azure Active Directory che fornisce ai servizi di Azure un set di credenziali gestite automaticamente. Queste credenziali possono essere usate per eseguire l'autenticazione per qualsiasi servizio che supporti l'autenticazione Active Directory. L'uso di identità gestite non richiede l'archiviazione delle credenziali in file di codice o di configurazione.

Per altre informazioni, vedere [Identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-blob-file-system-driver"></a>Driver ABFS

Le applicazioni Apache Hadoop prevedono in modo nativo di leggere e scrivere i dati dallo spazio di archiviazione su disco locale. Un driver del file system Hadoop come ABFS consente alle applicazioni Hadoop di lavorare con l'archiviazione cloud. Funziona emulando le normali operazioni del file system Hadoop. Il driver converte questi comandi ricevuti dall'applicazione in operazioni riconosciute dalla piattaforma di archiviazione nel cloud effettiva.

In precedenza, il driver del file system Hadoop convertiva tutte le operazioni del file system in chiamate all'API REST di Archiviazione di Azure sul lato client. E poi richiamato l'API REST. Questa conversione lato client tuttavia restituisce più chiamate API REST per una singola operazione di file system come la ridenominazione di un file. ABFS ha spostato la logica del file system Hadoop dal lato client al lato server. L'API di Azure Data Lake Storage Gen2 viene ora eseguita in parallelo con l'API BLOB. Questa migrazione comporta un miglioramento delle prestazioni perché ora le operazioni comuni del file system Hadoop possono essere eseguite con una chiamata API REST.

Per altre informazioni, vedere Driver del file system BLOB di Azure (ABFS): Un driver di Archiviazione di Azure dedicato per Hadoop.For more information, see [The Azure Blob Filesystem driver (ABFS): A dedicated Azure Storage driver for Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Schema URI di Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 usa un nuovo schema URI per accedere ai file in Archiviazione di Azure da HDInsight:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Lo schema URI fornisce l'accesso con crittografia SSL.

`<FILE_SYSTEM_NAME>` identifica il percorso del file system di Data Lake Storage Gen2.

`<ACCOUNT_NAME>` identifica il nome dell'account di Archiviazione di Azure. È necessario specificare un nome di dominio completo (FQDN).

`<PATH>` è il nome del percorso HDFS di file o directory.

Se i valori per `<FILE_SYSTEM_NAME>` e `<ACCOUNT_NAME>` non vengono specificati, viene usato il file system predefinito. Per i file presenti nel file system predefinito, usare un percorso relativo o un percorso assoluto. Ad esempio, è possibile fare riferimento al file `hadoop-mapreduce-examples.jar` incluso nei cluster HDInsight usando uno dei percorsi seguenti:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]
> Nei cluster HDInsight versione 2.1 e 1.6 il nome del file è `hadoop-examples.jar`. Quando si usano file al di fuori di HDInsight, la maggior parte delle utilità non riconosce il formato ABFS, ma richiede invece un formato di percorso di base, ad esempio `example/jars/hadoop-mapreduce-examples.jar`.

Per altre informazioni, vedere [Usare l'URI di Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introduzione ad Archiviazione di Azure](../storage/common/storage-introduction.md)
* [Panoramica di Azure Data Lake Storage Gen1Azure Data Lake Storage Gen1 overview](./overview-data-lake-storage-gen1.md)