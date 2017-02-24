---
title: Installare e usare Giraph in HDInsight basato su Linux (Hadoop) | Documentazione Microsoft
description: "Informazioni su come installare Giraph nei cluster HDInsight basati su Linux utilizzando azioni di Script. Le azioni di script consentono di personalizzare il cluster durante la creazione, modificando la configurazione del cluster o installando servizi e utilità."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9fcac906-8f06-4002-9fe8-473e42f8fd0f
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 5ec4b964066687b506686709c3dc5ed5b402fbaf
ms.openlocfilehash: 699626f24e6f66f4b149feda5eb292e64d4b6647


---
# <a name="install-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Installare Giraph nei cluster HDInsight Hadoop e usarlo per elaborare grafici su vasta scala

È possibile installare Giraph in qualsiasi tipo di cluster in Hadoop su Azure HDInsight usando la funzionalità **Azione script** per personalizzare il cluster.

Questo articolo spiega come installare Giraph usando le azioni script. Dopo l'installazione di Giraph, verranno fornite anche informazioni su come usare Giraph per la maggior parte delle applicazioni tipiche, ovvero per l'elaborazione di grafici di grandi dimensioni.

> [!IMPORTANT]
> I passaggi descritti in questo documento richiedono un cluster HDInsight che usa Linux. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="a-namewhatisawhat-is-giraph"></a><a name="whatis"></a>Che cos'è Giraph?

[Apache Giraph](http://giraph.apache.org/) consente di elaborare grafici con Hadoop e può essere usato con Azure HDInsight. È possibile usare i grafici per modellare le relazioni tra gli oggetti, ad esempio le connessioni tra router in una rete di grandi dimensioni, come Internet, oppure le relazioni tra persone iscritte a social network, come nel cosiddetto grafico dei social network. Grazie all'elaborazione del grafico è possibile ottenere informazioni dettagliate sulle relazioni tra gli oggetti in un grafico e in particolare di:

* Identificare possibili amici sulla base delle relazioni correnti.

* Identificare la route più breve tra due computer di una rete.

* Calcolare la posizione in classifica di pagine Web.

> [!WARNING]
> I componenti forniti con il cluster HDInsight sono supportati in modo completo e il supporto tecnico Microsoft contribuirà a isolare e risolvere i problemi correlati a questi componenti.
> 
> I componenti personalizzati, ad esempio Giraph, ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. È possibile che si ottenga la risoluzione dei problemi o che venga richiesto di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto approfondito per la tecnologia specifica. È ad esempio possibile ricorrere a molti siti di community, come il [forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight) o [http://stackoverflow.com](http://stackoverflow.com). Anche per i progetti Apache sono disponibili siti specifici in [http://apache.org](http://apache.org), ad esempio [Hadoop](http://hadoop.apache.org/).


## <a name="what-the-script-does"></a>Funzionalità dello script

Lo script esegue le azioni seguenti:

* Installa Giraph in `/usr/hdp/current/giraph`

* Copia il file `giraph-examples.jar` nell'archivio BLOB di Azure (WASB) predefinito del cluster: `/example/jars/giraph-examples.jar`

## <a name="a-nameinstallainstall-giraph-using-script-actions"></a><a name="install"></a>Installare Giraph mediante azioni script

Uno script di esempio per l'installazione di Giraph in un cluster HDInsight è disponibile all'indirizzo seguente.

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Questa sezione descrive come usare lo script di esempio per creare il cluster con il portale di Azure. 

> [!NOTE]
> Per applicare le azioni script è possibile usare anche Azure PowerShell, l'interfaccia della riga di comando di Azure, HDInsight .NET SDK o i modelli di Azure Resource Manager. È anche possibile applicare azioni script a cluster già in esecuzione. Per altre informazioni, vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md).

1. Avviare la creazione di un cluster utilizzando la procedura descritta in [Creazione di cluster HDInsight basati su Linux](hdinsight-hadoop-create-linux-clusters-portal.md), ma non completare la creazione.

2. Nel pannello **Configurazione facoltativa** selezionare **Azioni script** e specificare le informazioni seguenti:
   
   * **NOME**: immettere un nome descrittivo per l'azione script.

   * **URI SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **HEAD**: selezionare questa opzione

   * **LAVORO**: lasciare deselezionato

   * **ZOOKEEPER**: lasciare deselezionato

   * **PARAMETRI**: lasciare questo campo vuoto

3. Nella parte inferiore di **Azioni di script** usare il pulsante **Seleziona** per salvare la configurazione. Usare infine il pulsante **Seleziona** nella parte inferiore del pannello **Configurazione facoltativa** per salvare le informazioni relative alla configurazione facoltativa.

4. Continuare a creare il cluster come descritto nell'argomento relativo alla [creazione di cluster HDInsight basati su Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="a-nameusegiraphahow-do-i-use-giraph-in-hdinsight"></a><a name="usegiraph"></a>Come si usa Giraph in HDInsight?

Una volta completata la creazione di un cluster, usare la procedura seguente per eseguire l'esempio SimpleShortestPathsComputation incluso in Giraph. Viene usata l'implementazione di base di [Pregel](http://people.apache.org/~edwardyoon/documents/pregel.pdf) per trovare il percorso più breve tra gli oggetti in un grafico.

1. Connettersi al cluster HDInsight usando SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Per altre informazioni sull'uso di SSH con HDInsight, vedere gli articoli seguenti:
   
   * [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix, OS X o Windows](hdinsight-hadoop-linux-use-ssh-unix.md)

   * [Usare SSH con Hadoop basato su Linux in HDInsight da Windows con PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Usare la stringa seguente per creare un nuovo file denominato **tiny_graph.txt**:
   
    ```
    nano tiny_graph.txt
    ```
   
    Usare il codice seguente come contenuto di questo file:
   
    ```
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```
   
    Questi dati descrivono una relazione tra gli oggetti in un grafico diretto, usando il formato `[source_id, source_value,[[dest_id], [edge_value],...]]`. Ogni riga rappresenta una relazione tra un oggetto `source_id` e uno o più oggetti `dest_id`. Il valore `edge_value` (o peso) costituisce la potenza o la distanza della connessione tra `source_id` e `dest\_id`.
   
    I dati disegnati usando il valore (o peso) come distanza tra gli oggetti sono simili a quelli raffigurati di seguito:
   
    ![tiny_graph.txt drawn as circles with lines of varying distance between](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

3. Per salvare il file usare **CTRL+X**, quindi **Y** e infine premere **INVIO** per accettare il nome del file.

4. Usare il codice seguente per archiviare i dati nell'archiviazione primaria del cluster HDInsight:
   
    ```
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Eseguire l'esempio SimpleShortestPathsComputation tramite il seguente comando:
   
    ```
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```
   
    I parametri usati con questo comando sono descritti nella tabella seguente.
   
   | Parametro | Risultato |
   | --- | --- |
   | `jar /usr/hdp/current/giraph/giraph-examples.jar` |File JAR contenente gli esempi. |
   | `org.apache.giraph.GiraphRunner` |Classe usata per avviare gli esempi. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |L'esempio usato. In questo caso, viene calcolato il percorso più breve tra ID 1 e tutti gli altri ID nel grafico. |
   | `-ca mapred.job.tracker=headnodehost:9010` |Nodo head del cluster. |
   | `-vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFromat` |Formato di input da usare per i dati di input. |
   | `-vip /example/data/tiny_graph.txt` |File di dati di input. |
   | `-vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat` |Formato di output. In questo caso, ID e valore come testo normale. |
   | `-op /example/output/shortestpaths` |Percorso di output. |
   | `-w 2` |Numero di ruoli di lavoro da usare. In questo caso, 2. |
   
    Per altre informazioni su questi e altri parametri usati con gli esempi di Giraph, vedere la [Guida introduttiva a Giraph](http://giraph.apache.org/quick_start.html).

6. Al termine del processo, i risultati vengono archiviati nella directory **/example/out/shortestpaths**. I file di output iniziano con **part-m-** e terminano con un numero che indica il primo file, il secondo e così via. Usare la stringa seguente per ver visualizzare l'output:
   
    ```
    hdfs dfs -text /example/output/shortestpaths/*
    ```
   
    L'output sarà simile al seguente:
   
        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0
   
    L'esempio SimpleShortestPathComputation è hardcoded in modo da essere avviato con l'ID oggetto 1 e individuare il percorso più breve ad altri oggetti. L'output dovrebbe quindi essere `destination_id distance`, in cui distance è il valore (o il peso) dei confini attraversati tra l'ID oggetto 1 e l'ID di destinazione.
   
    Con questa visualizzazione è possibile verificare i risultati attraversando i percorsi più brevi tra l'ID 1 e tutti gli altri oggetti. Notare che il percorso più breve tra l'ID 1 e l'ID 4 è 5, che corrisponde alla distanza totale tra <span style="color:orange">ID 1 e 3</span> e quindi tra <span style="color:red">ID 3 e 4</span>.
   
    ![Drawing of objects as circles with shortest paths drawn between](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)

## <a name="next-steps"></a>Passaggi successivi

* [Installare e usare Hue nei cluster HDInsight](hdinsight-hadoop-hue-linux.md). Hue è un'interfaccia utente che semplifica la creazione, l'esecuzione e il salvataggio di processi Pig e Hive, nonché l'esplorazione dell'archivio predefinito per il cluster HDInsight.

* [Installare R in cluster HDInsight](hdinsight-hadoop-r-scripts-linux.md): istruzioni su come usare la personalizzazione dei cluster per installare e usare R nei cluster Hadoop di HDInsight. R è un linguaggio open source e un ambiente per l'elaborazione statistica. Fornisce centinaia di funzioni statistiche predefinite e un proprio linguaggio che combina aspetti di programmazione funzionale con aspetti di programmazione orientata agli oggetti. Offre inoltre funzionalità complete di grafica.

* [Installare Solr in cluster HDInsight](hdinsight-hadoop-solr-install-linux.md). Usare la personalizzazione cluster per installare Solr in cluster Hadoop di HDInsight. Solr consente di eseguire operazioni di ricerca avanzate sui dati archiviati.




<!--HONumber=Feb17_HO2-->


