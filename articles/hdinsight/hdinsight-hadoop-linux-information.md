<properties
   pageTitle="Suggerimenti per l'uso di Hadoop su HDInsight basato su Linux | Microsoft Azure"
   description="Ottenere suggerimenti di implementazione per l’uso di cluster HDInsight (Hadoop) basati su Linux in un ambiente Linux familiare, in esecuzione nel cloud di Azure."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/12/2015"
   ms.author="larryfr"/>

# Informazioni sull'uso di HDInsight in Linux

I cluster Azure HDInsight basati su Linux forniscono Hadoop in un ambiente Linux familiare, in esecuzione nel cloud di Azure. Per la maggior parte delle operazioni, dovrebbe funzionare esattamente come qualsiasi altra installazione di Hadoop in Linux. Questo documento indica le differenze specifiche che è opportuno conoscere.

## Nomi di dominio

Il nome di dominio completo (FQDN) da usare per la connessione al cluster è **&lt;nome cluster>.azurehdinsight.net** o (solo per SSH) **&lt;nome cluster-ssh>.azurehdinsight.net**.

## Accesso remoto ai servizi

* **Ambari (Web)** - https://&lt;clustername>.azurehdinsight.net

	Eseguire l'autenticazione usando il nome utente e la password di amministratore cluster, quindi accedere ad Ambari. Anche Ambari usa l'utente e la password dell'amministratore cluster.

	L'autenticazione è in testo non crittografato. Usare sempre HTTPS per garantire che la connessione sia protetta.

	> [AZURE.IMPORTANT]Sebbene Ambari per il cluster sia accessibile direttamente su Internet, alcune funzionalità si basano sull'accesso ai nodi con il nome di dominio interno usato dal cluster. Poiché si tratta di un nome di dominio interno e non pubblico, si riceveranno errori di "server non trovato" se si tenta di accedere ad alcune funzionalità tramite Internet.
	>
	> Per usare le funzionalità complete dell'interfaccia utente Web di Ambari, usare un tunnel SSH per inoltrare il traffico Web al nodo head del cluster. Vedere [Usare il tunneling SSH per accedere all'interfaccia Web Ambari, ResourceManager, JobHistory, NameNode, Oozie e altre interfacce utente Web](hdinsight-linux-ambari-ssh-tunnel.md).

* **Ambari (REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

	> [AZURE.NOTE]Eseguire l'autenticazione usando il nome utente e la password di amministratore cluster.
	>
	> L'autenticazione è in testo non crittografato. Usare sempre HTTPS per garantire che la connessione sia protetta.

* **WebHCat (Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

	> [AZURE.NOTE]Eseguire l'autenticazione usando il nome utente e la password di amministratore cluster.
	>
	> L'autenticazione è in testo non crittografato. Usare sempre HTTPS per garantire che la connessione sia protetta.

* **SSH** - &lt;nome cluster>-ssh.azurehdinsight.net sulla porta 22 o 23. La porta 22 viene utilizzata per connettersi a headnode0, mentre la porta 23 viene utilizzata per connettersi a headnode1. Per altre informazioni sui nodi head, vedere [Disponibilità e affidabilità dei cluster Hadoop in HDInsight](hdinsight-high-availability-linux.md).

	> [AZURE.NOTE]È possibile accedere al nodo head del cluster solo tramite SSH da un computer client. Una volta connessi, è quindi possibile accedere ai nodi di lavoro mediante SSH dal nodo head.

## Percorsi dei file

I file relativi ad Hadoop si trovano nei nodi del cluster in `/usr/hdp`. La directory contiene le sottodirectory seguenti:

* __2.2.4.9-1__: questa directory è denominata in base alla versione di Hortonworks Data Platform usata da HDInsight, di conseguenza il numero visualizzato nel cluster potrebbe essere diverso da quello mostrato qui.
* __corrente__: questa directory contiene collegamenti alle directory all'interno della directory __2.2.4.9-1__ e consente di non dover digitare il numero della versione (che può cambiare) ogni volta che si vuole accedere a un file.

Dati di esempio e i file JAR sono disponibili nel file system Hadoop Distributed File System (HDFS) o nell'archivio BLOB di Azure in '/example' o 'wasb:///example'.

## Procedure consigliate relative al file system HDFS, all'archivio BLOB di Azure e all'archiviazione

Nella maggior parte delle distribuzioni di Hadoop, il file system HDFS è supportato dall'archiviazione locale nei computer del cluster. Sebbene sia un sistema efficiente, può essere costoso per una soluzione basata su cloud dove viene addebitata una tariffa oraria per risorse di calcolo.

HDInsight usa l'archivio BLOB di Azure come archivio predefinito, con i vantaggi seguenti:

* Archiviazione a lungo termine economica

* Accessibilità da servizi esterni, ad esempio siti Web, utilità di caricamento e download di file, SDK di linguaggi diversi e Web browser

Poiché è l'archivio predefinito per HDInsight, in genere non è necessario eseguire alcuna operazione per usarlo. Ad esempio, il comando seguente elencherà i file della cartella **/example/data**, disponibile nell'archivio BLOB di Azure:

	hadoop fs -ls /example/data

Alcuni comandi potrebbero richiedere di specificare se si usa l'archivio BLOB. In questi casi, è possibile anteporre il prefisso ****WASB://** al comando.

HDInsight consente anche di associare più account di archiviazione BLOB a un cluster. Per accedere ai dati in un account di archiviazione BLOB non predefinito, è possibile usare il formato **WASB://&lt;container-name>@&lt;nome account>.blob.core.windows.net/**. Ad esempio, il comando seguente elencherà il contenuto della directory **/example/data** per il contenitore l'account di archiviazione BLOB specificati:

	hadoop fs -ls wasb://mycontainer@mystorage.blob.core.windows.net/example/data

### Quale archivio BLOB viene usato dal cluster?

Durante la creazione del cluster si è scelto se usare un account e un contenitore di archiviazione di Azure esistenti o se crearne di nuovi. Successivamente questa operazione viene probabilmente dimenticata. È possibile trovare l'account di archiviazione e il contenitore predefiniti usando l'API REST Ambari.

1. Per recuperare le informazioni di configurazione HDFS, usare il comando seguente:

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1"

2. Nei dati JSON restituiti, trovare la voce `fs.defaultFS`. Questa conterrà il contenitore e il nome dell'account di archiviazione predefiniti in un formato simile al seguente:

        wasb://CONTAINTERNAME@STORAGEACCOUNTNAME.blob.core.windows.net

	> [AZURE.TIP]Se è stato installato [jq](http://stedolan.github.io/jq/), è possibile usare quanto segue per restituire solo la voce `fs.defaultFS`:
	>
	> `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'`

3. Per trovare la chiave utilizzata per autenticare l'account di archiviazione o per trovare gli account di archiviazione secondari associati al cluster, usare:

		curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1"

4. Nei dati JSON restituiti, trovare le voci che iniziano con `fs.azure.account.key`. Il resto del nome della voce è il nome dell’account di archiviazione. Ad esempio: `fs.azure.account.key.mystorage.blob.core.windows.net`. Il valore archiviato in questa voce è la chiave utilizzata per autenticare l'account di archiviazione.

	> [AZURE.TIP]Se è stato installato [jq](http://stedolan.github.io/jq/), è possibile usare quanto segue per restituire un elenco di chiavi e valori:
	>
	> `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties as $in | $in | keys[] | select(. | contains("fs.azure.account.key.")) as $item | $item | ltrimstr("fs.azure.account.key.") | { storage_account: ., storage_account_key: $in[$item] }'`

Per individuare le informazioni di archiviazione, è anche possibile usare il portale di anteprima di Azure:

1. Nel [portale di anteprima di Azure](https://portal.azure.com/) selezionare il cluster HDInsight.

2. Nella sezione __Informazioni di base__ selezionare __Tutte le impostazioni__.

3. In __Impostazioni__ selezionare __Chiavi di archiviazione di Azure__.

4. In __Chiavi di archiviazione di Azure__ selezionare uno degli account di archiviazione visualizzati nell'elenco. Verranno visualizzate le informazioni relative all'account di archiviazione.

5. Selezionare l'icona a forma di chiave. Verranno visualizzate le chiavi per l'account di archiviazione.

### Come si accede all'archivio BLOB?

Oltre che con il comando Hadoop dal cluster, è possibile procedere in diversi modi per accedere ai BLOB:

* [Interfaccia della riga di comando di Azure per Mac, Linux e Windows](../xplat-cli.md): comandi dell’interfaccia della riga di comando per l'uso con Azure. Dopo l'installazione, usare il comando `azure storage` per informazioni sull'uso dell'archiviazione o `azure blob` per i comandi specifici dei BLOB.

* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): uno script Python per l'uso con i BLOB in Archiviazione di Azure.

* Ampia gamma di SDK:

	* [Java](https://github.com/Azure/azure-sdk-for-java)

	* [Node.JS](https://github.com/Azure/azure-sdk-for-node)

	* [PHP](https://github.com/Azure/azure-sdk-for-php)

	* [Python](https://github.com/Azure/azure-sdk-for-python)

	* [Ruby](https://github.com/Azure/azure-sdk-for-ruby)

	* [.NET](https://github.com/Azure/azure-sdk-for-net)

* [API REST di archiviazione](https://msdn.microsoft.com/library/azure/dd135733.aspx)

##<a name="scaling"></a>Ridimensionamento del cluster

La funzionalità di scalabilità del cluster consente di modificare il numero di nodi dati usati da un cluster in esecuzione in Azure HDInsight senza dover eliminare e ricreare il cluster.

È possibile eseguire operazioni di ridimensionamento mentre altri processi sono in esecuzione nel cluster.

L'operazione di ridimensionamento può influire sui tipi di cluster come indicato di seguito:

* __Hadoop__: durante la riduzione del numero di nodi in un cluster, alcuni servizi nel cluster vengono riavviati. È quindi possibile che al termine dell'operazione di ridimensionamento, i processi in esecuzione o in sospeso abbiano esito negativo. In questo caso, inviare nuovamente i processi una volta completata l'operazione.

* __HBase__: i server a livello di area vengono bilanciati automaticamente entro pochi minuti dal completamento dell'operazione di ridimensionamento. Per bilanciare manualmente i server a livello di area, seguire questa procedura:

	1. Connettersi al cluster HDInsight tramite SSH. Per altre informazioni sull'uso di SSH con HDInsight, vedere i documenti seguenti:

		* [Uso di SSH con HDInsight da Linux, Unix e Mac OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

		* [Uso di SSH con HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

	1. Usare il codice seguente per avviare la shell HBase:

			hbase shell

	2. Una volta caricata la shell HBase, usare il codice seguente per bilanciare manualmente i server a livello di area:

			balancer

* __Storm__: al termine dell'operazione di ridimensionamento, ribilanciare qualsiasi topologia Storm in esecuzione. In questo modo, la topologia può rettificare le impostazioni di parallelismo in base al nuovo numero di nodi nel cluster. Per ribilanciare le topologie in esecuzione, usare una delle opzioni seguenti:

	* __SSH__: connettersi al server e usare il comando seguente per ribilanciare una topologia:

			storm rebalance TOPOLOGYNAME

		È anche possibile specificare parametri per eseguire l'override degli hint di parallelismo forniti in origine dalla topologia. Ad esempio, `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` riconfigurerà la topologia con 5 processi di lavoro, 3 esecutori per il componente blue-spout e 10 esecutori per il componente yellow-bolt.

	* __Interfaccia utente Storm__: usare la procedura seguente per ribilanciare una topologia tramite l'interfaccia utente Storm.

		1. [Creare un tunnel SSH al cluster e aprire l'interfaccia utente Web Ambari](hdinsight-linux-ambari-ssh-tunnel.md).

		2. Dall'elenco dei servizi nella parte sinistra della pagina, selezionare __Storm__. Selezionare quindi __Storm UI__ da __Quick Links__.

			![Voce Storm UI in Quick Links](./media/hdinsight-hadoop-linux-information/ambari-storm.png)

			Verrà visualizzata l'interfaccia utente Storm:

			![interfaccia utente Storm](./media/hdinsight-hadoop-linux-information/storm-ui.png)

		3. Selezionare la topologia da ribilanciare, quindi fare clic sul pulsante __Rebalance__. Specificare il ritardo prima dell'esecuzione dell'operazione di ribilanciamento.

Per informazioni specifiche sul ridimensionamento del cluster HDInsight, vedere:

* [Gestire cluster Hadoop in HDInsight tramite il portale di anteprima di Azure](hdinsight-administer-use-portal-linux.md#scaling)

* [Gestire cluster Hadoop in HDInsight tramite Azure PowerShell](hdinsight-administer-use-command-line.md#scaling)

## Come si installa Hue (o un altro componente Hadoop)?

HDInsight è un servizio gestito e quindi, se viene rilevato un problema, Azure può automaticamente eliminare i nodi nel cluster ed eseguirne di nuovo il provisioning. Per questo motivo, non è consigliabile installare manualmente componenti nei nodi del cluster.

Provare invece a usare le [azioni script di HDInsight](hdinsight-hadoop-customize-cluster.md).

Le azioni script sono script Bash che vengono eseguiti durante il provisioning del cluster e che possono essere usati per installare componenti aggiuntivi nel cluster. Sono disponibili script di esempio per installare i componenti seguenti:

* [Hue](hdinsight-hadoop-hue-linux.md)
* [Giraph,](hdinsight-hadoop-giraph-install-linux.md)
* [R](hdinsight-hadoop-r-scripts-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)
* [Spark](hdinsight-hadoop-spark-install-linux.md)

Per informazioni su come sviluppare azioni script personalizzate, vedere [Sviluppo di azioni script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

## Passaggi successivi

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare processi MapReduce con HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=Sept15_HO3-->