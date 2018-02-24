---
title: 'Azure Toolkit for IntelliJ: Creare applicazioni Spark per un cluster HDInsight | Microsoft Docs'
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
ms.date: 11/25/2017
ms.author: maxluk,jejiang
<<<<<<< HEAD
ms.openlocfilehash: 077805cedb7895c8c59b650b3ec691244168a9f5
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
=======
ms.openlocfilehash: 69f5857f89271b3e4865b93e42e5233ead572715
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
>>>>>>> e9608243da864c46ce93986fe11980d2656272ea
---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-an-hdinsight-cluster"></a>Usare Azure Toolkit for IntelliJ per creare applicazioni Spark per un cluster HDInsight

Usare il plug-in Azure Toolkit for IntelliJ per sviluppare applicazioni Spark scritte in Scala e quindi inoltrarle a un cluster HDInsight Spark direttamente dall'ambiente di sviluppo integrato (IDE) IntelliJ. È possibile usare il plug-in in vari modi:

* Sviluppare e inviare un'applicazione Spark in Scala in un cluster HDInsight Spark.
* Accedere alle risorse cluster HDInsight Spark di Azure.
* Sviluppare ed eseguire un'applicazione Spark in Scala localmente.

Per creare il progetto, guardare il video [Create Spark Applications with the Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (Creare applicazioni Spark con Azure Toolkit for IntelliJ).

> [!IMPORTANT]
> È possibile usare questo plug-in per creare e inviare applicazioni solo per cluster HDInsight Spark in Linux.
> 

## <a name="prerequisites"></a>prerequisiti

- Un cluster Apache Spark in HDInsight Linux. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).
- Kit di sviluppo di Oracle Java. È possibile installarlo dal [sito Web di Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- IntelliJ IDEA. In questo articolo viene usata la versione 2017.1. È possibile installarlo dal [sito Web di JetBrains](https://www.jetbrains.com/idea/download/).

## <a name="install-azure-toolkit-for-intellij"></a>Installare il Toolkit di Azure per IntelliJ
Per le istruzioni di installazione vedere [Installare Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="sign-in-to-your-azure-subscription"></a>Accedere alla sottoscrizione di Azure.

1. Avviare l'IDE IntelliJ e aprire Azure Explorer. Nel menu **Visualizza** selezionare **Finestre degli strumenti** e quindi **Azure Explorer**.
       
   ![Collegamento di Azure Explorer](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. Fare clic con il pulsante destro del mouse sul nodo **Azure** e quindi scegliere **Sign In** (Accedi).

3. Nella finestra di dialogo di **Azure Sign In** (Accesso ad Azure) fare clic su **Sign-in** (Accedi) e immettere le credenziali di Azure.

    ![Finestra di dialogo di accesso di Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. Dopo l'accesso, la finestra di dialogo **Selezionare le sottoscrizioni** elenca tutte le sottoscrizioni di Azure associate alle credenziali. Fare clic sul pulsante **Seleziona**.

    ![Finestra di dialogo Seleziona sottoscrizioni](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

5. Nella scheda **Azure Explorer** espandere **HDInsight** per visualizzare i cluster HDInsight Spark disponibili nella sottoscrizione.
   
    ![Cluster HDInsight Spark in Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. Per visualizzare le risorse, ad esempio gli account di archiviazione, associate al cluster, è possibile espandere ancora un nodo di tipo nome del cluster.
   
    ![Nodo di tipo nome del cluster espanso](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>Collegare un cluster
È possibile collegare un cluster normale usando un nome utente Ambari gestito e anche collegare un cluster Hadoop di sicurezza usando un nome utente di dominio (ad esempio: user1@contoso.com). 
1. Fare clic su **Link a cluster** (Collega un cluster) in **Azure Explorer** (Esplora Azure).

   ![Menu di scelta rapida per collegare un cluster](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

2. Immettere un valore nei campi **Nome cluster**, **Account di archiviazione**, **Chiave di archiviazione** e quindi selezionare un contenitore in **Contenitore di archiviazione**. Immettere infine il nome utente e la password. È necessario verificare il nome utente e la password se viene restituito un errore di autenticazione.
   
   ![Finestra di dialogo di collegamento di un cluster](./media/apache-spark-intellij-tool-plugin/link-a-cluster-dialog.png)

   > [!NOTE]
   > Vengono usati la chiave di archiviazione, il nome utente e la password collegati se il cluster ha eseguito l'accesso alla sottoscrizione di Azure e ha collegato un cluster. 
   
3. È possibile visualizzare un cluster collegato nel nodo di **HDInsight** se le informazioni di input sono corrette. È ora possibile inviare un'applicazione al cluster collegato.

   ![Cluster collegato](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

4. È inoltre possibile scollegare un cluster in **Azure Explorer** (Esplora Azure).
   
   ![Cluster scollegato](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Eseguire un'applicazione Spark in Scala in un cluster HDInsight Spark

1. Avviare IntelliJ IDEA e creare un progetto. Nella finestra di dialogo **New Project** (Nuovo progetto) seguire questa procedura: 

   a. Selezionare **HDInsight** > **Spark on HDInsight (Scala)** (Spark in HDInsight - Scala).

   b. Nell'elenco **Build tool** (Strumento di compilazione) selezionare uno degli strumenti seguenti, in base alla necessità:

      * **Maven**, per ottenere supporto per la creazione guidata di un progetto Scala
      * **SBT**, per la gestione delle dipendenze e la compilazione per il progetto Scala

    ![Finestra di dialogo del nuovo progetto](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

2. Selezionare **Avanti**.

3. La creazione guidata del progetto Scala rileva automaticamente se è installato il plug-in Scala. Selezionare **Installa**.

   ![Verifica del plug-in Scala](./media/apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

4. Per scaricare il plug-in Scala, selezionare **OK**. Seguire le istruzioni per riavviare IntelliJ. 

   ![Finestra di dialogo di installazione del plug-in Scala](./media/apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

5. Nella finestra **New Project** (Nuovo progetto) seguire questa procedura:  

    ![Selezione di Spark SDK](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

   a. Immettere un nome e un percorso per il progetto.

   b. Nell'elenco a discesa **Project SDK** (SDK progetto) selezionare **Java 1.8** per il cluster Spark 2.x oppure **Java 1.7** per il cluster Spark 1.x.

   c. Nell'elenco a discesa **Spark version** (Versione di Spark) la creazione guidata del progetto Scala inserisce la versione corretta per Spark SDK e Scala SDK. Se la versione del cluster Spark è precedente alla 2.0, selezionare **Spark 1.x**. In caso contrario, selezionare **Spark 2.x**. In questo esempio viene usata la versione **Spark 2.0.2 (Scala 2.11.8)**.

6. Selezionare **Fine**.

7. Il progetto Spark crea automaticamente un elemento. Per visualizzare l'elemento, seguire questa procedura:

   a. Dal menu **File** selezionare **Project Structure** (Struttura progetto).

   b. Nella finestra di dialogo **Project Structure** (Struttura progetto) selezionare **Artifacts** (Elementi) per visualizzare l'elemento predefinito creato. È anche possibile creare un elemento personalizzato selezionando il segno più (**+**).

      ![Informazioni sull'elemento nella finestra di dialogo](./media/apache-spark-intellij-tool-plugin/default-artifact.png)
      
8. Aggiungere il codice sorgente dell'applicazione seguendo questa procedura:

   a. In Project Explorer (Esplora progetti) fare clic con il pulsante destro del mouse su **src**, selezionare **New** (Nuovo) e quindi selezionare **Scala class** (Classe Scala).
      
      ![Comandi per la creazione di una classe Scala in Project Explorer (Esplora progetti)](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   b. Nella finestra di dialogo **Create New Scala Class** (Crea nuova classe Scala) immettere un nome, selezionare **Object** (Oggetto) per il campo **Kind** (Tipologia) e quindi selezionare **OK**.
      
      ![Finestra di dialogo Create New Scala Class (Crea nuova classe Scala)](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   c. Nel file **MyClusterApp.scala** incollare il codice seguente. Il codice legge i dati dal file HVAC.csv, disponibile in tutti i cluster HDInsight Spark, recupera le righe con una sola cifra nella settima colonna del file CSV e scrive l'output in **/HVACOut** nel contenitore di archiviazione predefinito per il cluster.

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

9. Eseguire l'applicazione in un cluster HDInsight Spark seguendo questa procedura:

   a. In Project Explorer (Esplora progetti) fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Submit Spark Application to HDInsight** (Invia applicazione Spark a HDInsight).
      
      ![Comando di invio dell'applicazione Spark a HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

   b. Viene richiesto di immettere le credenziali della sottoscrizione di Azure. Nella finestra di dialogo **Spark Submission** (Invio Spark) specificare i valori seguenti e selezionare **Submit** (Invia).
      
      * Per **Spark clusters (Linux only)**(Cluster Spark - solo Linux) selezionare il cluster Spark HDInsight in cui eseguire l'applicazione.

      * Selezionare un elemento nel progetto IntelliJ oppure selezionarne uno dal disco rigido.

      * Nella casella **Main class name** (Nome classe principale) selezionare i puntini di sospensione (**...**), selezionare la classe principale nel codice sorgente dell'applicazione e quindi **OK**.

        ![Finestra di dialogo di selezione della classe principale](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

      * Poiché il codice dell'applicazione in questo esempio non richiede argomenti della riga di comando e non fa riferimento a JAR o altri file, è possibile lasciare vuote le rimanenti caselle di testo. Quando sono state fornite tutte le informazioni, la finestra di dialogo sarà simile all'immagine seguente.
        
        ![Finestra di dialogo Spark Submission (Invio Spark)](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

   c. Nella scheda **Spark Submission** (Invio Spark) nella parte inferiore della finestra verrà visualizzato lo stato di avanzamento. È anche possibile arrestare l'applicazione selezionando il pulsante rosso nella finestra **Spark Submission** (Invio Spark).
      
     ![Finestra Spark Submission (Invio Spark)](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      Per informazioni su come accedere all'output dei processi, vedere la sezione "Accedere e gestire i cluster HDInsight Spark tramite il Toolkit di Azure per IntelliJ" più avanti in questo articolo.

## <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Eseguire il debug di applicazioni Spark in modalità locale o remota in un cluster HDInsight 
Si consiglia anche di usare un altro modo per inviare l'applicazione Spark al cluster. È ad esempio possibile configurare i parametri nell'IDE **Run/Debug configurations** (Esegui/Esegui il debug delle configurazioni). Per altre informazioni, vedere [Eseguire il debug delle applicazioni Spark su un cluster HDInsight con Azure Toolkit for IntelliJ tramite SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).



## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Accedere e gestire i cluster HDInsight Spark tramite il Toolkit di Azure per IntelliJ
È possibile eseguire diverse operazioni mediante il Toolkit Azure per IntelliJ.

### <a name="access-the-job-view"></a>Accedere alla visualizzazione del processo
1. In Azure Explorer espandere **HDInsight**, espandere il nome del cluster Spark e quindi selezionare **Processi**.  

    ![Nodo di visualizzazione dei processi](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. Nel riquadro destro la scheda **Spark Job View** (Visualizzazione processi Spark) visualizza tutte le applicazioni eseguite nel cluster. Selezionare il nome dell'applicazione per cui si vogliono visualizzare altri dettagli.

    ![Dettagli applicazione](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)
    >Note
    >

3. Per visualizzare le informazioni di base sui processi in esecuzione, passare il mouse sopra il grafico relativo al processo. Per visualizzare i grafici sulle fasi e le informazioni generate da ogni processo, selezionare un nodo nel grafico relativo al processo.

    ![Dettagli delle fasi dei processi](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Per visualizzare i log di uso più frequente, ad esempio *Driver Stderr*, *Driver Stdout* e *Directory Info*, selezionare la scheda **Log**.

    ![Dettagli del log](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. È anche possibile visualizzare l'interfaccia utente della cronologia di Spark e l'interfaccia utente di YARN, a livello di applicazione, selezionando i rispettivi collegamenti nella parte superiore della finestra.

### <a name="access-the-spark-history-server"></a>Accedere al Server cronologia Spark
1. In Azure Explorer espandere **HDInsight**, fare clic con il pulsante destro del mouse sul nome del cluster Spark e quindi scegliere **Open Spark History UI** (Apri UI cronologia Spark). 

2. Quando richiesto, immettere le credenziali dell'amministratore del cluster, specificate durante la configurazione del cluster.

3. Nel dashboard del server della cronologia di Spark è possibile usare il nome dell'applicazione per cercare l'applicazione di cui è appena stata completata l'esecuzione. Nel codice precedente impostare il nome dell'applicazione usando `val conf = new SparkConf().setAppName("MyClusterApp")`. Il nome dell'applicazione Spark è quindi **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Avviare il portale di Ambari
1. In Azure Explorer espandere **HDInsight**, fare clic con il pulsante destro del mouse sul nome del cluster Spark e quindi scegliere **Open Cluster Management Portal (Ambari)** (Apri il portale di gestione cluster - Ambari). 

2. Quando richiesto, immettere le credenziali dell'amministratore per il cluster. Queste credenziali sono state specificate durante il processo di configurazione del cluster.

### <a name="manage-azure-subscriptions"></a>Gestire le sottoscrizioni di Azure
Per impostazione predefinita, il Toolkit di Azure per IntelliJ elenca i cluster Spark di tutte le sottoscrizioni di Azure. Se necessario, è possibile specificare le sottoscrizioni a cui si vuole accedere. 

1. In Azure Explorer fare clic con il pulsante destro del mouse sul nodo radice **Azure** e quindi scegliere **Gestisci sottoscrizioni**. 

2. Nella finestra di dialogo deselezionare le caselle di controllo accanto alla sottoscrizione alla quale non si vuole accedere e quindi selezionare **Chiudi**. È anche possibile selezionare **Esci** per uscire dalla sessione di sottoscrizione di Azure.

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Convertire le applicazioni IntelliJ IDEA esistenti per usare il Toolkit di Azure per IntelliJ
È possibile convertire le applicazioni Spark Scala esistenti create in IntelliJ IDEA per renderle compatibili con Azure Toolkit for IntelliJ. È possibile usare il plug-in per inviare le applicazioni a un cluster HDInsight Spark.

1. Per un'applicazione Spark Scala esistente creata tramite IntelliJ IDEA, aprire il file con estensione IML associato.

2. A livello di radice viene visualizzato un elemento **module** simile al seguente:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Modificare l'elemento per aggiungere `UniqueKey="HDInsightTool"` in modo che l'elemento **module** sia simile al seguente:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

3. Salvare le modifiche. L'applicazione dovrebbe ora essere compatibile con il Toolkit di Azure per IntelliJ. È possibile verificarlo facendo clic con il pulsante destro del mouse sul nome del progetto in Project Explorer (Esplora progetti). Nel menu a comparsa viene ora visualizzata l'opzione **Submit Spark Application to HDInsight**(Invia applicazione Spark a HDInsight).

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="error-in-local-run-please-use-a-larger-heap-size"></a>Errore nell'esecuzione locale: *Usare dimensioni di heap maggiori*
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

Questi errori si verificano perché le dimensioni heap non sono sufficientemente grandi da consentire l'esecuzione di Spark. Spark richiede almeno 471 MB. Per altre informazioni, vedere [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081). Una soluzione semplice consiste nell'uso di un SDK per Java a 64 bit. È anche possibile modificare le impostazioni JVM in IntelliJ aggiungendo le opzioni seguenti:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Aggiunta di opzioni alla casella "VM options" (Opzioni della macchina virtuale) in IntelliJ](./media/apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>Domande frequenti
Per inviare un'applicazione ad Azure Data Lake Store, selezionare la modalità **Interactive** (Interattivo) durante l'accesso ad Azure. Se si seleziona la modalità **Automated** (Automatico), viene visualizzato un errore.

![interative-signin](./media/apache-spark-intellij-tool-plugin/interative-signin.png)

A questo punto, il problema è risolto. È possibile scegliere un cluster di Azure Data Lake per inviare l'applicazione con qualsiasi metodo di accesso.

## <a name="feedback-and-known-issues"></a>Commenti, suggerimenti e problemi noti
Il supporto della visualizzazione diretta degli output di Spark non è al momento disponibile.

Per eventuali commenti o suggerimenti oppure se si riscontrano problemi nell'uso di questo plug-in, inviare un messaggio di posta elettronica all'indirizzo hdivstool@microsoft.com.

## <a name="seealso"></a>Passaggi successivi
* [Panoramica: Apache Spark su Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demo
* Creare il progetto Scala (video): [Create Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (Creare applicazioni Spark Scala)
* Debug remoto (video): [Use Azure Toolkit for IntelliJ to debug Spark applications remotely on HDInsight Cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Spark nel cluster HDInsight)

### <a name="scenarios"></a>Scenari
* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: usare Spark in HDInsight per l'analisi della temperatura di compilazione usando dati HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per stimare i risultati dell'ispezione cibo](apache-spark-machine-learning-mllib-ipython.md)
* [Analisi dei log del sito Web mediante Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Creazione ed esecuzione di applicazioni
* [Creare un'applicazione autonoma con Scala](apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Spark usando Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni
* [Usare Azure Toolkit per IntelliJ per il debug remoto di applicazioni Spark tramite VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare Azure Toolkit per IntelliJ per il debug remoto di applicazioni Spark tramite SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usare gli strumenti HDInsight per IntelliJ con Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Use HDInsight Tools in Azure Toolkit for Eclipse to create Spark applications (Usare gli strumenti HDInsight nel Toolkit di Azure per Eclipse per creare applicazioni Spark)](apache-spark-eclipse-tool-plugin.md)
* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gestione delle risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)

