---
title: "Azure Toolkit per IntelliJ: debug delle applicazioni Spark in modalità remota tramite ssh | Microsoft Docs"
description: Istruzioni dettagliate su come usare gli Strumenti HDInsight in Azure Toolkit per IntelliJ per il debug remoto di applicazioni in cluster di HDInsight tramite ssh
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
ms.date: 06/05/2017
ms.author: Jenny Jiang
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 5ee6504cd4bf69c8f2c14a3623ff537e66030ce1
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="remotely-debug-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Eseguire il debug remoto delle applicazioni Spark su un cluster HDInsight con Azure Toolkit per IntelliJ tramite SSH

Questo articolo contiene istruzioni dettagliate su come usare gli Strumenti HDInsight in Azure Toolkit per IntelliJ per il debug remoto di applicazioni in un cluster di HDInsight. È possibile seguire questo [video](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) per eseguire il debug del progetto.

**Prerequisiti:**

* **Strumenti HDInsight in Azure Toolkit per IntelliJ**. Parte di Azure Toolkit per IntelliJ. Per altre informazioni, vedere [Installazione di Azure Toolkit per IntelliJ](https://docs.microsoft.com/en-us/azure/azure-toolkit-for-intellij-installation).
* **Azure Toolkit per IntelliJ**. Usare questo strumento per creare applicazioni Spark per cluster HDInsight. Per altre informazioni, seguire le istruzioni in [Usare Azure Toolkit per IntelliJ per creare applicazioni Spark per il cluster HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).
* **Servizio SSH di HDInsight con gestione di nome utente e password.** Per altre informazioni, vedere [Connettersi a HDInsight (Hadoop) con SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) e [Usare il tunneling SSH per accedere all'interfaccia utente Web di Ambari, JobHistory, NameNode, Oozie e altre interfacce utente Web](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 

## <a name="create-a-spark-scala-application-and-configure-it-for-remote-debugging"></a>Creare un'applicazione Scala Spark e configurarla per il debug remoto
1. Avviare IntelliJ IDEA e creare un progetto. Nella finestra di dialogo del nuovo progetto selezionare le opzioni seguenti e quindi fare clic su **Next** (Avanti). In questo articolo si usa un'**esecuzione di esempio di Spark in un cluster HDInsight (Scala)** come esempio.

     ![Creare un progetto di debug](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-create-projectfor-debug-remotely.png)
   - Selezionare **HDInsight** nel riquadro sinistro.
   - Nel riquadro di destra selezionare un modello Java o Scala in base alle preferenze. Scegliere una delle opzioni seguenti: 
      - **Spark in HDInsight (Scala)**
      - **Spark in HDInsight (Java)**
      - **Esecuzione di esempio di Spark in un cluster HDInsight (Scala)**
   - Strumento di compilazione: la procedura guidata di creazione del progetto Scala supporta Maven o SBT per la gestione delle dipendenze e la compilazione per il progetto Scala. Selezionarne uno in base alle necessità.
2. Nella finestra successiva specificare i dettagli del progetto.

   ![Selezione di Spark SDK](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-new-project.png)
   - Specificare un nome per il progetto e il relativo percorso.
   - Per **Project SDK**, usare **Java 1.8** per cluster **Spark 2. x** o **Java 1.7** per cluster **Spark 1.x**.
   - Per la **Versione di Spark**, la creazione guidata del progetto Scala integra la versione corretta per Spark SDK e Scala SDK. Se la versione del cluster Spark è inferiore a 2.0, scegliere **Spark 1.x**. In caso contrario, selezionare **Spark 2.x**. In questo articolo viene usata la versione **Spark 2.0.2 (Scala 2.11.8)** come esempio.
3. Selezionare **src** > **main** > **scala** per aprire il codice nel progetto. In questo articolo viene usato lo script **SparkCore_wasbloTest** come esempio.
4. Per accedere al menu **Modifica configurazioni** selezionare l'icona nell'angolo superiore destro. Da questo menu è possibile creare o modificare le configurazioni per il debug remoto.

   ![Modifica configurazioni](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-edit-configurations.png) 
5. Nella finestra **Run/Debug Configurations** (Esegui/Debug delle configurazioni) fare clic sul segno più (**+**). Selezionare quindi l'opzione **Submit Spark Job** (Invia processo Spark).

   ![Modifica configurazioni](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-add-new-Configuration.png)
6. Immettere le informazioni per **Nome**, **Cluster Spark**, e **Nome della classe principale**. Selezionare quindi **Configurazione avanzata**. 

   ![Eseguire configurazioni di debug](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-debug-configurations.png)
7. Nella finestra di dialogo **Spark Submission Advanced Configuration** (Configurazione avanzata invio Spark) selezionare **Enable Spark remote debug** (Abilita debug remoto Spark). Immettere il nome utente o la password SSH oppure usare un file di chiave privata. Per salvare, selezionare **Ok**.

   ![Abilitare il debug remoto di Spark](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-enable-spark-remote-debug.png)
8. A questo punto, la configurazione viene salvata con il nome specificato. Per visualizzare i dettagli della configurazione, selezionare il relativo nome. Per apportare modifiche, selezionare **Modifica configurazioni**. 
9. Dopo aver completato le impostazioni di configurazione, è possibile eseguire il progetto con il cluster remoto oppure eseguire il debug remoto.

## <a name="learn-how-to-perform-remote-debugging-and-remote-run"></a>Informazioni su come effettuare il debug remoto e l'esecuzione remota
### <a name="scenario-1-perform-remote-run"></a>Scenario 1: Esecuzione remota
1. Per eseguire il progetto in modalità remota, selezionare l'icona **Esegui**.

   ![Fare clic sull'icona Esegui](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-icon.png)

2. La finestra **HDInsight Spark Submission** (Invio Spark in HDInsight) mostra lo stato di esecuzione dell'applicazione. È possibile monitorare lo stato di avanzamento del processo Scala in base alle informazioni mostrate qui.

   ![Fare clic sull'icona Esegui](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-result.png)

### <a name="scenario-2-perform-remote-debugging"></a>Scenario 2: Debug remoto
1. Impostare un punto di interruzione e quindi selezionare l'icona **Debug**.

    ![Fare clic sull'icona Debug](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-icon.png)
2. Quando l'esecuzione del programma raggiunge il punto di interruzione, nel riquadro inferiore verrà visualizzata la scheda **Debugger**. Vengono anche visualizzate le informazioni di parametro e variabile nella finestra **Variabile**. Fare clic sull'icona **Step Over** (Continua) per passare alla riga di codice successiva. A quel punto è possibile procedere ulteriormente nel codice. Selezionare l'icona **Resume Program** (Riprendi programma) per continuare a eseguire il codice. È possibile esaminare lo stato di esecuzione nella finestra **HDInsight Spark Submission** (Invio Spark in HDInsight). 

   ![Scheda di debug](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Scenario 3: Debug e correzione di bug da remoto
In questa sezione viene illustrato come aggiornare in modo dinamico il valore variabile usando la funzionalità di debug di IntelliJ per una correzione semplificata. Per l'esempio di codice seguente, viene generata un'eccezione poiché il file di destinazione esiste già.
  
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
1. Impostare due punti di interruzione e quindi fare clic sull'icona **Debug** per avviare il processo di debug remoto.

2. Il codice si ferma in corrispondenza del primo punto di interruzione e le informazioni di parametro e variabile vengono visualizzate nella finestra **Variabile**. 

3. Fare clic sull'icona **Resume Program** (Riprendi programma) per continuare. Il codice si interrompe in corrispondenza del secondo punto. L'eccezione viene rilevata come previsto.

  ![Errore di generazione](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-throw-error.png) 

4. Fare nuovamente clic sull'icona **Resume Program** (Riprendi programma). La finestra **HDInsight Spark Submission** (Invio Spark in HDInsight) mostra un errore di esecuzione processo non riuscita.

  ![Errore nell'invio](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-error-submission.png) 

5. Per aggiornare dinamicamente il valore della variabile usando la funzionalità di debug di IntelliJ, selezionare nuovamente **Debug**. Viene nuovamente visualizzata la finestra Variabile. 

6. Fare clic con il pulsante destro del mouse sulla destinazione nella scheda **Debug**, quindi selezionare **Imposta valore**. Indicare quindi un nuovo valore per la variabile e selezionare **Invia** per salvarlo. 

  ![Impostare il valore](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-set-value.png) 

7. Fare clic sull'icona **Resume Program** (Riprendi programma) per continuare a eseguire il programma. Questa volta, l'eccezione non viene rilevata. È possibile notare come il progetto venga eseguito correttamente senza eccezioni.

  ![Debug senza eccezioni](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Vedere anche
* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="demo"></a>Demo
* Creare il progetto Scala (video): [Creare applicazioni Spark in Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Debug remoto (video): [Usare Azure Toolkit per IntelliJ per il debug remoto di applicazioni Spark nel cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenari
* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: usare Spark in HDInsight per prevedere i risultati del controllo degli alimenti](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)
* [Analisi dei log del sito Web mediante Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni
* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni
* [Usare gli strumenti HDInsight nel Toolkit di Azure per IntelliJ per creare e inviare applicazioni Spark in Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Usare Azure Toolkit per IntelliJ per il debug remoto delle applicazioni su HDInsight Spark tramite VPN](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare gli strumenti HDInsight per IntelliJ con Hortonworks Sandbox](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Use HDInsight Tools in Azure Toolkit for Eclipse to create Spark applications (Usare gli strumenti HDInsight nel Toolkit di Azure per Eclipse per creare applicazioni Spark)](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-job-debugging.md)
 
