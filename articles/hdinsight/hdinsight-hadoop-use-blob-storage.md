---
title: Eseguire query sui dati da una risorsa di archiviazione di Azure compatibile con HDFS - Azure HDInsight
description: Informazioni su come eseguire query sui dati da Archiviazione di Azure e Azure Data Lake Storage per archiviare i risultati dell'analisi.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/01/2019
ms.openlocfilehash: d934568f09e62ad8c1b472583cbfee79d2c837f6
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936865"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Usare una risorsa di archiviazione di Azure con cluster Azure HDInsight

Per analizzare i dati nel cluster HDInsight, è possibile archiviare i dati in [archiviazione di Azure](../storage/common/storage-introduction.md), [Azure Data Lake storage generazione 1](../data-lake-store/data-lake-store-overview.md)/[Azure Data Lake storage generazione 2](../storage/blobs/data-lake-storage-introduction.md)o una combinazione. Queste opzioni di archiviazione consentono di eliminare in modo sicuro i cluster HDInsight usati per il calcolo senza perdere i dati utente.

Apache Hadoop supporta una nozione del file system predefinito. Il file system predefinito implica uno schema e un'autorità predefiniti e può essere usato anche per risolvere percorsi relativi. Durante il processo di creazione del cluster HDInsight è possibile specificare un contenitore BLOB in Archiviazione di Azure come file system predefinito. In alternativa, con HDInsight 3.6 è possibile selezionare Archiviazione di Azure o Azure Data Lake Storage Gen 1/ Azure Data Lake Storage Gen 2 come file system predefinito, con alcune eccezioni. Per il supporto dell'uso di Data Lake Storage Gen 1 come risorsa di archiviazione sia predefinita che collegata, vedere [Disponibilità per il cluster HDInsight](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

Questo articolo illustra come usare Archiviazione di Azure con i cluster HDInsight. Per informazioni sull'uso di Data Lake Storage Gen 1 con i cluster HDInsight, vedere [Usare Azure Data Lake Storage con cluster Azure HDInsight](hdinsight-hadoop-use-data-lake-store.md). Per altre informazioni sulla creazione di un cluster HDInsight, vedere [Configurare i cluster di HDInsight con Hadoop, Spark, Kafka e altro ancora](hdinsight-hadoop-provision-linux-clusters.md).

Archiviazione di Azure è una soluzione di archiviazione affidabile, con finalità generali che si integra facilmente con HDInsight. HDInsight può usare un contenitore BLOB in Archiviazione di Azure come file system predefinito per il cluster. Grazie a un'interfaccia HDFS (Hadoop Distributed File System), tutti i componenti disponibili in HDInsight possono agire direttamente sui dati strutturati o non strutturati archiviati come BLOB.

> [!IMPORTANT]  
> Il tipo di account di archiviazione **BlobStorage** può essere usato solo come risorsa di archiviazione secondaria per i cluster HDInsight.

| Tipologia account di archiviazione | Servizi supportati | Livelli di prestazioni supportati | Livelli di accesso supportati |
|----------------------|--------------------|-----------------------------|------------------------|
| Archiviazione v2 (utilizzo generico V2)  | Blob     | Standard                    | Frequente, ad accesso sporadico, archivio\*   |
| Archiviazione (utilizzo generico V1)   | Blob     | Standard                    | N/D                    |
| BlobStorage                    | Blob     | Standard                    | Frequente, ad accesso sporadico, archivio\*   |

Non è consigliabile usare il contenitore BLOB predefinito per l'archiviazione dei dati aziendali. È consigliabile eliminare il contenitore BLOB predefinito dopo ogni uso per ridurre i costi di archiviazione. Il contenitore predefinito include log di sistema e applicazioni. Assicurarsi di recuperare i log prima di eliminare il contenitore.

La condivisione di un contenitore BLOB come file system predefinito per più cluster non è supportata.

> [!NOTE]  
> Il livello di accesso dell'archivio è un livello offline con una latenza di recupero di diverse ore e non è consigliato per l'uso con HDInsight. Per altre informazioni, vedere [Livello di accesso archivio](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

Se si sceglie di proteggere l'account di archiviazione con le restrizioni relative a **firewall e reti virtuali** nelle **reti selezionate**, assicurarsi di abilitare l'eccezione **Consenti servizi Microsoft attendibili...** in modo che HDInsight possa accedere alla risorsa di archiviazione account.

## <a name="hdinsight-storage-architecture"></a>Architettura di archiviazione di HDInsight

Nel diagramma seguente viene mostrata una visualizzazione astratta dell'architettura di archiviazione HDInsight dell'uso di Archiviazione di Azure:

![I cluster Hadoop usano l'API HDFS per accedere e archiviare i dati nell'archivio BLOB](./media/hdinsight-hadoop-use-blob-storage/storage-architecture.png "Architettura archiviazione HDInsight")

HDInsight offre accesso al file system distribuito collegato localmente ai nodi di calcolo. Il file system è accessibile tramite l'URI completo, ad esempio:

    hdfs://<namenodehost>/<path>

HDInsight consente anche di accedere ai dati archiviati in Archiviazione di Azure. La sintassi è:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

Di seguito sono riportate alcune considerazioni sull'uso di un account di Archiviazione di Azure con i cluster HDInsight.

* **Contenitori negli account di archiviazione connessi a un cluster:** poiché il nome e la chiave dell'account sono associati al cluster durante il processo di creazione, si disporrà di un accesso completo ai BLOB presenti in tali contenitori.

* **Contenitori pubblici o BLOB pubblici negli account di archiviazione che non sono connessi a un cluster:** si dispone dell'autorizzazione di sola lettura ai BLOB nei contenitori.
  
> [!NOTE]  
> Un contenitore pubblico consente di ottenere un elenco di tutti i BLOB disponibili al suo interno, nonché i metadati del contenitore stesso. È possibile accedere a un BLOB pubblico solo se ne conosce l'URL esatto. Per altre informazioni, vedere [Gestire l'accesso a contenitori e Blob](../storage/blobs/storage-manage-access-to-resources.md).

* **Contenitori privati negli account di archiviazione NON connessi a un cluster:** non è possibile accedere ai BLOB nei contenitori a meno che non sia stato definito l'account di archiviazione quando sono stati inviati i processi WebHCat. Questo concetto verrà spiegato più avanti nell'articolo.

Gli account di archiviazione definiti nel processo di creazione e le relative chiavi vengono archiviati in `%HADOOP_HOME%/conf/core-site.xml` nei nodi del cluster. Il comportamento predefinito di HDInsight consiste nell'usare gli account di archiviazione definiti nel file core-site.xml. È possibile modificare questa impostazione usando [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Più processi WebHCat, inclusi Apache Hive, MapReduce, lo streaming Apache Hadoop e Apache Pig, possono includere una descrizione degli account di archiviazione e dei metadati. (questo è valido solo per Pig con account di archiviazione, non per i metadati). Per altre informazioni, vedere [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx) (Uso di un cluster HDInsight con account di archiviazione e metastore alternativi).

I BLOB possono essere usati per i dati strutturati e non strutturati. I contenitori BLOB archiviano i dati come coppie chiave/valore e non esiste alcuna gerarchia di directory. È tuttavia possibile usare il carattere barra ( / ) all'interno del nome della chiave per far sembrare che un file sia archiviato in una struttura di directory. Ad esempio, la chiave di un BLOB potrebbe essere *input/log1.txt*. Non esiste realmente una directory *input* ma, grazie alla presenza del carattere barra, il nome della chiave ha l'aspetto di un percorso di file.

## <a id="benefits"></a>Vantaggi di Archiviazione di Azure

Il costo implicito in termini di prestazioni di non condivisione dei cluster di calcolo e delle risorse di archiviazione è mitigato dal modo in cui i cluster di calcolo vengono creati vicino alle risorse dell'account di archiviazione all'interno dell'area di Azure, in cui la rete ad alta velocità lo rende efficace per il nodi di calcolo per accedere ai dati all'interno di archiviazione di Azure.

L'archiviazione dei dati in Archiviazione di Azure anziché in HDFS offre numerosi vantaggi:

* **Condivisione e riutilizzo dei dati:** i dati in HDFS sono situati all'interno del cluster di elaborazione. Solo le applicazioni che hanno accesso al cluster di calcolo, quindi, possono usare i dati tramite le API HDFS. È possibile accedere ai dati in archiviazione di Azure tramite le API HDFS o tramite le [API REST di archiviazione BLOB](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API). Di conseguenza, è possibile produrre e usare i dati mediante un set più ampio di strumenti e applicazioni, compresi altri cluster HDInsight.

* **Archiviazione dati:** l'archiviazione dei dati in Archiviazione di Azure consente l'eliminazione sicura dei cluster HDInsight usati per i calcoli, senza perdita di dati utente.

* **Costo di archiviazione dei dati:** l'archiviazione a lungo termine dei dati in DFS è più costosa rispetto ad Archiviazione di Azure, dato che il costo di un cluster di elaborazione è più alto di quello di Archiviazione di Azure. Inoltre, poiché i dati non devono essere ricaricati per ogni generazione del cluster di calcolo, vengono salvati anche i costi di caricamento dei dati.

* **Scalabilità orizzontale elastica:** anche se Hadoop Distributed File System offre scalabilità orizzontale del file system, la scalabilità è determinata dal numero di nodi creati per il cluster. Modificare la scalabilità può diventare quindi un processo più complicato rispetto al semplice uso delle funzionalità di scalabilità elastica automaticamente disponibili in Archiviazione di Azure.

* **Replica geografica:** Archiviazione di Azure può eseguire la replica geografica. Sebbene questo offra ripristino geografico e ridondanza dei dati, un failover nella posizione sottoposta a replica geografica incide molto negativamente sulle prestazioni e può comportare costi aggiuntivi. È pertanto consigliabile scegliere la scelta della replica geografica con oculatezza e solo se il valore dei dati giustifica i costi aggiuntivi.

Alcuni pacchetti e processi MapReduce possono creare risultati intermedi che non vale la pena di archiviare in Archiviazione di Azure. In questo caso, è possibile scegliere di archiviare i dati nel file system HDFS locale. In effetti, HDInsight utilizza DFS per molti di questi risultati intermedi nei processi Hive e in altri processi.

> [!NOTE]  
> La maggior parte dei comandi Hadoop Distributed File System, ad esempio `ls`, `copyFromLocal` e `mkdir`, funziona come previsto. Solo i comandi specifici dell'implementazione Hadoop Distributed File System nativa (denominata DFS), ad esempio `fschk` e `dfsadmin`, hanno un comportamento diverso in Archiviazione di Azure.

## <a name="address-files-in-azure-storage"></a>Accedere ai file in Archiviazione di Azure

Lo schema URI per l'accesso ai file in Archiviazione di Azure da HDInsight è il seguente:

```config
wasbs://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

Lo schema URI offre l'accesso non crittografato (con il prefisso *wasb:* ) e l'accesso con crittografia SSL (con il prefisso *wasbs*). Se possibile, è consigliabile usare *wasbs* anche per accedere ai dati presenti nella stessa area di Azure.

`<BlobStorageContainerName>` Identifica il nome del contenitore BLOB in archiviazione di Azure.
`<StorageAccountName>` Identifica il nome dell'account di archiviazione di Azure. È necessario specificare un nome di dominio completo (FQDN).

Se né `<StorageAccountName>` né è stato specificato, viene utilizzato il file System predefinito. `<BlobStorageContainerName>` Per i file presenti nel file system predefinito è possibile usare un percorso relativo o un percorso assoluto. Ad esempio, è possibile fare riferimento al file *hadoop-mapreduce-examples.jar* nei cluster HDInsight nei due modi seguenti:

```config
wasbs://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasbs:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Nei cluster HDInsight versione 2.1 e 1.6 il nome del file è `hadoop-examples.jar`.

Il percorso è il nome del percorso HDFS del file o della directory. Poiché i contenitori in Archiviazione di Azure sono archivi chiave-valore, non esiste un vero file system gerarchico. Un carattere barra ( / ) all'interno di una chiave BLOB viene interpretato come un separatore di directory. Ad esempio, il nome BLOB di *hadoop-mapreduce-examples.jar* è:

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Quando si usano i BLOB al di fuori di HDInsight, la maggior parte delle utilità non riconosce il formato WASB e richiede invece un formato del percorso di base, ad esempio `example/jars/hadoop-mapreduce-examples.jar`.

## <a name="blob-containers"></a>Contenitori BLOB

Per usare i BLOB, è necessario creare prima un [account di archiviazione di Azure](../storage/common/storage-create-storage-account.md). Come parte della procedura, è necessario specificare l'area di Azure in cui viene creato l'account di archiviazione. L'account di archiviazione deve trovarsi nella stessa area del cluster. Il database di SQL Server del metastore Hive, inoltre, deve trovarsi nella stessa area del database di SQL Server del metastore Apache Oozie.

Ovunque si trovi, ogni oggetto BLOB creato appartiene a un contenitore presente nell'account di archiviazione di Azure. Può trattarsi di un contenitore BLOB esistente, creato all'esterno di HDInsight, oppure di un contenitore creato per un cluster HDInsight.

Il contenitore BLOB predefinito archivia informazioni specifiche del cluster come i log e la cronologia processo. Non condividere un contenitore BLOB predefinito con più cluster HDInsight. Questa operazione potrebbe danneggiare la cronologia processo. È consigliabile usare un contenitore diverso per ogni cluster e inserire i dati condivisi in un account di archiviazione collegato specificato nella distribuzione di tutti i cluster pertinenti anziché nell'account di archiviazione predefinito. Per altre informazioni sulla configurazione degli account di archiviazione collegati, vedere [creare cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md). È comunque possibile riusare un contenitore di archiviazione predefinito dopo l'eliminazione del cluster HDInsight originale. Per i cluster HBase, è possibile salvare lo schema e i dati della tabella HBase creando un nuovo cluster HBase usando il contenitore BLOB predefinito usato da un cluster HBase che è stato eliminato.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>Interazione con archiviazione di Azure

Microsoft fornisce gli strumenti seguenti per lavorare con archiviazione di Azure:

| Tool | Linux | OS X | Windows |
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
* [Usare le firme di accesso condiviso di archiviazione di Azure per limitare l'accesso ai dati con HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
