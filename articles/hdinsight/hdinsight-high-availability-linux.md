<properties
	pageTitle="Funzionalità a disponibilità elevata di HDInsight (Hadoop) basati su Linux | Microsoft Azure"
	description="Informazioni su come i cluster HDInsight basati su Linux migliorano l'affidabilità e la disponibilità tramite l’utilizzo di un nodo head aggiuntivo. Informazioni su come questo influisce sui servizi di Hadoop come Ambari e Hive, e anche su come connettersi singolarmente a ogni nodo head utilizzando SSH."
	services="hdinsight"
	editor="cgronlun"
	manager="jhubbard"
	authors="Blackmist"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/05/2016"
	ms.author="larryfr"/>

#Disponibilità e affidabilità dei cluster Hadoop in HDInsight

Hadoop ottiene disponibilità e l'affidabilità elevate distribuendo copie ridondanti di servizi e dati tra i nodi di un cluster. Tuttavia le distribuzioni standard di Hadoop hanno in genere un singolo nodo head. Eventuali interruzioni del singolo nodo head possono causare l'interruzione del funzionamento del cluster.

Per risolvere questo problema, i cluster HDInsight basati su Linux in Azure forniscono due nodi head per aumentare la disponibilità e l'affidabilità dei servizi Hadoop e dei processi in esecuzione.

> [AZURE.NOTE] I passaggi descritti in questo documento sono specifici per i cluster HDInsight basati su Linux. Se si utilizza un cluster basato su Windows, vedere [Disponibilità e affidabilità dei cluster Hadoop basati su Windows in HDInsight](hdinsight-high-availability.md) per informazioni specifiche su Windows.

##Informazioni sui nodi

I nodi in un cluster HDInsight vengono implementati con macchine virtuali di Azure. Nel caso di errore di un nodo, questo viene portato offline e viene creato un nuovo nodo per sostituire il nodo che non funziona. Mentre il nodo è offline, viene usato un altro nodo dello stesso tipo fino a quando il nuovo nodo viene portato online.

> [AZURE.NOTE] Se il nodo sta analizzando dati quando si verifica l'errore, l'avanzamento dell'analisi nel processo viene perso. Il processo che usava il nodo quando si è verificato l'errore verrà inviato di nuovo a un altro nodo.

Le sezioni seguenti illustrano i singoli tipi di nodo usati con HDInsight. Non tutti i tipi di nodo vengono usati per un tipo di cluster. Ad esempio, un tipo di cluster Hadoop non avrà alcun nodo Nimbus. Per altre informazioni sui nodi usati dai tipi di cluster HDInsight, vedere la sezione Tipi di cluster in [Creare cluster Hadoop basati su Linux in HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

###Nodi head

Alcune implementazioni di Hadoop hanno un solo nodo head che ospita i servizi e i componenti che gestiscono gli errori dei nodi di lavoro senza problemi. Tuttavia, eventuali interruzioni dei servizi master in esecuzione nel nodo head causerebbero l’interruzione del funzionamento del cluster.

I cluster HDInsight forniscono un nodo head secondario, che consente ai servizi master e ai componenti di continuare a funzionare nel nodo secondario in caso di errore nel server primario.

> [AZURE.IMPORTANT] Entrambi i nodi head sono contemporaneamente attivi e in esecuzione all'interno del cluster. Alcuni servizi, ad esempio HDFS o YARN, sono 'attivi' soltanto in un nodo head (e in ‘standby’ nell'altro nodo head). Altri servizi come HiveServer2 o Hive Metastore sono attivi su entrambi i nodi head allo tesso tempo.

###Nodi Nimbus

Per i cluster Storm, i nodi Nimbus forniscono una funzionalità simile a JobTracker di Hadoop mediante la distribuzione e il monitoraggio dell'elaborazione tra nodi di lavoro. HDInsight offre 2 nodi Nimbus per il tipo di cluster Storm.

###Nodi Zookeeper

I nodi [ZooKeeper](http://zookeeper.apache.org/) (ZKs) vengono utilizzati per l’elezione del leader dei servizi master nei nodi head e per garantire che i servizi, i nodi di dati (ruolo di lavoro) e i gateway sappiano su quale nodo head è attivo un servizio master. Per impostazione predefinita, HDInsight fornisce 3 nodi ZooKeeper.

###Nodi di lavoro

I nodi di lavoro eseguono l'analisi dei dati effettivi quando un processo viene inviato al cluster. In caso di errore di un nodo di lavoro, l'attività che stava eseguendo verrà inviata a un altro nodo di lavoro. Per impostazione predefinita, HDInsight crea 4 nodi di lavoro. Tuttavia, è possibile modificare questo numero in base alle esigenze durante e dopo la creazione del cluster.

###Nodo perimetrale

Un nodo perimetrale non partecipa attivamente all'analisi dei dati all'interno del cluster, ma viene invece usato dagli sviluppatori o dai data scientist quando utilizzano Hadoop. Il nodo perimetrale si trova nella stessa rete virtuale di Azure come gli altri nodi del cluster e può accedere direttamente a tutti gli altri nodi. Poiché non è coinvolto nell'analisi dei dati per il cluster, può essere usato senza alcun preoccuparsi di sottrarre risorse ai servizi critici di Hadoop o ai processi di analisi.

Attualmente, Server R in HDInsight è l'unico tipo di cluster che fornisce un nodo perimetrale per impostazione predefinita. Per Server R in HDInsight, il nodo perimetrale viene usato per testare il codice R in locale nel nodo prima dell'invio al cluster per l'elaborazione distribuita.

[Creare un cluster HDInsight basato su Linux con Hue in un nodo perimetrale](https://azure.microsoft.com/documentation/templates/hdinsight-linux-with-hue-on-edge-node/) include un modello di esempio che può essere usato per creare un tipo di cluster Hadoop con un nodo perimetrale.


## Accesso ai nodi head

L'accesso al cluster tramite Internet viene fornito tramite un gateway pubblico ed è limitato alla connessione ai nodi head e, in presenza di un Server di R in cluster HDInsight, al nodo perimetrale. La presenta di più nodi head non influisce sull'accesso ai servizi in esecuzione nei nodi head, perché il gateway pubblico instrada le richieste al nodo head che ospita il servizio richiesto. Ad esempio, se Ambari è attualmente ospitato nel nodo head, 1, il gateway indirizza le richieste in ingresso per Ambari a quel nodo.

Se l'accesso al cluster avviene tramite SSH e la connessione avviene tramite la porta 22 (impostazione predefinita per SSH,) si stabilirà una connessione con il nodo head 0; se la connessione avviene tramite la porta 23 si stabilirà, invece, una connessione con il nodo head 1. Ad esempio, `ssh username@mycluster-ssh.azurehdinsight.net` si connetterà al nodo head 0 del cluster denominato __mycluster__.

> [AZURE.NOTE] Questo vale anche per protocolli basati su SSH, ad esempio SFTP (SSH File Transfer Protocol).

Al nodo perimetrale fornito con Server R nei cluster HDInsight è anche possibile accedere direttamente usando SSH tramite la porta 22. Ad esempio, `ssh username@RServer.mycluster.ssh.azurehdinsight.net` si connetterà al nodo perimetrale per un Server R nel cluster HDInsight denominato __mycluster__.

### Nomi di dominio completo interni (FQDN)

I nodi in un cluster HDInsight dispongono di un indirizzo IP interno e di FQDN accessibili solo dal cluster (ad esempio, una sessione SSH per il nodo head o un processo in esecuzione nel cluster.) Quando si accede ai servizi del cluster utilizzando l'indirizzo IP o i FQDN interni, è necessario utilizzare Ambari per verificare l'IP o i FQDN da utilizzare per l’accesso al servizio.

Ad esempio, il servizio Oozie può essere eseguito solo su un nodo head e l’utilizzo del comando `oozie` da una sessione SSH richiede l'URL del servizio. Esso può essere recuperato da Ambari utilizzando il comando seguente:

	curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

Verrà restituito un valore simile al seguente, che contiene l'URL interno da utilizzare con il comando `oozie`:

	"oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

### Accesso ad altri tipi di nodo

È possibile connettersi ai nodi non accessibili direttamente tramite internet usando i metodi seguenti.

* __SSH__: una volta connessi a un nodo head tramite SSH, è quindi possibile usare SSH dal nodo head per connettersi ad altri nodi del cluster.
* __Tunnel SSH__: se occorre accedere a un servizio Web ospitato in uno dei nodi non esposti a Internet, è necessario [usare un tunnel SSH](hdinsight-linux-ambari-ssh-tunnel.md).
* __Rete virtuale di Azure__: se il cluster HDInsight fa parte di una rete virtuale di Azure, tutte le risorse della stessa rete virtuale possono accedere direttamente a tutti i nodi del cluster.

## Come controllare lo stato di un servizio

È possibile usare l'interfaccia utente Web di Ambari o l'API REST Ambari per controllare lo stato dei servizi in esecuzione nel nodo head.

###Interfaccia utente Web Ambari

L'interfaccia utente Web Ambari è visualizzabile in https://CLUSTERNAME.azurehdinsight.net. Sostituire **CLUSTERNAME** con il nome del cluster. Se richiesto, immettere le credenziali dell’utente HTTP del cluster. Il nome utente HTTP predefinito è **admin** e la password è la password inserita durante la creazione del cluster.

Nella pagina Ambari, i servizi installati verranno elencati a sinistra.

![Servizi installati](./media/hdinsight-high-availability-linux/services.png)

Esistono molte icone che possono essere visualizzate accanto a un servizio per indicare lo stato. È possibile visualizzare eventuali avvisi relativi a un servizio utilizzando il collegamento **Avvisi** nella parte superiore della pagina. È possibile selezionare ogni servizio per visualizzare ulteriori informazioni su di esso.

La pagina del servizio fornisce informazioni sullo stato e sulla configurazione di ogni servizio, ma non indica su quale nodo head esso è in esecuzione. Per visualizzare questa informazione, utilizzare il collegamento**Host** nella parte superiore della pagina. Verranno visualizzati gli host del cluster, inclusi i nodi head.

![elenco di host](./media/hdinsight-high-availability-linux/hosts.png)

Selezionando il collegamento per uno dei nodi head verranno visualizzati i servizi e i componenti in esecuzione su tale nodo.

![Stato dei componenti](./media/hdinsight-high-availability-linux/nodeservices.png)

###API REST Ambari

L'API REST Ambari è disponibile su Internet e il gateway pubblico gestisce il routing delle richieste per il nodo head che attualmente ospita l'API REST.

È possibile utilizzare il comando seguente per verificare lo stato di un servizio tramite l'API REST di Ambari:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* Sostituire **PASSWORD** con la password dell’account dell'utente HTTP (admin)

* Sostituire **CLUSTERNAME** con il nome del cluster

* Sostituire **SERVICENAME** con il nome del servizio per controllare lo stato di

Ad esempio, per controllare lo stato del servizio **HDFS** in un cluster denominato **mycluster**, con la **password**, è necessario utilizzare quanto segue:

	curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

La risposta sarà simile a quanto riportato di seguito:

	{
	  "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
	  "ServiceInfo" : {
	    "cluster_name" : "mycluster",
	    "service_name" : "HDFS",
	    "state" : "STARTED"
	  }
	}

L'URL indica che il servizio è in esecuzione su **nodo head 0**.

Lo stato indica che il servizio è in esecuzione, o **AVVIATO**.

Se non si sa quali servizi sono installati nel cluster, è possibile utilizzare quanto segue per recuperarne un elenco:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

####Componenti del servizio

I servizi possono includere componenti di cui si desidera controllare lo stato singolarmente. HDFS, ad esempio, contiene il componente NameNode. Per visualizzare informazioni su un componente, il comando è:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

Se non si sa quali servizi sono forniti dal cluster, è possibile utilizzare quanto segue per recuperarne un elenco:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
    
## Come accedere ai file di log nel nodo head

###SSH

Durante la connessione a un nodo head tramite SSH, i file di registro sono disponibili su**/var/log**. Ad esempio, **/var/log/hadoop-yarn/yarn** contiene i registri per YARN.

Ogni nodo head può avere voci di registro univoche, perciò è consigliabile controllare i registri su entrambi.

###SFTP

È anche possibile connettersi al nodo head usando SSH File Transfer Protocol o Secure File Transfer Protocol (SFTP) e scaricare direttamente i file di log.

In modo analogo all'uso di un client SSH, quando si stabilisce la connessione al cluster è necessario specificare il nome dell'account utente SSH e l'indirizzo SSH del cluster. ad esempio `sftp username@mycluster-ssh.azurehdinsight.net`. È anche necessario fornire la password per l'account quando richiesto o fornire una chiave pubblica con il parametro `-i`.

Una volta connessi, viene visualizzato un prompt `sftp>`. Da questo prompt è possibile passare a una directory, caricare e scaricare i file. Ad esempio, i comandi seguenti consentono di passare alla directory **/var/log/hadoop/hdfs** directory e quindi scaricare tutti i file nella directory.

    cd /var/log/hadoop/hdfs
    get *

Per un elenco di comandi disponibili, immettere `help` al prompt `sftp>`.

> [AZURE.NOTE] Esistono anche interfacce grafiche che consentono di visualizzare il file system quando si è connessi tramite SFTP. Ad esempio, [MobaXTerm](http://mobaxterm.mobatek.net/) consente di sfogliare il file system con un'interfaccia simile a Esplora risorse.


###Ambari

> [AZURE.NOTE] L’accesso ai file di registro tramite Ambari richiede un tunnel SSH, poichè i siti web per i singoli servizi non sono esposti pubblicamente in Internet. Per informazioni sull'uso di un tunnel SSH, vedere [Usare il tunneling SSH per accedere all'interfaccia Web di Ambari, ResourceManager, JobHistory, NameNode, Oozie e altre interfacce Web](hdinsight-linux-ambari-ssh-tunnel.md).

Dall'interfaccia utente Web Ambari, selezionare il servizio per il quale si desidera visualizzare i registri (ad esempio, YARN) e quindi utilizzare **Collegamenti rapidi** per selezionare il nodo head per il quale si vuole visualizzare i registri.

![Utilizzo dei collegamenti rapidi per visualizzare i registri](./media/hdinsight-high-availability-linux/viewlogs.png)

## Come configurare le dimensioni del nodo ##

È possibile selezionare le dimensioni del nodo head solo durante la creazione del cluster. È possibile trovare un elenco delle varie dimensioni di VM disponibili per HDInsight, inclusi core, memoria e archiviazione locale per ognuna, sulla [pagina dei prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Quando si crea un nuovo cluster, è possibile specificare le dimensioni dei nodi. Di seguito viene spiegato come specificare le dimensioni utilizzando il [portale di Azure][preview-portal], [Azure PowerShell][azure-powershell], e [l’interfaccia della riga di comando di Azure][azure-cli]\:

* **Portale di Azure**: quando si crea un nuovo cluster, è possibile impostare le dimensioni (piano tariffario) dei nodi head, di lavoro e ZooKeeper, se usato dal tipo di cluster, per il cluster:

	![Immagine della creazione guidata di cluster con la selezione delle dimensioni del nodo](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Interfaccia della riga di comando di Azure**: quando si usa il comando `azure hdinsight cluster create`, è possibile impostare le dimensioni dei nodi head, di lavoro e ZooKeeper usando i parametri `--headNodeSize`, `--workerNodeSize` e `--zookeeperNodeSize`.

* **Azure PowerShell**: quando si usa il cmdlet `New-AzureRmHDInsightCluster`, è possibile impostare le dimensioni dei nodi head, di lavoro e ZooKeeper usando i parametri `-HeadNodeVMSize`, `-WorkerNodeSize` e `-ZookeeperNodeSize`.

##Passaggi successivi

In questo documento si è appreso il modo in cui Azure HDInsight fornisce disponibilità elevata per Hadoop. Per ulteriori informazioni sugli aspetti menzionati in questo documento, utilizzare quanto segue.

- [Riferimento REST Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

- [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md)

- [Installare e configurare Azure PowerShell](../powershell-install-configure.md)

- [Gestire HDInsight tramite Ambari](hdinsight-hadoop-manage-ambari.md)

- [provisioning di cluster HDInsight basati su Linux](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: ../powershell-install-configure.md
[azure-cli]: ../xplat-cli-install.md

<!---HONumber=AcomDC_0914_2016-->