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
ms.date: 11/25/2017
ms.author: jejiang
ms.openlocfilehash: 6ea6d94453583c6b5ed680f96ebc43d33d0262a0
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Eseguire il debug delle applicazioni Spark in modalità remota o locale su un cluster HDInsight con Azure Toolkit per IntelliJ tramite SSH

Questo articolo contiene istruzioni dettagliate su come usare gli strumenti HDInsight in Azure Toolkit for IntelliJ per eseguire il debug remoto di applicazioni in un cluster di HDInsight. Per eseguire il debug del progetto, è anche possibile guardare il video [Debug HDInsight Spark applications with Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Eseguire il debug delle applicazioni Spark di HDInsight con Azure Toolkit for IntelliJ).

**Prerequisiti**
* **Strumenti HDInsight in Azure Toolkit per IntelliJ**. Questo strumento fa parte di Azure Toolkit for IntelliJ. Per altre informazioni, vedere [Installare Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). **Azure Toolkit for IntelliJ**. Usare questo toolkit per creare applicazioni Spark per cluster HDInsight. Per altre informazioni, seguire le istruzioni in [Usare Azure Toolkit per IntelliJ per creare applicazioni Spark per il cluster HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).

* **Servizio SSH di HDInsight con gestione di nome utente e password**. Per altre informazioni, vedere [Connettersi a HDInsight (Hadoop) con SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) e [Usare il tunneling SSH per accedere all'interfaccia utente Web di Ambari, JobHistory, NameNode, Oozie e altre interfacce utente Web](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Informazioni su esecuzione e debug in modalità locale
### <a name="scenario-1-create-a-spark-scala-application"></a>Scenario 1: Creare un'applicazione Spark Scala 

1. Avviare IntelliJ IDEA e creare un progetto. Nella finestra di dialogo **New Project** (Nuovo progetto) seguire questa procedura:

   a. Selezionare **HDInsight**. 

   b. Selezionare un modello Java o Scala in base alle preferenze. Scegliere una delle opzioni seguenti:

      - **Spark in HDInsight (Scala)**

      - **Spark in HDInsight (Java)**

      - **Spark on HDInsight Sample (Scala)** (Esempio di Spark in HDInsight (Scala))

      In questo esempio si usa un modello **Spark on HDInsight Sample (Scala)** (Esempio di Spark in HDInsight (Scala)).

   c. Nell'elenco **Build tool** (Strumento di compilazione) selezionare uno degli strumenti seguenti, in base alla necessità:

      - **Maven**, per ottenere supporto per la creazione guidata di un progetto Scala

      -  **SBT**, per la gestione delle dipendenze e la compilazione per il progetto Scala 

      ![Creare un progetto di debug](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Selezionare **Avanti**.     
 
2. Nella finestra **New Project** (Nuovo progetto) successiva seguire questa procedura:

   ![Selezionare l'SDK di Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Specificare un nome per il progetto e il relativo percorso.

   b. Nell'elenco a discesa **Project SDK** (SDK progetto) selezionare **Java 1.8** per il cluster **Spark 2.x** oppure **Java 1.7** per il cluster **Spark 1.x**.

   c. Nell'elenco a discesa **Spark version** (Versione di Spark) la creazione guidata del progetto Scala inserisce la versione corretta per Spark SDK e Scala SDK. Se la versione del cluster Spark è precedente alla 2.0, selezionare **Spark 1.x**. In caso contrario, selezionare **Spark 2.x**. In questo esempio viene usata la versione **Spark 2.0.2 (Scala 2.11.8)**.

   d. Selezionare **Fine**.

3. Selezionare **src** > **main** > **scala** per aprire il codice nel progetto. In questo esempio viene usato lo script **SparkCore_wasbloTest**.

### <a name="prerequisite-for-windows"></a>Prerequisito per Windows
Quando si esegue l'applicazione Spark Scala locale in un computer Windows, potrebbe essere restituita un'eccezione, come spiegato in [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356), che si verifica a causa di un file WinUtils.exe mancante in Windows. 

Per risolvere questo errore, [scaricare il file eseguibile](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) in un percorso come **C:\WinUtils\bin**. È quindi necessario aggiungere una variabile di ambiente **HADOOP_HOME** e impostare il valore della variabile su **C:\WinUtils**.

### <a name="scenario-2-perform-local-run"></a>Scenario 2: Eseguire l'esecuzione in locale
1. Aprire lo script **SparkCore_wasbloTest**, fare clic con il pulsante destro del mouse sull'editor di script e quindi selezionare l'opzione **Run '[Spark Job]XXX'** (Esegui '[Processo Spark]XXX') per avviare l'esecuzione in modalità locale.
2. Dopo aver completato l'esecuzione in locale, è possibile visualizzare il file di output salvato nella directory **data** > **__default__** della finestra di gestione del progetto corrente.

    ![Risultato dell'esecuzione in locale](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-result.png)
3. Per questi strumenti la configurazione dell'esecuzione in locale predefinita viene impostata automaticamente quando l'esecuzione e il debug vengono eseguiti in modalità locale. Aprire la configurazione **[Spark Job] XXX** ([Processo Spark] XXX) nell'angolo superiore a destra per visualizzare la voce **[Spark Job]XXX** ([Processo Spark]XXX) già creata in **Azure HDInsight Spark Job** (Processo Spark in Azure HDInsight). Passare alla scheda **Locally Run** (Esecuzione in locale).

    ![Configurazione dell'esecuzione in locale](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [Environment variables](#prerequisite-for-windows) (Variabili di ambiente): se la variabile di ambiente di sistema **HADOOP_HOME** è già stata impostata su **C:\WinUtils**, viene automaticamente rilevato che l'aggiunta manuale non è necessaria.
    - [WinUtils.exe Location](#prerequisite-for-windows) (Posizione WinUtils.exe): se la variabile di ambiente di sistema non è stata impostata, per trovare la posizione è sufficiente fare clic sul relativo pulsante.
    - È sufficiente scegliere una delle due opzioni, che non sono necessarie in MacOS e Linux.
4. È anche possibile impostare la configurazione manualmente prima di eseguire l'esecuzione e il debug in modalità locale. Nello screenshot precedente selezionare il segno più (**+**). Selezionare quindi l'opzione **Azure HDInsight Spark Job** (Processo Spark in Azure HDInsight). Immettere le informazioni per il **nome** e il **nome della classe principale** da salvare e quindi fare clic sul pulsante Esecuzione in locale.

### <a name="scenario-3-perform-local-debugging"></a>Scenario 3: Eseguire l'esecuzione in locale
1. Aprire lo script **SparkCore_wasbloTest** e impostare i punti di interruzione.
2. Fare clic con il pulsante destro del mouse sull'editor di script e quindi selezionare l'opzione **Debug '[Spark Job]XXX'** (Esegui '[Processo Spark]XXX') per eseguire il debug in modalità locale.   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Informazioni su esecuzione e debug in modalità remota
### <a name="scenario-1-perform-remote-run"></a>Scenario 1: Esecuzione remota

1. Per accedere al menu **Edit Configurations** (Modifica configurazioni) selezionare l'icona nell'angolo in alto a destra. Da questo menu è possibile creare o modificare le configurazioni per il debug remoto.

   ![Modifica configurazioni](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

2. Nella finestra di dialogo **Run/Debug Configurations** (Esegui/Debug delle configurazioni) selezionare il segno più (**+**). Selezionare quindi l'opzione **Azure HDInsight Spark Job** (Processo Spark in Azure HDInsight).

   ![Aggiungere una nuova configurazione](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
3. Passare alla scheda **Remotely Run in Cluster** (Esecuzione remota nel cluster). Immettere le informazioni per **Nome**, **Cluster Spark**, e **Nome della classe principale**. Selezionare quindi **Configurazione avanzata**. Questi strumenti supportano il debug con **executor**. Il valore predefinito della chiave **numExectors** è 5. È consigliabile non impostarlo su un valore maggiore di 3.

   ![Eseguire configurazioni di debug](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

4. Nella finestra di dialogo **Spark Submission Advanced Configuration** (Configurazione avanzata invio Spark) selezionare **Enable Spark remote debug** (Abilita debug remoto Spark). Immettere il nome utente SSH e inserire una password oppure usare un file di chiave privata. Selezionare **OK** per salvare la configurazione. Se si desidera eseguire il debug in modalità remota, è necessario impostare questa opzione. Non è necessario impostarla se si desidera usare l'esecuzione in modalità remota.

   ![Abilitare il debug remoto di Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

5. A questo punto, la configurazione viene salvata con il nome specificato. Per visualizzare i dettagli della configurazione, selezionare il relativo nome. Per apportare modifiche, selezionare **Modifica configurazioni**. 

6. Dopo aver completato le impostazioni di configurazione, è possibile eseguire il progetto con il cluster remoto oppure eseguire il debug remoto.
   
   ![Pulsante di esecuzione remota](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run.png)

7. Fare clic sul pulsante **Disconnetti**. I log di invio non vengono visualizzati nel riquadro sinistro, ma l'esecuzione è ancora in corso nel back-end.

   ![Pulsante di esecuzione remota](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/remote-run-result.png)



### <a name="scenario-2-perform-remote-debugging"></a>Scenario 2: Debug remoto
1. Impostare i punti di interruzione e quindi fare clic sull'icona **Remote debug** (Debug remoto). La differenza rispetto all'invio remoto risiede nel fatto che non è necessario configurare il nome utente/password SSH.

   ![Selezionare l'icona Debug](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

2. Quando l'esecuzione del programma raggiunge il punto di interruzione, nel riquadro **Debugger** vengono visualizzate la scheda **Diver** e le due schede **Executor**. Selezionare l'icona **Resume Program** (Riprendi programma) per continuare l'esecuzione del codice, che raggiungerà il punto di interruzione successivo. È necessario passare alla scheda **Executor** (Esecutore) corretta per trovare l'esecutore di destinazione da sottoporre a debug. È possibile visualizzare i log di esecuzione nella scheda **Console** corrispondente.

   ![Scheda di debug](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Scenario 3: Debug e correzione di bug da remoto
1. Impostare due punti di interruzione e quindi selezionare l'icona **Debug** per avviare il processo di debug remoto.

2. Il codice si interrompe in corrispondenza del primo punto di interruzione e le informazioni di parametro e variabile vengono visualizzate nella finestra **Variables** (Variabili). 

3. Selezionare l'icona **Resume Program** (Riprendi programma) per continuare. Il codice si interrompe in corrispondenza del secondo punto. L'eccezione viene rilevata come previsto.

   ![Errore di generazione](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

4. Selezionare di nuovo l'icona **Resume Program** (Riprendi programma). La finestra **HDInsight Spark Submission** (Invio Spark in HDInsight) mostra un errore di "esecuzione processo non riuscita".

   ![Errore nell'invio](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

5. Per aggiornare dinamicamente il valore della variabile usando la funzionalità di debug di IntelliJ, selezionare nuovamente **Debug**. Viene visualizzato di nuovo il riquadro **Variables** (Variabili). 

6. Fare clic con il pulsante destro del mouse sulla destinazione nella scheda **Debug**, quindi selezionare **Imposta valore**. Successivamente inserire un nuovo valore per la variabile. e selezionare **Invia** per salvarlo. 

   ![Impostare il valore](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

7. Selezionare l'icona **Resume Program** (Riprendi programma) per continuare a eseguire il programma. Questa volta, non viene rilevata alcuna eccezione. È possibile notare come il progetto venga eseguito correttamente senza eccezioni.

   ![Debug senza eccezioni](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Passaggi successivi
* [Panoramica: Apache Spark su Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demo
* Creare il progetto Scala (video): [Create Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (Creare applicazioni Spark Scala)
* Debug remoto (video): [Usare Azure Toolkit for IntelliJ per eseguire il debug remoto di applicazioni Spark nel cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenari
* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: usare Spark in HDInsight per l'analisi della temperatura di compilazione usando dati HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per stimare i risultati dell'ispezione cibo](apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](apache-spark-eventhub-streaming.md)
* [Analisi dei log del sito Web mediante Spark in HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni
* [Creare un'applicazione autonoma con Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Spark usando Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni
* [Usare Azure Toolkit for IntelliJ per creare applicazioni Spark per un cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Usare Azure Toolkit per IntelliJ per il debug remoto di applicazioni Spark tramite VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare gli strumenti HDInsight per IntelliJ con Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Use HDInsight Tools in Azure Toolkit for Eclipse to create Spark applications (Usare gli strumenti HDInsight nel Toolkit di Azure per Eclipse per creare applicazioni Spark)](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)
