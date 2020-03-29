---
title: 'Azure Toolkit for IntelliJ: Debug Spark apps with SSH - HDInsight'
description: Istruzioni dettagliate su come usare gli strumenti HDInsight in Azure Toolkit for IntelliJ per eseguire il debug remoto di applicazioni in cluster di HDInsight tramite SSH
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: 67660e3e98f5a12236798d74cc61f71616e6751d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934757"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Debug Apache Spark applications on an HDInsight cluster with Azure Toolkit for IntelliJ through SSH

Questo articolo fornisce istruzioni dettagliate su come usare gli strumenti HDInsight in Azure Toolkit per IntelliJ per eseguire il debug delle applicazioni in remoto in un cluster HDInsight.This article provides step-by-step guidance on how to use HDInsight Tools in [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) to debug applications remotely on an HDInsight cluster. Per eseguire il debug del progetto, è anche possibile guardare il video [Debug HDInsight Spark applications with Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Eseguire il debug delle applicazioni Spark di HDInsight con Azure Toolkit for IntelliJ).

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Spark in HDInsight. Vedere [Creare un cluster Apache Spark](../spark/apache-spark-jupyter-spark-sql-use-portal.md).

* Per gli utenti di Windows: durante l'esecuzione dell'applicazione locale Spark Scala in un computer Windows, è possibile che venga generata un'eccezione, come illustrato in [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). che si verifica a causa di un file WinUtils.exe mancante in Windows.

    Per risolvere l'errore, scaricare [Winutils.exe](https://github.com/steveloughran/winutils) in un percorso quale **C:** Aggiungere quindi la variabile di ambiente **HADOOP_HOME**, quindi impostare il valore della variabile su **C:.**

* [IntelliJ IDEA](https://www.jetbrains.com/idea/download/#section=windows) (L'edizione Community è gratuita.).

* [Azure Toolkit per IntelliJ](https://docs.microsoft.com/azure/java/intellij/azure-toolkit-for-intellij-installation).

* [Plug-in Scala per IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).

* Un client SSH. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-spark-scala-application"></a>Creare un'applicazione Spark Scala

1. Avviare IntelliJ IDEA e selezionare **Crea nuovo progetto** per aprire la finestra **Nuovo progetto**.

1. Selezionare **Apache Spark/HDInsight** nel riquadro sinistro.

1. Selezionare **Progetto Spark con esempi (Scala)** dalla finestra principale.

1. Nell'elenco **Strumento di compilazione** selezionare uno degli strumenti seguenti:

    * Supporto per la creazione guidata progetto **Maven** for Scala.
    * **SBT** per la gestione delle dipendenze e la compilazione per il progetto Scala.

     ![Intellij Crea nuovo progetto Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

1. Fare clic su **Avanti**.

1. Nella finestra **Nuovo progetto** successiva, fornire le seguenti informazioni:

    |Proprietà |Descrizione |
    |---|---|
    |Project name (Nome progetto)|Immettere un nome. Questa passeggiata `myApp`utilizza .|
    |Posizione del progetto|Immettere il percorso desiderato in cui salvare il progetto.|
    |Project SDK (SDK progetto)|Se vuoto, selezionare **Nuovo...** e passare al JDK.|
    |Versione Spark|La creazione guidata integra la versione corretta dell'SDK di Spark e Scala. Se la versione del cluster Spark è precedente alla 2.0, selezionare **Spark 1.x**. In caso contrario, selezionare **Spark 2.x.**. In questo esempio viene usata la versione **Spark 2.3.0 (Scala 2.11.8)**.|

   ![Intellij Nuovo progetto selezionare versione Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

1. Fare clic su **Fine**. Potrebbero occorrere alcuni minuti prima che il progetto diventi disponibile. Guarda l'angolo in basso a destra per progredire.

1. Espandere il progetto e **src** > passare all'esempio**scala** > **sample****principale** > . Fare doppio clic **su SparkCore_WasbIOTest**.

## <a name="perform-local-run"></a>Eseguire l'esecuzione in locale

1. Dallo script **SparkCore_WasbIOTest,** fare clic con il pulsante destro del mouse sull'editor di script, quindi selezionare l'opzione **Esegui 'SparkCore_WasbIOTest'** per eseguire l'esecuzione locale.

1. Al termine dell'esecuzione locale, è possibile visualizzare il salvataggio del file di output nei **dati** > di Esplora progetti correnti.**__default__**

    ![Risultato dell'esecuzione locale del progetto IntellijIntellij Project local run result](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)

1. Per questi strumenti la configurazione dell'esecuzione in locale predefinita viene impostata automaticamente quando l'esecuzione e il debug vengono eseguiti in modalità locale. Aprire la configurazione **[Spark su HDInsight] XXX** nell'angolo superiore destro, è possibile visualizzare **[Spark on HDInsight]XXX** già creato in **Apache Spark in HDInsight**. Passare alla scheda **Locally Run** (Esecuzione in locale).

    ![Intellij Esegue l'esecuzione locale delle configurazioni di debug](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)

    - [Environment variables](#prerequisites) (Variabili di ambiente): se la variabile di ambiente di sistema **HADOOP_HOME** è già stata impostata su **C:\WinUtils**, viene automaticamente rilevato che l'aggiunta manuale non è necessaria.
    - [WinUtils.exe Location](#prerequisites) (Posizione WinUtils.exe): se la variabile di ambiente di sistema non è stata impostata, per trovare la posizione è sufficiente fare clic sul relativo pulsante.
    - È sufficiente scegliere una delle due opzioni, che non sono necessarie in MacOS e Linux.

1. È anche possibile impostare la configurazione manualmente prima di eseguire l'esecuzione e il debug in modalità locale. Nella schermata precedente selezionare il**+** segno più ( ). Selezionare quindi l'opzione **Apache Spark in HDInsight.Then select the Apache Spark on HDInsight** option. Immettere le informazioni per il **nome** e il **nome della classe principale** da salvare e quindi fare clic sul pulsante Esecuzione in locale.

## <a name="perform-local-debugging"></a>Eseguire il debug in locale

1. Aprire lo script **SparkCore_wasbloTest** e impostare i punti di interruzione.

1. Fare clic con il pulsante destro del mouse sull'editor di script e quindi selezionare l'opzione **Esegui debug '[Spark in HDInsight]XXX'** per eseguire il debug locale.

## <a name="perform-remote-run"></a>Esecuzione remota

1. Passare a **Esegui** > **configurazioni di modifica...**. Da questo menu è possibile creare o modificare le configurazioni per il debug remoto.

1. Nella finestra di dialogo **Run/Debug Configurations** (Esegui/Debug delle configurazioni) selezionare il segno più (**+**). Selezionare quindi l'opzione **Apache Spark in HDInsight.Then select the Apache Spark on HDInsight** option.

   ![Intellij Aggiungi nuova configurazione](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)

1. Passare alla scheda Esegui in remoto **Name**nel **Spark cluster** **cluster.** **Main class name** Fare quindi clic su **Configurazione avanzata (debug remoto)**. Questi strumenti supportano il debug con **executor**. Il valore predefinito della chiave **numExectors** è 5. È consigliabile non impostarlo su un valore maggiore di 3.

   ![Configurazioni di debug di Intellij Eseguire](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. Nella parte **Configurazione avanzata (debug remoto)** selezionare **Abilita debug remoto Spark**. Immettere il nome utente SSH e inserire una password oppure usare un file di chiave privata. Se si desidera eseguire il debug in modalità remota, è necessario impostare questa opzione. Non è necessario impostarla se si desidera usare l'esecuzione in modalità remota.

   ![Intellij Advanced Configuration abilita il debug remoto di spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. A questo punto, la configurazione viene salvata con il nome specificato. Per visualizzare i dettagli della configurazione, selezionare il relativo nome. Per apportare modifiche, selezionare **Modifica configurazioni**.

1. Dopo aver completato le impostazioni di configurazione, è possibile eseguire il progetto con il cluster remoto oppure eseguire il debug remoto.

   ![Pulsante Esegui remoto processo Spark remoto di debug Intellij](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. Fare clic sul pulsante **Disconnetti**. I log di invio non vengono visualizzati nel riquadro sinistro, ma l'esecuzione è ancora in corso nel back-end.

   ![Risultato dell'esecuzione remota del processo Spark remoto di debug IntellijIntellij Debug Remote Spark Job Remote run Remote run](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

## <a name="perform-remote-debugging"></a>Eseguire l'esecuzione da remoto

1. Impostare i punti di interruzione e quindi fare clic sull'icona **Remote debug** (Debug remoto). La differenza rispetto all'invio remoto risiede nel fatto che non è necessario configurare il nome utente/password SSH.

   ![Icona di debug del processo Spark remoto di debug Intellij](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Quando l'esecuzione del programma raggiunge il punto di interruzione, nel riquadro **Debugger** vengono visualizzate la scheda **Diver** e le due schede **Executor**. Selezionare l'icona **Resume Program** (Riprendi programma) per continuare l'esecuzione del codice, che raggiungerà il punto di interruzione successivo. È necessario passare alla scheda **Executor** (Esecutore) corretta per trovare l'esecutore di destinazione da sottoporre a debug. È possibile visualizzare i log di esecuzione nella scheda **Console** corrispondente.

   ![Scheda Debug processo Spark remoto di debug Intellij](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="perform-remote-debugging-and-bug-fixing"></a>Eseguire il debug e la correzione di bug da remoto

1. Impostare due punti di interruzione e quindi selezionare l'icona **Debug** per avviare il processo di debug remoto.

1. Il codice si interrompe in corrispondenza del primo punto di interruzione e le informazioni di parametro e variabile vengono visualizzate nella finestra **Variables** (Variabili).

1. Selezionare l'icona **Resume Program** (Riprendi programma) per continuare. Il codice si interrompe in corrispondenza del secondo punto. L'eccezione viene rilevata come previsto.

   ![Errore del processo Spark remoto di debug Intellij Intellij](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png)

1. Selezionare di nuovo l'icona **Resume Program** (Riprendi programma). La finestra **HDInsight Spark Submission** (Invio Spark in HDInsight) mostra un errore di "esecuzione processo non riuscita".

   ![Invio dell'errore del processo Spark remoto di debug IntellijIntellij Debug Remote Spark Job Error](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png)

1. Per aggiornare dinamicamente il valore della variabile usando la funzionalità di debug di IntelliJ, selezionare nuovamente **Debug**. Viene visualizzato di nuovo il riquadro **Variables** (Variabili).

1. Fare clic con il pulsante destro del mouse sulla destinazione nella scheda **Debug**, quindi selezionare **Imposta valore**. Successivamente inserire un nuovo valore per la variabile. e selezionare **Invia** per salvarlo.

   ![Valore del set di processi Spark remoti di debug IntellijIntellij Debug Remote Spark Job set value](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. Selezionare l'icona **Resume Program** (Riprendi programma) per continuare a eseguire il programma. Questa volta, non viene rilevata alcuna eccezione. È possibile notare come il progetto venga eseguito correttamente senza eccezioni.

   ![Processo Spark remoto di debug Intellij senza eccezioniIntellij Debug Remote Spark Job without exception](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica: Apache Spark su Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demo

* Creare il progetto Scala (video): [Create Apache Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (Creare applicazioni Apache Spark in Scala)
* Debug remoto (video): [Use Azure Toolkit for IntelliJ to debug Apache Spark applications remotely on HDInsight Cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark nel cluster HDInsight)

### <a name="scenarios"></a>Scenari

* [Apache Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: usare Spark in HDInsight per l'analisi della temperatura di compilazione usando dati HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark con Machine Learning: utilizzare Spark in HDInsight per prevedere i risultati di un controllo alimentare](apache-spark-machine-learning-mllib-ipython.md)
* [Analisi dei log del sito Web con Apache Spark in HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni

* [Creare un'applicazione autonoma con Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni

* [Usare Azure Toolkit for IntelliJ per creare applicazioni Apache Spark per un cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark tramite VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare gli strumenti HDInsight in Azure Toolkit for Eclipse per creare applicazioni Apache Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Usare i notebook di Apache Zeppelin con un cluster Apache Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Apache Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestione di risorse

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)
