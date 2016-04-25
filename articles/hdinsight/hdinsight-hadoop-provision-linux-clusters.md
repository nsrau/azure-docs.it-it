<properties
   	pageTitle="Creare cluster Hadoop, HBase, Storm o Spark in Linux in HDInsight | Microsoft Azure"
   	description="Informazioni su come creare cluster Hadoop, HBase, Storm o Spark in Linux per HDInsight mediante un browser, la CLI di Azure, Azure PowerShell, REST o un SDK."
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="03/25/2016"
   	ms.author="jgao"/>


# Creare cluster Hadoop basati su Linux in HDInsight

[AZURE.INCLUDE [selettore](../../includes/hdinsight-selector-create-clusters.md)]

Un cluster Hadoop è costituito da alcune macchine virtuali (nodi) che vengono usate per l'elaborazione distribuita di attività sul cluster. Azure consente di astrarre i dettagli di implementazione dell'installazione e della configurazione dei singoli nodi. È quindi necessario specificare solo le informazioni di configurazione generali. Questo articolo illustra queste impostazioni di configurazione.

##Tipi di cluster

Attualmente, HDInsight offre 5 tipi diversi di cluster, ognuno con un set di componenti per fornire funzionalità specifiche:

| Tipo di cluster | Usare questo per... |
| ------------ | ----------------------------- |
| Hadoop | Query e analisi (processi batch). |
| HBase | Archiviazione di dati NoSQL. |
| Storm | Elaborazione di eventi in tempo reale. |
| Spark (anteprima) | Elaborazione in memoria, query interattive, elaborazione di flussi di micro batch. |
| R Server in Spark | R supporta un'ampia gamma di statistiche di Big Data, la modellazione predittiva e funzionalità di Machine Learning. |

Ogni tipo di cluster usa una terminologia specifica per i nodi nel cluster, oltre a un numero di nodi specifico e a dimensioni predefinite per le VM per ogni tipo di nodo:

| Tipo| Nodi (numero di nodi)| Diagramma|
|-----|------|--------|
|Hadoop| Nodo head (2), nodo dati (1+)|![Nodi del cluster HDInsight Hadoop](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)|
|HBase|Server head (2), server di area (1+), nodo Master/Zookeeper (3)|![Nodi del cluster HDInsight HBase](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)|
|Storm|Nodo Nimbus (2), server Supervisor (1+), nodo Zookeeper (3)|![Nodi del cluster HDInsight Storm](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)|
|Spark|Nodo head (2), nodo Worker (1+), nodo Zookeeper (3) (gratuito per le VM Zookeeper con dimensioni A1)|![Nodi del cluster HDInsight Spark](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)|

* Tra parentesi è indicato il numero di nodi per ogni tipo di nodo.

> [AZURE.IMPORTANT] Se si prevedono più di 32 nodi di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM.

È possibile aggiungere altri componenti, ad esempio Hue o R, a questi tipi base mediante [Azioni script](#customize-clusters-using-script-action).

## Livelli di cluster

Azure HDInsight presenta le offerte cloud per i Big Data in due categorie: Standard e [Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium). HDInsight Premium include R e altri componenti aggiuntivi. HDInsight Premium è supportato solo in HDInsight versione 3.4.

Nella tabella seguente sono elencati il tipo di cluster HDInsight e la matrice di supporto HDInsight Premium.

| Tipo di cluster | Standard | Premium |
|--------------|---------------|--------------|
| Hadoop | Sì | Sì |
| Spark | Sì | Sì |
| HBase | Sì | No |
| Storm | Sì | No |
| R Server in Spark | No | Sì |

Questa tabella verrà aggiornata man mano che vengono inclusi altri cluster in HDInsight Premium. La schermata seguente mostra le informazioni sul portale di Azure per la scelta dei tipi di cluster:

![Configurazione di HDInsight Premium](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-type-configuration.png)


## Opzioni di configurazione di base

Di seguito sono riportate le opzioni di configurazione di base per la creazione di un cluster HDInsight.

- **Nome cluster**

	Il campo Nome cluster consente di identificare un cluster. Il nome del cluster deve essere globalmente univoco e seguire le convenzioni di denominazione seguenti:

	- Il campo deve essere una stringa contenente da 3 a 63 caratteri.
	- Il campo può contenere solo lettere, numeri e trattini.

- **Tipo di cluster**

    Vedere [Tipi di cluster](#cluster-types) e [Livelli di cluster](#cluster-tiers).

- **Sistema operativo**

	È possibile creare cluster HDInsight in uno dei due sistemi operativi seguenti:
	- **HDInsight in Linux (Ubuntu 12.04 LTS for Linux)**: HDInsight consente di configurare cluster Linux in Azure. Se si ha familiarità con Linux o Unix, è infatti possibile configurare un cluster Linux eseguendo la migrazione da una soluzione Hadoop basata su Linux esistente oppure è possibile scegliere di integrarsi facilmente con i componenti dell'ecosistema Hadoop sviluppati per Linux. Per altre informazioni, vedere l'articolo di [introduzione all'uso di Hadoop su Linux in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
	- **HDInsight in Windows (Windows Server 2012 R2 Datacenter)**:
    
- **Versione HDInsight**

	Consente di determinare la versione di HDInsight da usare per questo cluster. Per altre informazioni, vedere [Versioni di HDInsight e componenti di Hadoop](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)

- **Nome sottoscrizione**

	Ogni cluster HDInsight è associato a una sottoscrizione di Azure.
    
- **Nome del gruppo di risorse**

	[Azure Resource Manager (ARM)](resource-group-overview.md) consente di usare le risorse dell'applicazione come gruppo, detto gruppo di risorse di Azure. È quindi possibile distribuire, aggiornare, monitorare o eliminare tutte le risorse per l'applicazione con una singola operazione coordinata.

- **Credenziali**

	I cluster HDInsight consentono di configurare due account utente durante la creazione del cluster:

	- Utente HTTP. Il nome utente predefinito è admin con la configurazione di base nel portale di Azure. In alcuni casi è detto "utente cluster".
	- Utente SSH (cluster Linux): usato per la connessione ai cluster tramite SSH. È possibile creare account utente SSH aggiuntivi dopo la creazione del cluster tramite l'esecuzione dei passaggi illustrati in [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md) o [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-unix.md).

    >[AZURE.NOTE] Per i cluster basati su Windows, è possibile creare un utente RDP e usarlo per connettersi al cluster tramite RDP.

- **Origine dati**

	L'interfaccia HDFS originale usa molti dischi locali nel cluster. HDInsight usa invece l'archivio BLOB di Azure per l'archiviazione dei dati. L'archiviazione BLOB di Azure è una soluzione di archiviazione affidabile, con finalità generali che si integra facilmente con HDInsight. Grazie a un'interfaccia HDFS (Hadoop Distributed File System), tutti i componenti disponibili in HDInsight possono agire direttamente sui dati strutturati o non strutturati presenti nell'archiviazione BLOB. L'archiviazione dei dati nell'archiviazione BLOB consente l'eliminazione sicura dei cluster HDInsight usati per i calcoli, senza perdita di dati utente.

	Durante la configurazione è necessario specificare un account di archiviazione di Azure e un contenitore di archiviazione BLOB di Azure nell'account di archiviazione di Azure. Alcuni processi di creazione richiedono prima di tutto la creazione dell'account di archiviazione di Azure e del contenitore di archiviazione BLOB. Il contenitore di archiviazione BLOB viene usato dal cluster come posizione di archiviazione predefinita. Facoltativamente, è possibile specificare account di archiviazione di Azure aggiuntivi (account di archiviazione collegati) a cui il cluster potrà accedere. Il cluster può accedere anche a eventuali contenitori BLOB configurati con accesso in lettura pubblico completo o accesso in lettura pubblico solo per i BLOB. Per altre informazioni sull'accesso con limitazioni, vedere [Gestire l'accesso alle risorse di archiviazione di Azure](storage-manage-access-to-resources.md).

	![Archiviazione di HDInsight](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE] Un contenitore di archiviazione BLOB offre un raggruppamento di un set di BLOB, come illustrato nell'immagine:

	![Archivio BLOB di Azure](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)

    Non è consigliabile usare il contenitore BLOB predefinito per l'archiviazione dei dati aziendali. È consigliabile eliminare il contenitore BLOB predefinito dopo ogni uso per ridurre i costi di archiviazione. Si noti che il contenitore predefinito include registri di sistema e applicazioni. Assicurarsi di recuperare i registri prima di eliminare il contenitore.
    
	>[AZURE.WARNING] La condivisione di un contenitore di archiviazione BLOB tra più cluster non è supportata.

	Per altre informazioni sull'uso degli archivi BLOB secondari, vedere [Uso dell'archiviazione BLOB di Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).

    Oltre all'archiviazione BLOB di Azure, è possibile usare anche [Archivio Azure Data Lake](data-lake-store-overview.md) come account di archiviazione predefinito per il cluster HBase in HDInsight e come spazio di archiviazione collegato per tutti e 4 i tipi di cluster HDInsight. Per le istruzioni, vedere [Creare un cluster HDInsight con Archivio Data Lake tramite il portale di Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
    
- **Località (Area)**

	Il cluster HDInsight e l'account di archiviazione predefinito devono trovarsi nella stessa località di Azure.
	
	![Aree di Azure](./media/hdinsight-provision-clusters/Azure.regions.png)

	Per un elenco di aree supportate, fare clic sull'elenco a discesa **Area** in [Prezzi di HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

- **Piani tariffari per il nodo**

    L'uso di questi nodi viene addebitato ai clienti per la durata del ciclo di vita del cluster. La fatturazione inizia dopo la creazione di un cluster e si interrompe quando il cluster viene eliminato. Non è possibile deallocare o mettere in attesa i cluster.

	Diversi tipi di cluster hanno diversi tipi, numeri e dimensioni di nodi. Ad esempio, un cluster Hadoop ha due _nodi head_ e, per impostazione predefinita, quattro _nodi dati_, mentre un cluster di tipo Storm ha due _nodi Nimbus_, tre _nodi Zookeeper_ e, per impostazione predefinita, quattro _nodi supervisore_. Il costo del cluster HDInsight è determinato dal numero di nodi e dalle dimensioni delle macchine virtuali per i nodi. Ad esempio, se si prevede di eseguire operazioni che richiedono una quantità elevata di memoria, è consigliabile selezionare una risorsa di calcolo con più memoria. Ai fini dell'apprendimento, è consigliabile usare un nodo di dati. Per altre informazioni sui prezzi di HDInsight, vedere [Prezzi di HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

	>[AZURE.NOTE] Il limite relativo alle dimensioni del cluster dipende dalla sottoscrizione di Azure. Per aumentare il limite, contattare il team del supporto fatturazione.
	
    >I nodi usati dal cluster non contano come macchine virtuali, in quanto le immagini di macchine virtuali usate per i nodi sono un dettaglio di implementazione del servizio HDInsight. Tuttavia, i core di calcolo usati dai nodi influiscono sul numero totale di core di calcolo disponibili per la sottoscrizione. Durante la creazione di un cluster HDInsight è possibile visualizzare il numero di core che verranno usati dal cluster, nonché il numero di core disponibili, nella sezione di riepilogo del pannello Piani tariffari per il nodo.

	Quando si usa il portale di Azure per configurare il cluster, le dimensioni del nodo vengono esposte tramite il pannello __Piano tariffario per il nodo__ e verrà visualizzato anche il costo associato alle diverse dimensioni di nodo. La schermata seguente mostra le opzioni disponibili per un cluster Hadoop basato su Linux:

	![Dimensioni dei nodi delle VM di HDInsight](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

    Le tabelle seguenti illustrano le dimensioni supportate dai cluster HDInsight e le capacità offerte.

    - Livello Standard: serie A

        Nel modello di distribuzione classico alcune dimensioni delle macchine virtuali sono leggermente diverse in PowerShell e nell'interfaccia della riga di comando.

        * Standard\_A3 è Large
        * Standard\_A4 è ExtraLarge

        <br>

        |Dimensione |Core CPU|Memoria|NIC (Max)|Dimensione disco max.|Dischi di dati max. (1023 GB ciascuno)|Max. IOPS (500 per disco)|
        |---|---|---|---|---|---|---|
        |Standard\_A3\\Large|4|7 GB|2|Temporaneo = 285 GB |8|8x500|
        |Standard\_A4\\ExtraLarge|8|14 GB|4|Temporaneo = 605 GB |16|16x500|
        |Standard\_A6|4|28 GB|2|Temporaneo = 285 GB |8|8x500|
        |Standard\_A7|8|56 GB|4|Temporaneo = 605 GB |16|16x500|


    - Livello Standard: serie D

        |Dimensione |Core CPU|Memoria|NIC (Max)|Dimensione disco max.|Dischi di dati max. (1023 GB ciascuno)|Max. IOPS (500 per disco)|
        |---|---|---|---|---|---|---|
        |Standard\_D3 |4|14 GB|4|Temporaneo (SSD) = 200 GB |8|8x500|
        |Standard\_D4 |8|28 GB|8|Temporaneo (SSD) = 400 GB |16|16x500|
        |Standard\_D12 |4|28 GB|4|Temporaneo (SSD) = 200 GB |8|8x500|
        |Standard\_D13 |8|56 GB|8|Temporaneo (SSD) = 400 GB |16|16x500|
        |Standard\_D14 |16|112 GB|8|Temporaneo (SSD) = 800 GB |32|32x500|

    - Livello standard: serie Dv2

        |Dimensione |Core CPU|Memoria|NIC (Max)|Dimensione disco max.|Dischi di dati max. (1023 GB ciascuno)|Max. IOPS (500 per disco)|
        |---|---|---|---|---|---|---|
        |Standard\_D3\_v2 |4|14 GB|4|Temporaneo (SSD) = 200 GB |8|8x500|
        |Standard\_D4\_v2 |8|28 GB|8|Temporaneo (SSD) = 400 GB |16|16x500|
        |Standard\_D12\_v2 |4|28 GB|4|Temporaneo (SSD) = 200 GB |8|8x500|
        |Standard\_D13\_v2 |8|56 GB|8|Temporaneo (SSD) = 400 GB |16|16x500|
        |Standard\_D14\_v2 |16|112 GB|8|Temporaneo (SSD) = 800 GB |32|32x500|    
 
    Per considerazioni sulla distribuzione da tenere presenti quando si prevede di usare queste risorse, vedere [Dimensioni delle macchine virtuali](../virtual-machines/virtual-machines-size-specs.md). Per informazioni sui prezzi delle varie dimensioni, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).
    
	> [AZURE.IMPORTANT] Se si prevedono più di 32 nodi di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM. La fatturazione inizia dopo la creazione del nodo e si interrompe solo quando il nodo viene eliminato. Per altre informazioni sui prezzi, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


## Usare risorse di archiviazione aggiuntive

In alcuni casi potrebbe essere necessario aggiungere altre risorse al cluster, ad esempio, se sono disponibili più account di archiviazione di Azure per diverse aree geografiche o per diversi servizi ma si vuole analizzare tutti gli account con HDInsight.

Per altre informazioni sull'uso degli archivi BLOB secondari, vedere [Uso dell'archivio BLOB di Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md). Per altre informazioni sull'uso degli archivi Data Lake secondari, vedere [Creare un cluster HDInsight con Archivio Data Lake tramite il portale di Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)


## Usare metastore Hive/Oozie

È consigliabile usare un metastore personalizzato per conservare le tabelle Hive dopo aver eliminato il cluster HDInsight, allo scopo di associare il metastore a un altro cluster HDInsight in futuro.

Il metastore contiene metadati Hive e Oozie, tra cui tabelle di Hive, partizioni, schemi e colonne. Consente di conservare i propri metadati Hive e Oozie, in modo che non sia necessario creare di nuovo tabelle di Hive o processi Oozie quando si crea un nuovo cluster. Per impostazione predefinita, Hive usa un database SQL di Azure incorporato per l'archiviazione di queste informazioni. Se il cluster viene eliminato, tuttavia, i metadati non vengono mantenuti nel database incorporato. Ad esempio, è disponibile un cluster creato con un metastore Hive. Sono state create alcune tabelle Hive. Dopo avere eliminato il cluster e averlo ricreato usando lo stesso metastore Hive, si potranno visualizzare le tabelle Hive create nel cluster originale.

> [AZURE.NOTE] La configurazione Metastore non è disponibile per i tipi di cluster HBase.

## Usare le reti virtuali di Azure

Una [rete virtuale di Azure](https://azure.microsoft.com/documentation/services/virtual-network/) consente di creare una rete sicura e persistente che contiene le risorse necessarie per la propria soluzione. Una rete virtuale consente di:

* Connettere le risorse cloud tra loro in una rete privata (solo cloud).

	![Diagramma di una configurazione solo cloud](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* Connettere le risorse cloud alla rete del data center locale (da sito a sito o da punto a sito) usando una rete privata virtuale (VPN).

    | Configurazione da sito a sito | Configurazione da punto a sito |
    | -------------------------- | --------------------------- |
    | La configurazione da sito a sito consente di connettere più risorse dal data center alla rete virtuale di Azure usando una rete VPN hardware o il servizio Routing e accesso remoto.<br />![Diagramma di una configurazione da sito a sito](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) | La configurazione da punto a sito consente di connettere una risorsa specifica alla rete virtuale di Azure usando una rete VPN software.<br />![Diagramma di una configurazione da punto a sito](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

Per altre informazioni sull'uso di HDInsight con una rete virtuale, inclusi i requisiti di configurazione specifici per la rete virtuale, vedere [Estendere le funzionalità di HDInsight usando Rete virtuale di Azure](hdinsight-extend-hadoop-virtual-network.md).

## Personalizzare i cluster mediante la personalizzazione dei cluster HDInsight (Bootstrap)

A volte potrebbe essere necessario modificare i file di configurazione:

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

Per mantenere le modifiche per tutta la durata del cluster, è possibile usare la funzionalità di personalizzazione dei cluster HDInsight durante il processo di creazione oppure usare Ambari nei cluster basati su Linux in modo sicuro. Per altre informazioni, vedere [Personalizzare cluster HDInsight tramite Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

>[AZURE.NOTE] I cluster basati su Windows non possono conservare le modifiche a causa del re-imaging. Per ulteriori informazioni, vedere [Riavvi delle istanze del ruolo dovuti ad aggiornamenti del sistema operativo](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Per mantenere le modifiche per tutta la durata dei cluster, è necessario usare la funzionalità di personalizzazione dei cluster HDInsight durante il processo di creazione.

## Personalizzare i cluster mediante l'azione script

L'uso di script durante la creazione consente di installare componenti aggiuntivi o personalizzare la configurazione di un cluster. Gli script vengono chiamati tramite un'**azione script**, ovvero un'opzione di configurazione che può essere usata dal portale, dai cmdlet di Windows PowerShell per HDInsight o da .NET SDK per HDInsight. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster-linux.md).

Nel cluster è possibile eseguire alcuni componenti Java nativi, come Mahout e Cascading, sotto forma di file JAR. Tali file JAR possono essere distribuiti nell'archivio BLOB di Azure e inviati ai cluster HDInsight usando i meccanismi di invio dei processi Hadoop. Per altre informazioni, vedere [Inviare processi Hadoop a livello di codice](hdinsight-submit-hadoop-jobs-programmatically.md).

>[AZURE.NOTE] In caso di problemi durante la distribuzione di file JAR in cluster HDInsight o la chiamata di file JAR in cluster HDInsight, contattare il [Supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).

> Cascading non è supportato da HDInsight, pertanto in caso di problemi non è possibile rivolgersi al Supporto Microsoft. Per gli elenchi dei componenti supportati, vedere [Novità delle versioni cluster incluse con HDInsight](hdinsight-component-versioning.md).

## Metodi di creazione di cluster

In questo articolo sono state fornite informazioni di base sulla creazione di un cluster HDInsight basato su Linux. Usare la tabella seguente per trovare informazioni specifiche su come creare un cluster usando un metodo ottimale per le proprie esigenze:

| Per creare cluster usare... | Usare un Web browser... | Usare la riga di comando | Usare l'API REST | Usare l'SDK | Da Linux, Mac OS X o Unix | Da Windows |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [Data factory di Azure](hdinsight-hadoop-create-linux-clusters-adf.md) | ✔ | ✔ | ✔ |✔ | ✔ | ✔ |
| [Interfaccia della riga di comando di Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |
| [Modelli di Gestione risorse di Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |

<!---HONumber=AcomDC_0413_2016-->