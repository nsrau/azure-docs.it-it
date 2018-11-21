---
title: 'Azure Toolkit for IntelliJ: Creare applicazioni Spark per un cluster HDInsight '
description: Usare il Toolkit di Azure per IntelliJ per sviluppare applicazioni Spark scritte in Scala e inoltrarle a un cluster HDInsight Spark.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: maxluk
ms.openlocfilehash: ff7cfcd56158bd38d031a29a21247fb9eb6b91f9
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51289071"
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

## <a name="prerequisites"></a>Prerequisiti

- Un cluster Apache Spark in HDInsight Linux. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).
- Kit di sviluppo di Oracle Java. È possibile installarlo dal [sito Web di Oracle](https://aka.ms/azure-jdks).
- IntelliJ IDEA. In questo articolo viene usata la versione 2017.1. È possibile installarlo dal [sito Web di JetBrains](https://www.jetbrains.com/idea/download/).

## <a name="install-azure-toolkit-for-intellij"></a>Installare il Toolkit di Azure per IntelliJ
Per le istruzioni di installazione vedere [Installare Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="get-started"></a>Attività iniziali
Per iniziare è possibile [accedere alla sottoscrizione di Azure](#sign-in-to-your-azure-subscription) oppure [collegare un cluster HDInsight](#link-a-cluster) usando un nome utente e una password Ambari o le credenziali di un account aggiunto al dominio.


## <a name="sign-in-to-your-azure-subscription"></a>Accedere alla sottoscrizione di Azure.

1. Avviare l'IDE IntelliJ e aprire Azure Explorer. Nel menu **Visualizza** selezionare **Finestre degli strumenti** e quindi **Azure Explorer**.
       
   ![Collegamento di Azure Explorer](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

1. Fare clic con il pulsante destro del mouse sul nodo **Azure** e quindi scegliere **Sign In** (Accedi).

1. Nella finestra di dialogo di **Azure Sign In** (Accesso ad Azure) fare clic su **Sign-in** (Accedi) e immettere le credenziali di Azure.

    ![Finestra di dialogo di accesso di Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

1. Dopo l'accesso, la finestra di dialogo **Selezionare le sottoscrizioni** elenca tutte le sottoscrizioni di Azure associate alle credenziali. Fare clic sul pulsante **Seleziona**.

    ![Finestra di dialogo Seleziona sottoscrizioni](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

1. Nella scheda **Azure Explorer** espandere **HDInsight** per visualizzare i cluster HDInsight Spark disponibili nella sottoscrizione.
   
    ![Cluster HDInsight Spark in Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

1. Per visualizzare le risorse, ad esempio gli account di archiviazione, associate al cluster, è possibile espandere ancora un nodo di tipo nome del cluster.
   
    ![Nodo di tipo nome del cluster espanso](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>Collegare un cluster
Puoi collegare un normale cluster HDInsight usando lo username gestito di Ambari. Analogamente, è possibile collegare un cluster HDInsight aggiunto al dominio tramite il dominio e il nome utente, come user1@contoso.com. È anche possibile collegare il cluster del servizio Livy.

1. Fare clic su **Collega un cluster** in **Esplora Azure**.

   ![Menu di scelta rapida per collegare un cluster](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

2. Per collegare i cluster, è possibile scegliere tra due opzioni. 

   * Per collegare un cluster HDInsight, scegliere **HDInsight Cluster** (Cluster HDInsight) nel campo **Cluster Info** (Informazioni cluster) e immettere i valori in **Cluster Name/URL** (URL/Nome cluster), **User Name** (Nome utente) e **Password**.

      ![Finestra di dialogo per il collegamento del cluster HDInsight](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

   * Per collegare il cluster del servizio Livy, scegliere **Livy Service** (Servizio Livy) nel campo **Cluster Info**(Informazioni cluster) e immettere i valori in **Livy Endpoint** (Endpoint Livy) e **Cluster Name** (Nome cluster). Il campo **Yarn Endpoint** (Endpoint Yarn) è facoltativo. Nel campo **Authentication** (Autenticazione) sono disponibili due opzioni. Le opzioni sono **Basic Authentication** (Autenticazione di base) e **No Authentication** (Nessuna autenticazione). Se si seleziona **Basic Authentication** (Autenticazione di base), è necessario specificare **User Name** (Nome utente) e **Password**. Se si verifica un errore di autenticazione, controllare il nome utente e la password.
      
      ![Finestra di dialogo di collegamento di un cluster Livy](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)
   
3. È possibile visualizzare un cluster collegato nel nodo di **HDInsight** se le informazioni di input sono corrette. È ora possibile inviare un'applicazione al cluster collegato.

   ![Cluster collegato](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

4. È inoltre possibile scollegare un cluster in **Azure Explorer** (Esplora Azure).
   
   ![Cluster scollegato](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="create-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Creare un'applicazione Scala Spark in un cluster HDInsight Spark

1. Avviare IntelliJ IDEA e creare un progetto. Nella finestra di dialogo **New Project** (Nuovo progetto) seguire questa procedura: 

   a. Selezionare **HDInsight** > **Spark on HDInsight (Scala)** (Spark in HDInsight - Scala).

   b. Nell'elenco **Build tool** (Strumento di compilazione) selezionare uno degli strumenti seguenti, in base alla necessità:

      * **Maven**, per ottenere supporto per la creazione guidata di un progetto Scala
      * **SBT**, per la gestione delle dipendenze e la compilazione per il progetto Scala

    ![Finestra di dialogo del nuovo progetto](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

1. Selezionare **Avanti**.

1. La creazione guidata del progetto Scala rileva automaticamente se è installato il plug-in Scala. Selezionare **Installa**.

   ![Verifica del plug-in Scala](./media/apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

1. Per scaricare il plug-in Scala, selezionare **OK**. Seguire le istruzioni per riavviare IntelliJ. 

   ![Finestra di dialogo di installazione del plug-in Scala](./media/apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

1. Nella finestra **New Project** (Nuovo progetto) seguire questa procedura:  

    ![Selezione di Spark SDK](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

   a. Immettere un nome e un percorso per il progetto.

   b. Nell'elenco a discesa **Project SDK** (SDK progetto) selezionare **Java 1.8** per il cluster Spark 2.x oppure **Java 1.7** per il cluster Spark 1.x.

   c. Nell'elenco a discesa **Spark version** (Versione di Spark) la creazione guidata del progetto Scala inserisce la versione corretta per Spark SDK e Scala SDK. Se la versione del cluster Spark è precedente alla 2.0, selezionare **Spark 1.x**. In caso contrario, selezionare **Spark 2.x**. In questo esempio viene usata la versione **Spark 2.0.2 (Scala 2.11.8)**.

1. Selezionare **Fine**.

1. Il progetto Spark crea automaticamente un elemento. Per visualizzare l'elemento, seguire questa procedura:

   a. Dal menu **File** selezionare **Project Structure** (Struttura progetto).

   b. Nella finestra di dialogo **Project Structure** (Struttura progetto) selezionare **Artifacts** (Elementi) per visualizzare l'elemento predefinito creato. È anche possibile creare un elemento personalizzato selezionando il segno più (**+**).

      ![Informazioni sull'elemento nella finestra di dialogo](./media/apache-spark-intellij-tool-plugin/default-artifact.png)
      
1. Aggiungere il codice sorgente dell'applicazione seguendo questa procedura:

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

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Eseguire un'applicazione Spark in Scala in un cluster HDInsight Spark
Dopo aver creato un'applicazione Scala, è possibile inviarla al cluster.

1. In Project Explorer (Esplora progetti) individuare un file Java o Scala e quindi scegliere **Submit Spark Application to HDInsight** (Invia applicazione Spark a HDInsight) dal menu di scelta rapida.
    
      ![Comando di invio dell'applicazione Spark a HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. Nella finestra di dialogo per la configurazione fornire i valori seguenti e quindi fare clic su **SparkJobRun**.

      ![Finestra di dialogo Spark Submission (Invio Spark)](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)
      
    * Per **Spark clusters (Linux only)**(Cluster Spark - solo Linux) selezionare il cluster Spark HDInsight in cui eseguire l'applicazione.

    * Selezionare un elemento nel progetto IntelliJ oppure selezionarne uno dal disco rigido.

    * Campo **Main class name** (Nome classe principale): il valore predefinito è la classe principale del file selezionato. È possibile modificare la classe selezionando i puntini di sospensione (**...**) e scegliendo un'altra classe.   

    * Campo **Job Configurations** (Configurazioni processo): i valori predefiniti sono illustrati nella figura seguente. È possibile modificare il valore o aggiungere un nuovo valore/chiave per l'invio del processo. Per altre informazioni, vedere [Apache Livy REST API](http://livy.incubator.apache.org./docs/latest/rest-api.html) (API REST di Apache Livy)

      ![Informazioni sulla configurazione del processo nella finestra di dialogo di invio di Spark](./media/apache-spark-intellij-tool-plugin/submit-job-configurations.png)

    * Campo **Command line arguments** (Argomenti della riga di comando): è possibile immettere i valori degli argomenti divisi da uno spazio per la classe principale, se necessario.

    * Campi **Referenced Jars** (JAR di riferimento) e **Referenced Files** (File di riferimento): è possibile immettere i percorsi per file e JAR di riferimento, se presenti. Per altre informazioni, vedere [Spark Configuration](https://spark.apache.org/docs/latest/configuration.html#runtime-environment) (Configurazione di Spark) 

      ![Informazioni sui file JAR nella finestra di dialogo di invio di Spark](./media/apache-spark-intellij-tool-plugin/jar-files-meaning.png)

       > [!NOTE]
       > Per caricare file e JAR di riferimento, vedere [Come caricare le risorse nel cluster](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer)
                         
    * **Upload Path** (Percorso di caricamento): è possibile indicare la posizione di archiviazione per l'invio di risorse del progetto Jar o Scala. Ci sono tre opzioni di archiviazione supportate: **Azure Blob** (BLOB di Azure), **Use Spark interactive session to upload artifacts** (Usa sessione interattiva di Spark per il caricamento di artefatti), **Use cluster default storage account** (Usa account di archiviazione predefinito del cluster) e **ADLS Gen1**. Nello screenshot seguente è selezionata l'opzione BLOB di Azure.

        ![Finestra di dialogo Spark Submission (Invio Spark)](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-upload-storage-types.png)

        ![Finestra di dialogo Spark Submission (Invio Spark)](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-upload-storage-blob.png)

3. Fare clic su **SparkJobRun** per inviare il progetto al cluster selezionato. Nella parte inferiore della scheda **Remote Spark Job in Cluster** (Processo Spark remoto nel cluster) è visualizzato lo stato dell'esecuzione del processo. È possibile arrestare l'applicazione facendo clic sul pulsante rosso. Per informazioni su come accedere all'output dei processi, vedere la sezione "Accedere e gestire i cluster HDInsight Spark tramite il Toolkit di Azure per IntelliJ" più avanti in questo articolo.
      
     ![Finestra Spark Submission (Invio Spark)](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)


## <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Eseguire il debug di applicazioni Spark in modalità locale o remota in un cluster HDInsight 
Si consiglia anche di usare un altro modo per inviare l'applicazione Spark al cluster. È ad esempio possibile configurare i parametri nell'IDE **Run/Debug configurations** (Esegui/Esegui il debug delle configurazioni). Per altre informazioni, vedere [Eseguire il debug delle applicazioni Spark su un cluster HDInsight con Azure Toolkit for IntelliJ tramite SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).



## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Accedere e gestire i cluster HDInsight Spark tramite il Toolkit di Azure per IntelliJ
È possibile eseguire diverse operazioni mediante il Toolkit Azure per IntelliJ.

### <a name="access-the-job-view"></a>Accedere alla visualizzazione del processo
1. In Azure Explorer espandere **HDInsight**, espandere il nome del cluster Spark e quindi selezionare **Processi**.  

    ![Nodo di visualizzazione dei processi](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

1. Nel riquadro destro la scheda **Spark Job View** (Visualizzazione processi Spark) visualizza tutte le applicazioni eseguite nel cluster. Selezionare il nome dell'applicazione per cui si vogliono visualizzare altri dettagli.

    ![Dettagli applicazione](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)
    >Note
    >

1. Per visualizzare le informazioni di base sui processi in esecuzione, passare il mouse sopra il grafico relativo al processo. Per visualizzare i grafici sulle fasi e le informazioni generate da ogni processo, selezionare un nodo nel grafico relativo al processo.

    ![Dettagli delle fasi dei processi](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

1. Per visualizzare i log di uso più frequente, ad esempio *Driver Stderr*, *Driver Stdout* e *Directory Info*, selezionare la scheda **Log**.

    ![Dettagli del log](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

1. È anche possibile visualizzare l'interfaccia utente della cronologia di Spark e l'interfaccia utente di YARN, a livello di applicazione, selezionando i rispettivi collegamenti nella parte superiore della finestra.

### <a name="access-the-spark-history-server"></a>Accedere al Server cronologia Spark
1. In Azure Explorer espandere **HDInsight**, fare clic con il pulsante destro del mouse sul nome del cluster Spark e quindi scegliere **Open Spark History UI** (Apri UI cronologia Spark). 

1. Quando richiesto, immettere le credenziali dell'amministratore del cluster, specificate durante la configurazione del cluster.

1. Nel dashboard del server della cronologia di Spark è possibile usare il nome dell'applicazione per cercare l'applicazione di cui è appena stata completata l'esecuzione. Nel codice precedente impostare il nome dell'applicazione usando `val conf = new SparkConf().setAppName("MyClusterApp")`. Il nome dell'applicazione Spark è quindi **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Avviare il portale di Ambari
1. In Azure Explorer espandere **HDInsight**, fare clic con il pulsante destro del mouse sul nome del cluster Spark e quindi scegliere **Open Cluster Management Portal (Ambari)** (Apri il portale di gestione cluster - Ambari). 

1. Quando richiesto, immettere le credenziali dell'amministratore per il cluster. Queste credenziali sono state specificate durante il processo di configurazione del cluster.

### <a name="manage-azure-subscriptions"></a>Gestire le sottoscrizioni di Azure
Per impostazione predefinita, il Toolkit di Azure per IntelliJ elenca i cluster Spark di tutte le sottoscrizioni di Azure. Se necessario, è possibile specificare le sottoscrizioni a cui si vuole accedere. 

1. In Azure Explorer fare clic con il pulsante destro del mouse sul nodo radice **Azure** e quindi scegliere **Gestisci sottoscrizioni**. 

1. Nella finestra di dialogo deselezionare le caselle di controllo accanto alla sottoscrizione alla quale non si vuole accedere e quindi selezionare **Chiudi**. È anche possibile selezionare **Esci** per uscire dalla sessione di sottoscrizione di Azure.

## <a name="spark-console"></a>Console Spark
È possibile eseguire la console locale Spark (Scala) o la console di sessione interattiva Spark Livy (Scala).

### <a name="spark-local-consolescala"></a>Console locale Spark (Scala)
1. Impostare la configurazione, se non è già stato fatto. Nella finestra **Run/Debug Configurations** (Esecuzione/debug configurazioni) fare clic su **+**->**Azure HDInsight Spark**, selezionare la scheda **Locally Run** (Esecuzione in locale) e **Remotely Run in Cluster** (Esecuzione remota nel cluster), scegliere la classe principale e quindi fare clic su **OK**.

    ![Impostazione della configurazione nella console locale](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)
 
2. Aprire il file della classe principale corrispondente, fare clic con il pulsante destro del mouse su **Spark Console** (Console Spark) e quindi fare clic su **Run Spark Local Console(Scala)** (Esegui console locale Spark - Scala). In alternativa, passare al menu **Tools** (Strumenti) ->**Spark Console** (Console Spark) ->**Run Spark Local Console(Scala)** (Esegui console locale Spark - Scala) per avviare la console. Verranno quindi visualizzate due finestre di dialogo in cui viene richiesto se si vuole correggere automaticamente le dipendenze. Fare clic sul pulsante **Auto Fix** (Correggi automaticamente).

    ![Correzione automatica Spark1](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![Correzione automatica Spark2](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

    ![Punto di ingresso console locale Spark](./media/apache-spark-intellij-tool-plugin/spark-console-local-entry-script.png)

3. Dopo il corretto avvio della console, l'aspetto sarà simile al seguente. È possibile eseguire le operazioni desiderate. Ad esempio, immettere **sc.appName** e premere CTRL+INVIO. Il risultato verrà visualizzato. È possibile terminare la console locale facendo clic sul pulsante rosso.

    ![Risultato nella console locale](./media/apache-spark-intellij-tool-plugin/local-console-result.png)


### <a name="spark-livy-interactive-session-consolescala"></a>Console di sessione interattiva Spark Livy (Scala)
È supportata solo in IntelliJ 2018.2 e 2018.3.

1. Impostare la configurazione, se non è già stato fatto. Nella finestra **Run/Debug Configurations** (Esecuzione/debug configurazioni) fare clic su **+**->**Azure HDInsight Spark**, selezionare la scheda **Remotely Run in Cluster** (Esecuzione remota nel cluster), scegliere il nome del cluster e la classe principale e quindi fare clic su **OK**.

    ![Aggiunta della voce di configurazione nella console interattiva](./media/apache-spark-intellij-tool-plugin/interactive-console-add-config-entry.png)

    ![Impostazione della configurazione nella console interattiva](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

2. Aprire il file corrispondente alla classe principale e fare clic con il pulsante destro del mouse su **Spark Console** (Console Spark) e quindi su **Run Spark Livy Interactive Session Console(Scala)** (Esegui console di sessione interattiva Spark Livy - Scala). In alternativa, passare al menu **Tools** (Strumenti) e fare clic su **Spark Console** (Console Spark) e quindi su **Run Spark Livy Interactive Session Console(Scala)** (Esegui console di sessione interattiva Spark Livy - Scala) per avviare la console.

3. Dopo l'avvio della console, è possibile eseguire le operazioni desiderate. Ad esempio, immettere **sc.appName** e premere CTRL+INVIO. Il risultato verrà visualizzato.

    ![Risultato nella console interattiva](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Send Selection to Spark Console (Invia selezione alla console Spark)
È utile prevedere il risultato dello script inviando una parte di codice alla console locale o alla console di sessione interattiva Livy (Scala). Evidenziare una parte di codice nel file di Scala, quindi fare clic con il pulsante destro del mouse su **Send Selection To Spark Console** (Invia selezione alla console Spark). Il codice selezionato verrà inviato alla console e verrà eseguito. Il risultato verrà visualizzato dopo il codice nella console. La console verificherà se sono presenti errori. 

   ![Send Selection to Spark Console (Invia selezione alla console Spark)](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Convertire le applicazioni IntelliJ IDEA esistenti per usare il Toolkit di Azure per IntelliJ
È possibile convertire le applicazioni Spark Scala esistenti create in IntelliJ IDEA per renderle compatibili con Azure Toolkit for IntelliJ. È possibile usare il plug-in per inviare le applicazioni a un cluster HDInsight Spark.

1. Per un'applicazione Spark Scala esistente creata tramite IntelliJ IDEA, aprire il file con estensione IML associato.

1. A livello di radice viene visualizzato un elemento **module** simile al seguente:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Modificare l'elemento per aggiungere `UniqueKey="HDInsightTool"` in modo che l'elemento **module** sia simile al seguente:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

1. Salvare le modifiche. L'applicazione dovrebbe ora essere compatibile con il Toolkit di Azure per IntelliJ. È possibile verificarlo facendo clic con il pulsante destro del mouse sul nome del progetto in Project Explorer (Esplora progetti). Nel menu a comparsa viene ora visualizzata l'opzione **Submit Spark Application to HDInsight**(Invia applicazione Spark a HDInsight).

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
Se il cluster è occupato, è possibile che venga visualizzato l'errore riportato di seguito.

![Errore Intellij in caso di cluster occupato](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-upload.png)

![Errore Intellij in caso di cluster occupato](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-submit.png)

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
* [Analisi dei log del sito Web con Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

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
