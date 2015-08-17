<properties
   pageTitle="Suggerimenti per l'uso di Hadoop su HDInsight basato su Linux | Microsoft Azure"
   description="Ottenere suggerimenti di implementazione per l’uso di cluster HDInsight (Hadoop) basati su Linux in un ambiente Linux familiare, in esecuzione nel cloud di Azure."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>


<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/24/2015"
   ms.author="larryfr"/>


# Informazioni sull'utilizzo di HDInsight in Linux (anteprima)

I cluster Azure HDInsight basati su Linux forniscono Hadoop in un ambiente Linux familiare, in esecuzione nel cloud di Azure. Per la maggior parte delle operazioni, dovrebbe funzionare esattamente come qualsiasi altra installazione di Hadoop in Linux. Questo documento indica le differenze specifiche che è opportuno conoscere.

## Nomi di dominio

Il nome di dominio completo (FQDN) da usare per la connessione al cluster è **&lt;nome cluster>.azurehdinsight.net** o (solo per SSH) **&lt;nome cluster-ssh>.azurehdinsight.net**.

## Accesso remoto ai servizi

* **Ambari (Web)** - https://&lt;clustername>.azurehdinsight.net

	> [AZURE.NOTE]Eseguire l'autenticazione usando il nome utente e la password di amministratore cluster, quindi accedere ad Ambari. Anche Ambari usa l'utente e la password dell'amministratore cluster.
	>
	> L'autenticazione è in testo non crittografato. Usare sempre HTTPS per garantire che la connessione sia protetta.

	Sebbene Ambari per il cluster sia accessibile direttamente su Internet, alcune funzionalità si basano sull'accesso ai nodi con il nome di dominio interno usato dal cluster. Poiché si tratta di un nome di dominio interno e non pubblico, si riceveranno errori di "server non trovato" se si tenta di accedere ad alcune funzionalità tramite Internet.

	Per risolvere questo problema, usare un tunnel SSH per inoltrare il traffico Web al nodo head del cluster. Usare la sezione **Tunneling SSH** degli articoli seguenti per creare un tunnel SSH da una porta nel computer locale al cluster.

	* [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md): passaggi relativi alla creazione di un tunnel SSH usando il comando `ssh`.

	* [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows): passaggi relativi all'uso di PuTTY per creare un tunnel SSH.

* **Ambari (REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

	> [AZURE.NOTE]Eseguire l'autenticazione usando il nome utente e la password di amministratore cluster.
	>
	> L'autenticazione è in testo non crittografato. Usare sempre HTTPS per garantire che la connessione sia protetta.

* **WebHCat (Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

	> [AZURE.NOTE]Eseguire l'autenticazione usando il nome utente e la password di amministratore cluster.
	>
	> L'autenticazione è in testo non crittografato. Usare sempre HTTPS per garantire che la connessione sia protetta.

* **SSH** - &lt;nome cluster>-ssh.azurehdinsight.net sulla porta 22

	> [AZURE.NOTE]È possibile accedere al nodo head del cluster solo tramite SSH da un computer client. Una volta connessi, è quindi possibile accedere ai nodi di lavoro mediante SSH dal nodo head.

## Percorsi dei file

I file relativi ad Hadoop si trovano nei nodi del cluster in `/usr/hdp/current`.

Dati di esempio e i file JAR sono disponibili nel file system Hadoop Distributed File System (HDFS) o nell'archivio BLOB di Azure in '/example' o 'wasb:///example'.

## Procedure consigliate relative al file system HDFS, all'archivio BLOB di Azure e all'archiviazione

Nella maggior parte delle distribuzioni di Hadoop, il file system HDFS è supportato dall'archiviazione locale nei computer del cluster. Sebbene sia un sistema efficiente, può essere costoso per una soluzione basata su cloud dove viene addebitata una tariffa oraria per risorse di calcolo.

HDInsight usa l'archivio BLOB di Azure come archivio predefinito, con i vantaggi seguenti:

* Archiviazione a lungo termine economica

* Accessibilità da servizi esterni, ad esempio siti Web, utilità di caricamento e download di file, SDK di linguaggi diversi e Web browser

Poiché è l'archivio predefinito per HDInsight, in genere non è necessario eseguire alcuna operazione per usarlo. Ad esempio, il comando seguente elencherà i file della cartella **/example/data**, disponibile nell'archivio BLOB di Azure:

	hadoop fs -ls /example/data

Alcuni comandi potrebbero richiedere di specificare se si usa l'archivio BLOB. In questi casi, è possibile anteporre ****WASB://** al comando.

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

## Passaggi successivi

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare processi MapReduce con HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=August15_HO6-->