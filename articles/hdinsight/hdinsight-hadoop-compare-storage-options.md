---
title: Confrontare le opzioni di archiviazione per l'uso con i cluster Azure HDInsight
description: Questo articolo fornisce una panoramica dei tipi di archiviazione e del relativo funzionamento con Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: d036e56a4ccf826ccd19fb7424b7b76568839b23
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104527"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Confrontare le opzioni di archiviazione per l'uso con i cluster Azure HDInsight

È possibile scegliere tra alcuni servizi di archiviazione di Azure diversi quando si creano cluster HDInsight:

* Archiviazione di Azure
* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1

Questo articolo offre una panoramica di questi tipi di archiviazione e delle relative funzionalità univoche.

La tabella seguente riepiloga i servizi di archiviazione di Azure supportati con diverse versioni di HDInsight:

| Servizio di archiviazione | Tipo di account | Tipo di spazio dei nomi | Servizi supportati | Livelli di prestazioni supportati | Livelli di accesso supportati | HDInsight Version | Tipo di cluster |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Utilizzo generico v2 | Gerarchico (filesystem) | BLOB | Standard | Frequente, ad accesso sporadico, archivio | 3.6+ | Tutti |
|Archiviazione di Azure| Utilizzo generico v2 | Oggetto | BLOB | Standard | Frequente, ad accesso sporadico, archivio | 3.6+ | Tutti |
|Archiviazione di Azure| Utilizzo generico v1 | Oggetto | BLOB | Standard | N/D | Tutti | Tutti |
|Archiviazione di Azure| Archiviazione BLOB * * | Object | BLOB in blocchi | Standard | Frequente, ad accesso sporadico, archivio | Tutti | Tutti |
|Azure Data Lake Storage Gen1| N/D | Gerarchico (filesystem) | N/D | N/D | N/D | solo 3,6 | Tutti tranne HBase |

\* * Per i cluster HDInsight, solo gli account di archiviazione secondari possono essere di tipo BlobStorage e il BLOB di pagine non è un'opzione di archiviazione supportata.

Per altre informazioni sui tipi di account di archiviazione di Azure, vedere [Panoramica dell'account di archiviazione di Azure](../storage/common/storage-account-overview.md)

Per altre informazioni sui livelli di accesso di archiviazione di Azure [, vedere Archiviazione BLOB di Azure: Livelli di archiviazione Premium (anteprima), ad accesso frequente, ad accesso sporadico e archivio](../storage/blobs/storage-blob-storage-tiers.md)

È possibile creare un cluster usando diverse combinazioni di servizi per l'archiviazione secondaria primaria e facoltativa. Nella tabella seguente sono riepilogate le configurazioni di archiviazione del cluster attualmente supportate in HDInsight:

| HDInsight Version | Risorsa di archiviazione primaria | Archiviazione secondaria | Supportato |
|---|---|---|---|
| 3,6 & 4,0 | Per utilizzo generico V1, per utilizzo generico V2 | Per utilizzo generico V1, per utilizzo generico V2, BlobStorage (BLOB in blocchi) | Sì |
| 3,6 & 4,0 | Per utilizzo generico V1, per utilizzo generico V2 | Data Lake Storage Gen2 | No |
| 3,6 & 4,0 | Per utilizzo generico V1, per utilizzo generico V2 | Data Lake Storage Gen1 | Sì |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Data Lake Storage Gen2 | Sì |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Per utilizzo generico V1, per utilizzo generico V2, BlobStorage (BLOB in blocchi) | Yes |
| 3,6 & 4,0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | No |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Sì |
| 3.6 | Data Lake Storage Gen1 | Per utilizzo generico V1, per utilizzo generico V2, BlobStorage (BLOB in blocchi) | Yes |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | No |
| 4.0 | Data Lake Storage Gen1 | Qualsiasi | No |

\* = Può trattarsi di uno o più account Data Lake Storage Gen2, purché tutti i set di impostazioni usino la stessa identità gestita per l'accesso al cluster.

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Usare Azure Data Lake Storage Gen2 con Apache Hadoop in Azure HDInsight

Azure Data Lake Storage Gen2 usa le funzionalità di base di Azure Data Lake Storage Gen1 e le integra in Archiviazione BLOB di Azure. Queste funzionalità includono un file system compatibile con Hadoop, Azure Active Directory (Azure AD) ed elenchi di controllo di accesso (ACL) basati su POSIX. Questa combinazione consente di sfruttare le prestazioni di Azure Data Lake Storage Gen1 e di usare anche la suddivisione in livelli e la gestione del ciclo di vita dei dati di Archiviazione BLOB.

Per altre informazioni su Azure Data Lake Storage Gen2, consultare [Introduzione ad Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Funzionalità principali di Azure Data Lake Storage Gen2

* **Accesso compatibile con Hadoop:** in Azure Data Lake Storage Gen2 è possibile gestire i dati e accedervi esattamente come con Hadoop Distributed File System (HDFS). Il driver del file system BLOB di Azure (ABFS) è disponibile in tutti gli ambienti Apache Hadoop, tra cui Azure HDInsight e Azure Databricks. Usarlo per accedere ai dati archiviati in Data Lake Storage Gen2.

* **Superset di autorizzazioni POSIX:** il modello di sicurezza per Data Lake Gen2 supporta l'elenco di controllo di accesso e le autorizzazioni POSIX oltre a una granularità aggiuntiva specifica di Data Lake Storage Gen2. È possibile configurare le impostazioni tramite gli strumenti di amministrazione o framework quali Apache Hive e Apache Spark.

* **Convenienza:** Data Lake Storage Gen2 offre capacità di archiviazione e transazioni a basso costo. Funzionalità come il ciclo di vita di Archiviazione BLOB di Azure consentono di ridurre i costi modificando le tariffe di fatturazione man mano che procede il ciclo di vita dei dati.

* **Compatibilità con gli strumenti, i framework e le app di archiviazione BLOB:** Data Lake Storage Gen2 continua a funzionare con una vasta gamma di strumenti, framework e applicazioni per archiviazione Blob.

* **Driver ottimizzato:** il driver ABFS è ottimizzato appositamente per l'analisi dei Big Data. Le API REST corrispondenti vengono rilevate tramite l'endpoint del file system distribuito (DFS), ovvero dfs.core.windows.net.

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Novità di Azure Data Lake Storage Gen2

#### <a name="managed-identities-for-secure-file-access"></a>Identità gestite per un accesso sicuro ai file

Azure HDInsight usa identità gestite per proteggere l'accesso del cluster ai file in Azure Data Lake Storage Gen2. Le identità gestite sono una funzionalità di Azure Active Directory che fornisce ai servizi di Azure un set di credenziali gestite automaticamente. Queste credenziali possono essere usate per eseguire l'autenticazione per qualsiasi servizio che supporti l'autenticazione Active Directory. L'uso di identità gestite non richiede l'archiviazione delle credenziali in file di codice o di configurazione.

Per altre informazioni, vedere [Identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-file-system-driver"></a>Driver ABFS

Le applicazioni Apache Hadoop prevedono in modo nativo di leggere e scrivere i dati dallo spazio di archiviazione su disco locale. Un driver del file system Hadoop come ABFS consente alle applicazioni Hadoop di usare l'archiviazione nel cloud emulando le normali operazioni del file system Hadoop. Il driver converte questi comandi ricevuti dall'applicazione in operazioni riconosciute dalla piattaforma di archiviazione nel cloud effettiva.

Nelle versioni precedenti il driver del file system Hadoop converte tutte le operazioni del file system in chiamate API REST di Archiviazione di Azure sul lato client e quindi richiama l'API REST. Questa conversione lato client tuttavia restituisce più chiamate API REST per una singola operazione di file system come la ridenominazione di un file. ABFS ha spostato parte della logica del file system Hadoop dal lato client al lato server. L'API di Azure Data Lake Storage Gen2 viene ora eseguita in parallelo con l'API BLOB. Questa migrazione comporta un miglioramento delle prestazioni perché ora le operazioni comuni del file system Hadoop possono essere eseguite con una chiamata API REST.

Per altre informazioni, vedere [Driver del file system del BLOB di Azure: un driver di Archiviazione di Azure dedicato per Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Schema URI di Azure Data Lake Storage Gen2 

Azure Data Lake Storage Gen2 usa un nuovo schema URI per accedere ai file in Archiviazione di Azure da HDInsight:

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Lo schema URI offre l'accesso con crittografia SSL (prefisso `abfss://`) e l'accesso non crittografato (prefisso `abfs://`). Se possibile, usare `abfss` anche per accedere ai dati presenti nella stessa area di Azure.

`<FILE_SYSTEM_NAME>` identifica il percorso del file system di Data Lake Storage Gen2.

`<ACCOUNT_NAME>` identifica il nome dell'account di Archiviazione di Azure. È necessario specificare un nome di dominio completo (FQDN).

`<PATH>` è il nome del percorso HDFS di file o directory.

Se i valori per `<FILE_SYSTEM_NAME>` e `<ACCOUNT_NAME>` non vengono specificati, viene usato il file system predefinito. Per i file presenti nel file system predefinito, usare un percorso relativo o un percorso assoluto. Ad esempio, è possibile fare riferimento al file `hadoop-mapreduce-examples.jar` incluso nei cluster HDInsight usando uno dei percorsi seguenti:

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> Nei cluster HDInsight versione 2.1 e 1.6 il nome del file è `hadoop-examples.jar`. Quando si usano file al di fuori di HDInsight, la maggior parte delle utilità non riconosce il formato ABFS, ma richiede invece un formato di percorso di base, ad esempio `example/jars/hadoop-mapreduce-examples.jar`.

Per altre informazioni, vedere [Usare l'URI di Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="azure-storage"></a>Archiviazione di Azure

Archiviazione di Azure è una soluzione di archiviazione affidabile di utilizzo generico che si integra perfettamente con HDInsight. HDInsight può usare un contenitore BLOB in Archiviazione di Azure come file system predefinito per il cluster. Grazie a un'interfaccia HDFS, tutti i componenti disponibili in HDInsight possono agire direttamente su dati strutturati o non strutturati archiviati come BLOB.

Si consiglia di usare contenitori di archiviazione separati per l'archiviazione del cluster predefinita e i dati aziendali, per isolare i log HDInsight e i file temporanei dai dati aziendali. È inoltre consigliabile eliminare il contenitore BLOB predefinito, che contiene i registri applicazioni e di sistema, dopo ogni uso per ridurre i costi di archiviazione. Assicurarsi di recuperare i log prima di eliminare il contenitore.

Se si sceglie di proteggere l'account di archiviazione con le restrizioni relative a **firewall e reti virtuali** nelle **reti selezionate**, assicurarsi di abilitare l'eccezione **Consenti servizi Microsoft attendibili...** in modo che HDInsight possa accedere alla risorsa di archiviazione account.

### <a name="hdinsight-storage-architecture"></a>Architettura di archiviazione di HDInsight

Nel diagramma seguente viene sintetizzata l'architettura HDInsight di Archiviazione di Azure:

![Architettura archiviazione HDInsight](./media/hdinsight-hadoop-compare-storage-options/storage-architecture.png "Architettura archiviazione HDInsight")

HDInsight offre accesso al file system distribuito collegato localmente ai nodi di calcolo. Il file system è accessibile tramite l'URI completo, ad esempio:

    hdfs://<namenodehost>/<path>

Tramite HDInsight è anche possibile accedere ai dati in Archiviazione di Azure. La sintassi è la seguente:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Tenere conto delle considerazioni seguenti quando si usa un account di Archiviazione di Azure con cluster HDInsight:

* **Contenitori negli account di archiviazione connessi a un cluster:** poiché il nome e la chiave dell'account sono associati al cluster durante il processo di creazione, si disporrà di un accesso completo ai BLOB presenti in tali contenitori.

* **Contenitori pubblici o BLOB pubblici in account di archiviazione *non* connessi a un cluster:** si dispone dell'autorizzazione di sola lettura ai BLOB nei contenitori.
  
  > [!NOTE]  
  > Un contenitore pubblico consente di ottenere un elenco di tutti i BLOB in esso disponibili, nonché i metadati del contenitore stesso. È possibile accedere a un BLOB pubblico solo se ne conosce l'URL esatto. Per altre informazioni, vedere [Gestire l'accesso in lettura anonimo a contenitori e BLOB](../storage/blobs/storage-manage-access-to-resources.md).

* **Contenitori privati in account di archiviazione *non* connessi a un cluster:** non è possibile accedere ai BLOB nei contenitori a meno che non sia stato definito l'account di archiviazione quando sono stati inviati i processi WebHCat. 

Gli account di archiviazione definiti durante il processo di creazione, con le rispettive chiavi, sono archiviati in %HADOOP_HOME%/conf/core-site.xml nei nodi del cluster. Per impostazione predefinita, HDInsight usa gli account di archiviazione definiti nel file core-site.xml. È possibile modificare questa impostazione usando [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Più processi WebHCat, inclusi Apache Hive, MapReduce, lo streaming Apache Hadoop e Apache Pig, possono includere una descrizione degli account di archiviazione e dei metadati. Questo è valido attualmente per Pig con account di archiviazione, ma non per i metadati. Per altre informazioni, vedere [Using an HDInsight cluster with alternate storage accounts and metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx) (Uso di un cluster HDInsight con account di archiviazione e metastore alternativi).

I BLOB possono essere usati per i dati strutturati e non strutturati. I contenitori BLOB archiviano i dati come coppie chiave/valore e non dispongono di una gerarchia di directory. Il nome della chiave tuttavia può includere una barra (/) per far sembrare che un file sia archiviato in una struttura di directory. Ad esempio, la chiave di un BLOB può essere `input/log1.txt`. Non esiste una directory `input`, ma a causa della barra nel nome, la chiave è simile a un percorso file.

### <a id="benefits"></a>Vantaggi di Archiviazione di Azure
I cluster di elaborazione e le risorse di archiviazione senza percorso condiviso hanno costi di prestazioni impliciti. Questi costi vengono ridotti creando i cluster di elaborazione in prossimità delle risorse di account di archiviazione all'interno dell'area di Azure. In quest'area i nodi di calcolo possono accedere in modo efficiente ai dati attraverso la rete ad alta velocità all'interno di Archiviazione di Azure.

Se si archiviano i dati in Archiviazione di Azure anziché in HDFS, si ottengono diversi vantaggi:

* **Condivisione e riutilizzo dei dati:** i dati in HDFS sono situati all'interno del cluster di elaborazione. Solo le applicazioni che hanno accesso al cluster di calcolo, quindi, possono usare i dati tramite le API HDFS. È possibile accedere ai dati in Archiviazione di Azure invece tramite le API HDFS o tramite le API REST dell'archiviazione BLOB. Con questa soluzione, è possibile usare una più ampia gamma di strumenti e applicazioni, compresi altri cluster HDInsight, per produrre e usare i dati.

* **Archiviazione dati:** quando si archiviano dati in Archiviazione di Azure, i cluster HDInsight usati per i calcoli possono essere eliminati in modo sicuro senza perdita di dati utente.

* **Costo di archiviazione dei dati:** l'archiviazione a lungo termine di dati in DFS è più costosa rispetto ad Archiviazione di Azure, dato che il costo di un cluster di elaborazione è più alto rispetto a quello di Archiviazione di Azure. Poiché inoltre non è necessario ricaricare i dati ogni volta che si genera un cluster di elaborazione, si risparmiano anche i costi di caricamento dei dati.

* **Scalabilità orizzontale elastica:** anche se Hadoop Distributed File System offre scalabilità orizzontale del file system, la scalabilità è determinata dal numero di nodi creati per il cluster. Il ridimensionamento del cluster può essere quindi un processo più complicato rispetto al semplice uso delle funzionalità di scalabilità elastica automaticamente disponibili in Archiviazione di Azure.

* **Replica geografica:** Archiviazione di Azure supporta la replica geografica. Sebbene la replica geografica offra ripristino geografico e ridondanza dei dati, un failover nella posizione sottoposta a replica geografica incide negativamente sulle prestazioni e può comportare costi aggiuntivi. Scegliere pertanto la replica geografica con cautela e solo se il valore dei dati giustifica i costi aggiuntivi.

Alcuni pacchetti e processi MapReduce possono creare risultati intermedi che non si vuole archiviare in Archiviazione di Azure. In questo caso, è possibile scegliere di archiviare i dati nel file system HDFS locale. HDInsight usa DFS per molti di questi risultati intermedi nei processi Hive e in altri processi.

> [!NOTE]  
> La maggior parte dei comandi HDFS, ad esempio `ls`, `copyFromLocal` e `mkdir`, funziona come previsto in Archiviazione di Azure. Solo i comandi specifici dell'implementazione HDFS nativa (denominata DFS), ad esempio `fschk` e `dfsadmin`, hanno un comportamento diverso in Archiviazione di Azure.

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Panoramica di Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 è un repository con iperscalabilità a livello aziendale per carichi di lavoro di analisi di Big Data. Con Azure Data Lake è possibile acquisire dati di qualsiasi dimensione, tipo e velocità di inserimento in un'unica posizione per le analisi esplorative e operative.

Accedere a Data Lake Storage Gen1 da Hadoop (disponibile con un cluster HDInsight) mediante le API REST compatibili con WebHDFS. Data Lake Storage Gen1 è progettato per abilitare l'analisi dei dati archiviati e le prestazioni sono ottimizzate per scenari di analisi dei dati. Per impostazione predefinita, include le funzionalità essenziali per casi d'uso aziendali reali. Queste funzionalità includono sicurezza, gestibilità, scalabilità, affidabilità e disponibilità.

Per altre informazioni su Azure Data Lake Storage Gen1, vedere l'articolo dettagliato [Panoramica di Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

Di seguito sono riportate le principali funzionalità di Data Lake Storage Gen1.

### <a name="compatibility-with-hadoop"></a>Compatibilità con Hadoop

Data Lake Storage Gen1 è un file system Apache Hadoop compatibile con HDFS e che funziona con l'ecosistema Hadoop.  Le applicazioni HDInsight esistenti o i servizi che usano l'API WebHDFS possono integrarsi facilmente con Data Lake Storage Gen1. Data Lake Storage Gen1 presenta anche un'interfaccia REST compatibile con WebHDFS per le applicazioni.

I dati archiviati in Data Lake Storage Gen1 possono essere analizzati facilmente mediante framework di analisi di Hadoop, come MapReduce o Hive. È possibile eseguire il provisioning dei cluster Azure HDInsight e configurarli per accedere direttamente ai dati archiviati in Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Archiviazione illimitata, file dei petabyte

Data Lake Storage Gen1 offre un'archiviazione illimitata ed è adatto per l'archiviazione di una serie di dati per l'analisi. Non impone limiti per le dimensioni dell'account, le dimensioni dei file o la quantità di dati che possono essere archiviati in un Data Lake. I singoli file possono avere dimensioni di ordine variabile, da kilobyte a petabyte, rendendo Data Lake Storage Gen1 la scelta ideale per l'archiviazione di qualsiasi tipo di dati. I dati vengono archiviati in modo permanente creando più copie e non esistono limiti di tempo per la permanenza nel Data Lake.

### <a name="performance-tuning-for-big-data-analytics"></a>Ottimizzazione delle prestazioni per l'analisi di Big Data

Data Lake Storage Gen1 è progettato per l'esecuzione di sistemi di analisi su larga scala che richiedono una velocità effettiva elevata per eseguire query e analisi su grandi quantità di dati. Il Data Lake distribuisce parti di un file su più server di archiviazione singoli. Durante l'analisi dei dati, questa configurazione migliora la velocità effettiva di lettura quando il file viene letto in parallelo.

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>Idoneità per le aziende - Sicurezza e disponibilità elevate

Data Lake Storage Gen1 offre affidabilità e disponibilità standard del settore. Gli asset di dati vengono archiviati in modo permanente: copie ridondanti salvaguardano da errori imprevisti. Le aziende possono usare Data Lake Storage Gen1 nelle loro soluzioni come una parte importante della piattaforma di dati esistente.

Data Lake Storage Gen1 offre anche protezione a livello aziendale per i dati archiviati. Per altre informazioni, vedere [Protezione dei dati in Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="flexible-data-structures"></a>Strutture di dati flessibili

Data Lake Storage Gen1 può archiviare i dati nel formato nativo, così come sono, senza alcuna trasformazione preliminare. Non richiede la definizione di uno schema prima che i dati vengono caricati. Il singolo framework di analisi interpreta i dati e definisce uno schema al momento dell'analisi. Poiché può archiviare file di dimensioni e formati arbitrari, Data Lake Storage Gen1 può gestire dati strutturati, semistrutturati e non strutturati.

I contenitori Data Lake Storage Gen1 per i dati sono essenzialmente cartelle e file. È possibile agire sui dati archiviati mediante SDK, il portale di Azure e Azure PowerShell. Dopo aver inserito i dati nell'archivio usando queste interfacce e i contenitori appropriati, è possibile archiviare qualsiasi tipo di dati. Data Lake Storage Gen1 non esegue una gestione speciale dei dati in base al tipo di dati archiviati.

## <a name="DataLakeStoreSecurity"></a>Sicurezza dei dati in Data Lake Storage Gen1
Data Lake Storage Gen1 usa Azure Active Directory per l'autenticazione e gli elenchi di controllo di accesso (ACL) per la gestione dell'accesso ai dati.

| **Funzionalità** | **Descrizione** |
| --- | --- |
| Authentication |Data Lake Storage Gen1 si integra con Azure Active Directory (Azure AD) per la gestione delle identità e degli accessi per tutti i dati in esso archiviati. Grazie a questa integrazione, Data Lake Storage Gen1 usufruisce di tutte le funzionalità di Azure AD. Queste funzionalità includono l'autenticazione a più fattori, l'accesso condizionale, il controllo degli accessi in base al ruolo, il monitoraggio dell'utilizzo delle applicazioni, il monitoraggio della sicurezza e gli avvisi e così via. Data Lake Storage Gen1 supporta il protocollo OAuth 2.0 per l'autenticazione nell'interfaccia REST. Vedere [Autenticazione con Azure Data Lake Storage Gen1 tramite Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).|
| Controllo di accesso |Data Lake Storage Gen1 offre il controllo di accesso mediante il supporto delle autorizzazioni di tipo POSIX esposte dal protocollo WebHDFS. Gli elenchi di controllo di accesso possono essere abilitati nella cartella radice, nelle sottocartelle e nei singoli file. Per altre informazioni sul funzionamento degli ACL nel contesto di Data Lake Storage Gen1, vedere [Controllo di accesso in Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Crittografia |Data Lake Storage Gen1 offre anche la crittografia per i dati archiviati nell'account. Le impostazioni della crittografia vengono specificate durante la creazione di un account Data Lake Storage Gen1. È possibile scegliere di crittografare i dati oppure di fare a meno della crittografia. Per altre informazioni, vedere [Crittografia in Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Per istruzioni su come specificare una configurazione relativa alla crittografia, vedere [Iniziare a usare Azure Data Lake Storage Gen1 tramite il portale di Azure](../data-lake-store/data-lake-store-get-started-portal.md). |

Per altre informazioni su come proteggere i dati in Data Lake Storage Gen1, vedere [Protezione dei dati archiviati in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Applicazioni compatibili con Data Lake Storage Gen1
Data Lake Storage Gen1 è compatibile con la maggior parte dei componenti open source nell'ecosistema Hadoop. Si integra bene anche con altri servizi di Azure.  Vedere i collegamenti seguenti per altre informazioni su come usare Data Lake Storage Gen1 con componenti open source e altri servizi di Azure.

* Vedere [Applicazioni Big Data open source compatibili con Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) per un elenco delle applicazioni open source che interagiscono con Data Lake Storage Gen1.
* Vedere [Integrazione di Azure Data Lake Storage Gen1 con altri servizi di Azure](../data-lake-store/data-lake-store-integrate-with-other-services.md) per informazioni su come usare Data Lake Storage Gen1 con altri servizi di Azure per abilitare una più ampia gamma di scenari.
* Vedere [Uso di Azure Data Lake Storage Gen1 per i requisiti di Big Data](../data-lake-store/data-lake-store-data-scenarios.md) per informazioni su come usare Data Lake Storage Gen1 in scenari come l'inserimento, l'elaborazione, il download e la visualizzazione dei dati.

## <a name="data-lake-storage-gen1-file-system-adl"></a>File system di Data Lake Storage Gen1 (adl://)
Negli ambienti Hadoop (disponibili con un cluster HDInsight) è possibile accedere a Data Lake Storage Gen1 tramite il nuovo file system, AzureDataLakeFilesystem (adl://). Le prestazioni delle applicazioni e dei servizi che usano adl:// possono essere ottimizzate in modi che non sono attualmente disponibili in WebHDFS. Di conseguenza, quando si usa Data Lake Storage Gen1, è possibile scegliere se ottenere migliori prestazioni con l'uso consigliato di adl:// o mantenere il codice esistente continuando a usare direttamente l'API WebHDFS. Azure HDInsight sfrutta al massimo AzureDataLakeFilesystem per offrire le migliori prestazioni in Data Lake Storage Gen1.

Per accedere ai dati in Data Lake Storage Gen1 è possibile usare:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Per altre informazioni su come accedere ai dati in Data Lake Storage Gen1, vedere [Azioni disponibili sui dati archiviati](../data-lake-store/data-lake-store-get-started-portal.md#properties).



## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introduzione ad Archiviazione di Azure](../storage/common/storage-introduction.md)
