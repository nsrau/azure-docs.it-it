---
title: Creare cluster Hadoop, HBase, Storm o Spark in Linux in HDInsight | Microsoft Docs
description: Informazioni su come creare cluster Hadoop, HBase, Storm o Spark in Linux per HDInsight mediante un browser, la CLI di Azure, Azure PowerShell, REST o un SDK.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2016
ms.author: jgao

---
# Creare cluster Hadoop basati su Linux in HDInsight
[!INCLUDE [selettore](../../includes/hdinsight-selector-create-clusters.md)]

Un cluster Hadoop è costituito da alcune macchine virtuali (nodi) che vengono usate per l'elaborazione distribuita di attività nel cluster. Azure consente di astrarre i dettagli di implementazione dell'installazione e della configurazione dei singoli nodi. È quindi necessario specificare solo le informazioni di configurazione generali. Questo articolo illustra queste impostazioni di configurazione.

## Tipi di cluster
Azure HDInsight offre attualmente cinque tipi diversi di cluster, ognuno con un set di componenti per fornire funzionalità specifiche.

| Tipo di cluster | Funzionalità |
| --- | --- |
| Hadoop |Query e analisi (processi batch) |
| HBase |Archiviazione di dati NoSQL |
| Storm |Elaborazione di eventi in tempo reale |
| Spark (anteprima) |Elaborazione in memoria, query interattive, elaborazione di flussi di micro batch |
| R Server in Spark |Ampia gamma di statistiche di Big Data, modellazione predittiva e funzionalità di Machine Learning |

Ogni tipo di cluster usa il proprio numero di nodi del cluster, una terminologia specifica per i nodi del cluster e dimensioni predefinite per le VM per ogni tipo di nodo. Nella tabella seguente, il numero di nodi per ogni tipo di nodo è indicato tra parentesi.

| Tipo | Nodi | Diagramma |
| --- | --- | --- |
| Hadoop |Nodo head (2), nodo dati (1+) |![Nodi del cluster HDInsight Hadoop](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png) |
| HBase |Server head (2), server di area (1+), nodo Master/Zookeeper (3) |![Nodi del cluster HDInsight HBase](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png) |
| Storm |Nodo Nimbus (2), server Supervisor (1+), nodo Zookeeper (3) |![Nodi del cluster HDInsight Storm](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png) |
| Spark |Nodo head (2), nodo Worker (1+), nodo Zookeeper (3) (gratuito per le VM Zookeeper con dimensioni A1) |![Nodi del cluster HDInsight Spark](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png) |

La tabella seguente elenca le dimensioni di VM predefinite per HDInsight.

| Tipo di cluster | Hadoop | HBase | Storm | Spark |
| --- | --- | --- | --- | --- |
| Head: dimensione VM predefinita |D3 |A3 |A3 |D12 |
| Head: dimensioni VM consigliate |D3, D4, D12 |A3, A4, A5 |A3, A4, A5 |D12, D13, D14 |
| Ruolo di lavoro: dimensione VM predefinita |D3 |D3 |D3 |D12 |
| Ruolo di lavoro: dimensioni VM consigliate |D3, D4, D12 |D3, D4, D12 |D3, D4, D12 |D12, D13, D14 |
| Zookeeper: dimensione VM predefinita | |A2 |A2 | |
| Zookeeper: dimensioni VM consigliate | |A2, A3, A4 |A2, A3, A4 | |

Si noti che il nodo Head è chiamato *Nimbus* per il tipo di cluster Storm. Il ruolo di lavoro è chiamato *Area* per il tipo di cluster HBase e *Supervisore* per il tipo di cluster Storm.

> [!IMPORTANT]
> Se si prevedono più di 32 nodi del ruolo di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM.
> 
> 

È possibile aggiungere altri componenti, ad esempio Hue o R, a questi tipi di base mediante [Azioni script](#customize-clusters-using-script-action).

## Livelli di cluster
Azure HDInsight presenta le offerte cloud per i Big Data in due categorie: Standard e [Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium). HDInsight Premium include R e altri componenti aggiuntivi. HDInsight Premium è supportato solo in HDInsight versione 3.4.

Nella tabella seguente sono elencati il tipo di cluster HDInsight e la matrice di supporto HDInsight Premium.

| Tipo di cluster | Standard | Premium |
| --- | --- | --- |
| Hadoop |Sì |Sì |
| Spark |Sì |Sì |
| HBase |Sì |No |
| Storm |Sì |No |
| R Server in Spark |No |Sì |

Questa tabella verrà aggiornata man mano che vengono inclusi altri cluster in HDInsight Premium. La schermata seguente mostra le informazioni sul portale di Azure per la scelta dei tipi di cluster.

![Configurazione di HDInsight Premium](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-type-configuration.png)

## Opzioni di configurazione di base
Di seguito sono riportate le opzioni di configurazione di base per la creazione di un cluster HDInsight.

### Nome del cluster
Il campo Nome cluster consente di identificare un cluster. Il nome del cluster deve essere globalmente univoco e seguire le convenzioni di denominazione seguenti:

* Il campo deve essere una stringa contenente da 3 a 63 caratteri.
* Il campo può contenere solo lettere, numeri e trattini.

### Tipo di cluster
Vedere [Tipi di cluster](#cluster-types) e [Livelli di cluster](#cluster-tiers).

### Sistema operativo
È possibile creare cluster HDInsight in uno dei due sistemi operativi seguenti:

* HDInsight in Linux. HDInsight consente di configurare cluster Linux in Azure. Se si ha familiarità con Linux o Unix, è infatti possibile configurare un cluster Linux eseguendo la migrazione da una soluzione Hadoop basata su Linux esistente oppure è possibile scegliere di integrarsi facilmente con i componenti dell'ecosistema Hadoop sviluppati per Linux. Per altre informazioni, vedere l'articolo di [introduzione all'uso di Hadoop su Linux in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
* HDInsight in Windows (Windows Server 2012 R2 Datacenter).

### Versione HDInsight
Consente di determinare la versione di HDInsight necessaria per questo cluster. Per altre informazioni, vedere [Quali sono i diversi componenti di Hadoop disponibili in HDInsight?](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)

### Nome della sottoscrizione
Ogni cluster HDInsight è associato a una sottoscrizione di Azure.

### Nome del gruppo di risorse
[Azure Resource Manager](../resource-group-overview.md) consente di usare le risorse dell'applicazione come gruppo, denominato Gruppo di risorse di Azure. È possibile distribuire, aggiornare, monitorare o eliminare tutte le risorse per l'applicazione con una singola operazione coordinata.

### Credenziali
Con i cluster HDInsight è possibile configurare due account utente durante la creazione del cluster:

* Utente HTTP. Il nome utente predefinito è *admin* con la configurazione di base nel portale di Azure. In alcuni casi, viene chiamato "utente cluster".
* Utente SSH (cluster Linux). Usato per la connessione ai cluster tramite SSH. È possibile creare altri account utente SSH dopo la creazione del cluster seguendo la procedura descritta in [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md) o [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-unix.md).
  
  > [!NOTE]
  > Per i cluster basati su Windows, è possibile creare un utente RDP e usarlo per connettersi al cluster tramite RDP.
  > 
  > 

### Origine dati
Hadoop Distributed File System (HDFS) originale usa molti dischi locali nel cluster. HDInsight usa l'archivio BLOB di Azure per l'archiviazione dei dati. L'archiviazione BLOB di Azure è una soluzione di archiviazione affidabile, con finalità generali che si integra facilmente con HDInsight. Grazie a un'interfaccia HDFS, tutti i componenti disponibili in HDInsight possono agire direttamente sui dati strutturati o non strutturati presenti nell'archivio BLOB. L'archiviazione dei dati nell'archivio BLOB consente l'eliminazione sicura dei cluster HDInsight usati per i calcoli, senza perdita di dati utente.

Durante la configurazione è necessario specificare un account di archiviazione di Azure e un contenitore di archiviazione BLOB di Azure nell'account di archiviazione di Azure. Alcuni processi di creazione richiedono prima di tutto la creazione dell'account di archiviazione di Azure e del contenitore di archiviazione BLOB. Il contenitore di archiviazione BLOB viene usato dal cluster come posizione di archiviazione predefinita. Facoltativamente, è possibile specificare account di archiviazione di Azure aggiuntivi (account di archiviazione collegati) a cui il cluster potrà accedere. Il cluster può accedere anche a eventuali contenitori di archiviazione BLOB configurati con accesso in lettura pubblico completo o accesso in lettura pubblico solo per i BLOB. Per ulteriori informazioni, vedere [gestione dell'accesso alle risorse di archiviazione Azure](../storage/storage-manage-access-to-resources.md).

![Archiviazione di HDInsight](./media/hdinsight-provision-clusters/HDInsight.storage.png)

> [!NOTE]
> Un contenitore di archiviazione BLOB offre un raggruppamento di un set di BLOB, come illustrato nell'immagine seguente.
> 
> 

![Archivio BLOB di Azure](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)

Non è consigliabile usare il contenitore di archiviazione BLOB predefinito per l'archiviazione dei dati aziendali. È consigliabile eliminare il contenitore di archiviazione BLOB predefinito dopo ogni uso per ridurre i costi di archiviazione. Si noti che il contenitore predefinito include registri di sistema e applicazioni. Assicurarsi di recuperare i registri prima di eliminare il contenitore.

> [!WARNING]
> La condivisione di un contenitore di archiviazione BLOB tra più cluster non è supportata.
> 
> 

Per altre informazioni sull'uso degli archivi BLOB secondari, vedere [Uso dell'archivio BLOB di Azure compatibile con HDFS con Hadoop in HDInsight](hdinsight-hadoop-use-blob-storage.md).

Oltre all'archivio BLOB di Azure, è possibile usare anche [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) come account di archiviazione predefinito per il cluster HBase in HDInsight e come spazio di archiviazione collegato per tutti e quattro i tipi di cluster HDInsight. Per altre informazioni, vedere [Creare un cluster HDInsight con Data Lake Store tramite il portale di Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

### Località (Area)
Il cluster HDInsight e l'account di archiviazione predefinito devono trovarsi nella stessa località di Azure.

![Aree di Azure](./media/hdinsight-provision-clusters/Azure.regions.png)

Per un elenco di aree supportate, fare clic sull'elenco a discesa **Area** in [Prezzi di HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

### Piani tariffari per il nodo
L'uso di questi nodi viene addebitato ai clienti per la durata del ciclo di vita del cluster. La fatturazione inizia con la creazione del cluster e si interrompe quando il cluster viene eliminato. I cluster non possono essere deallocati o messi in attesa.

Diversi tipi di cluster hanno diversi tipi, numeri e dimensioni di nodi. Ad esempio, un cluster Hadoop ha due *nodi head* e, per impostazione predefinita, quattro *nodi dati*, mentre un cluster di tipo Storm ha due *nodi Nimbus*, tre *nodi Zookeeper* e, per impostazione predefinita, quattro *nodi supervisore*. Il costo del cluster HDInsight è determinato dal numero di nodi e dalle dimensioni delle macchine virtuali per i nodi. Ad esempio, se si prevede di eseguire operazioni che richiedono una quantità elevata di memoria, è consigliabile selezionare una risorsa di calcolo con più memoria. Ai fini dell'apprendimento, è consigliabile usare un solo nodo di dati. Per altre informazioni sui prezzi di HDInsight, vedere [Prezzi di HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]
> Il limite relativo alle dimensioni del cluster dipende dalla sottoscrizione di Azure. Per aumentare il limite, contattare il team del supporto fatturazione.
> 
> I nodi usati dal cluster non contano come macchine virtuali perché le immagini di macchine virtuali usate per i nodi sono un dettaglio di implementazione del servizio HDInsight. I core di calcolo usati dai nodi influiscono tuttavia sul numero totale di core di calcolo disponibili per la sottoscrizione. Durante la creazione di un cluster HDInsight è possibile visualizzare il numero di core disponibili e i core che verranno usati dal cluster nella sezione di riepilogo del pannello Piani tariffari per il nodo.
> 
> 

Quando si usa il portale di Azure per configurare il cluster, le dimensioni del nodo vengono esposte tramite il pannello **Piano tariffario per il nodo**. È anche possibile visualizzare il costo associato alle diverse dimensioni dei nodi. Lo screenshot seguente mostra le opzioni disponibili per un cluster Hadoop basato su Linux.

![Dimensioni dei nodi delle VM di HDInsight](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

Le tabelle seguenti illustrano le dimensioni supportate dai cluster HDInsight e le capacità offerte.

#### Livello Standard: serie A
Nel modello di distribuzione classico alcune dimensioni delle macchine virtuali sono leggermente diverse in PowerShell e nell'interfaccia della riga di comando.

* Standard\_A3 è Large
* Standard\_A4 è ExtraLarge

| Dimensione | Core CPU | Memoria | NIC (Max) | Dimensione disco max. | Dischi di dati max. (1023 GB ciascuno) | Max. IOPS (500 per disco) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard\_A3\\Large |4 |7 GB |2 |Temporaneo = 285 GB |8 |8x500 |
| Standard\_A4\\ExtraLarge |8 |14 GB |4 |Temporaneo = 605 GB |16 |16x500 |
| Standard\_A6 |4 |28 GB |2 |Temporaneo = 285 GB |8 |8x500 |
| Standard\_A7 |8 |56 GB |4 |Temporaneo = 605 GB |16 |16x500 |

#### Livello Standard: serie D
| Dimensione | Core CPU | Memoria | NIC (Max) | Dimensione disco max. | Dischi di dati max. (1023 GB ciascuno) | Max. IOPS (500 per disco) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard\_D3 |4 |14 GB |4 |Temporaneo (SSD) = 200 GB |8 |8x500 |
| Standard\_D4 |8 |28 GB |8 |Temporaneo (SSD) = 400 GB |16 |16x500 |
| Standard\_D12 |4 |28 GB |4 |Temporaneo (SSD) = 200 GB |8 |8x500 |
| Standard\_D13 |8 |56 GB |8 |Temporaneo (SSD) = 400 GB |16 |16x500 |
| Standard\_D14 |16 |112 GB |8 |Temporaneo (SSD) = 800 GB |32 |32x500 |

#### Livello standard: serie Dv2
| Dimensione | Core CPU | Memoria | NIC (Max) | Dimensione disco max. | Dischi di dati max. (1023 GB ciascuno) | Max. IOPS (500 per disco) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard\_D3\_v2 |4 |14 GB |4 |Temporaneo (SSD) = 200 GB |8 |8x500 |
| Standard\_D4\_v2 |8 |28 GB |8 |Temporaneo (SSD) = 400 GB |16 |16x500 |
| Standard\_D12\_v2 |4 |28 GB |4 |Temporaneo (SSD) = 200 GB |8 |8x500 |
| Standard\_D13\_v2 |8 |56 GB |8 |Temporaneo (SSD) = 400 GB |16 |16x500 |
| Standard\_D14\_v2 |16 |112 GB |8 |Temporaneo (SSD) = 800 GB |32 |32x500 |

Per considerazioni sulla distribuzione da tenere presenti quando si prevede di usare queste risorse, vedere [Dimensioni delle macchine virtuali](../virtual-machines/virtual-machines-windows-sizes.md). Per informazioni sui prezzi delle varie dimensioni, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).

> [!IMPORTANT]
> Se si prevedono più di 32 nodi del ruolo di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM.
> 
> 

La fatturazione inizia con la creazione del cluster e si interrompe quando il cluster viene eliminato. Per altre informazioni sui prezzi, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## Usare risorse di archiviazione aggiuntive
In alcuni casi potrebbe essere necessario aggiungere altre risorse al cluster, ad esempio se sono presenti più account di archiviazione di Azure per diverse aree geografiche o per diversi servizi, ma si vuole analizzare tutti gli account con HDInsight.

Quando si crea un cluster HDInsight o dopo aver creato un cluster, è possibile aggiungere account di archiviazione. Vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md).

Per altre informazioni sugli archivi BLOB secondari, vedere [Uso dell'archivio BLOB di Azure compatibile con HDFS con Hadoop in HDInsight](hdinsight-hadoop-use-blob-storage.md). Per altre informazioni sull'uso degli archivi Data Lake secondari, vedere [Creare un cluster HDInsight con Data Lake Store tramite il portale di Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

## Usare metastore Hive/Oozie
È consigliabile usare un metastore personalizzato per conservare le tabelle Hive dopo aver eliminato il cluster HDInsight. Sarà possibile associare il metastore a un altro cluster HDInsight.

> [!IMPORTANT]
> Il metastore HDInsight non è compatibile con le versioni precedenti. Ad esempio, non è possibile utilizzare un metastore di un cluster HDInsight 3.4 per creare un cluster HDInsight 3.3.
> 
> 

Il metastore contiene metadati Hive e Oozie, tra cui tabelle di Hive, partizioni, schemi e colonne. Consente di conservare i metadati Hive e Oozie, in modo che non sia necessario creare di nuovo tabelle Hive o processi Oozie quando si crea un nuovo cluster. Per impostazione predefinita, Hive usa un database SQL di Azure incorporato per l'archiviazione di queste informazioni. Se il cluster viene eliminato, tuttavia, i metadati non vengono mantenuti nel database incorporato. Quando si creano tabelle Hive in un cluster HDInsight con un metastore Hive configurato, le tabelle verranno conservate quando si crea nuovamente il cluster usando lo stesso metastore Hive.

La configurazione Metastore non è disponibile per i tipi di cluster HBase.

> [!IMPORTANT]
> Quando si crea un metastore personalizzato, non usare un nome di database che contiene trattini o segni meno perché in quel caso il processo di creazione del cluster non andrebbe a buon fine.
> 
> 

## Usare le reti virtuali di Azure
Con una [rete virtuale di Azure](https://azure.microsoft.com/documentation/services/virtual-network/) è possibile creare una rete sicura e persistente che contiene le risorse necessarie per la propria soluzione. Con una rete virtuale è possibile:

* Connettere le risorse cloud tra loro in una rete privata (solo cloud).
  
    ![Diagramma di una configurazione solo cloud](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)
* Connettere le risorse cloud alla rete del data center locale (da sito a sito o da punto a sito) usando una rete privata virtuale (VPN)

| Configurazione da sito a sito | Configurazione da punto a sito |
| --- | --- |
| Con la configurazione da sito a sito è possibile connettere più risorse dal data center alla rete virtuale di Azure usando una rete VPN hardware o il servizio Routing e Accesso remoto.<br />![Diagramma di una configurazione da sito a sito](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) |Con la configurazione da punto a sito è possibile connettere una risorsa specifica alla rete virtuale di Azure usando una rete VPN software.<br />![Diagramma di una configurazione da punto a sito](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

I cluster basati su Windows richiedono una rete virtuale v1 (versione classica), mentre i cluster basati su Linux richiedono una rete virtuale v2 (Azure Resource Manager). Se non si dispone del tipo di rete corretto, non sarà utilizzabile quando si crea il cluster.

Per altre informazioni sull'uso di HDInsight con una rete virtuale, inclusi i requisiti di configurazione specifici per la rete virtuale, vedere [Estendere le funzionalità di HDInsight usando Rete virtuale di Azure](hdinsight-extend-hadoop-virtual-network.md).

## Personalizzare i cluster mediante la personalizzazione dei cluster HDInsight (Bootstrap)
A volte potrebbe essere necessario modificare i file di configurazione seguenti:

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

Per mantenere le modifiche per tutta la durata del cluster, è possibile usare la funzionalità di personalizzazione dei cluster HDInsight durante il processo di creazione oppure usare Ambari nei cluster basati su Linux. Per altre informazioni, vedere [Personalizzare cluster HDInsight tramite Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

> [!NOTE]
> I cluster basati su Windows non possono conservare le modifiche a causa del re-imaging. Per ulteriori informazioni, vedere [Riavvi delle istanze del ruolo dovuti ad aggiornamenti del sistema operativo](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Per mantenere le modifiche per tutta la durata dei cluster, è necessario usare la funzionalità di personalizzazione dei cluster HDInsight durante il processo di creazione.
> 
> 

## Personalizzare i cluster con l'azione script
L'uso di script durante la creazione consente di installare componenti aggiuntivi o personalizzare la configurazione di un cluster. Gli script vengono chiamati tramite un'**azione script**, ovvero un'opzione di configurazione che può essere usata da portale di Azure, dai cmdlet di Windows PowerShell per HDInsight o da .NET SDK per HDInsight. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster-linux.md).

Nel cluster è possibile eseguire alcuni componenti Java nativi, come Mahout e Cascading, sotto forma di file JAR (Java Archive). Questi file JAR possono essere distribuiti nell'archivio BLOB di Azure e inviati ai cluster HDInsight usando i meccanismi di invio dei processi Hadoop. Per altre informazioni, vedere [Inviare processi Hadoop a livello di codice](hdinsight-submit-hadoop-jobs-programmatically.md).

> [!NOTE]
> In caso di problemi durante la distribuzione di file JAR in cluster HDInsight o la chiamata di file JAR in cluster HDInsight, contattare il [Supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).
> 
> Cascading non è supportato da HDInsight, quindi in caso di problemi non è possibile rivolgersi al Supporto Microsoft. Per gli elenchi dei componenti supportati, vedere [Quali sono i diversi componenti di Hadoop disponibili in HDInsight?](hdinsight-component-versioning.md)
> 
> 

## Usare il nodo perimetrale
 Il nodo perimetrale vuoto è una macchina virtuale Linux con gli stessi strumenti client installati e configurati come nel nodo head. È possibile usare il nodo perimetrale per accedere al cluster, testare e ospitare le applicazioni client. Per altre informazioni, vedere [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Usare nodi perimetrali vuoti in HDInsight).

## Metodi di creazione di cluster
In questo articolo sono state fornite informazioni di base sulla creazione di un cluster HDInsight basato su Linux. Usare la tabella seguente per trovare informazioni specifiche su come creare un cluster usando un metodo ottimale per le proprie esigenze.

| Cluster creati con | Web browser | Riga di comando | API REST | SDK | Linux, Mac OS X o Unix | Windows |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| [Il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |✔ |✔ |
| [Data factory di Azure](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |✔ |✔ |
| [Interfaccia della riga di comando di Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |✔ |✔ |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |✔ |✔ |
| [Modelli di Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |& nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |

<!---HONumber=AcomDC_0914_2016-->