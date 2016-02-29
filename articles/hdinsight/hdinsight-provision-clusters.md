<properties
   pageTitle="Creare cluster Hadoop basati su Windows in HDInsight | Microsoft Azure"
   	description="Informazioni su come creare cluster per Azure HDInsight."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/12/2016"
   ms.author="jgao"/>

# Creare cluster Hadoop basati su Windows in HDInsight

[AZURE.INCLUDE [selettore](../../includes/hdinsight-create-windows-cluster-selector.md)]

Informazioni su come pianificare la creazione di cluster HDInsight.

###Prerequisiti:

Per poter eseguire le istruzioni descritte nell'articolo è necessario disporre di:

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


## Opzioni di configurazione di base

Di seguito sono riportate le opzioni di configurazione di base per la creazione di un cluster HDInsight.

- **Nome cluster**

	Il campo Nome cluster consente di identificare un cluster. Il nome del cluster deve rispettare le linee guida seguenti:

	- Il campo deve essere una stringa contenente da 3 a 63 caratteri.
	- Il campo può contenere solo lettere, numeri e trattini.

- **Nome sottoscrizione**

	Un cluster HDInsight è associato a una sottoscrizione di Azure.

- **Nome del gruppo di risorse**

	Gestione risorse di Azure (ARM) consente di usare le risorse dell'applicazione come gruppo, detto Gruppo di risorse di Azure. È quindi possibile distribuire, aggiornare, monitorare o eliminare tutte le risorse per l'applicazione con una singola operazione coordinata. Per altre informazioni, vedere [Panoramica di Gestione risorse di Azure](resource-group-overview.md).
	
- **Sistema operativo**

	È possibile creare cluster HDInsight in uno dei due sistemi operativi seguenti: - **HDInsight su Windows (Windows Server 2012 R2 Datacenter)**: - **HDInsight su Linux (Ubuntu 12.04 LTS per Linux)**: HDInsight consente di configurare cluster Linux in Azure. Se si ha familiarità con Linux o Unix, è infatti possibile configurare un cluster Linux eseguendo la migrazione da una soluzione Hadoop basata su Linux esistente oppure è possibile scegliere di integrarsi facilmente con i componenti dell'ecosistema Hadoop sviluppati per Linux. Per altre informazioni, vedere l'articolo di [introduzione all'uso di Hadoop su Linux in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

- **Tipo di cluster** e **Dimensioni del cluster (definiti anche nodi di dati)**

	HDInsight consente ai clienti di distribuire svariati tipi di cluster, per diversi carichi di lavoro di analisi di dati. Sono attualmente disponibili i tipi di cluster seguenti:

	- Cluster Hadoop: per carichi di lavoro di query e analisi
	- Cluster HBase: per carichi di lavoro NoSQL
	- Cluster Storm: per carichi di lavoro di elaborazione di eventi in tempo reale
	- Cluster Spark: per carichi di lavoro di elaborazione in memoria, query interattive, streaming e Machine Learning.

	![Cluster HDInsight](./media/hdinsight-provision-clusters/hdinsight.clusters.png)

	> [AZURE.NOTE] Il *Cluster HDInsight di Azure* è definito anche *Cluster Hadoop in HDInsight* o *Cluster HDInsight*. In alcuni casi viene usato in modo intercambiabile con *Cluster Hadoop*. Tutte queste definizioni fanno riferimento ai cluster Hadoop ospitati nell'ambiente Microsoft Azure.

	Un determinato tipo di cluster include ruoli diversi per i diversi nodi, che consentono a un cliente di ridimensionare i nodi in un ruolo specifico in modo appropriato per i dettagli del carico di lavoro. Ad esempio, è possibile creare i nodi di lavoro di un cluster Hadoop con una quantità di memoria elevata, se il tipo di analisi da eseguire richiede un uso intensivo della memoria.

	![Ruoli dei cluster HDInsight Hadoop](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

	I cluster Hadoop per HDInsight vengono distribuiti con due ruoli:

	- Nodo head (2 nodi)
	- Nodo dati (almeno 1 nodo)

	![Ruoli dei cluster HDInsight Hadoop](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

	I cluster HBase per HDInsight vengono distribuiti con tre ruoli: - Server head (2 nodi) - Server area (almeno 1 nodo) - Nodi master/Zookeeper (3 nodi)

	![Ruoli dei cluster HDInsight Hadoop](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

	I cluster Storm per HDInsight vengono distribuiti con tre ruoli: - Nodi Nimbus (2 nodi) - Server Supervisor (almeno 1 nodo) - Nodi Zookeeper (3 nodi)


	![Ruoli dei cluster HDInsight Hadoop](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

	I cluster Spark per HDInsight vengono distribuiti con tre ruoli: - Nodo head (2 nodi) - Nodo di lavoro (almeno 1 nodo) - Nodi Zookeeper (3 nodi) (gratuiti per Zookeeper A1)

	L'uso di questi nodi viene addebitato ai clienti per la durata del ciclo di vita del cluster. La fatturazione inizia dopo la creazione di un cluster e si interrompe quando il cluster viene eliminato. Non è possibile deallocare o mettere in attesa i cluster. Le dimensioni del cluster influiscono sul prezzo del cluster. Ai fini dell'apprendimento, è consigliabile usare un nodo di dati. Per altre informazioni sui prezzi di HDInsight, vedere [Prezzi di HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).


	>[AZURE.NOTE] Il limite relativo alle dimensioni del cluster dipende dalla sottoscrizione di Azure. Per aumentare il limite, contattare il team del supporto fatturazione.

- **Versione HDInsight**

	Consente di determinare la versione di HDInsight da usare per questo cluster. Per altre informazioni, vedere [Versioni del cluster e componenti di Hadoop in HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)


- **Località (Area)**

	Il cluster HDInsight e l'account di archiviazione predefinito devono trovarsi nella stessa località di Azure.
	
	![Aree di Azure](./media/hdinsight-provision-clusters/Azure.regions.png)

	Per un elenco di aree supportate, fare clic sull'elenco a discesa **Area** in [Prezzi di HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

- **Dimensioni nodo**

	![Dimensioni dei nodi delle VM di HDInsight](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

	Selezionare le dimensioni delle VM per i nodi. Per altre informazioni, vedere [Dimensioni dei servizi cloud](cloud-services-sizes-specs.md). È possibile selezionare le dimensioni delle risorse di calcolo usate dal cluster. Ad esempio, se si prevede di eseguire operazioni che richiedono una quantità elevata di memoria, è consigliabile selezionare una risorsa di calcolo con più memoria.

	>[AZURE.NOTE] I nodi usati dal cluster non contano come macchine virtuali, in quanto le immagini di macchine virtuali usate per i nodi sono un dettaglio di implementazione del servizio HDInsight. Tuttavia, i core di calcolo usati dai nodi influiscono sul numero totale di core di calcolo disponibili per la sottoscrizione. Durante la creazione di un cluster HDInsight è possibile visualizzare il numero di core che verranno usati dal cluster, nonché il numero di core disponibili, nella sezione di riepilogo del pannello Piani tariffari per il nodo.

	Diversi tipi di cluster hanno diversi tipi, numeri e dimensioni di nodi. Ad esempio, un cluster Hadoop ha due _nodi head_ e, per impostazione predefinita, quattro _nodi dati_, mentre un cluster di tipo Storm ha due _nodi Nimbus_, tre _nodi Zookeeper_ e, per impostazione predefinita, quattro _nodi supervisore_.

	> [AZURE.IMPORTANT] Se si prevedono più di 32 nodi di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM.

	Quando si usa il portale di Azure per configurare il cluster, le dimensioni del nodo vengono esposte tramite il pannello __Piano tariffario per il nodo__ e verrà visualizzato anche il costo associato alle diverse dimensioni di nodo.

	> [AZURE.IMPORTANT] La fatturazione inizia dopo la creazione del nodo e si interrompe solo quando il nodo viene eliminato. Per altre informazioni sui prezzi, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


- **Utenti di HDInsight**

	I cluster HDInsight consentono di configurare due account utente durante il provisioning:

	- Utente HTTP. Il nome utente predefinito è admin con la configurazione di base nel portale di Azure.
	- Utente RDP (cluster Windows): usato per la connessione al cluster tramite RDP. Quando si crea l'account, è necessario impostare una scadenza corrispondente a una data entro 90 giorni dalla data odierna.
	- Utente SSH (cluster Linux): usato per la connessione ai cluster tramite SSH. È possibile creare account utente SSH aggiuntivi dopo la creazione del cluster tramite l'esecuzione dei passaggi illustrati in [Uso di SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md).



- **Account di archiviazione di Azure**

	L'interfaccia HDFS originale usa molti dischi locali sul cluster. HDInsight usa invece l'archivio BLOB di Azure per l'archiviazione dei dati. L'archiviazione BLOB di Azure è una soluzione di archiviazione affidabile, con finalità generali che si integra facilmente con HDInsight. Grazie a un'interfaccia HDFS (Hadoop Distributed File System), tutti i componenti disponibili in HDInsight possono agire direttamente sui dati strutturati o non strutturati presenti nell'archiviazione BLOB. L'archiviazione dei dati nell'archiviazione BLOB consente l'eliminazione sicura dei cluster HDInsight usati per i calcoli, senza perdita di dati utente.

	Durante la configurazione è necessario specificare un account di archiviazione di Azure e un contenitore di archiviazione BLOB di Azure nell'account di archiviazione di Azure. Alcuni processi di creazione richiedono prima di tutto la creazione dell'account di archiviazione di Azure e del contenitore di archiviazione BLOB. Il contenitore di archiviazione BLOB viene usato dal cluster come posizione di archiviazione predefinita. Facoltativamente, è possibile specificare account di archiviazione di Azure aggiuntivi (account di archiviazione collegati) a cui il cluster potrà accedere. Il cluster può accedere anche a eventuali contenitori BLOB configurati con accesso in lettura pubblico completo o accesso in lettura pubblico solo per i BLOB. Per altre informazioni sull'accesso con limitazioni, vedere [Gestire l'accesso alle risorse di archiviazione di Azure](storage-manage-access-to-resources.md).

	![Archiviazione di HDInsight](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE] Un contenitore di archiviazione BLOB offre un raggruppamento di un set di BLOB, come illustrato nell'immagine:

	![Archivio BLOB di Azure](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)


	>[AZURE.WARNING] Non condividere un contenitore di archiviazione BLOB tra più cluster. Questa operazione non è supportata.

	Per altre informazioni sull'uso degli archivi BLOB secondari, vedere [Uso dell'archiviazione BLOB di Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).

- **Metastore Hive/Oozie**

	Il metastore contiene metadati Hive e Oozie, tra cui tabelle di Hive, partizioni, schemi e colonne. Consente di conservare i propri metadati Hive e Oozie, in modo che non sia necessario creare di nuovo tabelle di Hive o processi Oozie quando si crea un nuovo cluster. Per impostazione predefinita, Hive usa un database SQL di Azure incorporato per l'archiviazione di queste informazioni. Se il cluster viene eliminato, tuttavia, i metadati non vengono mantenuti nel database incorporato. Ad esempio, è disponibile un cluster creato con un metastore Hive. Sono state create alcune tabelle Hive. Dopo avere eliminato il cluster e ricreato il cluster mediante lo stesso metastore Hive, sarà possibile visualizzare le tabelle Hive create nel cluster originale.
    
    > [AZURE.NOTE] La configurazione Metastore non è disponibile per i tipi di cluster HBase.

## Personalizzare i cluster mediante la personalizzazione dei cluster HDInsight (Bootstrap)

A volte potrebbe essere necessario modificare i file di configurazione:

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

I cluster non possono conservare le modifiche a causa del re-imaging. Per ulteriori informazioni, vedere [Riavvi delle istanze del ruolo dovuti ad aggiornamenti del sistema operativo](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Per mantenere le modifiche per l'intero ciclo di vita dei cluster, è possibile usare la personalizzazione dei cluster HDInsight durante il processo di creazione. Questo è il metodo consigliato per modificare le configurazioni di un cluster e mantenere questi eventi di riavvio Azure per la ricreazione dell'immagine. Queste modifiche alla configurazione vengono applicate prima dell'avvio del servizio, pertanto non sarà necessario riavviare i servizi.

Per altri esempi, vedere [Personalizzare cluster HDInsight tramite Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

## Personalizzare i cluster mediante Azione di script

L'uso di script durante la creazione consente di installare componenti aggiuntivi o personalizzare la configurazione di un cluster. Gli script vengono chiamati tramite un'**azione script**, ovvero un'opzione di configurazione che può essere usata dal portale, dai cmdlet di Windows PowerShell per HDInsight o da .NET SDK per HDInsight. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster.md).


## Usare le reti virtuali di Azure

[Rete virtuale di Azure](https://azure.microsoft.com/documentation/services/virtual-network/) permette di creare una rete sicura e persistente che contiene le risorse necessarie per la propria soluzione. Una rete virtuale consente di:

* Connettere le risorse cloud tra loro in una rete privata (solo cloud).

	![Diagramma di una configurazione solo cloud](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* Connettere le risorse cloud alla rete del data center locale (da sito a sito o da punto a sito) usando una rete privata virtuale (VPN).

	La configurazione da sito a sito consente di connettere più risorse dal data center alla rete virtuale di Azure usando una rete VPN hardware o il servizio Routing e accesso remoto.

	![Diagramma di una configurazione da sito a sito](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	La configurazione da punto a sito consente di connettere una risorsa specifica alla rete virtuale di Azure usando una rete VPN software.

	![Diagramma di una configurazione da punto a sito](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

Per informazioni sull'uso di HDInsight con una rete virtuale, inclusi i requisiti di configurazione specifici per la rete virtuale, vedere [Estendere le funzionalità di HDInsight usando Rete virtuale di Azure](hdinsight-extend-hadoop-virtual-network.md).

## Metodi di creazione di cluster

In questo articolo si sono apprese informazioni di base sulla creazione di un cluster HDInsight basato su Windows. Usare la tabella seguente per trovare informazioni specifiche su come creare un cluster usando un metodo ottimale per le proprie esigenze:

| Usare questo per creare un cluster... | Con un Web browser... | Con una riga di comando | Con un'API REST | Con un SDK | Da Linux, Mac OS X o Unix | Da Windows |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Portale di Azure](hdinsight-hadoop-create-windows-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [Interfaccia della riga di comando di Azure](hdinsight-hadoop-create-windows-clusters-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure PowerShell](hdinsight-hadoop-create-windows-clusters-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [.NET SDK](hdinsight-hadoop-create-windows-clusters-dotnet-sdk.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |
| [Modelli di Gestione risorse di Azure](hdinsight-hadoop-create-windows-clusters-arm-templates.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |

<!---HONumber=AcomDC_0218_2016-->