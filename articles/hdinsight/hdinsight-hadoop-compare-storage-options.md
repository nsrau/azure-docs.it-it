---
title: Confrontare le opzioni di archiviazione per l'uso con i cluster Azure HDInsight
description: Questo articolo fornisce una panoramica dei tipi di archiviazione e del relativo funzionamento con Azure HDInsight.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/20/2019
ms.openlocfilehash: 14db76068cc11d3f57a72e3e540a5e0da7e1c254
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54853613"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Confrontare le opzioni di archiviazione per l'uso con i cluster Azure HDInsight

Gli utenti di Azure HDInsight possono scegliere tra diverse opzioni di archiviazione quando creano cluster HDInsight:

* Azure Data Lake Storage Gen2
* Archiviazione di Azure
* Azure Data Lake Storage Gen1

Questo articolo offre una panoramica di questi diversi tipi di archiviazione e delle relative funzionalità uniche.

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Usare Azure Data Lake Storage Gen2 con Apache Hadoop in Azure HDInsight

Per altre informazioni su Azure Data Lake Storage Gen2, consultare [Introduzione ad Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

Azure Data Lake Storage Gen2 prende le funzionalità principali di Azure Data Lake Storage Gen1 come un file system compatibile con Hadoop, Azure Active Directory ed elenchi di controllo di accesso (ACL) basati su POSIX e le integra in Archiviazione BLOB di Azure. Questa combinazione consente di sfruttare le prestazioni di Azure Data Lake Storage Gen1 e di usare anche la suddivisione in livelli di Archiviazione BLOB e la gestione del ciclo di vita dei dati.

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Funzionalità principali di Azure Data Lake Storage Gen2

* Accesso compatibile con Hadoop: Azure Data Lake Storage Gen2 consente di gestire i dati e accedervi esattamente come si farebbe con Hadoop Distributed File System (HDFS). Il driver del file system del BLOB di Azure (ABFS) è disponibile all'interno di tutti gli ambienti di Apache Hadoop, tra cui Azure HDInsight e Azure Databricks, per accedere ai dati archiviati in Data Lake Storage Gen2.

* Un superset di autorizzazioni POSIX: il modello di sicurezza per Data Lake Gen2 supporta l'elenco di controllo di accesso e le autorizzazioni POSIX oltre a una granularità aggiuntiva specifica di Data Lake Storage Gen2. È possibile configurare le impostazioni tramite gli strumenti di amministrazione o i framework come Apache Hive e Apache Spark.

* Conveniente: Data Lake Storage Gen2 offre capacità di archiviazione e transazioni a basso costo. Funzionalità come il ciclo di vita di Archiviazione BLOB di Azure consentono di ridurre i costi modificando le tariffe di fatturazione man mano che i dati si spostano durante il loro ciclo di vita.

* Funziona con gli strumenti, i framework e le app di archiviazione BLOB: Data Lake Storage Gen2 continua a funzionare con un ampio array di strumenti, framework e applicazioni già presenti in Archiviazione Blob.

* Driver ottimizzato: il driver ABFS è ottimizzato appositamente per l'analisi dei Big Data. Le API REST corrispondenti vengono rilevate tramite l'endpoint dfs, ovvero dfs.core.windows.net.

### <a name="whats-new-about-azure-data-lake-storage-gen-2"></a>Novità di Azure Data Lake Storage Gen2

#### <a name="managed-identities-for-secure-file-access"></a>Identità gestite per un accesso sicuro ai file

Azure HDInsight usa identità gestite per proteggere l'accesso del cluster ai file in Azure Data Lake Storage Gen2. Le identità gestite sono una funzionalità di Azure Active Directory che fornisce ai servizi di Azure un set di credenziali gestite automaticamente. Queste credenziali possono essere usate per eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione AD. L'uso di identità gestite non richiede l'archiviazione delle credenziali in file di codice o di configurazione.

Per altre informazioni, vedere [Che cosa sono le identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-filesystem-abfs-driver"></a>Driver del file system del BLOB di Azure (ABFS)

Le applicazioni Apache Hadoop prevedono in modo nativo di leggere e scrivere i dati dallo spazio di archiviazione su disco locale. Un driver del file system Hadoop come ABFS consente alle applicazioni Hadoop di usare l'archiviazione nel cloud emulando le normali operazioni del file system Hadoop. Il driver converte questi comandi ricevuti dall'applicazione in operazioni riconosciute dalla piattaforma di archiviazione nel cloud effettiva.

In precedenza, il driver del filesystem Hadoop convertiva tutte le operazioni del filesystem in chiamate all'API REST di Archiviazione di Azure sul lato client e quindi richiamava l'API REST. Questa conversione lato client, tuttavia, restituiva più chiamate all'API REST per una singola operazione di filesystem come la ridenominazione di un file. ABFS ha spostato parte della logica del file system Hadoop dal lato client al lato server e adesso l'API di Azure Data Lake Storage Gen2 viene eseguita in parallelo con l'API BLOB. Questa migrazione migliora le prestazioni perché ora le comuni operazioni del filesystem Hadoop possono essere eseguite con una sola chiamata all'API REST.

Per altre informazioni, vedere [Driver del file system del BLOB di Azure: un driver di Archiviazione di Azure dedicato per Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

#### <a name="azure-data-lake-storage-gen-2-uri-scheme"></a>Schema URI di Azure Data Lake Storage Gen 2

Azure Data Lake Storage Gen2 usa un nuovo schema URI per accedere ai file in Archiviazione di Azure da HDInsight:

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

> [!Note]
> Nei cluster HDInsight versione 2.1 e 1.6 il nome del file è `hadoop-examples.jar`. Quando si usano file al di fuori di HDInsight, la maggior parte delle utilità non riconosce il formato ABFS e richiede invece un formato del percorso di base, ad esempio `example/jars/hadoop-mapreduce-examples.jar`.

Per altre informazioni, vedere [Usare l'URI di Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="use-azure-storage"></a>Usare l'archiviazione di Azure

Archiviazione di Azure è una soluzione di archiviazione affidabile, con finalità generali che si integra facilmente con HDInsight. HDInsight può usare un contenitore BLOB in Archiviazione di Azure come file system predefinito per il cluster. Grazie a un'interfaccia HDFS (Hadoop Distributed File System), tutti i componenti disponibili in HDInsight possono agire direttamente sui dati strutturati o non strutturati archiviati come BLOB.

> [!WARNING]  
> Sono disponibili diverse opzioni per la creazione di un account di archiviazione di Azure. La tabella seguente offre informazioni sulle opzioni supportate con HDInsight:

| Tipo di account di archiviazione | Servizi supportati | Livelli di prestazioni supportati | Livelli di accesso supportati |
|----------------------|--------------------|-----------------------------|------------------------|
| Utilizzo generico v2   | BLOB               | Standard                    | Hot, Cool, Archive*    |
| Utilizzo generico v1   | BLOB               | Standard                    | N/D                    |
| Archiviazione BLOB         | BLOB               | Standard                    | Hot, Cool, Archive*    |

Non è consigliabile usare il contenitore BLOB predefinito per l'archiviazione dei dati aziendali. È consigliabile eliminare il contenitore BLOB predefinito dopo ogni uso per ridurre i costi di archiviazione. Il contenitore predefinito include registri di sistema e applicazioni. Assicurarsi di recuperare i registri prima di eliminare il contenitore.

L'uso di un contenitore BLOB come file system predefinito tra più cluster non è supportato.
 
 > [!NOTE]  
 > Il livello di accesso archivio è un livello offline che ha una latenza di recupero di diverse ore e non è consigliato per l'uso con HDInsight. Per altre informazioni, vedere [Livello di accesso archivio](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

### <a name="hdinsight-storage-architecture"></a>Architettura di archiviazione di HDInsight
Nel diagramma seguente viene mostrata una visualizzazione astratta dell'architettura di archiviazione HDInsight dell'uso di Archiviazione di Azure:

I ![cluster Hadoop usano l'API HDFS per accedere e archiviare dati strutturati e non strutturati nell'archiviazione BLOB.](./media/hdinsight-hadoop-compare-storage-options/HDI.WASB.Arch.png "Architettura di archiviazione di HDInsight")

HDInsight offre accesso al file system distribuito collegato localmente ai nodi di calcolo. Il file system è accessibile tramite l'URI completo, ad esempio:

    hdfs://<namenodehost>/<path>

HDInsight consente anche di accedere ai dati archiviati in Archiviazione di Azure. La sintassi è:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Di seguito sono riportate alcune considerazioni sull'uso di un account di Archiviazione di Azure con i cluster HDInsight.

* **Contenitori negli account di archiviazione connessi a un cluster:** poiché il nome e la chiave dell'account sono associati al cluster durante il processo di creazione, si disporrà di un accesso completo ai BLOB presenti in tali contenitori.

* **Contenitori pubblici o BLOB pubblici negli account di archiviazione che non sono connessi a un cluster:** si dispone dell'autorizzazione di sola lettura ai BLOB nei contenitori.
  
  > [!NOTE]  
  > Un contenitore pubblico consente di ottenere un elenco di tutti i BLOB disponibili al suo interno, nonché i metadati del contenitore stesso. È possibile accedere a un BLOB pubblico solo se ne conosce l'URL esatto. Per altre informazioni, vedere [Gestire l'accesso a contenitori e Blob](../storage/blobs/storage-manage-access-to-resources.md).

* **Contenitori privati negli account di archiviazione NON connessi a un cluster:** non è possibile accedere ai BLOB nei contenitori a meno che non sia stato definito l'account di archiviazione quando sono stati inviati i processi WebHCat. Questo concetto verrà spiegato più avanti nell'articolo.

Gli account di archiviazione definiti durante il processo di creazione, con le rispettive chiavi, sono archiviati in %HADOOP_HOME%/conf/core-site.xml nei nodi del cluster. Il comportamento predefinito di HDInsight consiste nell'usare gli account di archiviazione definiti nel file core-site.xml. È possibile modificare questa impostazione usando [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Più processi WebHCat, inclusi Apache Hive, MapReduce, lo streaming Apache Hadoop e Apache Pig, possono includere una descrizione degli account di archiviazione e dei metadati. (questo è valido solo per Pig con account di archiviazione, non per i metadati). Per altre informazioni, vedere [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx) (Uso di un cluster HDInsight con account di archiviazione e metastore alternativi).

I BLOB possono essere usati per i dati strutturati e non strutturati. I contenitori BLOB archiviano i dati come coppie chiave-valore e non esiste una gerarchia di directory. È tuttavia possibile usare il carattere barra ( / ) all'interno del nome della chiave per far sembrare che un file sia archiviato in una struttura di directory. Ad esempio, la chiave di un BLOB potrebbe essere `input/log1.txt`. Non esiste realmente una directory `input` ma, grazie alla presenza del carattere barra, il nome della chiave ha l'aspetto di un percorso file.

### <a id="benefits"></a>Vantaggi di Archiviazione di Azure
I costi impliciti in termini di prestazioni associati alla mancata disponibilità di cluster di elaborazione e risorse di archiviazione in un ambiente condiviso sono mitigati dalla modalità di creazione dei cluster di elaborazione in prossimità delle risorse degli account di archiviazione nell'area di Azure dove la rete ad alta velocità rende efficiente l'accesso ai dati in Archiviazione di Azure dai nodi di calcolo.

L'archiviazione dei dati in Archiviazione di Azure anziché in HDFS offre numerosi vantaggi:

* **Condivisione e riutilizzo dei dati:** i dati in HDFS sono situati all'interno del cluster di elaborazione. Solo le applicazioni che hanno accesso al cluster di calcolo, quindi, possono usare i dati tramite le API HDFS. È possibile accedere ai dati in Archiviazione di Azure tramite le API HDFS o tramite le API REST dell'Archiviazione BLOB. Di conseguenza, è possibile produrre e usare i dati mediante un set più ampio di strumenti e applicazioni, compresi altri cluster HDInsight.
* **Archiviazione dati:** l'archiviazione dei dati in Archiviazione di Azure consente l'eliminazione sicura dei cluster HDInsight usati per i calcoli, senza perdita di dati utente.
* **Costo di archiviazione dei dati:** l'archiviazione a lungo termine dei dati in DFS è più costosa rispetto ad Archiviazione di Azure, dato che il costo di un cluster di elaborazione è più alto di quello di Archiviazione di Azure. Poiché, inoltre, non è necessario ricaricare i dati ogni volta che si genera un cluster di elaborazione, si risparmiano anche i costi di caricamento dei dati.
* **Scalabilità orizzontale elastica:** anche se Hadoop Distributed File System offre scalabilità orizzontale del file system, la scalabilità è determinata dal numero di nodi creati per il cluster. Modificare la scalabilità può diventare quindi un processo più complicato rispetto al semplice uso delle funzionalità di scalabilità elastica automaticamente disponibili in Archiviazione di Azure.
* **Replica geografica:** Archiviazione di Azure può eseguire la replica geografica. Sebbene questo offra ripristino geografico e ridondanza dei dati, un failover nella posizione sottoposta a replica geografica incide molto negativamente sulle prestazioni e può comportare costi aggiuntivi. È pertanto consigliabile scegliere la scelta della replica geografica con oculatezza e solo se il valore dei dati giustifica i costi aggiuntivi.

Alcuni pacchetti e processi MapReduce possono creare risultati intermedi che non vale la pena di archiviare in Archiviazione di Azure. In questo caso, è possibile scegliere di archiviare i dati nel file system HDFS locale. In effetti, HDInsight utilizza DFS per molti di questi risultati intermedi nei processi Hive e in altri processi.

> [!NOTE]  
> La maggior parte dei comandi Hadoop Distributed File System, ad esempio `ls`, `copyFromLocal` e `mkdir`, funziona come previsto. Solo i comandi specifici dell'implementazione Hadoop Distributed File System nativa (denominata DFS), ad esempio `fschk` e `dfsadmin`, hanno un comportamento diverso in Archiviazione di Azure.

## <a name="use-azure-data-lake-storage-gen1"></a>Usare Azure Data Lake Storage Gen1

Per altre informazioni su Azure Data Lake Storage Gen1, consultare [Panoramica di Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

Azure Data Lake Storage Gen1 è un repository su vasta scala a livello aziendale per carichi di lavoro di analisi di Big Data. Azure Data Lake consente di acquisire dati di qualsiasi dimensione, tipo e velocità di inserimento in un'unica posizione per le analisi esplorative e operative.

Si può accedere a Data Lake Storage Gen1 da Hadoop (disponibile con un cluster HDInsight) mediante le API REST compatibili con WebHDFS. È progettata per consentire l'analisi dei dati archiviati ed è ottimizzata per offrire prestazioni elevate in scenari di analisi dei dati. Per impostazione predefinita, include tutte le funzionalità a livello aziendale, protezione, gestibilità, scalabilità, affidabilità e disponibilità, essenziali per i casi di utilizzo aziendale reali.

![Azure Data Lake Storage](./media/hdinsight-hadoop-compare-storage-options/data-lake-store-concept.png "Architettura di Archiviazione HDInsight")

Di seguito sono riportate alcune delle principali funzionalità di Data Lake Storage Gen1.

### <a name="built-for-hadoop"></a>Creato per Hadoop

Data Lake Storage Gen1 è un file system Apache Hadoop compatibile con HDFS (Hadoop Distributed File System) e funziona con l'ecosistema Hadoop.  Le applicazioni HDInsight esistenti o i servizi che usano l'API WebHDFS possono integrarsi facilmente con Data Lake Storage Gen1. Data Lake Storage Gen1 presenta anche un'interfaccia REST compatibile con WebHDFS per le applicazioni

I dati archiviati in Data Lake Storage Gen1 possono essere analizzati facilmente mediante framework di analisi di Hadoop, come MapReduce o Hive. Si può eseguire il provisioning sui cluster di Microsoft Azure HDInsight e questi possono essere configurati per accedere direttamente ai dati archiviati in Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Archiviazione illimitata, file dei petabyte

Data Lake Storage Gen1 offre un'archiviazione illimitata ed è adatto per l'archiviazione di una serie di dati per l'analisi. Ciò non impone limiti per le dimensioni dell’account, le dimensioni dei file o la quantità di dati che possono essere archiviati in un Data Lake. I singoli file possono variare da kilobyte di petabyte rendendo la scelta ideale per memorizzare qualsiasi tipo di dati. I dati vengono archiviati in modo permanente creando più copie e non esiste alcun limite per la durata del tempo durante il quale i dati possono essere archiviati nel Data Lake.

### <a name="performance-tuned-for-big-data-analytics"></a>Prestazioni ottimizzate per l'analisi di Big Data

Data Lake Storage Gen1 è progettato per l'esecuzione di sistemi di analisi di scalabilità di grandi dimensioni che richiedono una velocità effettiva molto elevata per eseguire query e analisi su grandi quantità di dati. Il Data Lake propaga parti di un file su un numero di singoli server di archiviazione. Ciò migliora la velocità effettiva di lettura durante la lettura in parallelo del file per l'esecuzione dell’analisi dei dati.

### <a name="enterprise-ready-highly-available-and-secure"></a>Livello aziendale: Sicurezza e disponibilità elevate

Data Lake Storage Gen1 offre affidabilità e disponibilità standard del settore. Gli asset di dati vengono archiviati in modo permanente creando copie ridondanti per salvaguardarsi da eventuali errori imprevisti. Le aziende possono usare Data Lake Storage Gen1 nelle loro soluzioni come una parte importante della piattaforma di dati esistente.

Data Lake Storage Gen1 offre anche la protezione a livello aziendale per i dati archiviati. Per altre informazioni, vedere [Protezione dei dati in Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Tutti i dati

Data Lake Storage Gen1 può archiviare i dati nel loro formato originale, così come sono, senza alcuna trasformazione. Data Lake Storage Gen1 non richiede la definizione di uno schema prima che i dati vengano caricati, lasciando al singolo framework di analisi l'interpretazione dei dati e la definizione di uno schema al momento dell'analisi. La possibilità di archiviare i file di dimensioni e formati arbitrari consente a Data Lake Storage Gen1 di gestire dati strutturati, semi-strutturati e non strutturati.

I contenitori Data Lake Storage Gen1 per i dati sono essenzialmente cartelle e file. È possibile agire sui dati archiviati mediante SDK, portale di Azure e Azure PowerShell. Dopo aver inserito i dati nell'archivio usando queste interfacce e i contenitori appropriati, è possibile memorizzare qualsiasi tipo di dati. Data Lake Storage Gen1 non esegue una gestione particolare dei dati in base al tipo di dati archiviati.

## <a name="DataLakeStoreSecurity"></a>Proteggere i dati in Data Lake Storage Gen1
Data Lake Storage Gen1 usa Azure Active Directory per l'autenticazione e gli elenchi di controllo di accesso (ACL) per gestire l'accesso ai dati.

| Funzionalità | DESCRIZIONE |
| --- | --- |
| Authentication |Data Lake Storage Gen1 si integra con Azure Active Directory (AAD) per la gestione delle identità e degli accessi per tutti i dati archiviati in Data Lake Storage Gen1. Con l'integrazione, Data Lake Storage Gen1 può usare tutte le funzionalità di AAD che includono l'autenticazione a più fattori, l'accesso condizionale, il controllo degli accessi in base al ruolo, il monitoraggio dell'uso delle applicazioni, il monitoraggio e gli avvisi di sicurezza e così via. Data Lake Storage Gen1 supporta il protocollo OAuth 2.0 per l'autenticazione con l'interfaccia REST. Vedere [Autenticazione di Data Lake Storage Gen1](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Controllo di accesso |Data Lake Storage Gen1 offre il controllo di accesso mediante il supporto delle autorizzazioni di tipo POSIX esposte dal protocollo WebHDFS. Gli elenchi di controllo di accesso possono essere abilitati nella cartella radice, nelle sottocartelle e nei singoli file. Per altre informazioni sul funzionamento degli elenchi di controllo di accesso nel contesto di Data Lake Storage Gen1, vedere [Controllo di accesso in Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Crittografia |Data Lake Storage Gen1 offre anche la crittografia per i dati archiviati nell'account. Le impostazioni della crittografia vengono specificate durante la creazione di un account Data Lake Storage Gen1. È possibile scegliere di crittografare i dati oppure di fare a meno della crittografia. Per altre informazioni, vedere [Crittografia in Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Per istruzioni su come specificare la configurazione relativa alla crittografia, consultare [Iniziare a usare Azure Data Lake Storage Gen1 tramite il portale di Azure](../data-lake-store/data-lake-store-get-started-portal.md). |

Per altre informazioni sulla protezione dei dati in Data Lake Storage Gen1, fare clic sui collegamenti seguenti.

* Per istruzioni su come proteggere i dati in Data Lake Storage Gen1, vedere [Protezione dei dati in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-compatible-with-data-lake-storage-gen1"></a>Applicazioni compatibili con Data Lake Storage Gen1
Data Lake Storage Gen1 è compatibile con la maggior parte dei componenti open source nell'ecosistema Hadoop. Si integra bene anche con altri servizi di Azure.  Vedere i collegamenti seguenti per altre informazioni su come usare Data Lake Storage Gen1 con componenti open source e altri servizi di Azure.

* Consultare [Applicazioni e servizi compatibili con Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) per un elenco delle applicazioni open source che è possibile usare con Data Lake Storage Gen1.
* Vedere [Integrazione con altri servizi di Azure](../data-lake-store/data-lake-store-integrate-with-other-services.md) per informazioni su come usare Data Lake Storage Gen1 con altri servizi di Azure per abilitare una gamma di servizi più ampia.
* Vedere [Scenari per l'uso di Data Lake Storage Gen1](../data-lake-store/data-lake-store-data-scenarios.md) per informazioni su come usare Data Lake Storage Gen1 in scenari come l'inserimento, l'elaborazione, il download e la visualizzazione dei dati.

## <a name="what-is-data-lake-storage-gen1-file-system-adl"></a>Qual è il file system di Data Lake Storage Gen1 (adl://)?
È possibile accedere a Data Lake Storage Gen1 tramite il nuovo file system, AzureDataLakeFilesystem (adl://), negli ambienti Hadoop (disponibile con cluster HDInsight). Le applicazioni e servizi che utilizzano adl:// sono in grado di sfruttare altre ottimizzazioni delle prestazioni che non sono attualmente disponibili in WebHDFS. Data Lake Storage Gen1 offre in questo modo la flessibilità di scegliere se ottenere migliori prestazioni con l'uso consigliato di adl:// o mantenere il codice esistente continuando a usare direttamente l'API WebHDFS. Azure HDInsight usa AzureDataLakeFilesystem per offrire le migliori prestazioni in Data Lake Storage Gen1.

È possibile accedere ai dati in Data Lake Storage Gen1 usando `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Per altre informazioni su come accedere ai dati in Data Lake Storage Gen1, vedere [Visualizzare le proprietà dei dati archiviati](../data-lake-store/data-lake-store-get-started-portal.md#properties)



## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).
* [Introduzione ad Archiviazione di Azure](../storage/common/storage-introduction.md)