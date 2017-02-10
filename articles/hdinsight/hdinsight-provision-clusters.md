---
title: Creare cluster Hadoop basati su Windows in HDInsight | Documentazione Microsoft
description: Informazioni su come creare cluster per Azure HDInsight.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: a311f7e0-9333-4886-a726-def79e5db8cb
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/21/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: ede2e4ec5f3414d1c8a17f4c120011eba0d9a6ca
ms.openlocfilehash: 14dcb8fe12bb516bc27b27f94992801d935811c8


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight"></a>Creare cluster Hadoop basati su Windows in HDInsight

[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Un cluster Hadoop è costituito da alcune macchine virtuali (nodi) che vengono usate per l'elaborazione distribuita di attività nel cluster. Azure consente di astrarre i dettagli di implementazione dell'installazione e della configurazione dei singoli nodi. È quindi necessario specificare le informazioni di configurazione generali. In questo articolo vengono illustrate queste impostazioni di configurazione.

Le informazioni contenute in questo documento sono specifiche per i cluster HDInsight di Azure basati su Windows. Per informazioni sui cluster basati su Linux, vedere [Creare cluster Hadoop basati su Linux in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).



## <a name="access-control-requirements"></a>Requisiti di controllo di accesso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="cluster-types"></a>Tipi di cluster
Attualmente, HDInsight offre quattro tipi diversi di cluster, ognuno con un set di componenti che forniscono funzionalità specifiche.

| Tipo di cluster | Funzionalità |
| --- | --- |
| Hadoop |Query e analisi (processi batch) |
| HBase |Archiviazione di dati NoSQL |
| Storm |Elaborazione di eventi in tempo reale |
| Spark (anteprima) |Elaborazione in memoria, query interattive, elaborazione di flussi di micro batch |

Ogni tipo di cluster ha il proprio numero di nodi, una terminologia specifica per i nodi del cluster e dimensioni predefinite delle macchine virtuali per ogni tipo di nodo. Nella tabella seguente, il numero di nodi per ogni tipo di nodo è indicato tra parentesi.

| Tipo | Nodi (numero di nodi) | Diagramma |
| --- | --- | --- |
| Hadoop |Nodo head (2), nodo dati (1+) |![Nodi del cluster HDInsight Hadoop](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png) |
| HBase |Server head (2), server di area (1+), nodo master/ZooKeeper (3) |![Nodi del cluster HDInsight HBase](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png) |
| Storm |Nodo Nimbus (2), server supervisore (1+), nodo ZooKeeper (3) |![Nodi del cluster HDInsight Storm](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png) |
| Spark |Nodo head (2), nodo Worker (1+), nodo ZooKeeper (3), gratuito per le macchine virtuali ZooKeeper con dimensioni A1 |![Nodi del cluster HDInsight Spark](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png) |

> [!IMPORTANT]
> Se si prevedono più di 32 nodi del ruolo di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM.
> 
> 

È possibile aggiungere altri componenti, ad esempio Hue o R, a questi tipi di base mediante [azioni script](#customize-clusters-using-script-action).

> [!IMPORTANT]
> Sono disponibili molti tipi di cluster HDInsight, che corrispondono al carico di lavoro o alla tecnologia per cui è ottimizzato il cluster. Non è disponibile alcun metodo supportato per creare un cluster che combini più tipi, ad esempio Storm e HBase in un cluster. 
> 
> 

Se la soluzione richiede tecnologie che vengono distribuite tra più tipi di cluster HDInsight, è necessario creare una rete virtuale di Azure, nonché i tipi di cluster necessari all'interno della rete virtuale. In questo modo i cluster e il codice in essi distribuito possono comunicare direttamente tra loro.

Per altre informazioni sull'uso di una rete virtuale di Azure con HDInsight, vedere [Estendere HDInsight con le reti virtuali di Azure](hdinsight-extend-hadoop-virtual-network.md).

Per un esempio dell'uso di due tipi di cluster in una rete virtuale di Azure, vedere [Analizzare i dati del sensore con Storm e HBase](hdinsight-storm-sensor-data-analysis.md).

## <a name="basic-configuration-options"></a>Opzioni di configurazione di base
Di seguito sono riportate le opzioni di configurazione di base necessarie per la creazione di un cluster HDInsight.

### <a name="cluster-name"></a>Nome del cluster
Il campo Nome cluster consente di identificare un cluster. Il nome del cluster deve essere globalmente univoco e seguire le convenzioni di denominazione seguenti:

* Il campo deve essere una stringa contenente da 3 a 63 caratteri.
* Il campo può contenere solo lettere, numeri e trattini.

### <a name="cluster-type"></a>Tipo di cluster
Vedere [Tipi di cluster](#cluster-types).

### <a name="operating-system"></a>Sistema operativo
È possibile creare cluster HDInsight in uno dei due sistemi operativi seguenti:

* HDInsight in Linux. HDInsight consente di configurare cluster Linux in Azure. È possibile configurare un cluster Linux se si ha familiarità con Linux o Unix, si esegue la migrazione da una soluzione Hadoop basata su Linux esistente o si vuole integrare facilmente i componenti dell'ecosistema Hadoop compilati per Linux. Per altre informazioni, vedere l'articolo di [introduzione all'uso di Hadoop su Linux in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
* HDInsight in Windows (Windows Server 2012 R2 Datacenter).

### <a name="hdinsight-version"></a>Versione HDInsight
Viene usata per determinare la versione di HDInsight da usare per il cluster. Per altre informazioni, vedere [Quali sono i diversi componenti di Hadoop disponibili in HDInsight?](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)

### <a name="subscription-name"></a>Nome della sottoscrizione
Ogni cluster HDInsight è associato a una sottoscrizione di Azure.

### <a name="resource-group-name"></a>Nome del gruppo di risorse
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) permette di distribuire, aggiornare, monitorare o eliminare le risorse per l'applicazione.

### <a name="credentials"></a>Credenziali
Con i cluster HDInsight è possibile configurare tre account utente durante la creazione del cluster.

* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) consente di usare le risorse dell'applicazione come gruppo, denominato gruppo di risorse di Azure. È quindi possibile distribuire, aggiornare, monitorare o eliminare tutte le risorse per l'applicazione in un'unica operazione coordinata.
* Utente HTTP. Il nome utente predefinito è *admin* con la configurazione di base nel portale di Azure. In alcuni casi l'utente predefinito viene chiamato "utente cluster".
* Utente RDP (cluster Windows). Permette di connettersi al cluster tramite RDP. Quando si crea l'account, è necessario impostare una scadenza che sia entro 90 giorni dalla data di creazione dell'account.
* Utente SSH (cluster Linux). Permette di connettersi al cluster tramite SSH. È possibile creare account utente SSH aggiuntivi dopo la creazione del cluster tramite l'esecuzione dei passaggi illustrati in [Uso di SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="data-source"></a>Origine dati
Hadoop Distributed File System (HDFS) originale usa molti dischi locali nel cluster. HDInsight usa l'archivio BLOB di Azure per l'archiviazione dei dati. L'archiviazione BLOB di Azure è una soluzione di archiviazione affidabile, con finalità generali che si integra facilmente con HDInsight. Grazie a un'interfaccia HDFS, tutti i componenti disponibili in HDInsight possono agire direttamente sui dati strutturati o non strutturati presenti nell'archivio BLOB. La memorizzazione dei dati nell'archivio BLOB consente l'eliminazione sicura dei cluster HDInsight usati per i calcoli, senza perdita di dati utente.

Durante la configurazione è necessario specificare un account di archiviazione di Azure e un contenitore di archiviazione BLOB di Azure nell'account di archiviazione di Azure. Alcuni processi di creazione richiedono prima di tutto la creazione dell'account di archiviazione di Azure e del contenitore di archiviazione BLOB. Il contenitore di archiviazione BLOB viene usato dal cluster come posizione di archiviazione predefinita. Facoltativamente, è possibile specificare account di archiviazione di Azure aggiuntivi, ovvero account di archiviazione collegati, a cui il cluster potrà accedere. Il cluster può accedere anche a eventuali contenitori dell'archivio BLOB configurati con accesso in lettura pubblico completo o accesso in lettura pubblico solo per i BLOB.  Per ulteriori informazioni, vedere [gestione dell'accesso alle risorse di archiviazione Azure](../storage/storage-manage-access-to-resources.md).

![Archiviazione di HDInsight](./media/hdinsight-provision-clusters/HDInsight.storage.png)

> [!NOTE]
> Un contenitore di archiviazione BLOB offre un raggruppamento di un set di BLOB, come illustrato nell'immagine seguente.
> 
> 

Durante la configurazione è necessario specificare un account di archiviazione di Azure e un contenitore di archiviazione BLOB di Azure nell'account di archiviazione di Azure. Alcuni processi di creazione richiedono prima di tutto la creazione dell'account di archiviazione di Azure e del contenitore di archiviazione BLOB. Il contenitore di archiviazione BLOB viene usato dal cluster come posizione di archiviazione predefinita. Facoltativamente, è possibile specificare account di archiviazione di Azure aggiuntivi, ovvero account di archiviazione collegati, a cui il cluster può accedere. Il cluster può accedere anche a eventuali contenitori BLOB configurati con accesso in lettura pubblico completo o accesso in lettura pubblico solo per i BLOB. Per ulteriori informazioni, vedere [gestione dell'accesso alle risorse di archiviazione Azure](../storage/storage-manage-access-to-resources.md).

![Archiviazione BLOB di Azure](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)

Non è consigliabile usare il contenitore dell'archivio BLOB predefinito per la memorizzazione dei dati aziendali. È consigliabile eliminare il contenitore di archiviazione BLOB predefinito dopo ogni uso per ridurre i costi di archiviazione. Il contenitore predefinito include registri di sistema e applicazioni. Assicurarsi di recuperare i registri prima di eliminare il contenitore.

> [!WARNING]
> HDInsight non supporta la condivisione di un contenitore dell'archivio BLOB tra più cluster.
> 
> 

Per altre informazioni sugli archivi BLOB secondari, vedere [Usare un archivio BLOB di Azure compatibile con HDFS con Hadoop in HDInsight](hdinsight-hadoop-use-blob-storage.md).

Oltre all'archivio BLOB di Azure, è possibile usare anche [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) come account di archiviazione predefinito per un cluster HBase in HDInsight e come spazio di archiviazione collegato per tutti e quattro i tipi di cluster HDInsight. Per altre informazioni, vedere [Creare un cluster HDInsight con Data Lake Store tramite il portale di Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

### <a name="location-region"></a>Località (area)
Il cluster HDInsight e l'account di archiviazione predefinito devono trovarsi nella stessa località di Azure.

![Aree di Azure](./media/hdinsight-provision-clusters/Azure.regions.png)

Per un elenco di aree supportate, fare clic sull'elenco a discesa **Area** in [Prezzi di HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

### <a name="node-pricing-tiers"></a>Piani tariffari per il nodo
L'uso di questi nodi viene addebitato ai clienti per la durata del ciclo di vita del cluster. La fatturazione inizia con la creazione del cluster e si interrompe quando il cluster viene eliminato. I cluster non possono essere deallocati o messi in attesa.

Diversi tipi di cluster hanno diversi tipi, numeri e dimensioni di nodi. Ad esempio, un cluster Hadoop ha due *nodi head* e, per impostazione predefinita, quattro *nodi dati*, mentre un cluster di tipo Storm ha due *nodi Nimbus*, tre *nodi Zookeeper* e, per impostazione predefinita, quattro *nodi supervisore*. Il costo dei cluster HDInsight è determinato dal numero di nodi e dalle dimensioni delle macchine virtuali per i nodi. Ad esempio, se si prevede di eseguire operazioni che richiedono una quantità elevata di memoria, è consigliabile selezionare una risorsa di calcolo con più memoria. Ai fini dell'apprendimento, è consigliabile usare un solo nodo dati. Per altre informazioni sui prezzi di HDInsight, vedere [Prezzi di HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]
> Il limite relativo alle dimensioni del cluster dipende dalla sottoscrizione di Azure. Per aumentare il limite, contattare il team del supporto fatturazione.
> 
> I nodi usati dal cluster non contano come macchine virtuali perché le immagini di macchine virtuali usate per i nodi sono un dettaglio di implementazione del servizio HDInsight. I core di calcolo usati dai nodi influiscono tuttavia sul numero totale di core di calcolo disponibili per la sottoscrizione. Durante la creazione di un cluster HDInsight è possibile visualizzare i core disponibili e il numero di core che verranno usati dal cluster nella sezione di riepilogo del pannello Piani tariffari per il nodo.
> 
> 

Quando si usa il portale di Azure per configurare il cluster, le dimensioni del nodo sono disponibili tramite il pannello **Piano tariffario per il nodo** . È anche possibile visualizzare il costo associato alle diverse dimensioni dei nodi. Lo screenshot seguente mostra le opzioni disponibili per un cluster Hadoop basato su Linux.

![Dimensioni dei nodi delle VM di HDInsight](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

Le tabelle seguenti illustrano le dimensioni supportate dai cluster HDInsight e le capacità offerte.

### <a name="standard-tier-a-series"></a>Livello Standard: serie A
Nel modello di distribuzione classico alcune dimensioni delle macchine virtuali sono leggermente diverse in PowerShell e nell'interfaccia della riga di comando.

* Standard_A3 è Large
* Standard_A4 è ExtraLarge

| Dimensione | Core CPU | Memoria | NIC (max) | Max. Dimensioni disco | Max. (1023 GB ciascuno) | Dischi di dati max. IOPS (500 per disco) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A3\Large |4 |7 GB |2 |Temporaneo = 285 GB |8 |8x500 |
| Standard_A4\ExtraLarge |8 |14 GB |4 |Temporaneo = 605 GB |16 |16x500 |
| Standard_A6 |4 |28 GB |2 |Temporaneo = 285 GB |8 |8x500 |
| Standard_A7 |8 |56 GB |4 |Temporaneo = 605 GB |16 |16x500 |

### <a name="standard-tier-d-series"></a>Livello Standard: serie D
| Dimensione | Core CPU | Memoria | NIC (max) | Max. Dimensioni disco | Max. (1023 GB ciascuno) | Dischi di dati max. IOPS (500 per disco) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D3 |4 |14 GB |4 |Temporaneo (SSD) =&200; GB |8 |8x500 |
| Standard_D4 |8 |28 GB |8 |Temporaneo (SSD) =&400; GB |16 |16x500 |
| Standard_D12 |4 |28 GB |4 |Temporaneo (SSD) =&200; GB |8 |8x500 |
| Standard_D13 |8 |56 GB |8 |Temporaneo (SSD) =&400; GB |16 |16x500 |
| Standard_D14 |16 |112 GB |8 |Temporaneo (SSD) =&800; GB |32 |32x500 |

### <a name="standard-tier-dv2-series"></a>Livello standard: serie Dv2
| Dimensione | Core CPU | Memoria | NIC (max) | Max. Dimensioni disco | Max. (1023 GB ciascuno) | Dischi di dati max. IOPS (500 per disco) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D3_v2 |4 |14 GB |4 |Temporaneo (SSD) =&200; GB |8 |8x500 |
| Standard_D4_v2 |8 |28 GB |8 |Temporaneo (SSD) =&400; GB |16 |16x500 |
| Standard_D12_v2 |4 |28 GB |4 |Temporaneo (SSD) =&200; GB |8 |8x500 |
| Standard_D13_v2 |8 |56 GB |8 |Temporaneo (SSD) =&400; GB |16 |16x500 |
| Standard_D14_v2 |16 |112 GB |8 |Temporaneo (SSD) =&800; GB |32 |32x500 |

Per considerazioni sulla distribuzione da tenere presenti quando si prevede di usare queste risorse, vedere [Dimensioni delle macchine virtuali in Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Per informazioni sui prezzi delle varie dimensioni, vedere i [prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).   

> [!IMPORTANT]
> Se si prevedono più di 32 nodi del ruolo di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM.
> 
> 

La fatturazione inizia con la creazione del cluster e si interrompe quando il cluster viene eliminato. Per altre informazioni sui prezzi, vedere i [prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

| Dimensione | Core CPU | Memoria | NIC (max) | Max. Dimensioni disco | Max. (1023 GB ciascuno) | Dischi di dati max. IOPS (500 per disco) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D3_v2 |4 |14 GB |4 |Temporaneo (SSD) =&200; GB |8 |8x500 |
| Standard_D4_v2 |8 |28 GB |8 |Temporaneo (SSD) =&400; GB |16 |16x500 |
| Standard_D12_v2 |4 |28 GB |4 |Temporaneo (SSD) =&200; GB |8 |8x500 |
| Standard_D13_v2 |8 |56 GB |8 |Temporaneo (SSD) =&400; GB |16 |16x500 |
| Standard_D14_v2 |16 |112 GB |8 |Temporaneo (SSD) =&800; GB |32 |32x500 |

Per considerazioni sulla distribuzione da tenere presenti quando si prevede di usare queste risorse, vedere [Dimensioni delle macchine virtuali in Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Per informazioni sui prezzi delle varie dimensioni, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).  

> [!IMPORTANT]
> Se si prevedono più di 32 nodi del ruolo di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM.
> 
> 

 La fatturazione inizia con la creazione del cluster e si interrompe quando il cluster viene eliminato. Per altre informazioni sui prezzi, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="add-more-storage"></a>Aggiungere altre risorse di archiviazione
In alcuni casi potrebbe essere necessario aggiungere altre risorse di archiviazione al cluster, ad esempio se sono presenti più account di archiviazione di Azure per aree geografiche o servizi diversi, ma si vuole analizzare tutti gli account con HDInsight.

Per altre informazioni sugli archivi BLOB secondari, vedere [Usare un archivio BLOB di Azure compatibile con HDFS con Hadoop in HDInsight](hdinsight-hadoop-use-blob-storage.md). Per altre informazioni sull'uso degli archivi Data Lake secondari, vedere [Creare un cluster HDInsight con Data Lake Store tramite il portale di Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="use-a-hiveoozie-metastore"></a>Usare un metastore Hive/Oozie
È consigliabile usare un metastore personalizzato per conservare le tabelle Hive dopo aver eliminato il cluster HDInsight, allo scopo di associare il metastore a un altro cluster HDInsight in futuro.

> [!IMPORTANT]
> Il metastore HDInsight non è compatibile con le versioni precedenti. Ad esempio, non è possibile utilizzare un metastore di un cluster HDInsight 3.3 per creare un cluster HDInsight 3.2.
> 
> 

Il metastore contiene metadati Hive e Oozie, tra cui tabelle di Hive, partizioni, schemi e colonne. Il metastore permette di conservare i metadati Hive e Oozie. Quando si crea un nuovo cluster non è necessario creare nuovamente tabelle Hive o processi Oozie. Per impostazione predefinita, Hive usa un database SQL di Azure incorporato per l'archiviazione di queste informazioni. Se il cluster viene eliminato, tuttavia, i metadati non vengono mantenuti nel database incorporato. Ad esempio, quando si creano tabelle Hive in un cluster creato con un metastore Hive, è possibile visualizzare le tabelle se si elimina e si crea nuovamente il cluster con lo stesso metastore Hive.

La configurazione Metastore non è disponibile per i tipi di cluster HBase.

> [!IMPORTANT]
> Quando si crea un metastore personalizzato, non usare un nome di database che contiene trattini o segni meno, perché in quel caso il processo di creazione del cluster non andrebbe a buon fine.
> 
> 

## <a name="use-azure-virtual-network"></a>Usare una rete virtuale di Azure
[rete virtuale di Azure](https://azure.microsoft.com/documentation/services/virtual-network/) permette di creare una rete sicura e persistente che contiene le risorse necessarie per la propria soluzione. Con una rete virtuale è possibile:

* Connettere le risorse cloud tra loro in una rete privata (solo cloud).
  
    ![Diagramma di una configurazione solo cloud](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)
* Connettere le risorse cloud alla rete del data center locale (da sito a sito o da punto a sito) usando una rete privata virtuale (VPN)

| Configurazione da sito a sito | Configurazione da punto a sito |
| --- | --- |
| La configurazione da sito a sito permette di connettere più risorse dal data center alla rete virtuale di Azure usando una rete VPN hardware o il servizio Routing e Accesso remoto.<br />![Diagramma di una configurazione da sito a sito](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) |La configurazione da punto a sito permette di connettere una risorsa specifica alla rete virtuale di Azure usando una rete VPN software.<br />![Diagramma di una configurazione da punto a sito](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

I cluster basati su Windows richiedono una rete virtuale classica, mentre i cluster basati su Linux richiedono una rete virtuale di Azure Resource Manager. Se non si dispone del tipo di rete corretto, non sarà utilizzabile quando si crea il cluster.

Per altre informazioni sul funzionamento di HDInsight con una rete virtuale, inclusi i requisiti di configurazione specifici per la rete virtuale, vedere [Estendere le funzionalità di HDInsight usando Rete virtuale di Azure](hdinsight-extend-hadoop-virtual-network.md).

## <a name="customize-clusters-by-using-hdinsight-cluster-customization-bootstrap"></a>Personalizzare i cluster tramite la personalizzazione dei cluster HDInsight (bootstrap)
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

Per mantenere le modifiche per l'intero ciclo di vita dei cluster, è possibile usare la personalizzazione dei cluster HDInsight durante il processo di creazione. È anche possibile usare Ambari in cluster basati su Linux. Per altre informazioni, vedere [Personalizzare cluster HDInsight tramite Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

> [!NOTE]
> I cluster basati su Windows non possono conservare le modifiche perché le immagini vengono ricreate. Per ulteriori informazioni, vedere [Riavvi delle istanze del ruolo dovuti ad aggiornamenti del sistema operativo](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Per mantenere le modifiche per tutta la durata di un cluster, è necessario usare la funzionalità di personalizzazione dei cluster HDInsight durante il processo di creazione.
> 
> 

## <a name="customize-clusters-by-using-script-action"></a>Personalizzare i cluster tramite l'azione script
L'uso di script durante la creazione consente di installare componenti aggiuntivi o personalizzare la configurazione di un cluster. Gli script vengono chiamati tramite un' **azione script**, ovvero un'opzione di configurazione che può essere usata da portale di Azure, dai cmdlet di Windows PowerShell per HDInsight o da .NET SDK per HDInsight. Per altre informazioni, vedere [Personalizzare cluster HDInsight basati su Windows tramite Azione script](hdinsight-hadoop-customize-cluster.md).

## <a name="cluster-creation-methods"></a>Metodi di creazione di cluster
In questo articolo si sono apprese informazioni di base sulla creazione di un cluster HDInsight basato su Windows. Usare la tabella seguente per trovare informazioni specifiche su come creare un cluster usando un metodo ottimale per le proprie esigenze.

| Cluster creati con | Web browser | Riga di comando | API REST | SDK | Linux, Mac OS X o Unix | Windows |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| [Il portale di Azure](hdinsight-hadoop-create-windows-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |✔ |✔ |
| [Interfaccia della riga di comando di Azure](hdinsight-hadoop-create-windows-clusters-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |
| [Azure PowerShell](hdinsight-hadoop-create-windows-clusters-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |✔ |✔ |
| [.NET SDK](hdinsight-hadoop-create-windows-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |✔ |✔ |
| [Modelli di Gestione risorse di Azure](hdinsight-hadoop-create-windows-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |




<!--HONumber=Jan17_HO3-->


