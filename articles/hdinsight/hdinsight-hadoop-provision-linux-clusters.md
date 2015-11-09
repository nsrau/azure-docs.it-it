<properties
   	pageTitle="Creare cluster Hadoop, HBase o Storm in Linux in HDInsight | Microsoft Azure"
   	description="Informazioni su come creare cluster Hadoop, HBase o Storm su Linux per HDInsight mediante un browser, l'interfaccia della riga di comando di Azure, Azure PowerShell, REST o un SDK."
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="10/23/2015"
   	ms.author="nitinme"/>


#Creare cluster basati su Linux in HDInsight

[AZURE.INCLUDE [selettore](../../includes/hdinsight-create-linux-cluster-selector.md)]

In questo documento sono disponibili informazioni sui diversi modi per creare un cluster HDInsight basato su Linux in Azure e sulle configurazioni facoltative che possono essere usate con il cluster. HDInsight offre Apache Hadoop, Apache Storm e Apache HBase come servizi nella piattaforma cloud di Azure.

> [AZURE.NOTE]Questo documento fornisce istruzioni sui diversi modi disponibili per creare un cluster. Per informazioni su un approccio rapido alla creazione di un cluster, vedere [Introduzione ad Azure HDInsight su Linux](../hdinsight-hadoop-linux-get-started.md).

## Informazioni sui cluster HDInsight

Un cluster Hadoop è costituito da alcune macchine virtuali (nodi) che vengono usate per l'elaborazione distribuita di attività sul cluster. Azure consente di astrarre i dettagli di implementazione dell'installazione e della configurazione dei singoli nodi. È quindi necessario specificare solo le informazioni di configurazione generali.

###Tipi di cluster

Sono disponibili diversi tipi di cluster HDInsight:

| Tipo di cluster | Usare questo per... |
| ------------ | ----------------------------- |
| Hadoop | Query e analisi (processi batch) |
| HBase | Archiviazione di dati NoSQL |
| Storm | Elaborazione di eventi in tempo reale |
| Spark (anteprima solo Windows) | Elaborazione in memoria, query interattive, elaborazione di flussi di micro batch |

Durante la configurazione sarà possibile selezionare uno di questi tipi di cluster. È possibile aggiungere altre tecnologie, ad esempio Hue, Spark o R, a questi tipi di base mediante [Azioni script](#scriptaction).

Ogni tipo di cluster usa una terminologia specifica per i nodi nel cluster, oltre a un numero di nodi specifico e a dimensioni predefinite per le VM per ogni tipo di nodo:

> [AZURE.IMPORTANT]Se si prevedono più di 32 nodi di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM.

![Nodi del cluster HDInsight Hadoop](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

I cluster Hadoop per HDInsight hanno due tipi di nodi:

- Nodo head (2 nodi)
- Nodo dati (almeno 1 nodo)

![Nodi del cluster HDInsight HBase](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

I cluster HBase per HDInsight hanno tre tipi di nodi: - Server head (2 nodi) - Server area (almeno 1 nodo) - Nodi master/Zookeeper (3 nodi)

![Nodi del cluster HDInsight Storm](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

I cluster Storm per HDInsight hanno tre tipi di nodi: - Nodi Nimbus (2 nodi) - Server supervisori (almeno 1 nodo) - Nodi Zookeeper (3 nodi)


![Nodi del cluster HDInsight Spark](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

I cluster Spark per HDInsight hanno tre tipi di nodi: - Nodo Head (2 nodi) - Nodo di lavoro (almeno 1 nodo) - Nodi Zookeeper (3 nodi) (gratuiti per VM Zookeeper di dimensioni A1)

###Archiviazione di Azure per HDInsight

Ogni tipo di cluster sarà anche associato a uno o più account di archiviazione di Azure. HDInsight usa i BLOB di Azure di questi account di archiviazione come archivio di dati per il cluster. Mantenere i dati separati dal cluster consente di eliminare i cluster quando non sono in uso, conservando comunque i dati. Sarà quindi possibile usare lo stesso account di archiviazione per un nuovo cluster, se è necessario eseguire altre analisi. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight](../hdinsight-use-blob-storage.md).

## <a id="configuration"></a>Opzioni di configurazione di base

Le sezioni seguenti illustrano le opzioni di configurazione disponibili quando si crea un cluster HDInsight.

###Nome del cluster

Il nome del cluster fornisce un identificatore univoco per il cluster e viene usato come parte del nome di dominio per l'accesso al cluster tramite Internet. Ad esempio, un cluster denominato _mycluster_ sarà disponibile su Internet come _mycluster_.azurehdinsight.net.

Il nome del cluster deve rispettare le linee guida seguenti:

- Il campo deve contenere una stringa di lunghezza compresa tra 3 e 63 caratteri.
- Il campo può contenere solo lettere, numeri e trattini.

###Tipo di cluster

Il tipo di cluster consente di selezionare configurazioni con finalità specifiche per il cluster. Ecco i tipi disponibili per HDInsight basato su Linux:

| Tipo di cluster | Usare questo per... |
| ------------ | ----------------------------- |
| Hadoop | Query e analisi (processi batch) |
| HBase | Archiviazione di dati NoSQL |
| Storm | Elaborazione di eventi in tempo reale |
| Spark (anteprima solo Windows) | Elaborazione in memoria, query interattive, elaborazione di flussi di micro batch |

È possibile aggiungere altre tecnologie, ad esempio Hue, Spark o R, a questi tipi di base mediante [Azioni script](#scriptaction).

###Sistema operativo cluster

È possibile eseguire il provisioning di cluster HDInsight in uno dei due sistemi operativi seguenti:

- **HDInsight su Windows (Windows Server 2012 R2 Datacenter)**: selezionare questa opzione se è necessaria l'integrazione con tecnologie e servizi basati su Windows che saranno eseguiti sul cluster con Hadoop o se si esegue la migrazione da una distribuzione di Hadoop esistente basata su Windows.

- **HDInsight su Linux (Ubuntu 12.04 LTS per Linux)**: selezionare questa opzione se si ha familiarità con Linux o Unix, si esegue la migrazione da una soluzione Hadoop basata su Linux esistente o si vuole un metodo facile per l'integrazione con i componenti dell'ecosistema Hadoop sviluppati per Linux. Per altre informazioni, vedere l'articolo di [introduzione all'uso di Hadoop su Linux in HDInsight](hdinsight-hadoop-linux-get-started.md).

> [AZURE.NOTE]Le informazioni in questo documento presuppongono che si usi un cluster HDInsight basato su Linux. Per informazioni specifiche per i cluster basati su Windows, vedere [Creare cluster Hadoop basati su Windows in HDInsight](hdinsight-provision-clusters.md).

###Nome sottoscrizione

Se sono disponibili più sottoscrizioni di Azure, usare questa opzione per selezionare la sottoscrizione da usare quando si crea il cluster HDInsight.

###Gruppo di risorse

Le applicazioni sono in genere costituite da molti componenti, ad esempio app Web, database, server di database, risorsa di archiviazione e servizi di terze parti. Gestione risorse di Azure (ARM) consente di usare le risorse dell'applicazione come gruppo, detto Gruppo di risorse di Azure. È quindi possibile distribuire, aggiornare, monitorare o eliminare tutte le risorse per l'applicazione con una singola operazione coordinata. È possibile descrivere le risorse del gruppo in un modello JSON per la distribuzione e quindi usare tale modello per ambienti diversi, ad esempio di testing, staging e produzione. È possibile chiarire la fatturazione per l'organizzazione visualizzando i costi per l'intero gruppo. Per altre informazioni, vedere [Panoramica di Gestione risorse di Azure](resource-group-overview.md).

###Credenziali

È possibile usare due tipi di autenticazione con i cluster HDInsight:

* Utente __Admin__ o __HTTPS__: l'account admin per un cluster viene usato principalmente quando si accede a servizi Web o REST esposti dal cluster. Non è possibile usarlo per l'accesso diretto al cluster.

* __Nome utente SSH__: un account utente SSH viene usato per l'accesso remoto al cluster mediante un client [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell). Viene usato spesso per fornire un accesso remoto tramite riga di comando ai nodi head del cluster.

L'account admin è protetto da una password e tutte le comunicazioni Web per il cluster tramite l'account admin devono essere eseguite mediante una connessione HTTPS sicura.

L'utente SSH può essere autenticato mediante una password o un certificato. L'autenticazione tramite certificato rappresenta l'opzione più sicura, ma richiede la creazione e la gestione di una coppia di certificati pubblici e privati.

Per altre informazioni sull'uso di SSH con HDInsight, incluse informazioni su come creare e usare le chiavi SSH, vedere uno degli articoli seguenti:

* [Per client Linux, Unix o Mac OS X - Uso di SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Per client Windows - HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md)

###Origine dati

HDInsight usa l'archivio BLOB di Azure come archivio sottostante per il cluster. Internamente, Hadoop e altro software nel cluster vedono questo archivio come un sistema compatibile con HDFS (Hadoop Distributed File System).

Quando si crea un nuovo cluster, è necessario creare un nuovo account di archiviazione di Azure o usarne uno esistente.

> [AZURE.IMPORTANT]La posizione geografica selezionata per l'account di archiviazione determinerà la posizione del cluster HDInsight, perché il cluster deve trovarsi nello stesso data center dell'account di archiviazione predefinito.
>
> Per un elenco di aree supportate, fare clic sull'elenco a discesa **Area** in [Prezzi di HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

####Contenitore di archiviazione predefinito

HDInsight creerà anche un _contenitore di archiviazione predefinito_ nell'account di archiviazione. Si tratta dell'archivio predefinito per il cluster HDInsight.

Per impostazione predefinita, questo contenitore ha lo stesso nome del cluster. Per altre informazioni sul funzionamento di HDInsight con l'archivio BLOB di Azure, vedere [Usare l'archiviazione BLOB di Azure compatibile con HDFS con Hadoop in HDInsight](hdinsight-hadoop-use-blob-storage.md).

>[AZURE.WARNING]Non condividere un contenitore tra più cluster. Questa operazione non è supportata.

###Dimensioni nodo

È possibile selezionare le dimensioni delle risorse di calcolo usate dal cluster. Ad esempio, se si prevede di eseguire operazioni che richiedono una quantità elevata di memoria, è consigliabile selezionare una risorsa di calcolo con più memoria.

Diversi tipi di cluster hanno diversi tipi, numeri e dimensioni di nodi. Ad esempio, un cluster Hadoop ha due _nodi head_ e, per impostazione predefinita, quattro _nodi dati_, mentre un cluster di tipo Storm ha due _nodi Nimbus_, tre _nodi Zookeeper_ e, per impostazione predefinita, quattro _nodi supervisore_.

> [AZURE.IMPORTANT]Se si prevedono più di 32 nodi di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM.

Quando si usa il portale di anteprima di Azure per configurare il cluster, le dimensioni del nodo vengono esposte tramite il pannello __Piano tariffario per il nodo__ e verrà visualizzato anche il costo associato alle diverse dimensioni di nodo.

> [AZURE.IMPORTANT]La fatturazione inizia dopo la creazione del nodo e si interrompe solo quando il nodo viene eliminato. Per altre informazioni sui prezzi, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a id="optionalconfiguration"></a>Configurazione facoltativa

Le sezioni seguenti descrivono opzioni di configurazione facoltative, oltre a scenari che richiederebbero queste configurazioni.

### Versione HDInsight

Consente di determinare la versione di HDInsight usata per questo cluster. Per altre informazioni, vedere [Versioni di HDInsight e componenti di Hadoop](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)

### Usare le reti virtuali di Azure

Una [rete virtuale di Azure](http://azure.microsoft.com/documentation/services/virtual-network/) consente di creare una rete sicura e persistente che contiene le risorse necessarie per la propria soluzione. Una rete virtuale consente di:

* Connettere le risorse cloud tra loro in una rete privata (solo cloud).

	![Diagramma di una configurazione solo cloud](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* Connettere le risorse cloud alla rete del data center locale (da sito a sito o da punto a sito) usando una rete privata virtuale (VPN).

    | Configurazione da sito a sito | Configurazione da punto a sito |
    | -------------------------- | --------------------------- |
    | La configurazione da sito a sito consente di connettere più risorse dal data center alla rete virtuale di Azure usando una rete VPN hardware o il servizio Routing e accesso remoto.<br />![Diagramma di una configurazione da sito a sito](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) | La configurazione da punto a sito consente di connettere una risorsa specifica alla rete virtuale di Azure usando una rete VPN software.<br />![Diagramma di una configurazione da punto a sito](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

Per altre informazioni sulle funzionalità, i vantaggi e le capacità della rete virtuale, vedere la [panoramica della rete virtuale di Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE]È necessario creare la rete virtuale di Azure prima di creare un cluster. Per alte informazioni, vedere [Come creare una rete virtuale](virtual-networks-create-vnet.md).
>
> Azure HDInsight supporta solo reti virtuali basate sulla posizione e attualmente non funziona con le reti virtuali basate su set di affinità. Usare il cmdlet Get-AzureVNetConfig di Azure PowerShell per verificare se una rete virtuale esistente di Azure è basata sulla posizione. Se la rete virtuale non è basata sulla posizione, saranno disponibili le opzioni seguenti:
>
> - Esportare la configurazione di rete virtuale esistente, quindi creare una nuova rete virtuale. Per impostazione predefinita, tutte le nuove reti virtuali sono basate sulla posizione.
> - Eseguire la migrazione a una rete virtuale basata sulla posizione. Vedere la pagina relativa alla [migrazione di servizi esistenti a un ambito a livello di area](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/).
>
> È consigliabile designare una singola subnet per un cluster.
>
> Attualmente (25/08/2015) è possibile creare solo cluster basati su Linux in una rete virtuale di Azure.
>
> Non è possibile usare una rete virtuale di Azure v1 (classica) con HDInsight basato su Linux. La rete virtuale deve essere v2 (Gestione risorse di Azure) per poter essere elencata come opzione durante il processo di creazione di cluster HDInsight nel portale di anteprima di Azure o possa essere usata durante la creazione di un cluster mediante l'interfaccia della riga di comando di Azure o Azure PowerShell.
>
> Se si dispone di risorse in una rete v1 e si desidera rendere HDInsight direttamente accessibile a tali risorse attraverso una rete virtuale, vedere [Connessione di reti virtuali classiche a nuove reti virtuali](../virtual-network/virtual-networks-arm-asm-s2s.md) per informazioni su come connettersi una rete virtuale v2 a una rete virtuale v1. Una volta stabilita la connessione, è possibile creare il cluster HDInsight nella rete virtuale v2.

### Metastore

Il metastore contiene metadati Hive e Oozie, ad esempio informazioni su tabelle di Hive, partizioni, schemi e colonne. Consente di conservare i propri metadati Hive e Oozie, in modo che non sia necessario creare di nuovo tabelle di Hive o processi Oozie quando si crea un nuovo cluster.

L'uso dell'opzione di configurazione Metastore consente di specificare un metastore esterno mediante il database SQL. Ciò consente di conservare le informazioni dei metadati quando si elimina un cluster, perché vengono archiviate esternamente nel database. Per istruzioni su come creare un database SQL in Azure, vedere [Creare il primo Database di SQL Azure](sql-database-get-started.md).

###<a id="scriptaction"></a>Azione script

L'uso di script durante il provisioning del cluster consente di installare componenti aggiuntivi o personalizzare la configurazione di un cluster. Questi script vengono richiamati mediante **Azione script**. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster-linux.md).

> [AZURE.IMPORTANT]Non è possibile aggiungere altri componenti dopo la creazione di un cluster, poiché tali componenti non saranno disponibili dopo che è stata ricreata l'immagine di un nodo del cluster. I componenti installati tramite azioni script vengono reinstallati come parte del processo rimanente.

### Risorse di archiviazione aggiuntive

In alcuni casi potrebbe essere necessario aggiungere altre risorse al cluster, ad esempio, se sono disponibili più account di archiviazione di Azure per diverse aree geografiche o per diversi servizi ma si vuole analizzare tutti gli account con HDInsight.

Per altre informazioni sull'uso degli archivi BLOB secondari, vedere [Uso dell'archivio BLOB di Azure con HDInsight](../hdinsight-use-blob-storage.md).

##<a id="nextsteps"></a><a id="options"></a> Metodi di creazione

In questo articolo sono state fornite informazioni di base sulla creazione di un cluster HDInsight basato su Linux. Usare la tabella seguente per trovare informazioni specifiche su come creare un cluster usando un metodo ottimale per le proprie esigenze:

| Usare questo per creare un cluster... | Con un Web browser... | Con una riga di comando | Con un'API REST | Con un SDK | Da Linux, Mac OS X o Unix | Da Windows |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Portale di anteprima di Azure](hdinsight-hadoop-create-linux-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [Interfaccia della riga di comando di Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |



[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-create-storageaccount]: ../storage-create-storage-account/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[azure-preview-portal]: https://portal.azure.com


[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "Elenco e visualizzazione di cluster"

[image-hdi-ps-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.provision.png
[image-hdi-ps-config-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.config.provision.png

[img-hdi-cluster]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.png

  [89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Usare Sqoop con HDInsight"

<!---HONumber=Nov15_HO1-->