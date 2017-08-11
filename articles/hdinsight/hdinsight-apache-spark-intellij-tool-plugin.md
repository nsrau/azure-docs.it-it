---
title: 'Toolkit di Azure per IntelliJ: creare applicazioni Scala per HDInsight Spark | Microsoft Docs'
description: Usare il Toolkit di Azure per IntelliJ per sviluppare applicazioni Spark scritte in Scala e inoltrarle a un cluster HDInsight Spark.
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 73304272-6c8b-482e-af7c-cd25d95dab4d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: 2a7d0f2d557f33531a2d11f857594680e064da9f
ms.contentlocale: it-it
ms.lasthandoff: 07/27/2017

---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-hdinsight-cluster"></a>Usare Azure Toolkit for IntelliJ per creare applicazioni Spark per il cluster HDInsight

Usare il plug-in Toolkit di Azure per IntelliJ per sviluppare applicazioni Spark scritte in Scala e inoltrarle a un cluster HDInsight Spark direttamente dall'IDE IntelliJ. È possibile usare il plug-in in diversi modi:

* Per sviluppare e inviare un'applicazione Spark in Scala in un cluster HDInsight Spark
* Per accedere alle risorse cluster HDInsight Spark di Azure
* Per sviluppare ed eseguire un'applicazione Spark in Scala localmente

È possibile seguire questo [video](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) per creare il progetto.

> [!IMPORTANT]
> Questo plug-in può essere usato per creare e inviare applicazioni solo per cluster HDInsight Spark in Linux.
> 
> 

## <a name="prerequisites"></a>Prerequisiti

- Un cluster Apache Spark in HDInsight Linux. Per istruzioni, vedere l'articolo relativo alla [creazione di cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
- Kit di sviluppo di Oracle Java. È possibile installarlo dal [sito Web di Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- IntelliJ IDEA. In questo articolo viene usata la versione 2017.1. È possibile installarlo dal [sito Web di JetBrains](https://www.jetbrains.com/idea/download/).

## <a name="install-azure-toolkit-for-intellij"></a>Installare il Toolkit di Azure per IntelliJ
Per le istruzioni di installazione vedere [Installazione del Toolkit di Azure per IntelliJ](../azure-toolkit-for-intellij-installation.md).

## <a name="sign-in-to-your-azure-subscription"></a>Accedere alla sottoscrizione di Azure.
1. Avviare l'IDE IntelliJ e aprire Azure Explorer. Nel menu **Visualizza** fare clic su **Finestre degli strumenti** e quindi su **Azure Explorer**.
       
   ![Comandi selezionati nel menu Visualizza](./media/hdinsight-apache-spark-intellij-tool-plugin/show-azure-explorer.png)
2. Fare clic con il pulsante destro del mouse sul nodo **Azure**, quindi fare clic su **Accedi**.
3. Nella finestra di dialogo di **Azure Sign In** (Accesso ad Azure) fare clic su **Sign-in** (Accedi) e immettere le credenziali di Azure.
![Finestra di dialogo di accesso di Azure](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-2.png)
4. Dopo l'accesso, la finestra di dialogo **Selezionare le sottoscrizioni** elenca tutte le sottoscrizioni di Azure associate alle credenziali. Fare clic su **Seleziona** per chiudere la finestra di dialogo.

    ![Finestra di dialogo Selezionare le sottoscrizioni](./media/hdinsight-apache-spark-intellij-tool-plugin/Select-Subscriptions.png)
5. Nella scheda **Azure Explorer** espandere **HDInsight** per visualizzare i cluster HDInsight Spark nella sottoscrizione.
   
    ![Cluster HDInsight Spark in Azure Explorer](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-3.png)
6. È possibile espandere ancora un nodo del nome cluster per vedere le risorse, ad esempio gli account di archiviazione, associate al cluster.
   
    ![Espansione di un nome del cluster per vedere le risorse](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Eseguire un'applicazione Spark in Scala in un cluster HDInsight Spark
1. Avviare IntelliJ IDEA e creare un progetto. Nella finestra di dialogo **New Project** (Nuovo progetto) selezionare le opzioni seguenti e quindi fare clic su **Next** (Avanti). 
![Finestra di dialogo Nuovo progetto](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)
   - Selezionare **HDInsight** nel riquadro sinistro.
   - Selezionare **Spark on HDInsight (Scala)**(Spark in HDInsight - Scala) nel riquadro destro.
   - Strumento di compilazione: la procedura guidata di creazione del progetto Scala supporta Maven o SBT per la gestione delle dipendenze e la compilazione per il progetto Scala. Selezionarne uno in base alle necessità.
2. La creazione guidata del progetto Scala rileva automaticamente se è installato il plug-in Scala. Fare clic su **Installa** per continuare.

    ![Controllo di Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 
3. Fare clic su **OK** per scaricare il plug-in Scala. Seguire le istruzioni per riavviare IntelliJ. 

   ![Installazione di Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

4. Nella finestra successiva inserire i dettagli di progetto seguenti e quindi fare clic su **Finish** (Fine).  
![Selezione di Spark SDK](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-new-project.png)
   - Specificare un nome per il progetto e il relativo percorso.
   - Per **Project SDK**, usare Java 1.8 per cluster Spark 2.x, Java 1.7 per cluster Spark 1.x.
   - Per la **Versione di Spark**, la creazione guidata del progetto Scala integra la versione corretta per Spark SDK e Scala SDK. Se la versione del cluster Spark è inferiore a 2.0, scegliere Spark 1.x. In caso contrario, selezionare Spark 2.x. In questo esempio viene usata la versione Spark 2.0.2 (Scala 2.11.8).

5. Il progetto Spark crea automaticamente un elemento. Per visualizzare l'elemento, seguire questa procedura:

   1. Fare clic su **Project Structure** (Struttura progetto) nel menu **File**.
   2. Nella finestra di dialogo **Project Structure** (Struttura progetto) fare clic su **Artifacts** (Elementi) per visualizzare l'elemento predefinito creato. È anche possibile creare un elemento personalizzato facendo clic sull'icona **+**.
             ![Informazioni sull'elemento nella finestra di dialogo](./media/hdinsight-apache-spark-intellij-tool-plugin/default-artifact.png)
      
6. Aggiungere il codice sorgente dell'applicazione.
   1. In Project Explorer (Esplora progetti) fare clic con il pulsante destro del mouse su **src**, puntare a **New** (Nuovo) e quindi fare clic su **Scala class** (Classe Scala).
      
       ![Comandi per la creazione di una classe Scala in Project Explorer (Esplora progetti)](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)
   2. Nella finestra di dialogo **Create New Scala Class** (Crea nuova classe Scala) immettere un nome, selezionare **Object** (Oggetto) per il campo **Kind** (Tipologia) e quindi fare clic su **OK**.
      
       ![Finestra di dialogo Create New Scala Class (Crea nuova classe Scala)](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)
   3. Nel file **MyClusterApp.scala** incollare il codice seguente. Questo codice legge i dati dal file HVAC.csv, disponibile in tutti i cluster HDInsight Spark, recupera le righe con una sola cifra nella settima colonna del file CSV e scrive l'output in **/HVACOut** nel contenitore di archiviazione predefinito per il cluster.

            import org.apache.spark.SparkConf
            import org.apache.spark.SparkContext

            object MyClusterApp{
              def main (arg: Array[String]): Unit = {
                val conf = new SparkConf().setAppName("MyClusterApp")
                val sc = new SparkContext(conf)

                val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

                //find the rows that have only one digit in the seventh column in the CSV file
                val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

                rdd1.saveAsTextFile("wasb:///HVACOut")
              }

            }

7. Eseguire l'applicazione in un cluster HDInsight Spark.
   1. In Project Explorer (Esplora progetti) fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Submit Spark Application to HDInsight** (Invia applicazione Spark a HDInsight).
      
       ![Selezione di Submit Spark Application to HDInsight (Invia applicazione Spark a HDInsight)](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)
   2. Viene richiesto di immettere le credenziali della sottoscrizione di Azure. Nella finestra di dialogo **Spark Submission** (Invio Spark) specificare i valori seguenti e fare clic su **Submit** (Invia).
      
      - Per **Spark clusters (Linux only)**(Cluster Spark - solo Linux) selezionare il cluster Spark HDInsight in cui eseguire l'applicazione.
      - Selezionare un elemento nel progetto IntelliJ oppure selezionarne uno dal disco rigido.
      - Nella casella **Main class name** (Nome classe principale) fare clic sui puntini di sospensione (![puntini di sospensione](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png)), selezionare la classe principale nel codice sorgente dell'applicazione e quindi fare clic su **OK**.
      ![Finestra di dialogo selezione classe principale](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)
      * Poiché il codice dell'applicazione in questo esempio non richiede argomenti della riga di comando e non fa riferimento a JAR o altri file, è possibile lasciare vuote le rimanenti caselle di testo.
        Dopo aver dato tutti gli input, la finestra di dialogo sarà simile all'immagine seguente.
        
          ![Finestra di dialogo di invio di Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)
   3. Nella scheda **Spark Submission** (Invio Spark) nella parte inferiore della finestra verrà visualizzato lo stato di avanzamento. È anche possibile arrestare l'applicazione facendo clic sul pulsante rosso nella finestra **Spark Submission** (Invio Spark).
      
       ![Finestra di invio di Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      Più avanti in questo articolo, nella sezione "Accedere e gestire i cluster HDInsight Spark tramite il Toolkit di Azure per IntelliJ" viene spiegato come accedere all'output del processo.

## <a name="run-as-or-debug-as-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Eseguire o eseguire il debug come un'applicazione Spark in Scala in un cluster HDInsight Spark
Si consiglia inoltre di usare un altro modo per inviare l'applicazione Spark al cluster. Impostando i parametri nell'IDE **Run/Debug configrurations** (Esegui/Esegui il debug delle configurazioni). Per altre informazioni vedere [Eseguire il debug remoto delle applicazioni Spark su un cluster HDInsight con Azure Toolkit per IntelliJ tramite SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh)

## <a name="choose-azure-data-lake-store-as-spark-scala-application-storage"></a>Scegliere Azure Data Lake Store come risorsa di archiviazione per l'applicazione Spark Scala
Se si desidera inviare un'applicazione ad Azure Data Lake Store, è necessario selezionare la modalità **Interactive** (Interattivo) durante l'accesso ad Azure. 

   ![Opzione Interactive (Interattivo) al momento dell'accesso](./media/hdinsight-apache-spark-intellij-tool-plugin/authentication-interactive.png)

Se si seleziona la modalità **Automated** (Automatico), viene visualizzato l'errore seguente:

   ![Errore di accesso](./media/hdinsight-apache-spark-intellij-tool-plugin/authentication-error.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Accedere e gestire i cluster HDInsight Spark tramite il Toolkit di Azure per IntelliJ
È possibile eseguire diverse operazioni mediante il Toolkit Azure per IntelliJ.

### <a name="access-the-job-view"></a>Accedere alla visualizzazione del processo
1. In Azure Explorer espandere **HDInsight**, espandere il nome del cluster Spark e quindi fare clic su **Processi**.  
       ![Nodo vista processi](./media/hdinsight-apache-spark-intellij-tool-plugin/job-view-node.png)
2. Nel riquadro destro la scheda **Spark Job View** (Visualizzazione processi Spark) visualizza tutte le applicazioni eseguite nel cluster. Fare clic sul nome dell'applicazione per cui si desidera visualizzare altri dettagli.
       ![Dettagli applicazione](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)
3. Passare il mouse sul grafico del processo: vengono mostrate le informazioni di base del processo in esecuzione. Fare clic sul grafico del processo: viene visualizzato il grafico delle fasi e le informazioni generate da ogni processo.
       ![Dettagli della fase del processo](./media/hdinsight-apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. I log usati di frequente che include informazioni della directory, del driver Stdout, del driver Stderr sono elencati nella scheda **Log**.
       ![Dettagli del log](./media/hdinsight-apache-spark-intellij-tool-plugin/Job-log-info.png)
5. È anche possibile aprire l'interfaccia utente della cronologia di Spark e l'interfaccia utente di YARN, a livello di applicazione, facendo clic sui rispettivi collegamenti ipertestuali nella parte superiore della finestra.

### <a name="access-the-spark-history-server"></a>Accedere al server della cronologia di Spark
1. In Azure Explorer espandere **HDInsight**, fare clic con il pulsante destro del mouse sul nome del cluster Spark e quindi scegliere **Open Spark History UI** (Apri UI cronologia Spark). Quando richiesto, immettere le credenziali dell'amministratore per il cluster. È necessario specificare le credenziali durante il provisioning del cluster.
2. Nel dashboard del server della cronologia di Spark è possibile usare il nome dell'applicazione per cercare l'applicazione di cui è appena stata completata l'esecuzione. Nel codice precedente, impostare il nome dell'applicazione usando `val conf = new SparkConf().setAppName("MyClusterApp")`. Il nome dell'applicazione Spark è **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Avviare il portale di Ambari
1. In Azure Explorer espandere **HDInsight**, fare clic con il pulsante destro del mouse sul nome del cluster Spark e quindi scegliere **Open Cluster Management Portal (Ambari)** (Apri il portale di gestione cluster - Ambari). 
2. Quando richiesto, immettere le credenziali dell'amministratore per il cluster. Queste credenziali sono state specificate durante il processo di provisioning del cluster.

### <a name="manage-azure-subscriptions"></a>Gestire le sottoscrizioni di Azure
Per impostazione predefinita, il Toolkit di Azure per IntelliJ elenca i cluster Spark di tutte le sottoscrizioni di Azure. Se necessario, è possibile specificare le sottoscrizioni per cui si vuole accedere al cluster. 

1. In Azure Explorer fare clic con il pulsante destro del mouse sul nodo radice **Azure** e quindi scegliere **Gestisci sottoscrizioni**. 
2. Nella finestra di dialogo deselezionare le caselle di controllo della sottoscrizione alla quale non si vuole accedere e quindi fare clic su **Chiudi**. È anche possibile fare clic su **Esci** per uscire dalla sessione di sottoscrizione di Azure.

## <a name="run-a-spark-scala-application-locally"></a>Eseguire un'applicazione Spark in Scala localmente
È possibile usare il Toolkit di Azure per IntelliJ per eseguire applicazioni Spark Scala localmente nella workstation. Tali applicazioni in genere non richiedono l'accesso a risorse del cluster quali il contenitore di archiviazione e possono essere eseguite e testate localmente.

### <a name="prerequisite"></a>Prerequisito
Quando si esegue l'applicazione Spark Scala locale in un computer Windows, è possibile che venga restituita un'eccezione, come spiegato in [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356), che si verifica a causa di un file WinUtils.exe mancante in Windows. 

Per risolvere questo errore, è necessario [scaricare il file eseguibile](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) in un percorso come **C:\WinUtils\bin**. È quindi necessario aggiungere una variabile di ambiente **HADOOP_HOME** e impostare il valore della variabile su **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Eseguire un'applicazione Spark in Scala locale
1. Avviare IntelliJ IDEA e creare un progetto. Nella finestra di dialogo **New Project** (Nuovo progetto) selezionare le opzioni seguenti e quindi fare clic su **Next** (Avanti).
   
    - Selezionare **HDInsight** nel riquadro sinistro.
    - Selezionare **Spark on HDInsight Local Run Sample (Scala)** (Esecuzione locale di esempio Spark in HDInsight - Scala) nel riquadro destro.
    - Strumento di compilazione: la procedura guidata di creazione del progetto Scala supporta Maven o SBT per la gestione delle dipendenze e la compilazione per il progetto Scala. Selezionarne uno in base alle necessità.
    ![Finestra di dialogo selezioni nel nuovo progetto](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)
2. Nella finestra successiva inserire i dettagli di progetto seguenti e quindi fare clic su **Finish** (Fine).
   
    - Specificare un nome per il progetto e il relativo percorso.
    - In **Project SDK** (SDK progetto) assicurarsi di specificare una versione di Java successiva alla 7.
    - Per la **Versione di Spark**, selezionare la versione di Scala da usare: Scala 2.11.x per Spark 2.0 e Scala 2.10.x per Spark 1.6.
![Selezione di Spark SDK](./media/hdinsight-apache-spark-intellij-tool-plugin/Create-local-project.PNG)
3. Il modello aggiunge un codice di esempio (**LogQuery**) sotto la cartella **src** eseguibile in locale nel computer in uso.
   
    ![Percorso di LogQuery](./media/hdinsight-apache-spark-intellij-tool-plugin/local-app.png)
4. Fare clic con il pulsante destro del mouse sull'applicazione **LogQuery** e quindi scegliere **Run 'LogQuery'** (Esegui "LogQuery"). Viene visualizzato un output simile al seguente nella scheda **Run** (Esegui) in basso.
   
   ![Risultato dell'esecuzione locale dell'applicazione Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Convertire le applicazioni IntelliJ IDEA esistenti per usare il Toolkit di Azure per IntelliJ
È possibile convertire le applicazioni Spark Scala esistenti create in IntelliJ IDEA per renderle compatibili il Toolkit di Azure per IntelliJ. È possibile usare il plug-in per inviare le applicazioni a un cluster HDInsight Spark.

1. Per un'applicazione Spark Scala esistente creata tramite IntelliJ IDEA, aprire il file con estensione IML associato.
2. A livello di radice viene visualizzato un elemento **module** simile al seguente:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
   Modificare l'elemento per aggiungere `UniqueKey="HDInsightTool"` in modo che l'elemento **module** sia simile al seguente:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
3. Salvare le modifiche. L'applicazione dovrebbe ora essere compatibile con il Toolkit di Azure per IntelliJ. È possibile verificarlo facendo clic con il pulsante destro del mouse sul nome del progetto in Project Explorer (Esplora progetti). Nel menu a comparsa viene ora visualizzata l'opzione **Submit Spark Application to HDInsight**(Invia applicazione Spark a HDInsight).

## <a name="troubleshooting"></a>Risoluzione dei problemi
### <a name="please-use-a-larger-heap-size-error-in-local-run"></a>Errore relativo all'uso di dimensioni heap maggiori nell'esecuzione locale
Se in Spark 1.6 si usa un SDK per Java a 32 bit durante l'esecuzione locale, è possibile riscontrare gli errori seguenti:

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

Questi errori si verificano perché le dimensioni heap non sono sufficientemente grandi da consentire l'esecuzione di Spark. Spark richiede almeno 471 MB. È possibile avere altri dettagli in [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081). Una soluzione semplice consiste nell'uso di un SDK per Java a 64 bit. È anche possibile modificare le impostazioni JVM in IntelliJ aggiungendo le opzioni seguenti:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Aggiunta di opzioni alla casella "VM options" (Opzioni della macchina virtuale) in IntelliJ](./media/hdinsight-apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="feedback-and-known-issues"></a>Commenti, suggerimenti e problemi noti
Il supporto della visualizzazione diretta degli output di Spark non è al momento disponibile.

Per eventuali commenti o suggerimenti oppure se si riscontrano problemi nell'uso di questo strumento, inviare un messaggio di posta elettronica all'indirizzo hdivstool@microsoft.com.

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

### <a name="creating-and-running-applications"></a>Creazione ed esecuzione di applicazioni
* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni
* [Usare Azure Toolkit per IntelliJ per il debug remoto di applicazioni Spark tramite VPN](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare Azure Toolkit per IntelliJ per il debug remoto di applicazioni Spark tramite SSH](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usare gli strumenti HDInsight per IntelliJ con Hortonworks Sandbox](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Use HDInsight Tools in Azure Toolkit for Eclipse to create Spark applications (Usare gli strumenti HDInsight nel Toolkit di Azure per Eclipse per creare applicazioni Spark)](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gestione delle risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-job-debugging.md)


