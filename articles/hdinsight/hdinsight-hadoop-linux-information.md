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
   ms.date="03/18/2016"
   ms.author="larryfr"/>

# Informazioni sull'uso di HDInsight in Linux

I cluster Azure HDInsight basati su Linux forniscono Hadoop in un ambiente Linux familiare, in esecuzione nel cloud di Azure. Per la maggior parte delle operazioni, dovrebbe funzionare esattamente come qualsiasi altra installazione di Hadoop in Linux. Questo documento indica le differenze specifiche che è opportuno conoscere.

## Nomi di dominio

Il nome di dominio completo (FQDN) da usare per la connessione al cluster da Internet è **&lt;nome cluster>.azurehdinsight.net** o (solo per SSH) **&lt;nome cluster-ssh>.azurehdinsight.net**.

Internamente, ogni nodo del cluster ha un nome assegnato durante la configurazione del cluster. Per trovare i nomi del cluster, è possibile visitare la pagina __Hosts__ nell'interfaccia utente Web di Ambari o usare il comando seguente per ottenere un elenco di host dall'API REST Ambari con [cURL](http://curl.haxx.se/) e [jq](https://stedolan.github.io/jq/):

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Sostituire __PASSWORD__ con la password dell'account amministratore e __CLUSTERNAME__ con il nome del cluster. Verrà restituito un documento JSON che contiene un elenco degli host nel cluster, quindi jq estrae il valore dell'elemento `host_name` per ogni host del cluster.

Se è necessario trovare il nome del nodo per un servizio specifico, è possibile eseguire una query in Ambari per tale componente. Ad esempio, per trovare gli host per il nodo con nome HDFS, usare il comando seguente.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Viene restituito un documento JSON che descrive il servizio e quindi jq estrae solo il valore `host_name` per gli host.

## Accesso remoto ai servizi

* **Ambari (Web)** - https://&lt;clustername>.azurehdinsight.net

	Eseguire l'autenticazione usando il nome utente e la password di amministratore cluster, quindi accedere ad Ambari. Anche Ambari usa l'utente e la password dell'amministratore cluster.

	L'autenticazione è in testo non crittografato. Usare sempre HTTPS per garantire che la connessione sia protetta.

	> [AZURE.IMPORTANT] Sebbene Ambari per il cluster sia accessibile direttamente su Internet, alcune funzionalità si basano sull'accesso ai nodi con il nome di dominio interno usato dal cluster. Poiché si tratta di un nome di dominio interno e non pubblico, si riceveranno errori di "server non trovato" se si tenta di accedere ad alcune funzionalità tramite Internet.
	>
	> Per usare le funzionalità complete dell'interfaccia utente Web di Ambari, usare un tunnel SSH per inoltrare il traffico Web al nodo head del cluster. Vedere [Usare il tunneling SSH per accedere all'interfaccia Web di Ambari, ResourceManager, JobHistory, NameNode, Oozie e altre interfacce Web](hdinsight-linux-ambari-ssh-tunnel.md).

* **Ambari (REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

	> [AZURE.NOTE] Eseguire l'autenticazione usando il nome utente e la password di amministratore cluster.
	>
	> L'autenticazione è in testo non crittografato. Usare sempre HTTPS per garantire che la connessione sia protetta.

* **WebHCat (Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

	> [AZURE.NOTE] Eseguire l'autenticazione usando il nome utente e la password di amministratore cluster.
	>
	> L'autenticazione è in testo non crittografato. Usare sempre HTTPS per garantire che la connessione sia protetta.

* **SSH** - &lt;nome cluster>-ssh.azurehdinsight.net sulla porta 22 o 23. La porta 22 viene usata per connettersi al nodo head 0, mentre la porta 23 viene usata per connettersi al nodo head 1. Per maggiori informazioni sui nodi head, vedere [Disponibilità e affidabilità dei cluster Hadoop in HDInsight](hdinsight-high-availability-linux.md).

	> [AZURE.NOTE] È possibile accedere al nodo head del cluster solo tramite SSH da un computer client. Una volta connessi, è quindi possibile accedere ai nodi di lavoro mediante SSH dal nodo head.

## Percorsi dei file

I file relativi ad Hadoop si trovano nei nodi del cluster in `/usr/hdp`. La directory contiene le sottodirectory seguenti:

* __2.2.4.9-1__: questa directory è denominata per la versione di Hortonworks Data Platform utilizzato da HDInsight, pertanto il numero il cluster potrebbe essere diverso da quelle elencate di seguito.
* __current__: questa directory contiene collegamenti alle directory all'interno della directory __2.2.4.9-1__ e consente di non dover digitare il numero della versione (che può cambiare) ogni volta che si vuole accedere a un file.

Dati di esempio e i file con estensione jar sono disponibili nel file system Hadoop Distributed File System (HDFS) o nell'archivio BLOB di Azure in '/example' o 'wasb:///example'.

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

1. Usare il comando seguente per recuperare le informazioni di configurazione HDFS tramite curl e filtrarle tramite [jq](https://stedolan.github.io/jq/):

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
    > [AZURE.NOTE] Verrà restituita la prima configurazione applicata al server (`service_config_version=1`) che conterrà queste informazioni. Se si recupera un valore che è stato modificato dopo la creazione del cluster, potrebbe essere necessario elencare le versioni della configurazione e recuperare la versione più recente.

    Verrà restituito un valore analogo al seguente, dove __CONTAINER__ è il contenitore predefinito e __ACCOUNTNAME__ è il nome dell'account di archiviazione di Azure:

        wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net

1. Ottenere il gruppo di risorse per l'account di archiviazione, usando l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md). Nel comando seguente, sostituire __ACCOUNTNAME__ con il nome dell'account di archiviazione recuperato da Ambari:

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Verrà restituito il nome del gruppo di risorse per l'account.
    
    > [AZURE.NOTE] Se il comando non restituisce risultati, può essere necessario modificare l'interfaccia della riga di comando di Azure in modalità Gestione risorse di Azure ed eseguire nuovamente il comando. Per passare alla modalità Gestione risorse di Azure, usare il comando seguente.
    >
    > `azure config mode arm`
    
2. Ottenere la chiave per l'account di archiviazione. Sostituire __GROUPNAME__ con il gruppo di risorse del passaggio precedente. Sostituire __ACCOUNTNAME__ con il nome dell'account di archiviazione:

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    Verrà restituita la chiave primaria per l'account.

Per individuare le informazioni di archiviazione, è anche possibile usare il portale di Azure:

1. Nel [portale di Azure](https://portal.azure.com/) selezionare il cluster HDInsight.

2. Nella sezione __Informazioni di base__ selezionare __Tutte le impostazioni__.

3. In __Impostazioni__ selezionare __Chiavi di archiviazione di Azure__.

4. In __Chiavi di archiviazione di Azure__ selezionare uno degli account di archiviazione visualizzati nell'elenco. Verranno visualizzate le informazioni relative all'account di archiviazione.

5. Selezionare l'icona a forma di chiave. Verranno visualizzate le chiavi per l'account di archiviazione.

### Come si accede all'archivio BLOB?

Oltre che con il comando Hadoop dal cluster, è possibile procedere in diversi modi per accedere ai BLOB:

* [Interfaccia della riga di comando di Azure per Mac, Linux e Windows](../xplat-cli-install.md): comandi dell’interfaccia della riga di comando per l'uso con Azure. Dopo l'installazione, usare il comando `azure storage` per informazioni sull'uso dell'archiviazione o `azure blob` per i comandi specifici dei BLOB.

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

		1. Aprire \_\___https://CLUSTERNAME.azurehdinsight.net/stormui__ nel Web browser, dove CLUSTERNAME è il nome del cluster Storm. Se richiesto, immettere il nome amministratore (admin) del cluster HDInsight e la password specificata durante la creazione del cluster.

		3. Selezionare la topologia da ribilanciare e quindi fare clic sul pulsante __Rebalance__. Specificare il ritardo prima dell'esecuzione dell'operazione di ribilanciamento.

Per informazioni specifiche sul ridimensionamento del cluster HDInsight, vedere:

* [Gestire cluster Hadoop in HDInsight tramite il portale di Azure](hdinsight-administer-use-portal-linux.md#scaling)

* [Gestire cluster Hadoop in HDInsight tramite Azure PowerShell](hdinsight-administer-use-command-line.md#scaling)

## Come si installa Hue (o un altro componente Hadoop)?

HDInsight è un servizio gestito e quindi, se viene rilevato un problema, Azure può automaticamente eliminare i nodi nel cluster ed eseguirne di nuovo il provisioning. Per questo motivo, non è consigliabile eseguire manualmente l'installazione direttamente nei nodi del cluster. Usare piuttosto [azioni script di HDInsight](hdinsight-hadoop-customize-cluster.md) quando è necessario eseguire le installazioni seguenti:

* Un servizio o un sito Web, ad esempio Spark o Hue.
* Un componente che richiede modifiche di configurazione in più nodi del cluster, ad esempio una variabile di ambiente necessaria, la creazione di una directory di registrazione o la creazione di un file di configurazione.

Le azioni script sono script Bash che vengono eseguiti durante il provisioning del cluster e che possono essere usati per installare e configurare componenti aggiuntivi nel cluster. Sono disponibili script di esempio per installare i componenti seguenti:

* [Hue](hdinsight-hadoop-hue-linux.md)
* [Giraph,](hdinsight-hadoop-giraph-install-linux.md)
* [R](hdinsight-hadoop-r-scripts-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)

Per informazioni su come sviluppare azioni script personalizzate, vedere [Sviluppo di azioni script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

###File con estensione jar

Alcune tecnologie Hadoop vengono fornite in file con estensione jar indipendenti contenenti funzioni usate come parte di un processo MapReduce o dall'interno di Pig o Hive. Sebbene sia possibile installarli usando azioni script, spesso non richiedono alcuna installazione e possono essere caricati nel cluster dopo il provisioning ed essere usati direttamente. Se si desidera assicurarsi che il componente venga mantenuto dopo la nuova creazione dell'immagine del cluster, è possibile archiviare il file con estensione jar in WASB.

Se ad esempio si desidera usare l'ultima versione di [DataFu](http://datafu.incubator.apache.org/), è possibile scaricare un file con estensione jar contenente il progetto e caricarlo nel cluster HDInsight. Seguire quindi la documentazione di DataFu per informazioni sull'uso da Pig o Hive.

> [AZURE.IMPORTANT] Alcuni componenti che sono file con estensione jar autonomi vengono forniti con HDInsight, ma non sono presenti nel percorso. Se si desidera un componente specifico, è possibile usare il comando seguente per cercarlo nel cluster:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Verrà restituito il percorso dei file con estensione jar corrispondenti.

Se il cluster fornisce già una versione di un componente come file con estensione jar autonomo, ma si desidera usare una versione diversa, è possibile caricare una nuova versione del componente nel cluster e provare a usarla nei processi.

> [AZURE.WARNING] I componenti forniti con il cluster HDInsight sono supportati in modo completo e il supporto tecnico Microsoft contribuirà a isolare e risolvere i problemi correlati a questi componenti.
>
> I componenti personalizzati ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. È possibile che si ottenga la risoluzione dei problemi o che venga richiesto di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto approfondito per la tecnologia specifica. È ad esempio possibile ricorrere a molti siti di community, come il [forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/it-IT/home?forum=hdinsight) o [http://stackoverflow.com](http://stackoverflow.com). Per i progetti Apache sono inoltre disponibili siti specifici in [http://apache.org](http://apache.org), ad esempio [Hadoop](http://hadoop.apache.org/) e [Spark](http://spark.apache.org/).

## Passaggi successivi

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare processi MapReduce con HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0323_2016-->