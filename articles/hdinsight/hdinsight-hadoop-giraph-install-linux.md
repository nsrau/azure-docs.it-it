---
title: Installare e usare Giraph in Azure HDInsight
description: Informazioni su come installare Giraph nei cluster HDInsight mediante azioni script. È possibile usare Giraph per l'elaborazione di grafici in Apache Hadoop nel cloud di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: f1ca536ffa2166df4ef6cf51654b7b410e72ea66
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962031"
---
# <a name="install-apache-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Installare Apache Giraph nei cluster HDInsight Hadoop e usarlo per elaborare grafici su vasta scala

Informazioni su come installare Apache Giraph in un cluster HDInsight. La funzione di azione script di HDInsight consente di personalizzare il cluster eseguendo uno script bash. È possibile usare gli script per personalizzare i cluster sia durante che dopo la creazione del cluster.

## <a name="whatis"></a>Che cos'è Giraph?

[Apache Giraph](https://giraph.apache.org/) consente di elaborare grafici con Hadoop e può essere usato con Azure HDInsight. È possibile usare i grafici per modellare le relazioni tra gli oggetti, ad esempio le connessioni tra router in una rete di grandi dimensioni, come Internet, oppure le relazioni tra persone iscritte a social network. Grazie all'elaborazione del grafico è possibile ottenere informazioni dettagliate sulle relazioni tra gli oggetti in un grafico e in particolare di:

* Identificare possibili amici sulla base delle relazioni correnti.

* Identificare la route più breve tra due computer di una rete.

* Calcolare la posizione in classifica di pagine Web.

> [!WARNING]  
> I componenti forniti con il cluster HDInsight sono supportati in modo completo e il supporto tecnico Microsoft contribuirà a isolare e risolvere i problemi correlati a questi componenti.
>
> I componenti personalizzati, ad esempio Giraph, ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. È possibile che il supporto tecnico Microsoft sia in grado di risolvere il problema. In caso contrario, è necessario consultare le community open source, da cui è possibile ottenere supporto approfondito per la tecnologia specifica. È ad esempio possibile ricorrere a molti siti di community, come: il [forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Anche per i progetti Apache sono disponibili siti specifici in [https://apache.org](https://apache.org), per esempio: [Hadoop](https://hadoop.apache.org/).


## <a name="what-the-script-does"></a>Funzionalità dello script

Lo script esegue le azioni seguenti:

* Installa Giraph in `/usr/hdp/current/giraph`

* Copia il file `giraph-examples.jar` nell'archivio BLOB di Azure (WASB) predefinito del cluster: `/example/jars/giraph-examples.jar`

## <a name="install"></a>Installare Giraph mediante azioni script

Uno script di esempio per l'installazione di Giraph in un cluster HDInsight è disponibile all'indirizzo seguente:

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Questa sezione fornisce istruzioni su come usare lo script di esempio quando si crea il cluster usando il portale di Azure.

> [!NOTE]  
> Un'azione script può essere applicata usando uno dei metodi seguenti:
> * Azure PowerShell
> * Interfaccia della riga di comando di Azure
> * HDInsight .NET SDK
> * Modelli di Gestione risorse di Azure
> 
> È anche possibile applicare azioni script a cluster già in esecuzione. Per altre informazioni, vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md).

1. Avviare la creazione di un cluster utilizzando la procedura descritta in [Creazione di cluster HDInsight basati su Linux](hdinsight-hadoop-create-linux-clusters-portal.md), ma non completare la creazione.

2. Nella sezione **Configurazione facoltativa** selezionare **Azioni script** e specificare le informazioni seguenti:

   * **NOME**: Immettere un nome descrittivo per l'azione script.

   * **URI SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **HEAD**: selezionare questa voce.

   * **WORKER**: lasciare questa voce deselezionata.

   * **ZOOKEEPER**: lasciare questa voce deselezionata.

   * **PARAMETERS**: lasciare vuoto questo campo.

3. Nella parte inferiore di **Azioni di script** usare il pulsante **Seleziona** per salvare la configurazione. Usare infine il pulsante **Seleziona** nella parte inferiore della sezione **Configurazione facoltativa** per salvare le informazioni relative alla configurazione facoltativa.

4. Continuare a creare il cluster come descritto nell'argomento relativo alla [creazione di cluster HDInsight basati su Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Come si usa Giraph in HDInsight?

Dopo aver creato il cluster, usare la procedura seguente per eseguire l'esempio SimpleShortestPathsComputation incluso in Giraph. Viene usata l'implementazione di base di [Pregel](https://people.apache.org/~edwardyoon/documents/pregel.pdf) per trovare il percorso più breve tra gli oggetti in un grafico.

1. Connettersi al cluster HDInsight usando SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Usare il comando seguente per creare un file denominato **tiny_graph.txt**:

    ```bash
    nano tiny_graph.txt
    ```

    Usare il testo seguente come contenuto del file:

    ```text
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```

    Questi dati descrivono una relazione tra gli oggetti in un grafico diretto, usando il formato `[source_id, source_value,[[dest_id], [edge_value],...]]`. Ogni riga rappresenta una relazione tra un oggetto `source_id` e uno o più oggetti `dest_id`. Il valore `edge_value` può essere considerato come la potenza o la distanza della connessione tra `source_id` e `dest\_id`.

    I dati disegnati usando il valore (o peso) come distanza tra gli oggetti possono essere simili a quelli raffigurati nel diagramma seguente:

    ![tiny_graph.txt drawn as circles with lines of varying distance between](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph.png)

3. Per salvare il file usare **CTRL+X**, quindi **Y** e infine premere **INVIO** per accettare il nome del file.

4. Usare il codice seguente per archiviare i dati nell'archiviazione primaria del cluster HDInsight:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Eseguire l'esempio SimpleShortestPathsComputation con il comando seguente:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    I parametri usati con questo comando sono descritti nella tabella seguente:

   | Parametro | Risultato |
   | --- | --- |
   | `jar` |File JAR contenente gli esempi. |
   | `org.apache.giraph.GiraphRunner` |Classe usata per avviare gli esempi. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |L'esempio usato. In questo esempio, viene calcolato il percorso più breve tra ID 1 e tutti gli altri ID nel grafico. |
   | `-ca mapred.job.tracker` |Nodo head del cluster. |
   | `-vif` |Formato di input da usare per i dati di input. |
   | `-vip` |File di dati di input. |
   | `-vof` |Formato di output. In questo caso, ID e valore come testo normale. |
   | `-op` |Percorso di output. |
   | `-w 2` |Numero di ruoli di lavoro da usare. In questo esempio, 2. |

    Per altre informazioni su questi e altri parametri usati con gli esempi di Giraph, vedere la [Guida introduttiva a Giraph](https://giraph.apache.org/quick_start.html).

6. Al termine del processo, i risultati vengono archiviati nella directory **/example/out/shortestpaths** I file di output iniziano con **part-m-** e terminano con un numero che indica il primo file, il secondo e così via. Usare il comando seguente per visualizzare l'output:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    L'output appare simile al seguente testo:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    L'esempio SimpleShortestPathComputation è hardcoded in modo da essere avviato con l'ID oggetto 1 e individuare il percorso più breve ad altri oggetti. L'output è in formato `destination_id` e `distance`. In particolare, `distance` rappresenta il valore (o il peso) dei confini attraversati tra l'oggetto ID 1 e l'ID di destinazione.

    Con questa visualizzazione dei dati è possibile verificare i risultati attraversando i percorsi più brevi tra ID 1 e tutti gli altri oggetti. Osservare come il percorso più breve tra ID 1 e ID 4 sia 5, che corrisponde alla distanza totale tra <span style="color:orange">ID 1 e 3</span> e quindi tra <span style="color:red">ID 3 e 4</span>.

    ![Drawing of objects as circles with shortest paths drawn between](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph-out.png)

## <a name="next-steps"></a>Passaggi successivi

* [Installare e usare Hue nei cluster HDInsight](hdinsight-hadoop-hue-linux.md).
