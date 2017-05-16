---
title: Creare cluster Hadoop, HBase, Kafka, Storm o Spark in Azure HDInsight | Microsoft Docs
description: Informazioni su come creare cluster Hadoop, HBase, Storm o Spark in Linux per HDInsight usando un browser, l&quot;interfaccia della riga di comando di Azure, Azure PowerShell, REST o un SDK.
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 23a01938-3fe5-4e2e-8e8b-3368e1bbe2ca
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 9fc96db2b832f1e57813bebd2d46e4b78ed04677
ms.contentlocale: it-it
ms.lasthandoff: 05/03/2017


---
# <a name="create-hadoop-clusters-in-hdinsight"></a>Creare cluster Hadoop in HDInsight
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Un cluster Hadoop è costituito da alcune macchine virtuali (nodi) che vengono usate per l'elaborazione distribuita di attività nel cluster. Azure HDInsight consente di astrarre i dettagli di implementazione dell'installazione e della configurazione dei singoli nodi. È quindi necessario specificare solo le informazioni di configurazione generali. Questo articolo illustra queste impostazioni di configurazione.

## <a name="basic-configurations"></a>Configurazioni di base

Nel [portale di Azure](https://portal.azure.com) è possibile creare un cluster HDInsight con le opzioni *Creazione rapida* o *Personalizzato*. Questa sezione descrive le impostazioni di configurazione di base usate nell'opzione Creazione rapida. L'opzione Personalizzato include le configurazioni aggiuntive seguenti:

- [Applicazioni](#hdinsight-applications)
- [Dimensione del cluster](#cluster-size)
- Impostazioni avanzate

  - [Azioni script](#customize-clusters-using-script-action)
  - [Rete virtuale](#use-virtual-network)

### <a name="subscription"></a>Sottoscrizione 
Ogni cluster HDInsight è associato a una sottoscrizione di Azure.

### <a name="resource-group-name"></a>Nome del gruppo di risorse
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) consente di usare le risorse dell'applicazione come gruppo, denominato gruppo di risorse di Azure. È quindi possibile distribuire, aggiornare, monitorare o eliminare tutte le risorse per l'applicazione in un'unica operazione coordinata.

### <a name="cluster-name"></a>Nome del cluster
Il nome del cluster consente di identificare un cluster. Il nome del cluster deve essere globalmente univoco e seguire queste convenzioni di denominazione:

* Il campo deve essere una stringa contenente da 3 a 63 caratteri.
* Il campo può contenere solo lettere, numeri e trattini.

### <a name="cluster-types"></a> Tipo di cluster
In Azure HDInsight sono attualmente disponibili i tipi di cluster seguenti, ognuno con un set di componenti per offrire determinate funzionalità:

| Tipo di cluster | Funzionalità |
| --- | --- |
| [Hadoop](hdinsight-hadoop-introduction.md) |Query e analisi (processi batch) |
| [HBase](hdinsight-hbase-overview.md) |Archiviazione di dati NoSQL |
| [Storm](hdinsight-storm-overview.md) |Elaborazione di eventi in tempo reale |
| [Spark](hdinsight-apache-spark-overview.md) |Elaborazione in memoria, query interattive, elaborazione di flussi di micro batch |
| [Kafka (anteprima)](hdinsight-apache-kafka-introduction.md) | Piattaforma di streaming open source distribuita che può essere usata per compilare applicazioni e pipeline di dati in streaming in tempo reale. |
| [R Server](hdinsight-hadoop-r-server-overview.md) |Ampia gamma di statistiche di Big Data, modellazione predittiva e funzionalità di Machine Learning |
| [Interactive Hive (anteprima)](hdinsight-hadoop-use-interactive-hive.md) |Caching in memoria per query Hive interattive e più rapide |

Ogni tipo di cluster usa il proprio numero di nodi del cluster, una terminologia specifica per i nodi del cluster e dimensioni predefinite per le VM per ogni tipo di nodo. Nella tabella seguente, il numero di nodi per ogni tipo di nodo è indicato tra parentesi.

| Tipo | Nodi | Diagramma |
| --- | --- | --- |
| Hadoop |Nodo head (2), nodo dati (1+) |![Nodi del cluster HDInsight Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/HDInsight-Hadoop-roles.png) |
| HBase |Server head (2), server di area (1+), nodo master/ZooKeeper (3) |![Nodi del cluster HDInsight HBase](./media/hdinsight-hadoop-provision-linux-clusters/HDInsight-HBase-roles.png) |
| Storm |Nodo Nimbus (2), server supervisore (1+), nodo ZooKeeper (3) |![Nodi del cluster HDInsight Storm](./media/hdinsight-hadoop-provision-linux-clusters/HDInsight-Storm-roles.png) |
| Spark |Nodo head (2), nodo Worker (1+), nodo ZooKeeper (3), gratuito per le macchine virtuali ZooKeeper con dimensioni A1 |![Nodi del cluster HDInsight Spark](./media/hdinsight-hadoop-provision-linux-clusters/HDInsight-Spark-roles.png) |

La tabella seguente elenca le dimensioni di VM predefinite per HDInsight:

* Tutte le aree supportate tranne Brasile meridionale e Giappone occidentale:

  | Tipo di cluster | Hadoop | HBase | Storm | Spark | R Server |
  | --- | --- | --- | --- | --- | --- |
  | Head: dimensioni VM predefinite |D3 v2 |D3 v2 |A3 |D12 v2 |D12 v2 |
  | Head: dimensioni VM consigliate |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2 |A3, A4, A5 |D12 v2, D13 v2, D14 v2 |D12 v2, D13 v2, D14 v2 |
  | Ruolo di lavoro: dimensioni VM predefinite |D3 v2 |D3 v2 |D3 v2 |Windows: D12 v2; Linux: D4 v2 |Windows: D12 v2; Linux: D4 v2 |
  | Ruolo di lavoro: dimensioni VM consigliate |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
  | ZooKeeper: dimensioni VM predefinite | |A3 |A2 | | |
  | ZooKeeper: dimensioni VM consigliate | |A3, A4, A5 |A2, A3, A4 | | |
  | Edge: dimensioni VM predefinite | | | | |Windows: D12 v2; Linux: D4 v2 |
  | Edge: dimensioni VM consigliate | | | | |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
* Solo Brasile meridionale e Giappone occidentale (non sono disponibili dimensioni v2):

  | Tipo di cluster | Hadoop | HBase | Storm | Spark | R Server |
  | --- | --- | --- | --- | --- | --- |
  | Head: dimensioni VM predefinite |D3 |D3 |A3 |D12 |D12 |
  | Head: dimensioni VM consigliate |D3, D4, D12 |D3, D4, D12 |A3, A4, A5 |D12, D13, D14 |D12, D13, D14 |
  | Ruolo di lavoro: dimensioni VM predefinite |D3 |D3 |D3 |Windows: D12; Linux: D4 |Windows: D12; Linux: D4 |
  | Ruolo di lavoro: dimensioni VM consigliate |D3, D4, D12 |D3, D4, D12 |D3, D4, D12 |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |
  | ZooKeeper: dimensioni VM predefinite | |A2 |A2 | | |
  | ZooKeeper: dimensioni VM consigliate | |A2, A3, A4 |A2, A3, A4 | | |
  | Edge: dimensioni VM predefinite | | | | |Windows: D12; Linux: D4 |
  | Edge: dimensioni VM consigliate | | | | |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |

> [!NOTE]
> Il nodo Head è chiamato *Nimbus* per il tipo di cluster Storm. Il ruolo di lavoro è chiamato *Area* per il tipo di cluster HBase e *Supervisore* per il tipo di cluster Storm.

> [!IMPORTANT]
> Se si prevedono più di 32 nodi del ruolo di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM.
>
>

A questi tipi di base è possibile aggiungere altri componenti, ad esempio Hue, tramite [azioni di script](#customize-clusters-using-script-action).

> [!IMPORTANT]
> Sono disponibili molti tipi di cluster HDInsight, che corrispondono al carico di lavoro o alla tecnologia per cui è ottimizzato il cluster. Non è disponibile alcun metodo supportato per creare un cluster che combini più tipi, ad esempio Storm e HBase in un cluster.
>
>

Se la soluzione richiede tecnologie che vengono distribuite tra più tipi di cluster HDInsight, è necessario creare una rete virtuale di Azure, nonché i tipi di cluster necessari all'interno della rete virtuale. Questa configurazione consente ai cluster e al codice in essi distribuito di comunicare direttamente tra loro.

Per altre informazioni sull'uso di una rete virtuale di Azure con HDInsight, vedere [Estendere HDInsight con le reti virtuali di Azure](hdinsight-extend-hadoop-virtual-network.md).

Per un esempio dell'uso di due tipi di cluster in una rete virtuale di Azure, vedere [Analizzare i dati del sensore con Storm e HBase](hdinsight-storm-sensor-data-analysis.md).

### <a name="operating-system"></a>Sistema operativo
È possibile creare cluster HDInsight in Linux o Windows.  Per altre informazioni sulle versioni del sistema operativo, vedere [Versioni supportate di HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions). HDInsight 3.4 e versioni successive usano la distribuzione di Ubuntu Linux come sistema operativo sottostante per il cluster. 

### <a name="version"></a>Versione
Questa opzione consente di determinare la versione di HDInsight necessaria per questo cluster. Per altre informazioni, vedere [Versioni supportate di HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="cluster-tiers"></a>Livello cluster

Azure HDInsight presenta le offerte cloud per i Big Data in due categorie: Standard e Premium.  Per altre informazioni, vedere [HDInsight Standard e HDInsight Premium]](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium).

La schermata seguente mostra le informazioni sul portale di Azure per la scelta dei tipi di cluster.

![Configurazione di HDInsight Premium](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-type-configuration.png)


### <a name="credentials"></a>Credenziali
Con i cluster HDInsight è possibile configurare due account utente durante la creazione del cluster:

* Utente HTTP. Il nome utente predefinito è *admin* e usa la configurazione di base nel portale di Azure. In alcuni casi, viene chiamato "utente cluster".
* Utente SSH (cluster Linux). Usato per la connessione ai cluster tramite SSH. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="location"></a>Località
Non è necessario specificare in modo esplicito il percorso del cluster, perché condivide lo stesso percorso di archiviazione predefinito. Per un elenco di aree supportate, fare clic sull'elenco a discesa **Area** in [Prezzi di HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).


### <a name="storage"></a>Archiviazione

Hadoop Distributed File System (HDFS) originale usa molti dischi locali nel cluster. HDInsight usa entrambi i BLOB in [Archiviazione di Azure](hdinsight-hadoop-provision-linux-clusters.md#azure-storage) o [Azure Data Lake Store](hdinsight-hadoop-provision-linux-clusters.md#azure-data-lake-store). Per usare gli archivi Data Lake per HDInsight, è necessario rispettare alcuni requisiti specifici. Per altre informazioni, vedere la sezione introduttiva di [Creare cluster HDInsight con Data Lake Store tramite il portale di Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

![Archiviazione di HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-creation-storage.png)


#### <a name="azure-storage"></a>Archiviazione di Azure
Archiviazione di Azure è una soluzione di archiviazione affidabile, con finalità generali che si integra facilmente con HDInsight. Grazie a un'interfaccia HDFS, tutti i componenti disponibili in HDInsight possono agire direttamente sui dati strutturati o non strutturati archiviati nei BLOB. L'archiviazione dei dati in Archiviazione di Azure consente l'eliminazione sicura dei cluster HDInsight usati per i calcoli, senza perdita di dati utente. HDInsight supporta solo account di archiviazione di Azure __per utilizzo generico__. Non supporta attualmente il tipo di account di __archiviazione BLOB__.

Durante la configurazione si specifica un account di archiviazione di Azure e un contenitore BLOB in tale account. Il contenitore BLOB viene usato dal cluster come posizione di archiviazione predefinita. Facoltativamente, è possibile specificare account di Archiviazione di Azure aggiuntivi, ovvero account di archiviazione collegati, a cui il cluster può accedere. Il cluster può accedere anche a eventuali contenitori BLOB configurati con accesso in lettura pubblico completo o accesso in lettura pubblico solo per i BLOB.

Non è consigliabile usare il contenitore BLOB predefinito per l'archiviazione dei dati aziendali. È consigliabile eliminare il contenitore BLOB predefinito dopo ogni uso per ridurre i costi di archiviazione. Si noti che il contenitore predefinito include registri di sistema e applicazioni. Assicurarsi di recuperare i registri prima di eliminare il contenitore.

La condivisione di un contenitore BLOB tra più cluster non è supportata.

Per altre informazioni sull'uso di un account di archiviazione di Azure secondario, vedere [Uso di Archiviazione di Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Oltre ad Archiviazione di Azure, è possibile usare [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) come account di archiviazione predefinito per il cluster HBase in HDInsight e come archiviazione collegata per tutti e quattro i tipi di cluster HDInsight. Per altre informazioni, vedere [Creare un cluster HDInsight con Data Lake Store tramite il portale di Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

#### <a name="use-additional-storage"></a>Risorse di archiviazione aggiuntive

In alcuni casi è possibile aggiungere altre risorse al cluster, ad esempio se sono presenti più account di archiviazione di Azure per diverse aree geografiche o per diversi servizi, ma si vuole analizzare tutti gli account con HDInsight.

Quando si crea un cluster HDInsight o dopo aver creato un cluster, è possibile aggiungere account di archiviazione.  Vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md).

Per altre informazioni sull'account di Archiviazione di Azure secondario, vedere [Uso di Archiviazione di Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md). Per altre informazioni sull'uso degli archivi Data Lake secondari, vedere [Creare un cluster HDInsight con Data Lake Store tramite il portale di Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).


#### <a name="use-hiveoozie-metastore"></a>Metastore Hive/Oozie
È consigliabile usare un metastore personalizzato per conservare le tabelle Hive dopo aver eliminato il cluster HDInsight. Sarà possibile associare il metastore a un altro cluster HDInsight.

> [!IMPORTANT]
> Un metastore HDInsight creato per una versione del cluster HDInsight non può essere condiviso in versioni diverse del cluster HDInsight. Per un elenco di versioni di HDInsight, vedere [Versioni supportate di HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).
>
>

Il metastore contiene metadati Hive e Oozie, tra cui tabelle di Hive, partizioni, schemi e colonne. Consente di conservare i metadati Hive e Oozie, in modo che non sia necessario creare di nuovo tabelle Hive o processi Oozie quando si crea un nuovo cluster. Per impostazione predefinita, Hive usa un database SQL di Azure incorporato per l'archiviazione di queste informazioni. Se il cluster viene eliminato, tuttavia, i metadati non vengono mantenuti nel database incorporato. Quando si creano tabelle Hive in un cluster HDInsight con un metastore Hive configurato, le tabelle verranno conservate quando si crea nuovamente il cluster usando lo stesso metastore Hive.

La configurazione Metastore non è disponibile per i tipi di cluster HBase.

> [!IMPORTANT]
> Quando si crea un metastore personalizzato, non usare un nome di database che contiene trattini o segni meno perché in quel caso il processo di creazione del cluster non andrebbe a buon fine.
>
>

## <a name="install-hdinsight-applications"></a>Installare applicazioni HDInsight

Un'applicazione HDInsight è un'applicazione che gli utenti possono installare in un cluster HDInsight basato su Linux. Queste applicazioni possono essere sviluppate da Microsoft, da fornitori di software indipendenti (ISV) o dall'utente. Per altre informazioni, vedere [Installare applicazioni Hadoop di terze parti in Azure HDInsight](hdinsight-apps-install-applications.md).

La maggior parte delle applicazioni HDInsight viene installata in un nodo perimetrale vuoto.  Un nodo perimetrale vuoto è una macchina virtuale Linux in cui sono installati e configurati gli stessi strumenti client del nodo head. Il nodo perimetrale può essere usato per accedere al cluster e per testare e ospitare le applicazioni client. Per altre informazioni, vedere [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md)(Usare nodi perimetrali vuoti in HDInsight).

## <a name="configure-cluster-size"></a>Configurare le dimensioni del cluster

L'uso di questi nodi viene addebitato ai clienti per la durata del ciclo di vita del cluster. La fatturazione inizia con la creazione del cluster e si interrompe quando il cluster viene eliminato. I cluster non possono essere deallocati o messi in attesa.

Diversi tipi di cluster hanno diversi tipi, numeri e dimensioni di nodi. Un cluster Hadoop ha ad esempio due *nodi head* e quattro *nodi dati* per impostazione predefinita, mentre un cluster di tipo Storm ha due *nodi Nimbus*, tre *nodi ZooKeeper* e quattro *nodi supervisore* per impostazione predefinita. Il costo del cluster HDInsight è determinato dal numero di nodi e dalle dimensioni delle macchine virtuali per i nodi. Se ad esempio si prevede di eseguire operazioni che richiedono una quantità elevata di memoria, è consigliabile selezionare una risorsa di calcolo con più memoria. Ai fini dell'apprendimento è consigliabile usare un solo nodo dati. Per altre informazioni sui prezzi di HDInsight, vedere [Prezzi di HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]
> Il limite relativo alle dimensioni del cluster dipende dalla sottoscrizione di Azure. Per aumentare il limite, contattare il team del supporto fatturazione.
>
> I nodi usati dal cluster non contano come macchine virtuali perché le immagini di macchine virtuali usate per i nodi sono un dettaglio di implementazione del servizio HDInsight. I core di calcolo usati dai nodi influiscono tuttavia sul numero totale di core di calcolo disponibili per la sottoscrizione. Quando si crea un cluster HDInsight è possibile visualizzare il numero di core disponibili e i core che verranno usati dal cluster nella sezione di riepilogo del pannello **Dimensione del cluster**.
>
>

Quando si usa il portale di Azure per configurare il cluster, le dimensioni del nodo vengono esposte tramite il pannello **Piani tariffari per il nodo**. È anche possibile visualizzare il costo associato alle diverse dimensioni dei nodi. Lo screenshot seguente mostra le opzioni disponibili per un cluster Hadoop basato su Linux.

![Dimensioni dei nodi delle VM di HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-node-sizes.png)

Le tabelle seguenti illustrano le dimensioni supportate dai cluster HDInsight e le capacità offerte.

### <a name="standard-tier-a-series"></a>Livello Standard: serie A
Nel modello di distribuzione classica, alcune dimensioni delle macchine virtuali sono leggermente diverse in PowerShell e nell'interfaccia della riga di comando.

* Standard_A3 è Large
* Standard_A4 è ExtraLarge

| Dimensione | Core CPU | Memoria | NIC (Max) | Max. Dimensioni disco | Max. (1023 GB ciascuno) | Dischi di dati max. IOPS (500 per disco) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A3\Large |4 |7 GB |2 |Temporaneo = 285 GB |8 |8x500 |
| Standard_A4\ExtraLarge |8 |14 GB |4 |Temporaneo = 605 GB |16 |16x500 |
| Standard_A6 |4 |28 GB |2 |Temporaneo = 285 GB |8 |8x500 |
| Standard_A7 |8 |56 GB |4 |Temporaneo = 605 GB |16 |16x500 |

### <a name="standard-tier-d-series"></a>Livello Standard: serie D
| Dimensione | Core CPU | Memoria | NIC (Max) | Max. Dimensioni disco | Max. (1023 GB ciascuno) | Dischi di dati max. IOPS (500 per disco) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D3 |4 |14 GB |4 |Temporaneo (SSD) = 200 GB |8 |8x500 |
| Standard_D4 |8 |28 GB |8 |Temporaneo (SSD) = 400 GB |16 |16x500 |
| Standard_D12 |4 |28 GB |4 |Temporaneo (SSD) = 200 GB |8 |8x500 |
| Standard_D13 |8 |56 GB |8 |Temporaneo (SSD) = 400 GB |16 |16x500 |
| Standard_D14 |16 |112 GB |8 |Temporaneo (SSD) = 800 GB |32 |32x500 |

### <a name="standard-tier-dv2-series"></a>Livello standard: serie Dv2
| Dimensione | Core CPU | Memoria | NIC (Max) | Max. Dimensioni disco | Max. (1023 GB ciascuno) | Dischi di dati max. IOPS (500 per disco) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D3_v2 |4 |14 GB |4 |Temporaneo (SSD) = 200 GB |8 |8x500 |
| Standard_D4_v2 |8 |28 GB |8 |Temporaneo (SSD) = 400 GB |16 |16x500 |
| Standard_D12_v2 |4 |28 GB |4 |Temporaneo (SSD) = 200 GB |8 |8x500 |
| Standard_D13_v2 |8 |56 GB |8 |Temporaneo (SSD) = 400 GB |16 |16x500 |
| Standard_D14_v2 |16 |112 GB |8 |Temporaneo (SSD) = 800 GB |32 |32x500 |

Per considerazioni sulla distribuzione da tenere presenti quando si prevede di usare queste risorse, vedere [Dimensioni delle macchine virtuali in Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Per informazioni sui prezzi delle varie dimensioni, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).   

> [!IMPORTANT]
> Se si prevedono più di 32 nodi del ruolo di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM.
>
>

La fatturazione inizia con la creazione del cluster e si interrompe quando il cluster viene eliminato. Per altre informazioni sui prezzi, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


> [!WARNING]
> L'uso di un account di archiviazione aggiuntivo in una località diversa rispetto al cluster HDInsight non è supportato.



## <a name="use-virtual-network"></a>Usare la rete virtuale
La [rete virtuale di Azure](https://azure.microsoft.com/documentation/services/virtual-network/) consente di creare una rete sicura e persistente che contiene le risorse necessarie per la propria soluzione. Per altre informazioni sull'uso di HDInsight con una rete virtuale, inclusi i requisiti di configurazione specifici per la rete virtuale, vedere [Estendere le funzionalità di HDInsight usando Rete virtuale di Azure](hdinsight-extend-hadoop-virtual-network.md).


## <a name="customize-clusters-using-script-action"></a>Personalizzare i cluster con l'azione script

L'uso di script durante la creazione consente di installare componenti aggiuntivi o personalizzare la configurazione di un cluster. Gli script vengono chiamati tramite un' **azione script**, ovvero un'opzione di configurazione che può essere usata da portale di Azure, dai cmdlet di Windows PowerShell per HDInsight o da .NET SDK per HDInsight. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster-linux.md).

Nel cluster è possibile eseguire alcuni componenti Java nativi, come Mahout e Cascading, sotto forma di file JAR (Java Archive). Questi file JAR possono essere distribuiti in Archiviazione di Azure e inviati ai cluster HDInsight usando i meccanismi di invio dei processi Hadoop. Per altre informazioni, vedere [Inviare processi Hadoop a livello di codice](hdinsight-submit-hadoop-jobs-programmatically.md).

> [!NOTE]
> In caso di problemi durante la distribuzione di file JAR in cluster HDInsight o nella chiamata di file JAR in cluster HDInsight, contattare il [Supporto Microsoft](https://azure.microsoft.com/support/options/).
>
> Cascading non è supportato da HDInsight, quindi in caso di problemi non è possibile rivolgersi al Supporto Microsoft. Per gli elenchi dei componenti supportati, vedere [Novità delle versioni cluster incluse con HDInsight](hdinsight-component-versioning.md).
>
>

In alcuni casi è opportuno configurare i file di configurazione seguenti durante il processo di creazione:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Per altre informazioni, vedere [Personalizzare cluster HDInsight tramite Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="cluster-creation-methods"></a>Metodi di creazione di cluster
In questo articolo sono state fornite informazioni di base sulla creazione di un cluster HDInsight basato su Linux. Usare la tabella seguente per trovare informazioni specifiche su come creare un cluster usando un metodo ottimale per le proprie esigenze.

| Cluster creati con | Web browser | Riga di comando | API REST | SDK | Linux, Mac OS X o Unix | Windows |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| [Il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |✔ |✔ |
| [Data factory di Azure](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |✔ |✔ |
| [Interfaccia della riga di comando di Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |✔ |✔ |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |✔ |✔ |
| [Modelli di Gestione risorse di Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |

## <a name="troubleshoot"></a>Risoluzione dei problemi

Se si verificano problemi di creazione dei cluster HDInsight, vedere i [requisiti dei controlli di accesso](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione all'ecosistema Hadoop in Azure HDInsight](hdinsight-hadoop-introduction.md).
- [Esercitazione su Hadoop: introduzione all'uso di Hadoop in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
