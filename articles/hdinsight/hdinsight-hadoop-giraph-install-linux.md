<properties
	pageTitle="Installare e usare Giraph in HDInsight basati su Linux (Hadoop) | Microsoft Azure"
	description="Informazioni su come installare Giraph nei cluster HDInsight basati su Linux utilizzando azioni di Script. Le azioni di script consentono di personalizzare il cluster durante la creazione, modificando la configurazione del cluster o installando servizi e utilità."
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
	ms.date="02/29/2016"
	ms.author="larryfr"/>

# Installare Giraph nei cluster HDInsight Hadoop e usarlo per elaborare grafici su vasta scala

È possibile installare Giraph in qualsiasi tipo di cluster in Hadoop su Azure HDInsight usando la funzionalità **Azione script** per personalizzare il cluster.

In questo argomento si apprenderà come installare Giraph usando le azioni script. Dopo l'installazione di Giraph, verranno fornite anche informazioni su come usare Giraph per la maggior parte delle applicazioni tipiche, ovvero per l'elaborazione di grafici di grandi dimensioni.

> [AZURE.NOTE] Le informazioni contenute in questo articolo sono specifiche per i cluster HDInsight basati su Linux. Per informazioni sull'utilizzo di cluster basati su Windows, vedere [Installare Giraph nei cluster Hadoop HDInsight (Windows)](hdinsight-hadoop-giraph-install.md)

## <a name="whatis"></a>Che cos'è Giraph?

[Apache Giraph](http://giraph.apache.org/) consente di elaborare grafici con Hadoop e può essere usato con Azure HDInsight. È possibile usare i grafici per modellare le relazioni tra gli oggetti, ad esempio le connessioni tra router in una rete di grandi dimensioni, come Internet, oppure le relazioni tra persone iscritte a social network, come nel cosiddetto grafico dei social network. Grazie all'elaborazione del grafico è possibile ottenere informazioni dettagliate sulle relazioni tra gli oggetti in un grafico e in particolare di:

- Identificare possibili amici sulla base delle relazioni correnti.
- Identificare la route più breve tra due computer di una rete.
- Calcolare la posizione in classifica di pagine Web.

##Funzionalità dello script

Lo script esegue le azioni seguenti:

* Installa Giraph in `/usr/hdp/current/giraph`
* Copia il file `giraph-examples.jar` nell'archivio BLOB di Azure (WASB) predefinito del cluster: `/example/jars/giraph-examples.jar`

## <a name="install"></a>Installare Giraph mediante azioni script

Uno script di esempio per l'installazione di Giraph in un cluster HDInsight è disponibile all'indirizzo seguente.

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Questa sezione descrive come usare lo script di esempio per creare il cluster con il portale di Azure.

> [AZURE.NOTE] Per applicare le azioni script è possibile usare anche Azure PowerShell, l'interfaccia della riga di comando di Azure, HDInsight .NET SDK o i modelli di Azure Resource Manager. È anche possibile applicare azioni script a cluster già in esecuzione. Per altre informazioni, vedere [Personalizzare cluster HDInsight tramite azioni script](hdinsight-hadoop-customize-cluster-linux.md).

1. Avviare la creazione di un cluster utilizzando la procedura descritta in [Creazione di cluster HDInsight basati su Linux](hdinsight-provision-linux-clusters.md#portal), ma non completare la creazione.

2. Nel pannello **Configurazione facoltativa** selezionare **Azioni di script** e specificare le informazioni seguenti:

	* __NOME__: immettere un nome descrittivo per l'azione script.
	* __URI SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
	* __HEAD__: selezionare questa opzione.
	* __LAVORO__: selezionare questa opzione.
	* __ZOOKEEPER__: selezionare questa opzione per l'installazione nel nodo Zookeeper
	* __PARAMETERS__: lasciare questo campo vuoto

3. Nella parte inferiore di **Azioni di script** usare il pulsante **Seleziona** per salvare la configurazione. Usare infine il pulsante **Seleziona** nella parte inferiore del pannello **Configurazione facoltativa** per salvare le informazioni relative alla configurazione facoltativa.

4. Continuare a creare il cluster come descritto nell'argomento relativo alla [creazione di cluster HDInsight basati su Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Come si usa Giraph in HDInsight?

Una volta completata la creazione di un cluster, usare la procedura seguente per eseguire l'esempio SimpleShortestPathsComputation incluso in Giraph. Viene eseguita l'implementazione di base di <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> per trovare il percorso più breve tra gli oggetti in un grafico.

1. Connettersi al cluster HDInsight usando SSH:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	Per altre informazioni sull'uso di SSH con HDInsight, vedere gli articoli seguenti:

	* [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

1. Usare la stringa seguente per creare un nuovo file denominato __tiny\_graph.txt__:

		nano tiny_graph.txt

	Usare il codice seguente come contenuto di questo file:

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	Questi dati descrivono una relazione tra gli oggetti in un grafico diretto, usando il formato [source\_id, source\_value,[[dest\_id], [edge\_value],...]]. Ogni riga rappresenta una relazione tra un oggetto **source\_id** e uno o più oggetti **dest\_id**. Il valore **edge\_value** (o peso) costituisce la potenza o la distanza della connessione tra **source\_id** e **dest\_id**.

	I dati disegnati usando il valore (o peso) come distanza tra gli oggetti sono simili a quelli raffigurati di seguito:

	![tiny\_graph.txt drawn as circles with lines of varying distance between](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

2. Per salvare il file usare __CTRL+X__, quindi __Y__ e infine premere __INVIO__ per accettare il nome del file.

3. Usare il codice seguente per archiviare i dati nell'archiviazione primaria del cluster HDInsight:

		hadoop fs -copyFromLocal tiny_graph.txt /example/data/tiny_graph.txt

3. Per ottenere il nome di dominio completo (FQDN) del nodo head del cluster, utilizzare quanto segue:

        hostname -f
        
4. Eseguire l'esempio SimpleShortestPathsComputation tramite il seguente comando: Sostituire __NODO HEAD__ con il nome FQDN restituito dal passaggio precedente:

		 hadoop jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=HEADNODE:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2

	I parametri usati con questo comando sono descritti nella tabella seguente.

	| Parametro | Risultato |
	| --------- | ------------ |
	| `jar /usr/hdp/current/giraph/giraph-examples.jar` | File JAR contenente gli esempi. |
	| `org.apache.giraph.GiraphRunner` | Classe usata per avviare gli esempi. |
	| `org.apache.giraph.examples.SimpleShortestPathsCoputation` | Esempio che verrà eseguito. In questo caso, calcolerà il percorso più breve tra ID 1 e tutti gli altri ID nel grafico. |
	| `-ca mapred.job.tracker=HEADNODE:9010` | Nodo head del cluster. |
	| `-vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFromat` | Formato di input da usare per i dati di input. |
	| `-vip /example/data/tiny_graph.txt` | File di dati di input. |
	| `-vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat` | Formato di output. In questo caso, ID e valore come testo normale. |
	| `-op /example/output/shortestpaths` | Percorso di output. |
	| `-w 2` | Numero di ruoli di lavoro da usare. In questo caso, 2. |

	Per altre informazioni su questi e altri parametri usati con gli esempi di Giraph, vedere la [Guida introduttiva a Giraph](http://giraph.apache.org/quick_start.html).

5. Al termine del processo, i risultati verranno archiviati nella directory \_\___wasb:///example/out/shotestpaths__. I file creati inizieranno con __part-m-__ e termineranno con un numero che indica il primo file, il secondo e così via. Usare la stringa seguente per ver visualizzare l'output:

		hadoop fs -text /example/output/shortestpaths/*

	L'output sarà simile al seguente:

		0	1.0
		4	5.0
		2	2.0
		1	0.0
		3	1.0

	L'esempio SimpleShortestPathComputation è hardcoded in modo da essere avviato con l'ID oggetto 1 e individuare il percorso più breve ad altri oggetti. L'output dovrebbe quindi essere `destination_id distance`, in cui distance è il valore (o il peso) dei confini attraversati tra l'ID oggetto 1 e l'ID di destinazione.

	Con questa visualizzazione è possibile verificare i risultati attraversando i percorsi più brevi tra l'ID 1 e tutti gli altri oggetti. Notare che il percorso più breve tra l'ID 1 e l'ID 4 è 5, che corrisponde alla distanza totale tra <span style="color:orange">ID 1 e 3</span> e quindi tra <span style="color:red">ID 3 e 4</span>.

	![Drawing of objects as circles with shortest paths drawn between](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)


## Passaggi successivi

- [Installare e usare Hue nei cluster HDInsight](hdinsight-hadoop-hue-linux.md). Hue è un'interfaccia utente che semplifica la creazione, l'esecuzione e il salvataggio di processi Pig e Hive, nonché l'esplorazione dell'archivio predefinito per il cluster HDInsight.

- [Installare R in cluster HDInsight](hdinsight-hadoop-r-scripts-linux.md): istruzioni su come usare la personalizzazione dei cluster per installare e usare R nei cluster Hadoop di HDInsight. R è un linguaggio open source e un ambiente per l'elaborazione statistica. Fornisce centinaia di funzioni statistiche predefinite e un proprio linguaggio che combina aspetti di programmazione funzionale con aspetti di programmazione orientata agli oggetti. Offre inoltre funzionalità complete di grafica.

- [Installare Solr in cluster HDInsight](hdinsight-hadoop-solr-install-linux.md). Usare la personalizzazione cluster per installare Solr in cluster Hadoop di HDInsight. Solr consente di eseguire operazioni di ricerca avanzate sui dati archiviati.

<!---HONumber=AcomDC_0406_2016-->