---
title: Usare Azure Data Lake Storage (ADLS) Gen2 con Apache Hadoop in Azure HDInsight
description: Fornisce una panoramica di Azure Data Lake Storage Gen2 e sul relativo funzionamento con Azure HDInsight.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: b7cde3a8990a51e95a8ce9ad85bca524d5669e0c
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53721122"
---
# <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Usare Azure Data Lake Storage Gen2 con Apache Hadoop in Azure HDInsight

Azure Data Lake Storage (ADLS) Gen2 prende le funzionalità principali di Azure Data Lake Storage Gen1 come un file system compatibile con Hadoop, Azure Active Directory e ACL basati su POSIX e le integra in Archiviazione BLOB di Azure. Questa combinazione consente di sfruttare le prestazioni di Azure Data Lake Storage Gen1 e di usare anche la suddivisione in livelli di Archiviazione BLOB e la gestione del ciclo di vita dei dati.

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Funzionalità principali di Azure Data Lake Storage Gen2

- Accesso compatibile con Hadoop: Azure Data Lake Storage Gen2 consente di gestire i dati e accedervi esattamente come si farebbe con Hadoop Distributed File System (HDFS). Il driver ABFS è disponibile all'interno di tutti gli ambienti di Apache Hadoop, tra cui Azure HDInsight e Azure Databricks, per accedere all'archivio dati di Data Lake Storage Gen2.

- Un superset di autorizzazioni POSIX: il modello di sicurezza per Data Lake Gen2 supporta l'elenco di controllo di accesso e le autorizzazioni POSIX oltre a una granularità aggiuntiva specifica di Data Lake Storage Gen2. È possibile configurare le impostazioni tramite gli strumenti di amministrazione o i framework come Apache Hive e Apache Spark.

- Conveniente: Data Lake Storage Gen2 offre capacità di archiviazione e transazioni a basso costo. Funzionalità come il ciclo di vita di Archiviazione BLOB di Azure consentono di ridurre i costi modificando le tariffe di fatturazione man mano che i dati si spostano durante il loro ciclo di vita.

- Funziona con gli strumenti, i framework e le app di archiviazione BLOB: Data Lake Storage Gen2 continua a funzionare con un ampio array di strumenti, framework e applicazioni già presenti in Archiviazione Blob.

- Driver ottimizzato: il driver ABFS è ottimizzato appositamente per l'analisi dei Big Data. Le API REST corrispondenti vengono rilevate tramite l'endpoint dfs, ovvero dfs.core.windows.net.

## <a name="whats-new-about-azure-data-lake-storage-gen-2"></a>Novità di Azure Data Lake Storage Gen2

### <a name="managed-identities-for-secure-file-access"></a>Identità gestite per un accesso sicuro ai file

Azure HDInsight usa identità gestite per proteggere l'accesso del cluster ai file in Azure Data Lake Storage Gen2. Le identità gestite sono una funzionalità di Azure Active Directory che fornisce ai servizi di Azure un set di credenziali gestite automaticamente. Queste credenziali possono essere usate per eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione AD. L'uso di identità gestite non richiede l'archiviazione delle credenziali in file di codice o di configurazione.

Per altre informazioni, vedere [Che cosa sono le identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-blob-filesystem-abfs-driver"></a>Driver del file system del BLOB di Azure (ABFS)

Le applicazioni Apache Hadoop prevedono in modo nativo di leggere e scrivere i dati dallo spazio di archiviazione su disco locale. Un driver del filesystem Hadoop come ABFS consente alle applicazioni Hadoop di usare l'archiviazione cloud emulando le normali operazioni del file system Hadoop per l'applicazione. Il driver converte quindi queste operazioni in operazioni riconosciute dalla piattaforma di archiviazione cloud effettiva.

In precedenza, il driver del filesystem Hadoop convertiva tutte le operazioni del filesystem in chiamate all'API REST di Archiviazione di Azure sul lato client e quindi richiamava l'API REST. Questa conversione lato client, tuttavia, restituiva più chiamate all'API REST per una singola operazione di filesystem come la ridenominazione di un file. ABFS ha spostato parte della logica del filesystem Hadoop dal lato client al lato server e l'API ADLS Gen2 viene eseguita ora in parallelo con l'API BLOB. Questa migrazione migliora le prestazioni perché ora le comuni operazioni del filesystem Hadoop possono essere eseguite con una sola chiamata all'API REST.

Per altre informazioni, vedere [Driver del file system del BLOB di Azure: un driver di Archiviazione di Azure dedicato per Apache Hadoop](../storage/data-lake-storage/abfs-driver.md).

### <a name="adls-gen-2-uri-scheme"></a>Schema URI di ADLS Gen 2

ADLS Gen2 usa un nuovo schema URI per accedere ai file in Archiviazione di Azure da HDInsight:

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Lo schema URI offre l'accesso con crittografia SSL (prefisso `abfss://`) e l'accesso non crittografato (prefisso `abfs://`). Se possibile, è consigliabile usare `abfss` anche per accedere ai dati presenti nella stessa area di Azure.

`<FILE_SYSTEM_NAME>` identifica il percorso del file system di Data Lake Storage Gen2.

`<ACCOUNT_NAME>` identifica il nome dell'account di Archiviazione di Azure. È necessario specificare un nome di dominio completo (FQDN).

`<PATH>` è il nome del percorso HDFS del file o della directory.

Se i valori per `<FILE_SYSTEM_NAME>` e `<ACCOUNT_NAME>` non vengono specificati, viene usato il file system predefinito. Per i file presenti nel file system predefinito è possibile usare un percorso relativo o un percorso assoluto. Ad esempio, è possibile fare riferimento al file `hadoop-mapreduce-examples.jar` incluso nei cluster HDInsight usando uno dei percorsi seguenti:

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Nei cluster HDInsight versione 2.1 e 1.6 il nome del file è `hadoop-examples.jar`. Quando si usano file al di fuori di HDInsight, la maggior parte delle utilità non riconosce il formato ABFS e richiede invece un formato del percorso di base, ad esempio `example/jars/hadoop-mapreduce-examples.jar`.

Per altre informazioni, vedere [Usare l'URI di Azure Data Lake Storage Gen2](../storage/data-lake-storage/introduction-abfs-uri.md).

## <a name="next-steps"></a>Passaggi successivi
- [Introduzione ad Azure Data Lake Storage Gen2](../storage/data-lake-storage/introduction.md)
- [Usare l'anteprima di Azure Data Lake Storage Gen2 con cluster Azure HDInsight](../storage/data-lake-storage/use-hdi-cluster.md)