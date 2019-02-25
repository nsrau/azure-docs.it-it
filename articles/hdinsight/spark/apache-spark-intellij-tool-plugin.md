---
title: 'Azure Toolkit for IntelliJ: creazione di applicazioni Spark per cluster HDInsight '
description: Usare il Toolkit di Azure per IntelliJ per sviluppare applicazioni Spark scritte in Scala e inoltrarle a un cluster HDInsight Spark.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: maxluk
ms.openlocfilehash: a22dd1114b6ad49695b1ce7cab2ff26f23b7e1be
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447855"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Usare Azure Toolkit for IntelliJ per creare applicazioni Apache Spark per un cluster HDInsight

Usare il plug-in Azure Toolkit for IntelliJ per sviluppare applicazioni [Apache Spark](https://spark.apache.org/) scritte in [Scala](https://www.scala-lang.org/) e quindi inoltrarle a un cluster HDInsight Spark direttamente dall'ambiente di sviluppo integrato (IDE) IntelliJ. È possibile usare il plug-in in vari modi:

* Sviluppare e inviare un'applicazione Spark in Scala in un cluster HDInsight Spark.
* Accedere alle risorse cluster HDInsight Spark di Azure.
* Sviluppare ed eseguire un'applicazione Spark in Scala localmente.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* [Kit di sviluppo di Oracle Java](https://www.azul.com/downloads/azure-only/zulu/).  Questa esercitazione usa Java versione 8.0.202.
* IntelliJ IDEA. Questo articolo usa [IntelliJ IDEA Community versione  2018.3.4](https://www.jetbrains.com/idea/download/).
* Azure Toolkit for IntelliJ.  Vedere [Installazione di Azure Toolkit per IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).
* WINUTILS.EXE.  Vedere [Problemi durante l'esecuzione di Hadoop in Windows](https://wiki.apache.org/hadoop/WindowsProblems).

## <a name="install-scala-plugin-for-intellij-idea"></a>Installare il plug-in Scala per IntelliJ IDEA
Per installare il plug-in di Scala, procedere come segue:

1. Aprire IntelliJ IDEA.

2. Nella schermata iniziale, passare a **Configura** > **Plug-in** per aprire la finestra **Plug-in**.
   
    ![Abilitare i plug-in Scala](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin.png)

3. Selezionare **Installa** per il plug-in Scala che è disponibile nella nuova finestra.  

    ![Installare i plug-in Scala](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. Dopo che il plug-in è stato installato correttamente, è necessario riavviare l'IDE.


## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Creare un'applicazione Scala Spark per un cluster HDInsight Spark

1. Avviare IntelliJ IDEA e selezionare **Crea nuovo progetto** per aprire la finestra **Nuovo progetto**.

2. Selezionare **Azure Spark/HDInsight** nel riquadro sinistro.

3. Selezionare **Progetto Spark (Scala)** dalla finestra principale.

4. Nell'elenco **Strumento di compilazione** selezionare uno degli strumenti seguenti:
      * **Maven**, per ottenere supporto per la creazione guidata di un progetto Scala.
      * **SBT**, per la gestione delle dipendenze e la compilazione per il progetto Scala.

    ![Finestra di dialogo del nuovo progetto](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Selezionare **Avanti**.

6. Nella finestra **Nuovo progetto** specificare le informazioni seguenti:  

    |  Proprietà   | DESCRIZIONE   |  
    | ----- | ----- |  
    |Project name (Nome progetto)| Immettere un nome.  Questa esercitazione usa `myApp`.|  
    |Project&nbsp;location (Percorso progetto)| Immettere il percorso desiderato in cui salvare il progetto.|
    |Project SDK (SDK progetto)| Potrebbe essere vuoto al primo uso di IDEA.  Selezionare **New...** (Nuovo) e passare al proprio JDK.|
    |Versione Spark|La procedura guidata di creazione integra la versione corretta dell'SDK di Spark e Scala. Se la versione del cluster Spark è precedente alla 2.0, selezionare **Spark 1.x**. In caso contrario, selezionare **Spark 2.x**. In questo esempio viene usata la versione **Spark 2.3.0 (Scala 2.11.8)**.|

    ![Selezione di Spark SDK](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

7. Selezionare **Fine**.  Potrebbero occorrere alcuni minuti prima che il progetto diventi disponibile.

8. Il progetto Spark crea automaticamente un elemento. Per visualizzare l'elemento, seguire questa procedura:

   a. Dalla barra dei menu passare a **File** > **Struttura del progetto...**.

   b. Dalla finestra **Struttura del progetto**, selezionare **Artefatti**.  

   c. Selezionare **Annulla** dopo aver visualizzato l'elemento.

      ![Informazioni sull'elemento nella finestra di dialogo](./media/apache-spark-intellij-tool-plugin/default-artifact.png)

9. Aggiungere il codice sorgente dell'applicazione seguendo questa procedura:

    a. Dal progetto, passare a **myApp** > **src** > **principale** > **scala**.  

    b. Fare doppio clic su **scala**, quindi passare a **Nuovo** > **classe Scala**.

   ![Comandi per la creazione di una classe Scala in Progetto](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. Nella finestra di dialogo **Create New Scala Class** (Crea nuova classe Scala) immettere un nome, selezionare **Object** (Oggetto) per il menu a discesa **Kind** (Tipologia) e quindi selezionare **OK**.

     ![Finestra di dialogo Create New Scala Class (Crea nuova classe Scala)](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. Il file **myApp.scala** viene quindi aperto nella visualizzazione principale. Sostituire il codice predefinito con il codice seguente:  

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object myApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("myApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasbs:///HVACOut")
            }
    
        }

    Il codice legge i dati dal file HVAC.csv, disponibile in tutti i cluster HDInsight Spark, recupera le righe con una sola cifra nella settima colonna del file CSV e scrive l'output in `/HVACOut` nel contenitore di archiviazione predefinito per il cluster.

## <a name="connect-to-your-hdinsight-cluster"></a>Connettersi al cluster HDInsight
Per iniziare è possibile [accedere alla sottoscrizione di Azure](#sign-in-to-your-azure-subscription) oppure [collegare un cluster HDInsight](#link-a-cluster) usando un nome utente e una password Ambari o le credenziali di un account aggiunto al dominio per connettersi al cluster HDInsight.

### <a name="sign-in-to-your-azure-subscription"></a>Accedere alla sottoscrizione di Azure.

1. Dalla barra dei menu, passare a **Visualizza** > **strumento Windows** > **Azure Explorer**.
       
   ![Collegamento di Azure Explorer](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. Da Azure Explorer, fare clic con il pulsante destro del mouse sul nodo **Azure** e quindi scegliere **Sign In** (Accedi).

3. Nella finestra di dialogo di **Azure Sign In** (Accesso ad Azure) fare clic su **Sign-in** (Accedi) e immettere le credenziali di Azure.

    ![Finestra di dialogo di accesso di Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. Dopo l'accesso, la finestra di dialogo **Selezionare le sottoscrizioni** elenca tutte le sottoscrizioni di Azure associate alle credenziali. Selezionare la sottoscrizione e quindi il pulsante **Aggiorna**.

    ![Finestra di dialogo Seleziona sottoscrizioni](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

5. Da **Azure Explorer**, espandere **HDInsight** per visualizzare i cluster HDInsight Spark disponibili nella sottoscrizione.

    ![Cluster HDInsight Spark in Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. Per visualizzare le risorse, ad esempio gli account di archiviazione, associate al cluster, è possibile espandere ancora un nodo di tipo nome del cluster.

    ![Nodo di tipo nome del cluster espanso](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

### <a name="link-a-cluster"></a>Collegare un cluster
È possibile collegare un cluster HDInsight usando lo username gestito di Apache Ambari. Analogamente, è possibile collegare un cluster HDInsight aggiunto al dominio tramite il dominio e il nome utente, come user1@contoso.com. È anche possibile collegare il cluster del servizio Livy.

1. Dalla barra dei menu, passare a **Visualizza** > **strumento Windows** > **Azure Explorer**.

2. Da Azure Explorer, fare clic con il pulsante destro del mouse sul nodo **HDInsight** e quindi scegliere **Link A Cluster** (collegare un cluster).

   ![Menu di scelta rapida per collegare un cluster](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

3. Le opzioni disponibili nella finestra **Link A Cluster** (collegare un cluster) variano a seconda di quale valore si seleziona dall'elenco a discesa **Link Resource Type** (tipo di risorsa di collegamento).  Immettere i valori e quindi selezionare **OK**.

    * **Cluster HDInsight**  
  
        |Proprietà |Valore |
        |----|----|
        |Tipo di risorsa di collegamento|Selezionare **HDInsight Cluster** nell'elenco a discesa.|
        |Nome/URL del cluster| Immettere il nome del cluster.|
        |Tipo di autenticazione| Lasciare come **autenticazione di base**|
        |User Name| Immettere il nome utente del cluster, il cui valore predefinito è admin.|
        |Password| Immettere la password per il nome utente.|
    
        ![Finestra di dialogo per il collegamento del cluster HDInsight](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Livy Service**  
  
        |Proprietà |Valore |
        |----|----|
        |Tipo di risorsa di collegamento|Selezionare **Livy Service** nell'elenco a discesa.|
        |Livy Endpoint| Inserire Livy Endpoint|
        |Cluster Name| Immettere il nome del cluster.|
        |Yarn Endpoint|facoltativo.|
        |Tipo di autenticazione| Lasciare come **autenticazione di base**|
        |User Name| Immettere il nome utente del cluster, il cui valore predefinito è admin.|
        |Password| Immettere la password per il nome utente.|

        ![Finestra di dialogo di collegamento di un cluster Livy](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

4. È possibile visualizzare il cluster collegato dal nodo **HDInsight**.

   ![Cluster collegato](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

5. È inoltre possibile scollegare un cluster in **Azure Explorer** (Esplora Azure).

   ![Cluster scollegato](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Eseguire un'applicazione Spark in Scala in un cluster HDInsight Spark
Dopo aver creato un'applicazione Scala, è possibile inviarla al cluster.

1. Da Progetto, passare a **myApp** > **src** > **principale** > **scala** > **myApp**.  Fare clic con il pulsante destro del mouse su **myApp** e selezionare **Submit Spark Application** (probabilmente sarà disponibile nella parte inferiore dell'elenco).
    
      ![Comando di invio dell'applicazione Spark a HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. Nella finestra di dialogo **Submit Spark Application**, selezionare **1. Spark in HDInsight**.

3. Nella finestra **Modifica configurazione** specificare i valori seguenti e quindi selezionare **OK**:

    |Proprietà |Valore |
    |----|----|
    |Cluster Spark (solo Linux)|Selezionare il cluster HDInsight Spark in cui eseguire l'applicazione.|
    |Selezionare un artefatto da inviare|Lasciare l'impostazione predefinita.|
    |Nome classe principale|il valore predefinito è la classe principale del file selezionato. È possibile modificare la classe selezionando i puntini di sospensione (**...**) e scegliendo un'altra classe.|
    |Configurazioni del processo|È possibile modificare le chiavi predefinite e/o i valori. Per altre informazioni, vedere [Apache Livy REST API](http://livy.incubator.apache.org./docs/latest/rest-api.html) (API REST di Apache Livy).|
    |Argomenti della riga di comando|È possibile immettere gli argomenti divisi da uno spazio per la classe principale, se necessario.|
    |Referenced Jars (file JAR di riferimento) e Referenced Files (file di riferimento)|è possibile immettere i percorsi dei file JAR di riferimento e dei file di riferimento, se presenti. Per altre informazioni: [Apache Spark Configuration](https://spark.apache.org/docs/latest/configuration.html#runtime-environment) (Configurazione di Apache Spark).  Vedere anche [Come caricare le risorse nel cluster](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).|
    |Archivio di caricamento del processo|Espandere per visualizzare le opzioni aggiuntive.|
    |Tipo di archiviazione|Selezionare **Usare Azure Blob per caricare** nell'elenco a discesa.|
    |Account di archiviazione|Immettere l'account di archiviazione.|
    |Chiave di archiviazione|Immettere la chiave di archiviazione.|
    |Contenitore di archiviazione|Selezionare il contenitore di archiviazione dall'elenco a discesa una volta immessi **Account di archiviazione** e **chiave di archiviazione**.|

    ![Finestra di dialogo Spark Submission (Invio Spark)](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

4. Fare clic su **SparkJobRun** per inviare il progetto al cluster selezionato. Nella parte inferiore della scheda **Remote Spark Job in Cluster** (Processo Spark remoto nel cluster) è visualizzato lo stato dell'esecuzione del processo. È possibile arrestare l'applicazione facendo clic sul pulsante rosso. Per informazioni su come accedere all'output dei processi, vedere la sezione "Accedere e gestire i cluster HDInsight Spark tramite il Toolkit di Azure per IntelliJ" più avanti in questo articolo.  
      
    ![Finestra Spark Submission (Invio Spark)](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Eseguire il debug di applicazioni Apache Spark in modalità locale o remota in un cluster HDInsight 
Si consiglia anche di usare un altro modo per inviare l'applicazione Spark al cluster. È ad esempio possibile configurare i parametri nell'IDE **Run/Debug configurations** (Esegui/Esegui il debug delle configurazioni). Per altre informazioni, vedere [Eseguire il debug delle applicazioni Apache Spark in modalità remota o locale su un cluster HDInsight con Azure Toolkit for IntelliJ tramite SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Accedere e gestire i cluster HDInsight Spark tramite il Toolkit di Azure per IntelliJ
È possibile eseguire diverse operazioni mediante il Toolkit Azure per IntelliJ.  La maggior parte delle operazioni vengono avviate da **Azure Explorer**.  Dalla barra dei menu, passare a **Visualizza** > **Strumento Windows** > **Azure Explorer**.

### <a name="access-the-job-view"></a>Accedere alla visualizzazione del processo

1. Da Azure Explorer, passare a **HDInsight** > \<Cluster >> **Processi**.

    ![Nodo di visualizzazione dei processi](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. Nel riquadro destro la scheda **Spark Job View** (Visualizzazione processi Spark) visualizza tutte le applicazioni eseguite nel cluster. Selezionare il nome dell'applicazione per cui si vogliono visualizzare altri dettagli.

    ![Dettagli applicazione](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

3. Per visualizzare le informazioni di base sui processi in esecuzione, passare il mouse sopra il grafico relativo al processo. Per visualizzare i grafici sulle fasi e le informazioni generate da ogni processo, selezionare un nodo nel grafico relativo al processo.

    ![Dettagli delle fasi dei processi](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Per visualizzare i log di uso più frequente, ad esempio *Driver Stderr*, *Driver Stdout* e *Directory Info*, selezionare la scheda **Log**.

    ![Dettagli del log](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. È anche possibile visualizzare l'interfaccia utente della cronologia di Spark e l'interfaccia utente di YARN, a livello di applicazione, selezionando i rispettivi collegamenti nella parte superiore della finestra.

### <a name="access-the-spark-history-server"></a>Accedere al Server cronologia Spark

1. Da Azure Explorer espandere **HDInsight**, fare clic con il pulsante destro del mouse sul nome del cluster Spark e quindi scegliere **Open Spark History UI** (Apri UI cronologia Spark).  
2. Quando richiesto, immettere le credenziali dell'amministratore del cluster, specificate durante la configurazione del cluster.

3. Nel dashboard del server della cronologia di Spark è possibile usare il nome dell'applicazione per cercare l'applicazione di cui è appena stata completata l'esecuzione. Nel codice precedente impostare il nome dell'applicazione usando `val conf = new SparkConf().setAppName("myApp")`. Il nome dell'applicazione Spark è quindi **myApp**.

### <a name="start-the-ambari-portal"></a>Avviare il portale di Ambari

1. Da Azure Explorer espandere **HDInsight**, fare clic con il pulsante destro del mouse sul nome del cluster Spark e quindi scegliere **Open Cluster Management Portal (Ambari)** (Apri il portale di gestione cluster - Ambari).  

2. Quando richiesto, immettere le credenziali dell'amministratore per il cluster. Queste credenziali sono state specificate durante il processo di configurazione del cluster.

### <a name="manage-azure-subscriptions"></a>Gestire le sottoscrizioni di Azure
Per impostazione predefinita, il Toolkit di Azure per IntelliJ elenca i cluster Spark di tutte le sottoscrizioni di Azure. Se necessario, è possibile specificare le sottoscrizioni a cui si vuole accedere.  

1. Da Azure Explorer fare clic con il pulsante destro del mouse sul nodo radice **Azure** e quindi scegliere **Scegli sottoscrizioni**.  

2. Dalla finestra **Seleziona sottoscrizioni**, deselezionare le caselle di controllo accanto alla sottoscrizione alla quale non si vuole accedere e quindi selezionare **Chiudi**.

## <a name="spark-console"></a>Console Spark
È possibile eseguire la console locale Spark (Scala) o la console di sessione interattiva Spark Livy (Scala).

### <a name="spark-local-consolescala"></a>Console locale Spark (Scala)
Assicurarsi di aver soddisfatto il prerequisito WINUTILS.EXE.

1. Dalla barra dei menu, passare a **Eseguiti** > **Modifica configurazioni...**.

2. Dalla finestra **Eseguire configurazioni di debug**, nel riquadro a sinistra, passare a **Apache Spark in HDInsight** > **[Spark in HDInsight] myApp**.

3. Nella finestra principale, selezionare la scheda **Esecuzione locale**.

4. Fornire i valori seguenti e quindi selezionare **OK**:

    |Proprietà |Valore |
    |----|----|
    |Classe principale di processo|il valore predefinito è la classe principale del file selezionato. È possibile modificare la classe selezionando i puntini di sospensione (**...**) e scegliendo un'altra classe.|
    |Variabili di ambiente|Verificare che il valore per HADOOP_HOME sia corretto.|
    |WinUtils.exe Location (Posizione WinUtils.exe)|Verificare che il percorso sia corretto.|

    ![Impostazione della configurazione nella console locale](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. Da Progetto, passare a **myApp** > **src** > **principale** > **scala** > **myApp**.  

6. Dalla barra dei menu, passare a **Strumenti** > **Console Spark** > **Eseguire Console locale Spark (Scala)**.

7. Si potrebbero quindi visualizzare due finestre di dialogo in cui viene richiesto se si vuole correggere automaticamente le dipendenze. In questo caso, selezionare **Correggi automaticamente**.

    ![Correzione automatica Spark1](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![Correzione automatica Spark2](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

8. La console sarà simile alla figura riportata di seguito. Nella finestra della console digitare `sc.appName`, quindi premere ctrl + invio.  Verrà visualizzato il risultato. È possibile terminare la console locale facendo clic sul pulsante rosso.

    ![Risultato nella console locale](./media/apache-spark-intellij-tool-plugin/local-console-result.png)


### <a name="spark-livy-interactive-session-consolescala"></a>Console di sessione interattiva Spark Livy (Scala)
È supportata solo in IntelliJ 2018.2 e 2018.3.

1. Dalla barra dei menu, passare a **Eseguire** > **Modifica configurazioni...**.

2. Dalla finestra **Eseguire configurazioni di debug**, nel riquadro a sinistra, passare a **Apache Spark in HDInsight** > **[Spark in HDInsight] myApp**.

3. Nella finestra principale, selezionare la scheda **Remotely Run in Cluster** (Eseguito in modalità remota nel Cluster).

4. Fornire i valori seguenti e quindi selezionare **OK**:

    |Proprietà |Valore |
    |----|----|
    |Cluster Spark (solo Linux)|Selezionare il cluster HDInsight Spark in cui eseguire l'applicazione.|
    |Nome classe principale|il valore predefinito è la classe principale del file selezionato. È possibile modificare la classe selezionando i puntini di sospensione (**...**) e scegliendo un'altra classe.|

    ![Impostazione della configurazione nella console interattiva](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. Da Progetto, passare a **myApp** > **src** > **principale** > **scala** > **myApp**.  

6. Dalla barra dei menu, passare a **Strumenti** > **Console Spark** > **Eseguire Console locale Spark (Scala)**.

7. La console sarà simile alla figura riportata di seguito. Nella finestra della console digitare `sc.appName`, quindi premere ctrl + invio.  Verrà visualizzato il risultato. È possibile terminare la console locale facendo clic sul pulsante rosso.

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

1. Salvare le modifiche. L'applicazione dovrebbe ora essere compatibile con il Toolkit di Azure per IntelliJ. È possibile verificarlo facendo clic con il pulsante destro del mouse sul nome del progetto in Progetti. Nel menu a comparsa viene ora visualizzata l'opzione **Submit Spark Application to HDInsight**(Invia applicazione Spark a HDInsight).

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="error-in-local-run-use-a-larger-heap-size"></a>Errore nell'esecuzione locale: *Usare dimensioni di heap maggiori*
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
* [Panoramica: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demo
* Creare il progetto Scala (video): [Create Apache Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (Creare applicazioni Apache Spark in Scala)
* Debug remoto (video): [Use Azure Toolkit for IntelliJ to debug Apache Spark applications remotely on HDInsight Cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark nel cluster HDInsight)

### <a name="scenarios"></a>Scenari
* [Apache Spark con BI: eseguire l’analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](apache-spark-use-bi-tools.md)
* [Apache Spark con apprendimento automatico: usare Spark in HDInsight per analizzare la temperatura di un edificio con dati HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark con apprendimento automatico: usare Spark in HDInsight per stimare i risultati di controllo degli alimenti](apache-spark-machine-learning-mllib-ipython.md)
* [Analisi dei log del sito Web con Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Creazione ed esecuzione di applicazioni
* [Creare un'applicazione autonoma con Scala](apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni
* [Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark tramite VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark tramite SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usare gli strumenti HDInsight per IntelliJ con Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Usare gli strumenti HDInsight in Azure Toolkit for Eclipse per creare applicazioni Apache Spark](apache-spark-eclipse-tool-plugin.md)
* [Usare i notebook di Apache Zeppelin con cluster Apache Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Apache Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gestione delle risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)
