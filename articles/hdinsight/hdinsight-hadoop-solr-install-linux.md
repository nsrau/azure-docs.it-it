<properties
	pageTitle="Usare l’Azione di script per installare Solr in HDInsight basati su Linux | Microsoft Azure"
	description="Informazioni su come installare Solr nei cluster HDInsight Hadoop basati su Linux utilizzando azioni di Script."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2016"
	ms.author="larryfr"/>

# Installare e usare Solr nei cluster Hadoop di HDInsight

In questo argomento si apprenderà come installare Solr in Azure HDInsight usando un'azione script. Solr è una piattaforma di ricerca avanzata e offre funzionalità di ricerca di livello aziendale per i dati gestiti da Hadoop. Dopo l'installazione di Solr nel cluster HDInsight, verrà illustrato anche come eseguire ricerche nei dati usando Solr.

> [AZURE.NOTE] I passaggi descritti in questo documento richiedono un cluster HDInsight basato su Linux. Per informazioni sull'uso di Solr con un cluster basato su Windows, vedere [Installare e usare Solr nei cluster Hadoop HDInsight (Windows)](hdinsight-hadoop-solr-install.md)

Lo script di esempio usato in questo argomento crea un cluster Solr con una configurazione specifica. Per configurare il cluster Solr con raccolte, partizioni, schemi, repliche diverse e così via, sarà necessario modificare di conseguenza lo script e i file binari di Solr.

## <a name="whatis"></a>Che cos'è Solr?

[Apache Solr](http://lucene.apache.org/solr/features.html) è una piattaforma di ricerca aziendale che permette di eseguire ricerche full-text avanzate sui dati. Mentre Hadoop consente di archiviare e gestire quantità elevate di dati, Apache Solr offre le funzionalità di ricerca necessarie per recuperare rapidamente i dati. Questo argomento fornisce istruzioni su come personalizzare un cluster HDInsight per l'installazione di Solr.

> [AZURE.WARNING] I componenti forniti con il cluster HDInsight sono supportati in modo completo e il supporto tecnico Microsoft contribuirà a isolare e risolvere i problemi correlati a questi componenti.
>
> I componenti personalizzati, ad esempio Solr, ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. È possibile che si ottenga la risoluzione dei problemi o che venga richiesto di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto approfondito per la tecnologia specifica. È ad esempio possibile ricorrere a molti siti di community, come il [forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/it-IT/home?forum=hdinsight) o [http://stackoverflow.com](http://stackoverflow.com). Anche per i progetti Apache sono disponibili siti specifici in [http://apache.org](http://apache.org), ad esempio [Hadoop](http://hadoop.apache.org/).

## Caratteristiche dello script

Questo script apporta le modifiche seguenti al cluster HDInsight:

* Installa Solr in `/usr/hdp/current/solr`.
* Crea un nuovo utente __solrusr__ usato per eseguire il servizio Solr.
* Imposta __solruser__ come proprietario di `/usr/hdp/current/solr`.
* Aggiunge una configurazione [Upstart](http://upstart.ubuntu.com/) che consente di avviare Solr se si riavvia un nodo del cluster. Solr viene anche avviato automaticamente nei nodi del cluster dopo l'installazione.

## <a name="install"></a>Installare R mediante azioni script

Uno script di esempio per l'installazione di Solr in un cluster HDInsight è disponibile all'indirizzo seguente.

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

Questa sezione fornisce istruzioni su come usare lo script di esempio quando si crea un nuovo cluster usando il portale di Azure.

> [AZURE.NOTE] Per applicare le azioni script è possibile usare anche Azure PowerShell, l'interfaccia della riga di comando di Azure, HDInsight .NET SDK o i modelli di Azure Resource Manager. È anche possibile applicare azioni script a cluster già in esecuzione. Per altre informazioni, vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md).

1. Avviare il provisioning di un cluster seguendo i passaggi descritti in [Effettuare il provisioning di cluster HDInsight basati su Linux](hdinsight-provision-linux-clusters.md#portal) senza completarlo.

2. Nel pannello **Configurazione facoltativa** selezionare **Azioni script** e specificare le informazioni seguenti:

	* __NOME__: immettere un nome descrittivo per l'azione script.
	* __URI SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
	* __HEAD__: selezionare questa opzione.
	* __LAVORO__: selezionare questa opzione.
	* __ZOOKEEPER__: selezionare questa opzione per l'installazione nel nodo Zookeeper.
	* __PARAMETRI__: lasciare questo campo vuoto.

3. Nella parte inferiore di **Azioni di script** usare il pulsante **Seleziona** per salvare la configurazione. Usare infine il pulsante **Seleziona** nella parte inferiore del pannello **Configurazione facoltativa** per salvare le informazioni relative alla configurazione facoltativa.

4. Continuare il provisioning del cluster come descritto nell'argomento relativo all'[esecuzione del provisioning di cluster HDInsight basati su Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usesolr"></a>Come si usa Solr in HDInsight?

### Modalità di indicizzazione

È prima di tutto necessario indicizzare Solr con alcuni file di dati. Sarà quindi possibile usare Solr per eseguire query di ricerca sui dati indicizzati. Usare la procedura seguente per aggiungere alcuni dati di esempio a Solr e quindi eseguire una query:

1. Connettersi al cluster HDInsight usando SSH:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	Per altre informazioni sull'uso di SSH con HDInsight, vedere gli articoli seguenti:

	* [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

	> [AZURE.IMPORTANT] I passaggi successivi di questo documento usano un tunnel SSL per la connessione all'interfaccia utente Web di Solr. Per usare questi passaggi, è necessario stabilire un tunnel SSL e quindi configurare il browser perché lo usi.
	>
	> Per altre informazioni, vedere [Usare il tunneling SSH per accedere all'interfaccia Web di Ambari, ResourceManager, JobHistory, NameNode, Oozie e altre interfacce Web](hdinsight-linux-ambari-ssh-tunnel.md).

2. Usare i comandi seguenti per indicizzare i dati di esempio tramite Solr:

		cd /usr/hdp/current/solr/example/exampledocs
		java -jar post.jar solr.xml monitor.xml

	Nella console viene visualizzato il seguente risultato:

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	L'utilità post.jar indicizza Solr con due documenti di esempio, **solr.xml** e **monitor.xml**. Verranno archiviati in __collection1__ all'interno di Solr.

3. Usare la stringa seguente per eseguire una query sull'API REST esposta da Solr:

		curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"

	Viene generata una query su __collection1__ per la ricerca di tutti i documenti che corrispondono a __*:*__ (codificato come *%3A* nella stringa di query) e la restituzione della risposta nel formato JSON. La risposta dovrebbe essere simile alla seguente:

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }

### Uso del dashboard di Solr

Il dashboard di Solr è un'interfaccia utente Web che consente di utilizzare Solr con un Web browser. Il dashboard di Solr non viene esposto direttamente su Internet dal cluster HDInsight, ma è necessario accedervi tramite un tunnel SSH. Per altre informazioni sull'uso di un tunnel SSH, vedere [Usare il tunneling SSH per accedere all'interfaccia Web di Ambari, ResourceManager, JobHistory, NameNode, Oozie e altre interfacce Web](hdinsight-linux-ambari-ssh-tunnel.md).

Dopo aver stabilito un tunnel SSH, seguire questa procedura per usare il dashboard di Solr:

1. Determinare il nome host per il nodo head:

    1. In un browser, passare a https://CLUSTERNAME.azurehdinsight.net. Quando richiesto, utilizzare il nome utente amministratore e la password per l'autenticazione nel sito.
    
    2. Dal menu nella parte superiore della pagina selezionare __Hosts__.
    
    3. Selezionare la voce che inizia con __hn0__. Quando viene visualizzata la pagina, il nome host viene visualizzato nella parte superiore. Il formato del nome host è __hn0- PARTOFCLUSTERNAME.randomcharacters.cx.internal.cloudapp.net__. Questo è il nome host da utilizzare quando ci si connette al dashboard Solr.
    
1. Nel browser, connettersi a __http://HOSTNAME:8983/solr/#/__, dove __HOSTNAME\_\_ è il nome stabilito nei passaggi precedenti.

    La richiesta dovrebbe essere inviata attraverso il tunnel SSH al nodo head per il cluster HDInsight. Verrà visualizzata una pagina simile alla seguente:

	![Immagine del dashboard di Solr](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

2. Nel riquadro sinistro selezionare**Core Selector** nell'elenco a discesa **collection1**. Sotto __collection1__ dovrebbero essere selezionate diverse voci.

3. Dalle voci elencate in __collection1__ selezionare __Query__. Usare i valori seguenti per popolare la pagina di ricerca:

	* Nella casella di testo **q** immettere ***:***. Verranno restituiti tutti i documenti indicizzati in Solr. Per cercare una stringa specifica nei documenti, è possibile immettere qui la stringa.

	* Selezionare il formato di output nella casella di testo **wt**. Il valore predefinito è **json**.

	Selezionare infine il pulsante **Execute Query** in basso nella pagina di ricerca.

	![Usare l'azione script per personalizzare un cluster](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

	L'output restituisce i due documenti usati per l'indicizzazione di Solr. L'output sarà simile al seguente:

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }

### Avviare e arrestare Solr

Se è necessario arrestare o avviare Solr manualmente, usare i comandi seguenti:

	sudo stop solr

	sudo start solr

## Eseguire il backup dei dati indicizzati

È consigliabile eseguire il backup dei dati indicizzati dai nodi del cluster Solr nell'archivio BLOB di Azure. Eseguire quindi la procedura seguente:

1. Connettersi al cluster tramite SSH, quindi usare il comando seguente per ottenere il nome host per il nodo head:

        hostname -f
        
2. Utilizzare quanto segue per creare uno snapshot dei dati indicizzati. Sostituire __HOSTNAME__ con il nome restituito dal comando precedente:

		curl http://HOSTNAME:8983/solr/replication?command=backup

	Viene visualizzata una risposta simile alla seguente:

		<?xml version="1.0" encoding="UTF-8"?>
		<response>
		  <lst name="responseHeader">
		    <int name="status">0</int>
		    <int name="QTime">9</int>
		  </lst>
		  <str name="status">OK</str>
		</response>

2. Passare quindi alla directory __/usr/hdp/current/solr/example/solr__. Per ogni raccolta sarà presente una sottodirectory. Ogni directory della raccolta contiene una directory __data__ in cui si trova lo snapshot per tale raccolta.

	Ad esempio, se sono stati usati i passaggi precedenti per indicizzare i documenti di esempio, la directory __/usr/hdp/current/solr/example/solr/collection1/data__ ora dovrebbe contenere una directory denominata __snapshot.###########__ dove i caratteri # sono la data e l'ora dello snapshot.

3. Creare un archivio compresso della cartella dello snapshot usando un comando simile al seguente:

		tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855

	Verrà creato un nuovo archivio denominato __snapshot.20150806185338855.tgz__ che include il contenuto della directory __snapshot.20150806185338855__.

3. Si potrà quindi inserire l'archivio nella risorsa di archiviazione primaria del cluster usando il comando seguente:

	hadoop fs -copyFromLocal snapshot.20150806185338855.tgz /example/data

	> [AZURE.NOTE] Per archiviare gli snapshot di Solr, è possibile creare una directory dedicata, ad esempio `hadoop fs -mkdir /solrbackup`.

Per altre informazioni sulle operazioni di backup e ripristino di Solr, vedere l'articolo relativo a [creazione e ripristino dei backup di SolrCores](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups+of+SolrCores).


## Vedere anche

- [Installare e usare Hue nei cluster HDInsight](hdinsight-hadoop-hue-linux.md). Hue è un'interfaccia utente che semplifica la creazione, l'esecuzione e il salvataggio di processi Pig e Hive, nonché l'esplorazione dell'archivio predefinito per il cluster HDInsight.

- [Installare R nei cluster HDInsight][hdinsight-install-r]. Usare la personalizzazione dei cluster per installare R nei cluster Hadoop di HDInsight. R è un linguaggio open source e un ambiente per l'elaborazione statistica. Fornisce centinaia di funzioni statistiche predefinite e un proprio linguaggio che combina aspetti di programmazione funzionale con aspetti di programmazione orientata agli oggetti. Offre inoltre funzionalità complete di grafica.

- [Installare Giraph in cluster HDInsight](hdinsight-hadoop-giraph-install-linux.md). Usare la personalizzazione cluster per installare Giraph in cluster Hadoop di HDInsight. Giraph permette di elaborare grafici con Hadoop e può essere usato con Azure HDInsight.

- [Installare Hue nei cluster HDInsight](hdinsight-hadoop-hue-linux.md). Usare la personalizzazione dei cluster per installare Hue nei cluster Hadoop di HDInsight. Hue è un insieme di applicazioni Web che consente di interagire con un cluster Hadoop.



[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md

<!---HONumber=AcomDC_0727_2016-->