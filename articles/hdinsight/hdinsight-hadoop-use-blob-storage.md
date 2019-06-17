---
title: Eseguire query sui dati da una risorsa di archiviazione di Azure compatibile con HDFS - Azure HDInsight
description: Informazioni su come eseguire query sui dati da Archiviazione di Azure e Azure Data Lake Storage per archiviare i risultati dell'analisi.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 6e0192029decef95dcaecc0c60dce5fd5b6f99ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66479913"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Usare una risorsa di archiviazione di Azure con cluster Azure HDInsight

Per analizzare dati in un cluster HDInsight, è possibile archiviare i dati sia in [archiviazione di Azure](../storage/common/storage-introduction.md), [Azure Data Lake Storage generazione 1](../data-lake-store/data-lake-store-overview.md)/[Azure Data Lake Storage generazione 2](../storage/blobs/data-lake-storage-introduction.md), o una combinazione. Queste opzioni di archiviazione consentono di eliminare in modo sicuro il cluster HDInsight usati per i calcoli, senza perdita di dati utente.

Apache Hadoop supporta una nozione del file system predefinito. Il file system predefinito implica uno schema e un'autorità predefiniti e può essere usato anche per risolvere percorsi relativi. Durante il processo di creazione del cluster HDInsight è possibile specificare un contenitore BLOB in Archiviazione di Azure come file system predefinito. In alternativa, con HDInsight 3.6 è possibile selezionare Archiviazione di Azure o Azure Data Lake Storage Gen 1/ Azure Data Lake Storage Gen 2 come file system predefinito, con alcune eccezioni. Per il supporto dell'uso di Data Lake Storage Gen 1 come risorsa di archiviazione sia predefinita che collegata, vedere [Disponibilità per il cluster HDInsight](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

Questo articolo illustra come usare Archiviazione di Azure con i cluster HDInsight. Per informazioni sull'uso di Data Lake Storage Gen 1 con i cluster HDInsight, vedere [Usare Azure Data Lake Storage con cluster Azure HDInsight](hdinsight-hadoop-use-data-lake-store.md). Per altre informazioni sulla creazione di un cluster HDInsight, vedere [Configurare i cluster di HDInsight con Hadoop, Spark, Kafka e altro ancora](hdinsight-hadoop-provision-linux-clusters.md).

Archiviazione di Azure è una soluzione di archiviazione affidabile, con finalità generali che si integra facilmente con HDInsight. HDInsight può usare un contenitore BLOB in Archiviazione di Azure come file system predefinito per il cluster. Grazie a un'interfaccia HDFS (Hadoop Distributed File System), tutti i componenti disponibili in HDInsight possono agire direttamente sui dati strutturati o non strutturati archiviati come BLOB.

> [!WARNING]  
> Tipologia di account di archiviazione **BlobStorage** può essere utilizzato solo come archiviazione secondaria per i cluster HDInsight.

| Tipologia di account di archiviazione | Servizi supportati | Livelli di prestazioni supportati | Livelli di accesso supportati |
|----------------------|--------------------|-----------------------------|------------------------|
| Archiviazione v2 (utilizzo generico V2)  | Blob     | Standard                    | Accesso frequente, ad accesso sporadico, archivio\*   |
| Archiviazione (per utilizzo generico v1)   | Blob     | Standard                    | N/D                    |
| BlobStorage                    | Blob     | Standard                    | Accesso frequente, ad accesso sporadico, archivio\*   |

Non è consigliabile usare il contenitore BLOB predefinito per l'archiviazione dei dati aziendali. È consigliabile eliminare il contenitore BLOB predefinito dopo ogni uso per ridurre i costi di archiviazione. Il contenitore predefinito include log di sistema e applicazioni. Assicurarsi di recuperare i log prima di eliminare il contenitore.

La condivisione di un contenitore BLOB come file system predefinito tra più cluster non è supportata.

> [!NOTE]  
> Il livello di accesso archivio è un livello offline che ha una latenza di recupero di diverse ore e non è consigliato per l'uso con HDInsight. Per altre informazioni, vedere [Livello di accesso archivio](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

Se si sceglie di proteggere l'account di archiviazione con il **firewall e reti virtuali** restrizioni sulle **reti selezionate**, assicurarsi di abilitare l'eccezione **Consenti attendibili Microsoft servizi...**  in modo che HDInsight può accedere all'account di archiviazione.

## <a name="hdinsight-storage-architecture"></a>Architettura di archiviazione di HDInsight
Nel diagramma seguente viene mostrata una visualizzazione astratta dell'architettura di archiviazione HDInsight dell'uso di Archiviazione di Azure:

I ![cluster Hadoop usano l'API HDFS per accedere e archiviare dati strutturati e non strutturati nell'archiviazione BLOB.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "Architettura di archiviazione di HDInsight")

HDInsight offre accesso al file system distribuito collegato localmente ai nodi di calcolo. Il file system è accessibile tramite l'URI completo, ad esempio:

    hdfs://<namenodehost>/<path>

HDInsight consente anche di accedere ai dati archiviati in Archiviazione di Azure. La sintassi è:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Di seguito sono riportate alcune considerazioni sull'uso di un account di Archiviazione di Azure con i cluster HDInsight.

* **Contenitori negli account di archiviazione connessi a un cluster:** poiché il nome e la chiave dell'account sono associati al cluster durante il processo di creazione, si disporrà di un accesso completo ai BLOB presenti in tali contenitori.

* **Contenitori pubblici o BLOB pubblici negli account di archiviazione che non sono connessi a un cluster:** si dispone dell'autorizzazione di sola lettura ai BLOB nei contenitori.
  
> [!NOTE]  
> Un contenitore pubblico consente di ottenere un elenco di tutti i BLOB disponibili al suo interno, nonché i metadati del contenitore stesso. È possibile accedere a un BLOB pubblico solo se ne conosce l'URL esatto. Per altre informazioni, vedere [Gestire l'accesso a contenitori e Blob](../storage/blobs/storage-manage-access-to-resources.md).

* **Contenitori privati negli account di archiviazione NON connessi a un cluster:** non è possibile accedere ai BLOB nei contenitori a meno che non sia stato definito l'account di archiviazione quando sono stati inviati i processi WebHCat. Questo concetto verrà spiegato più avanti nell'articolo.

Gli account di archiviazione definiti nel processo di creazione e le relative chiavi vengono archiviati `%HADOOP_HOME%/conf/core-site.xml` nei nodi del cluster. Il comportamento predefinito di HDInsight consiste nell'usare gli account di archiviazione definiti nel file core-site.xml. È possibile modificare questa impostazione usando [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Più processi WebHCat, inclusi Apache Hive, MapReduce, lo streaming Apache Hadoop e Apache Pig, possono includere una descrizione degli account di archiviazione e dei metadati. (questo è valido solo per Pig con account di archiviazione, non per i metadati). Per altre informazioni, vedere [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx) (Uso di un cluster HDInsight con account di archiviazione e metastore alternativi).

I BLOB possono essere usati per i dati strutturati e non strutturati. I contenitori BLOB archiviano i dati come coppie chiave-valore e non esiste una gerarchia di directory. È tuttavia possibile usare il carattere barra ( / ) all'interno del nome della chiave per far sembrare che un file sia archiviato in una struttura di directory. Ad esempio, la chiave di un BLOB potrebbe essere *input/log1.txt*. Non esiste realmente una directory *input* ma, grazie alla presenza del carattere barra, il nome della chiave ha l'aspetto di un percorso di file.

## <a id="benefits"></a>Vantaggi di Archiviazione di Azure
Il costo di esecuzione implicita di non condivisione percorso per i cluster di calcolo e risorse di archiviazione è stata risolta dal modo in cui i cluster di calcolo vengono creati in prossimità delle risorse di account di archiviazione nell'area di Azure, in cui la rete ad alta velocità rende efficiente per il per i dati in archiviazione di Azure di accedere ai nodi di calcolo.

L'archiviazione dei dati in Archiviazione di Azure anziché in HDFS offre numerosi vantaggi:

* **Condivisione e riutilizzo dei dati:** i dati in HDFS sono situati all'interno del cluster di elaborazione. Solo le applicazioni che hanno accesso al cluster di calcolo, quindi, possono usare i dati tramite le API HDFS. I dati in archiviazione di Azure sono accessibili tramite le API HDFS o tramite il [API REST di archiviazione Blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API). Di conseguenza, è possibile produrre e usare i dati mediante un set più ampio di strumenti e applicazioni, compresi altri cluster HDInsight.

* **Archiviazione dati:** l'archiviazione dei dati in Archiviazione di Azure consente l'eliminazione sicura dei cluster HDInsight usati per i calcoli, senza perdita di dati utente.

* **Costo di archiviazione dei dati:** l'archiviazione a lungo termine dei dati in DFS è più costosa rispetto ad Archiviazione di Azure, dato che il costo di un cluster di elaborazione è più alto di quello di Archiviazione di Azure. Poiché, inoltre, non è necessario ricaricare i dati ogni volta che si genera un cluster di calcolo, si risparmiano anche i costi di caricamento dei dati.

* **Scalabilità orizzontale elastica:** anche se Hadoop Distributed File System offre scalabilità orizzontale del file system, la scalabilità è determinata dal numero di nodi creati per il cluster. Modificare la scalabilità può diventare quindi un processo più complicato rispetto al semplice uso delle funzionalità di scalabilità elastica automaticamente disponibili in Archiviazione di Azure.

* **Replica geografica:** Archiviazione di Azure può eseguire la replica geografica. Sebbene questo offra ripristino geografico e ridondanza dei dati, un failover nella posizione sottoposta a replica geografica incide molto negativamente sulle prestazioni e può comportare costi aggiuntivi. È pertanto consigliabile scegliere la scelta della replica geografica con oculatezza e solo se il valore dei dati giustifica i costi aggiuntivi.

Alcuni pacchetti e processi MapReduce possono creare risultati intermedi che non vale la pena di archiviare in Archiviazione di Azure. In questo caso, è possibile scegliere di archiviare i dati nel file system HDFS locale. In effetti, HDInsight utilizza DFS per molti di questi risultati intermedi nei processi Hive e in altri processi.

> [!NOTE]  
> La maggior parte dei comandi Hadoop Distributed File System, ad esempio `ls`, `copyFromLocal` e `mkdir`, funziona come previsto. Solo i comandi specifici dell'implementazione Hadoop Distributed File System nativa (denominata DFS), ad esempio `fschk` e `dfsadmin`, hanno un comportamento diverso in Archiviazione di Azure.

## <a name="address-files-in-azure-storage"></a>Accedere ai file in Archiviazione di Azure
Lo schema URI per l'accesso ai file in Archiviazione di Azure da HDInsight è il seguente:

```config
wasb://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

Lo schema URI offre l'accesso non crittografato (con il prefisso *wasb:* ) e l'accesso con crittografia SSL (con il prefisso *wasbs*). Se possibile, è consigliabile usare *wasbs* anche per accedere ai dati presenti nella stessa area di Azure.

Il `<BlobStorageContainerName>` identifica il nome del contenitore blob in archiviazione di Azure.
Il `<StorageAccountName>` identifica il nome di account di archiviazione di Azure. È necessario specificare un nome di dominio completo (FQDN).

Se nessuno di essi `<BlobStorageContainerName>` né `<StorageAccountName>` è stata specificata, viene usato il file system predefinito. Per i file presenti nel file system predefinito è possibile usare un percorso relativo o un percorso assoluto. Ad esempio, è possibile fare riferimento al file *hadoop-mapreduce-examples.jar* nei cluster HDInsight nei due modi seguenti:

```config
wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasb:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Nei cluster HDInsight versione 2.1 e 1.6 il nome del file è `hadoop-examples.jar`.

Il percorso è il nome del percorso file o directory HDFS. Poiché i contenitori in Archiviazione di Azure sono archivi chiave-valore, non esiste un vero file system gerarchico. Un carattere barra ( / ) all'interno di una chiave BLOB viene interpretato come un separatore di directory. Ad esempio, il nome BLOB di *hadoop-mapreduce-examples.jar* è:

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Quando si usano i BLOB al di fuori di HDInsight, la maggior parte delle utilità non riconosce il formato WASB e richiede invece un formato del percorso di base, ad esempio `example/jars/hadoop-mapreduce-examples.jar`.

##  <a name="blob-containers"></a>Contenitori BLOB
Per usare i BLOB, creare innanzitutto un' [account di archiviazione Azure](../storage/common/storage-create-storage-account.md). Come parte della procedura, è necessario specificare l'area di Azure in cui viene creato l'account di archiviazione. L'account di archiviazione deve trovarsi nella stessa area del cluster. Il database di SQL Server del metastore Hive, inoltre, deve trovarsi nella stessa area del database di SQL Server del metastore Apache Oozie.

Ovunque si trovi, ogni oggetto BLOB creato appartiene a un contenitore presente nell'account di archiviazione di Azure. Può trattarsi di un contenitore BLOB esistente, creato all'esterno di HDInsight, oppure di un contenitore creato per un cluster HDInsight.

Il contenitore BLOB predefinito archivia informazioni specifiche del cluster come i log e la cronologia processo. Non condividere un contenitore BLOB predefinito con più cluster HDInsight. Questa operazione potrebbe danneggiare la cronologia processo. È consigliabile utilizzare un contenitore diverso per ogni cluster e inserire i dati condivisi in un account di archiviazione collegato specificato nella distribuzione di tutti i cluster rilevanti, anziché l'account di archiviazione predefinito. Per altre informazioni sulla configurazione degli account di archiviazione collegati, vedere [i cluster HDInsight crea](hdinsight-hadoop-provision-linux-clusters.md). È comunque possibile riusare un contenitore di archiviazione predefinito dopo l'eliminazione del cluster HDInsight originale. Per i cluster HBase è in realtà possibile mantenere i dati e lo schema della tabella HBase creando un nuovo cluster HBase tramite il contenitore BLOB predefinito usato da un cluster HBase eliminato.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>L'interazione con archiviazione di Azure

Microsoft offre gli strumenti seguenti da usare con archiviazione di Azure:

| Strumento | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Portale di Azure](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Interfaccia della riga di comando di Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="use-additional-storage-accounts"></a>Usare account di archiviazione aggiuntivi

Durante la creazione di un cluster HDInsight viene specificato l'account di Archiviazione di Azure da associare al cluster. Oltre a questo account di archiviazione, durante il processo di creazione o dopo la creazione di un cluster è possibile aggiungere altri account di archiviazione della stessa sottoscrizione di Azure o di sottoscrizioni di Azure diverse. Per istruzioni sull'aggiunta di altri account di archiviazione, vedere [Creare cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> L'uso di un account di archiviazione aggiuntivo in una località diversa rispetto al cluster HDInsight non è supportato.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato descritto come usare una risorsa di archiviazione di Azure compatibile con HDFS con HDInsight. In questo modo sarà possibile creare soluzioni scalabili di acquisizione e archiviazione a lungo termine dei dati e usare HDInsight per sbloccare le informazioni all'interno dei dati strutturati e non strutturati archiviati.

Per altre informazioni, vedere:

* [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Introduzione ad Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Caricare dati in HDInsight](hdinsight-upload-data.md)
* [Usare Apache Hive con HDInsight](hadoop/hdinsight-use-hive.md)
* [Usare Pig con Hadoop in HDInsight](hadoop/hdinsight-use-pig.md)
* [Usare firme di accesso condiviso archiviazione di Azure per limitare l'accesso ai dati con HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)