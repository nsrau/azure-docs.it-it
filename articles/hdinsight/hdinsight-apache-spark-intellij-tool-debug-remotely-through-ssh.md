---
title: "Azure Toolkit for IntelliJ: debug delle applicazioni Spark in modalità remota tramite SSH | Microsoft Docs"
description: Istruzioni dettagliate su come usare gli strumenti HDInsight in Azure Toolkit for IntelliJ per eseguire il debug remoto di applicazioni in cluster di HDInsight tramite SSH
keywords: eseguire debug remoto di intellij, debug remoto di intellij, ssh, intellij, hdinsight, debug di intellij, debug
services: hdinsight
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 08/24/2017
ms.author: Jenny Jiang
ms.openlocfilehash: 19053e31d6eb097bc91a04ef9c6af5772aaa16da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="debug-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Eseguire il debug delle applicazioni Spark su un cluster HDInsight con Azure Toolkit for IntelliJ tramite SSH

Questo articolo contiene istruzioni dettagliate su come usare gli strumenti HDInsight in Azure Toolkit for IntelliJ per eseguire il debug remoto di applicazioni in un cluster di HDInsight. Per eseguire il debug del progetto, è anche possibile guardare il video [Debug HDInsight Spark applications with Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Eseguire il debug delle applicazioni Spark di HDInsight con Azure Toolkit for IntelliJ).

**Prerequisiti**

* **Strumenti HDInsight in Azure Toolkit per IntelliJ**. Questo strumento fa parte di Azure Toolkit for IntelliJ. Per altre informazioni, vedere [Installare Azure Toolkit for IntelliJ](https://docs.microsoft.com/en-us/azure/azure-toolkit-for-intellij-installation).
* **Azure Toolkit per IntelliJ**. Usare questo toolkit per creare applicazioni Spark per cluster HDInsight. Per altre informazioni, seguire le istruzioni in [Usare Azure Toolkit per IntelliJ per creare applicazioni Spark per il cluster HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).
* **Servizio SSH di HDInsight con gestione di nome utente e password**. Per altre informazioni, vedere [Connettersi a HDInsight (Hadoop) con SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) e [Usare il tunneling SSH per accedere all'interfaccia utente Web di Ambari, JobHistory, NameNode, Oozie e altre interfacce utente Web](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 

## <a name="create-a-spark-scala-application-and-configure-it-for-remote-debugging"></a>Creare un'applicazione Scala Spark e configurarla per il debug remoto

1. Avviare IntelliJ IDEA e creare un progetto. Nella finestra di dialogo **New Project** (Nuovo progetto) seguire questa procedura:

   a. Selezionare **HDInsight**. 

   b. Selezionare un modello Java o Scala in base alle preferenze. Scegliere una delle opzioni seguenti:

      - **Spark in HDInsight (Scala)**

      - **Spark in HDInsight (Java)**

      - **Esecuzione di esempio di Spark in un cluster HDInsight (Scala)**

      In questo esempio si usa un modello di **esempio per l'esecuzione di Spark in un cluster HDInsight (Scala)**.

   c. Nell'elenco **Build tool** (Strumento di compilazione) selezionare uno degli strumenti seguenti, in base alla necessità:

      - **Maven**, per ottenere supporto per la creazione guidata di un progetto Scala

      -  **SBT**, per la gestione delle dipendenze e la compilazione per il progetto Scala 

      ![Creare un progetto di debug](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-create-projectfor-debug-remotely.png)

   d. Selezionare **Avanti**.     
 
3. Nella finestra **New Project** (Nuovo progetto) successiva seguire questa procedura:

   ![Selezionare l'SDK di Spark](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-new-project.png)

   a. Specificare un nome per il progetto e il relativo percorso.

   b. Nell'elenco a discesa **Project SDK** (SDK progetto) selezionare **Java 1.8** per il cluster **Spark 2.x** oppure **Java 1.7** per il cluster **Spark 1.x**.

   c. Nell'elenco a discesa **Spark version** (Versione di Spark) la creazione guidata del progetto Scala inserisce la versione corretta per Spark SDK e Scala SDK. Se la versione del cluster Spark è precedente alla 2.0, selezionare **Spark 1.x**. In caso contrario, selezionare **Spark 2.x**. In questo esempio viene usata la versione **Spark 2.0.2 (Scala 2.11.8)**.

   d. Selezionare **Fine**.

4. Selezionare **src** > **main** > **scala** per aprire il codice nel progetto. In questo esempio viene usato lo script **SparkCore_wasbloTest**.

5. Per accedere al menu **Edit Configurations** (Modifica configurazioni) selezionare l'icona nell'angolo in alto a destra. Da questo menu è possibile creare o modificare le configurazioni per il debug remoto.

   ![Modifica configurazioni](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-edit-configurations.png) 

6. Nella finestra di dialogo **Run/Debug Configurations** (Esegui/Debug delle configurazioni) selezionare il segno più (**+**). Selezionare quindi l'opzione **Submit Spark Job** (Invia processo Spark).

   ![Aggiungere una nuova configurazione](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-add-new-Configuration.png)
7. Immettere le informazioni per **Nome**, **Cluster Spark**, e **Nome della classe principale**. Selezionare quindi **Configurazione avanzata**. 

   ![Eseguire configurazioni di debug](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-debug-configurations.png)

8. Nella finestra di dialogo **Spark Submission Advanced Configuration** (Configurazione avanzata invio Spark) selezionare **Enable Spark remote debug** (Abilita debug remoto Spark). Immettere il nome utente SSH e inserire una password oppure usare un file di chiave privata. Selezionare **OK** per salvare la configurazione.

   ![Abilitare il debug remoto di Spark](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-enable-spark-remote-debug.png)

9. A questo punto, la configurazione viene salvata con il nome specificato. Per visualizzare i dettagli della configurazione, selezionare il relativo nome. Per apportare modifiche, selezionare **Modifica configurazioni**. 

10. Dopo aver completato le impostazioni di configurazione, è possibile eseguire il progetto con il cluster remoto oppure eseguire il debug remoto.

## <a name="learn-how-to-perform-remote-debugging"></a>Informazioni su come effettuare il debug remoto
### <a name="scenario-1-perform-remote-run"></a>Scenario 1: Esecuzione remota

In questa sezione è illustrato come eseguire il debug di driver ed executor.

    import org.apache.spark.{SparkConf, SparkContext}

    object LogQuery {
      val exampleApacheLogs = List(
        """10.10.10.10 - "FRED" [18/Jan/2013:17:56:07 +1100] "GET http://images.com/2013/Generic.jpg
          | HTTP/1.1" 304 315 "http://referall.com/" "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1;
          | GTB7.4; .NET CLR 2.0.50727; .NET CLR 3.0.04506.30; .NET CLR 3.0.04506.648; .NET CLR
          | 3.5.21022; .NET CLR 3.0.4506.2152; .NET CLR 1.0.3705; .NET CLR 1.1.4322; .NET CLR
          | 3.5.30729; Release=ARP)" "UD-1" - "image/jpeg" "whatever" 0.350 "-" - "" 265 923 934 ""
          | 62.24.11.25 images.com 1358492167 - Whatup""".stripMargin.lines.mkString,
        """10.10.10.10 - "FRED" [18/Jan/2013:18:02:37 +1100] "GET http://images.com/2013/Generic.jpg
          | HTTP/1.1" 304 306 "http:/referall.com" "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1;
          | GTB7.4; .NET CLR 2.0.50727; .NET CLR 3.0.04506.30; .NET CLR 3.0.04506.648; .NET CLR
          | 3.5.21022; .NET CLR 3.0.4506.2152; .NET CLR 1.0.3705; .NET CLR 1.1.4322; .NET CLR
          | 3.5.30729; Release=ARP)" "UD-1" - "image/jpeg" "whatever" 0.352 "-" - "" 256 977 988 ""
          | 0 73.23.2.15 images.com 1358492557 - Whatup""".stripMargin.lines.mkString
      )
      def main(args: Array[String]) {
        val sparkconf = new SparkConf().setAppName("Log Query")
        val sc = new SparkContext(sparkconf)
        val dataSet = sc.parallelize(exampleApacheLogs)
        // scalastyle:off
        val apacheLogRegex =
          """^([\d.]+) (\S+) (\S+) \[([\w\d:/]+\s[+\-]\d{4})\] "(.+?)" (\d{3}) ([\d\-]+) "([^"]+)" "([^"]+)".*""".r
        // scalastyle:on
        /** Tracks the total query count and number of aggregate bytes for a particular group. */
        class Stats(val count: Int, val numBytes: Int) extends Serializable {
          def merge(other: Stats): Stats = new Stats(count + other.count, numBytes + other.numBytes)
          override def toString: String = "bytes=%s\tn=%s".format(numBytes, count)
        }
        def extractKey(line: String): (String, String, String) = {
          apacheLogRegex.findFirstIn(line) match {
            case Some(apacheLogRegex(ip, _, user, dateTime, query, status, bytes, referer, ua)) =>
              if (user != "\"-\"") (ip, user, query)
              else (null, null, null)
            case _ => (null, null, null)
          }
        }
        def extractStats(line: String): Stats = {
          apacheLogRegex.findFirstIn(line) match {
            case Some(apacheLogRegex(ip, _, user, dateTime, query, status, bytes, referer, ua)) =>
              new Stats(1, bytes.toInt)
            case _ => new Stats(1, 0)
          }
        }
        
        dataSet.map(line => (extractKey(line), extractStats(line)))
          .reduceByKey((a, b) => a.merge(b))
          .collect().foreach{
          case (user, query) => println("%s\t%s".format(user, query))}

        sc.stop()
      }
    }


1. Impostare i punti di interruzione e quindi selezionare l'icona **Debug**.

   ![Selezionare l'icona Debug](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-icon.png)

2. Quando l'esecuzione del programma raggiunge il punto di interruzione, nel riquadro **Debugger** vengono visualizzate la scheda **Diver** e le due schede **Executor**. Selezionare l'icona **Resume Program** (Riprendi programma) per continuare a eseguire il codice, che quindi raggiunge il punto di interruzione successivo e si concentra sulla scheda **Executor** corrispondente. È possibile esaminare i log di esecuzione nella scheda **Console** corrispondente.

   ![Scheda di debug](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debugger-tab.png)

### <a name="scenario-2-perform-remote-debugging-and-bug-fixing"></a>Scenario 2: eseguire il debug e la correzione di bug da remoto
In questa sezione viene illustrato come aggiornare in modo dinamico il valore variabile usando la funzionalità di debug di IntelliJ per una correzione semplificata. Nell'esempio di codice seguente viene generata un'eccezione poiché il file di destinazione esiste già.
  
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext

        object SparkCore_WasbIOTest {
          def main(arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkCore_WasbIOTest")
            val sc = new SparkContext(conf)
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            // Find the rows that have only one digit in the sixth column.
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)

            try {
              var target = "wasb:///HVACout2_testdebug1";
              rdd1.saveAsTextFile(target);
            } catch {
              case ex: Exception => {
                throw ex;
              }
            }
          }
        }


#### <a name="to-perform-remote-debugging-and-bug-fixing"></a>Per eseguire il debug e la correzione di bug da remoto
1. Impostare due punti di interruzione e quindi selezionare l'icona **Debug** per avviare il processo di debug remoto.

2. Il codice si interrompe in corrispondenza del primo punto di interruzione e le informazioni di parametro e variabile vengono visualizzate nella finestra **Variables** (Variabili). 

3. Selezionare l'icona **Resume Program** (Riprendi programma) per continuare. Il codice si interrompe in corrispondenza del secondo punto. L'eccezione viene rilevata come previsto.

  ![Errore di generazione](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-throw-error.png) 

4. Selezionare di nuovo l'icona **Resume Program** (Riprendi programma). La finestra **HDInsight Spark Submission** (Invio Spark in HDInsight) mostra un errore di "esecuzione processo non riuscita".

  ![Errore nell'invio](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-error-submission.png) 

5. Per aggiornare dinamicamente il valore della variabile usando la funzionalità di debug di IntelliJ, selezionare nuovamente **Debug**. Viene visualizzato di nuovo il riquadro **Variables** (Variabili). 

6. Fare clic con il pulsante destro del mouse sulla destinazione nella scheda **Debug**, quindi selezionare **Imposta valore**. Successivamente inserire un nuovo valore per la variabile. e selezionare **Invia** per salvarlo. 

  ![Impostare il valore](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-set-value.png) 

7. Selezionare l'icona **Resume Program** (Riprendi programma) per continuare a eseguire il programma. Questa volta, non viene rilevata alcuna eccezione. È possibile notare come il progetto venga eseguito correttamente senza eccezioni.

  ![Debug senza eccezioni](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Passaggi successivi
* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="demo"></a>Demo
* Creare il progetto Scala (video): [Create Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (Creare applicazioni Spark Scala)
* Debug remoto (video): [Usare Azure Toolkit for IntelliJ per eseguire il debug remoto di applicazioni Spark nel cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenari
* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: usare Spark in HDInsight per l'analisi della temperatura di compilazione usando dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per stimare i risultati dell'ispezione cibo](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)
* [Analisi dei log del sito Web mediante Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni
* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni
* [Usare Azure Toolkit for IntelliJ per creare applicazioni Spark per un cluster HDInsight](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Usare Azure Toolkit per IntelliJ per il debug remoto di applicazioni Spark tramite VPN](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare gli strumenti HDInsight per IntelliJ con Hortonworks Sandbox](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Use HDInsight Tools in Azure Toolkit for Eclipse to create Spark applications (Usare gli strumenti HDInsight nel Toolkit di Azure per Eclipse per creare applicazioni Spark)](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-job-debugging.md)
