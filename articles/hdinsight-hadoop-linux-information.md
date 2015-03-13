<properties
   pageTitle="Informazioni utili su Hadoop in HDInsight basato su Linux | Azure"
   description="I cluster HDInsight basati su Linux forniscono Hadoop in un ambiente Linux familiare, in esecuzione nel cloud di Azure."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags 
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Uso di HDInsight in Linux (anteprima)

I cluster HDInsight basati su Linux forniscono Hadoop in un ambiente Linux familiare, in esecuzione nel cloud di Azure. Per la maggior parte delle operazioni, dovrebbe funzionare esattamente come qualsiasi altra installazione di Hadoop in Linux. Questo documento indica le differenze specifiche che è opportuno conoscere.

## Nomi di dominio

Il nome di dominio completo (FQDN) da usare per la connessione al cluster è **&lt;nomecluster>.azurehdinsight.net** o **&lt;nomecluster>.aurehdinsight.net** (solo per SSH).

## Servizi accessibili in modalità remota

* **Ambari (Web)** - https://&lt;nomecluster>.azurehdinsight.net

	> [AZURE.NOTE] Eseguire l'autenticazione usando il nome utente e la password di amministratore cluster, quindi accedere ad Ambari. Anche Ambari usa l'utente e la password dell'amministratore cluster.
	> 
	> L'autenticazione è in testo non crittografato. Usare sempre HTTPS per garantire che la connessione sia protetta.

	Sebbene Ambari per il cluster sia accessibile direttamente su Internet, alcune funzionalità si basano sull'accesso ai nodi con il nome di dominio interno usato dal cluster. Poiché si tratta di un nome di dominio interno e non pubblico, si riceveranno errori di server non trovato se si tenta di accedere ad alcune funzionalità tramite Internet.

	Per risolvere questo problema, usare un tunnel SSH per inoltrare il traffico Web al nodo head del cluster. Usare i seguenti articoli per creare un tunnel SSH da una porta nel computer locale al cluster.

	* <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Uso di SSH con Hadoop basato su Linux in HDInsight da Linux, Unix oppure OS X:</a> procedura per la creazione di un tunnel SSH con il comando  `ssh`

	* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">Uso di SSH con Hadoop basato su Linux in HDInsight da Windows:</a> procedura per l'uso di Putty per la creazione di un tunnel SSH

* **Ambari (REST)** - https://&lt;nomecluster>.azurehdinsight.net/ambari

	> [AZURE.NOTE] Eseguire l'autenticazione usando il nome utente e la password di amministratore cluster.
	> 
	> L'autenticazione è in testo non crittografato. Usare sempre HTTPS per garantire che la connessione sia protetta.

* **WebHCat (Templeton)** - https://&lt;nomecluster>.azurehdinsight.net/templeton

	> [AZURE.NOTE] Eseguire l'autenticazione usando il nome utente e la password di amministratore cluster.
	> 
	> L'autenticazione è in testo non crittografato. Usare sempre HTTPS per garantire che la connessione sia protetta.

* **SSH** - &lt;nome cluster>-ssh.azurehdinsight.net sulla porta 22

	> [AZURE.NOTE] È possibile accedere al nodo head del cluster solo tramite SSH da un computer client. Una volta connessi, è quindi possibile accedere ai nodi di lavoro mediante SSH dal nodo head.

## Percorsi dei file

I file relativi ad Hadoop si trovano nei nodi del cluster in  `/usr/hdp/current`.

Dati di esempio e file JAR sono reperibili in HDFS (WASB) in '/example' o  'wasb:///example'.

## Procedure consigliate per HDFS, WASB e archiviazione

Nella maggior parte delle distribuzioni di Hadoop, Hadoop Distributed File System (HDFS) è supportato dall'archiviazione locale nei computer del cluster. Sebbene sia un sistema efficiente, può essere costoso per una soluzione basata su cloud dove viene addebitata una tariffa oraria per risorse di calcolo.

HDInsight usa l'archivio BLOB di Azure come archivio predefinito, con i seguenti vantaggi:

* Archiviazione economica a lungo termine

* Accesso da servizi esterni, ad esempio siti Web, utilità di caricamento e download di file, SDK di linguaggi diversi e Web browser

Poiché è l'archivio predefinito per HDInsight, in genere non è necessario eseguire alcuna operazione per usarlo. Ad esempio, il seguente comando elencherà i file della cartella **/example/data**, disponibile nell'archivio BLOB di Azure.

	hadoop fs -ls /example/data

Alcuni comandi potrebbero richiedere di specificare se si usa l'archivio BLOB. In questi casi, è possibile anteporre **WASB://** al comando.

HDInsight consente anche di associare più account di archiviazione BLOB a un cluster. Per accedere ai dati in un account di archiviazione BLOB non predefinito, è possibile usare il formato **WASB://&lt;nome contenitore>@&lt;nome account>.blob.core.windows.net/**. Ad esempio, il seguente comando elencherà il contenuto della directory **/example/data** per l'account e il contenitore di archiviazione specificati.

	hadoop fs -ls wasb://mycontainer@mystorage.blob.core.windows.net/example/data

### Quale archivio BLOB viene usato dal cluster?

Durante la creazione del cluster, si scegliere se usare un account e un contenitore di archiviazione esistenti o se crearne dei nuovi. Successivamente questa operazione viene probabilmente dimenticata. È possibile trovare l'account e il contenitore di archiviazione usando i seguenti metodi.

**Portale di Azure**

1. Nel <a href="https://manage.windowsazure.com/" target="_blank">portale di gestione di Azure</a>selezionare il cluster HDInsight.

2. Selezionare **Dashboard** nella parte superiore della pagina.

3. Gli account e i contenitori di archiviazione sono elencati nella sezione **Risorse collegate** della pagina.

	![linked resources](./media/hdinsight-hadoop-linux-information/storageportal.png)

**Interfaccia della riga di comando multipiattaforma di Azure**

*Disponibile prossimamente*

### Come è possibile accedere all'archivio BLOB?

Oltre che con il comando Hadoop dal cluster, è possibile procedere in diversi modi per accedere ai BLOB:

* <a href="http://azure.microsoft.com/ documentation/articles/xplat-cli/" target="_blank">Interfaccia della riga di comando multipiattaforma di Azure:</a> dopo l'installazione, vedere  `azure storage` per informazioni sull'uso dell'archiviazione o  `azure blob` per i comandi specifici dei BLOB.

* Ampia gamma di SDK:

	* <a href="https://github.com/Azure/azure-sdk-for-java" target="_blank">Java</a>

	* <a href="https://github.com/Azure/azure-sdk-for-node" target="_blank">Node.js</a>

	* <a href="https://github.com/Azure/azure-sdk-for-php" target="_blank">PHP</a>

	* <a href="https://github.com/Azure/azure-sdk-for-python" target="_blank">Python</a>

	* <a href="https://github.com/Azure/azure-sdk-for-ruby" target="_blank">Ruby</a>

	* <a href="https://github.com/Azure/azure-sdk-for-net" target="_blank">.NET</a>

* <a href="https://msdn.microsoft.com/it-it/library/azure/dd135733.aspx" target="_blank">API REST di archiviazione</a>


## Passaggi successivi

* [Usare Hive in HDInsight](../hdinsight-use-hive/)
* [Usare Pig in HDInsight](../hdinsight-use-pig/)
* [Usare processi MapReduce in HDInsight](../hdinsight-use-mapreduce)


<!--HONumber=45--> 
