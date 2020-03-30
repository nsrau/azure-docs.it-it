---
title: Debug Spark job with IntelliJ Azure Toolkit (preview) - HDInsight
description: Indicazioni sull'uso degli strumenti HDInsight in Azure Toolkit per IntelliJ per il debug delle applicazioni
keywords: eseguire debug remoto di intellij, debug remoto di intellij, ssh, intellij, hdinsight, debug di intellij, debug
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 1a0a6cf5a26854539dc4bbb0ae0254bbf08dad1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73494594"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Debug del processo di spark non riuscite con Azure Toolkit per IntelliJ (anteprima)Failure spark job debugging with Azure Toolkit for IntelliJ (preview)

Questo articolo fornisce istruzioni dettagliate su come usare gli strumenti HDInsight in Azure Toolkit per IntelliJ per l'esecuzione di applicazioni di debug degli **errori di Spark.This** article provides step-by-step guidance on how to use HDInsight Tools in [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) to run Spark Failure Debug applications.

## <a name="prerequisites"></a>Prerequisiti

* [Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Questa esercitazione usa Java versione 8.0.202.
  
* IntelliJ IDEA. In questo articolo viene utilizzata [IntelliJ IDEA Community ver. 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure Toolkit for IntelliJ. Vedere [Installazione di Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

* Connettersi al cluster HDInsight.Connect to your HDInsight cluster. Vedere [Connettersi al cluster HDInsight.](apache-spark-intellij-tool-plugin.md)

* Microsoft Azure Storage Explorer. Vedere [Scaricare Esplora archivi di Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Creare un progetto con modello di debugCreate a project with debugging template

Creare un progetto spark2.3.2 per continuare il debug degli errori, prendere un'attività di debug dell'attività in questo documento.

1. Aprire IntelliJ IDEA. Aprire la finestra **Nuovo progetto.**

   a. Selezionare **Azure Spark/HDInsight** nel riquadro sinistro.

   b. Selezionare Progetto Spark con Esempio di debug di **attività di errore (anteprima)(Scala)** dalla finestra principale.

     ![Intellij Creare un progetto di debugIntellij Create a debug project](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. Fare clic su **Avanti**.

2. Nella finestra di dialogo **New Project** (Nuovo progetto) eseguire questa procedura:

   ![Intellij Nuovo progetto selezionare versione Spark](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. Specificare un nome per il progetto e il relativo percorso.

   b. Nell'elenco a discesa **PROJECT SDK** selezionare **Java 1.8** per il cluster **Spark 2.3.2.**

   c. Nell'elenco a discesa **Versione Spark** selezionare **Spark 2.3.2(Scala 2.11.8)**.

   d. Fare clic su **Fine**.

3. Selezionare **src** > **scala** **principale** > per aprire il codice nel progetto. In questo esempio viene utilizzato lo script **AgeMean_Div().**

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Eseguire un'applicazione Spark Scala/Java in un cluster HDInsightRun a Spark Scala/Java application on an HDInsight cluster

Creare un'applicazione spark Scala/Java, quindi eseguire l'applicazione in un cluster Spark eseguendo la procedura seguente:

1. Fare clic su **Aggiungi configurazione** per aprire la finestra **Configurazioni di esecuzione/debug.**

   ![Configurazione dell'aggiunta di Intellij HDI](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. Nella finestra di dialogo **Run/Debug Configurations** (Esegui/Debug delle configurazioni) selezionare il segno più (**+**). Selezionare quindi l'opzione **Apache Spark in HDInsight.Then select the Apache Spark on HDInsight** option.

   ![Intellij Aggiungi nuova configurazione](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Passare alla scheda Esegui in remoto **Name**nel **Spark cluster** **cluster.** **Main class name** Questi strumenti supportano il debug con **executor**. Il **numExectors**, il valore predefinito è 5 e sarebbe meglio non impostare su un valore superiore a 3. Per ridurre il tempo di esecuzione, è possibile aggiungere **spark.yarn.maxAppAttempts** in **configurazioni di processo** e impostare il valore su 1. Fare clic sul pulsante **OK** per salvare la configurazione.

   ![Intellij Eseguire le configurazioni di debug nuovo](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. A questo punto, la configurazione viene salvata con il nome specificato. Per visualizzare i dettagli della configurazione, selezionare il relativo nome. Per apportare modifiche, selezionare **Modifica configurazioni**.

5. Dopo aver completato le impostazioni di configurazione, è possibile eseguire il progetto nel cluster remoto.

   ![Pulsante Esegui remoto processo Spark remoto di debug Intellij](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. È possibile controllare l'ID dell'applicazione dalla finestra di output.

   ![Risultato dell'esecuzione remota del processo Spark remoto di debug IntellijIntellij Debug Remote Spark Job Remote run Remote run](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>Download del profilo di lavoro non riuscito

Se l'invio del processo non riesce, è possibile scaricare il profilo del processo non riuscito nel computer locale per un ulteriore debug.

1. Aprire **Esplora archivi di Microsoft Azure**, individuare l'account HDInsight del cluster per il processo non riuscito, scaricare le risorse del processo non riuscito dal percorso corrispondente: **.hdp\\spark2-events .spark-failures\\\<ID applicazione>** in una cartella locale. La finestra **delle attività** mostrerà l'avanzamento del download.

   ![Errore di download di Azure Storage ExplorerAzure Storage Explorer download failure](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Download del download di Azure Storage Explorer](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Configurare l'ambiente di debug locale ed eseguire il debug in caso di erroreConfigure local debugging environment and debug on failure

1. Aprire il progetto originale o creare un nuovo progetto e associarlo al codice sorgente originale. Solo la versione spark2.3.2 è attualmente supportata per il debug degli errori.

1. In IntelliJ IDEA creare un file di configurazione **Spark Failure Debug,** selezionare il file FTD dalle risorse di processo non riuscite scaricate in precedenza per il campo **Percorso contesto errore processo Spark.**

   ![configurazione errore crete](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. Fare clic sul pulsante di esecuzione locale nella barra degli strumenti, l'errore verrà visualizzato nella finestra Esegui.

   ![run-failure-configuration1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![run-failure-configuration2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. Impostare il punto di interruzione come indicato nel log, quindi fare clic sul pulsante di debug locale per eseguire il debug locale come i normali progetti Scala / Java in IntelliJ.

1. Dopo il debug, se il progetto viene completato correttamente, è possibile inviare nuovamente il processo non riuscito al servizio di replica nel cluster HDInsight.After debugging, if the project completes successfully, you could resubmit the failed job to your spark on HDInsight cluster.

## <a name="next-steps"></a><a name="seealso"></a>Passaggi successivi

* [Panoramica: Debug di applicazioni Apache Spark](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Demo

* Creare il progetto Scala (video): [Create Apache Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (Creare applicazioni Apache Spark in Scala)
* Debug remoto (video): [Use Azure Toolkit for IntelliJ to debug Apache Spark applications remotely on HDInsight Cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark nel cluster HDInsight)

### <a name="scenarios"></a>Scenari

* [Apache Spark con BI: eseguire l'analisi interattiva dei dati tramite Spark in HDInsight con gli strumenti di BIApache Spark with BI: Do interactive data analysis by using Spark in HDInsight with BI tools](apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: usare Spark in HDInsight per l'analisi della temperatura di compilazione usando dati HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark con Machine Learning: utilizzare Spark in HDInsight per prevedere i risultati di un controllo alimentare](apache-spark-machine-learning-mllib-ipython.md)
* [Analisi dei log del sito Web con Apache Spark in HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni

* [Creare un'applicazione autonoma con Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni

* [Usare Azure Toolkit for IntelliJ per creare applicazioni Apache Spark per un cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark tramite VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare gli strumenti HDInsight per IntelliJ con Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Usare gli strumenti HDInsight in Azure Toolkit for Eclipse per creare applicazioni Apache Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Usare i notebook di Apache Zeppelin con un cluster Apache Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Apache Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestione di risorse

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)
