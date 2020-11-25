---
title: 'Azure Toolkit for IntelliJ: app Spark-HDInsight'
description: Usare il Toolkit di Azure per IntelliJ per sviluppare applicazioni Spark scritte in Scala e inoltrarle a un cluster HDInsight Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/13/2020
ms.openlocfilehash: b70de1915e6c068bd658dff60776085075412789
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024011"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-hdinsight-cluster"></a>Usare Azure Toolkit for IntelliJ per creare applicazioni Apache Spark per un cluster HDInsight

Questo articolo illustra come sviluppare applicazioni di Apache Spark in Azure HDInsight usando il plug-in **Azure Toolkit** per l'IDE di IntelliJ. [Azure HDInsight](../hdinsight-overview.md) è un servizio di analisi open source gestito nel cloud. Il servizio consente di usare framework open source come Hadoop, Apache Spark, Apache Hive e Apache Kafka.

È possibile usare il plug-in **Toolkit di Azure** in diversi modi:

* Sviluppare e inviare un'applicazione Spark in scala a un cluster HDInsight Spark.
* Accedere alle risorse cluster HDInsight Spark di Azure.
* Sviluppare ed eseguire un'applicazione Spark in Scala localmente.

In questo articolo vengono illustrate le operazioni seguenti:
> [!div class="checklist"]
> * Usare Azure Toolkit for IntelliJ
> * Sviluppare applicazioni Apache Spark
> * Inviare un'applicazione al cluster Azure HDInsight

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Kit di sviluppo di Oracle Java](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  Questo articolo usa la versione Java 8.0.202.

* IntelliJ IDEA. Questo articolo usa [IntelliJ IDEA Community versione  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Vedere [Installazione di Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/).

## <a name="install-scala-plugin-for-intellij-idea"></a>Installare il plug-in Scala per IntelliJ IDEA

Passaggi per installare il plug-in scala:

1. Aprire IntelliJ IDEA.

2. Nella schermata iniziale, passare a **Configure** (Configura) > **Plugin** per aprire la finestra **Plugin**.

    ![IntelliJ IDEA Abilita il plug-in scala](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin1.png)

3. Selezionare **Installa** per il plug-in Scala che è disponibile nella nuova finestra.  

    ![IntelliJ IDEA installa il plug-in scala](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. Dopo che il plug-in è stato installato correttamente, è necessario riavviare l'IDE.

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Creare un'applicazione Scala Spark per un cluster HDInsight Spark

1. Avviare IntelliJ IDEA e selezionare **Crea nuovo progetto** per aprire la finestra **Nuovo progetto**.

2. Selezionare **Azure Spark/HDInsight** nel riquadro sinistro.

3. Selezionare **Progetto Spark (Scala)** dalla finestra principale.

4. Dall'elenco a discesa **strumento di compilazione** , selezionare una delle opzioni seguenti:
   * **Maven**, per ottenere supporto per la creazione guidata di un progetto Scala.
   * **SBT**, per la gestione delle dipendenze e la compilazione per il progetto Scala.

     ![IntelliJ IDEA: finestra di dialogo per un nuovo progetto](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Selezionare **Avanti**.

6. Nella finestra **Nuovo progetto** specificare le informazioni seguenti:  

    |  Proprietà   | Descrizione   |  
    | ----- | ----- |  
    |Project name (Nome progetto)| Immettere un nome.  Questo articolo usa `myApp`.|  
    |Project&nbsp;location (Percorso progetto)| Immettere il percorso in cui salvare il progetto.|
    |Project SDK (SDK progetto)| Questo campo può essere vuoto al primo utilizzo di IDEA.  Selezionare **New** (Nuovo) e passare al proprio JDK.|
    |Versione Spark|La creazione guidata integra la versione corretta dell'SDK di Spark e Scala. Se la versione del cluster Spark è precedente alla 2.0, selezionare **Spark 1.x**. In caso contrario, selezionare **Spark2.x**. In questo esempio viene usata la versione **Spark 2.3.0 (Scala 2.11.8)** .|

    ![Selezione dell'SDK Apache Spark](./media/apache-spark-intellij-tool-plugin/intellij-new-project.png)

7. Selezionare **Fine**.  Potrebbero occorrere alcuni minuti prima che il progetto diventi disponibile.

8. Il progetto Spark crea automaticamente un artefatto. Per visualizzare l'artefatto, eseguire questa procedura:

   a. Dalla barra dei menu passare a **File** > **Struttura del progetto...** .

   b. Dalla finestra **Struttura del progetto**, selezionare **Artefatti**.  

   c. Selezionare **Annulla**  dopo aver visualizzato l'artefatto.

      ![Informazioni sull'artefatto nella finestra di dialogo](./media/apache-spark-intellij-tool-plugin/default-artifact-dialog.png)

9. Aggiungere il codice sorgente dell'applicazione seguendo questa procedura:

    a. Da Project, passare a **MyApp**  >  **src**  >  **Main**  >  **scala**.  

    b. Fare doppio clic su **scala**, quindi passare a **Nuovo** > **classe Scala**.

   ![Comandi per la creazione di una classe Scala in Progetto](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. Nella finestra di dialogo **Create New Scala Class** (Crea nuova classe Scala) immettere un nome, selezionare **Object** (Oggetto) per il menu a discesa **Kind** (Tipologia) e quindi selezionare **OK**.

     ![Finestra di dialogo Create New Scala Class (Crea nuova classe Scala)](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. Il file **myApp.scala** viene quindi aperto nella visualizzazione principale. Sostituire il codice predefinito con il codice seguente:  

    ```scala
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
    ```

    Il codice legge i dati dal file HVAC.csv, disponibile in tutti i cluster HDInsight Spark, recupera le righe con una sola cifra nella settima colonna del file CSV e scrive l'output in `/HVACOut` nel contenitore di archiviazione predefinito per il cluster.

## <a name="connect-to-your-hdinsight-cluster"></a>Connettersi al cluster HDInsight

L'utente può [accedere alla sottoscrizione di Azure](#sign-in-to-your-azure-subscription)o [collegare un cluster HDInsight](#link-a-cluster). Usare il nome utente/password di Ambari o le credenziali aggiunti a un dominio per connettersi al cluster HDInsight.

### <a name="sign-in-to-your-azure-subscription"></a>Accedere alla sottoscrizione di Azure.

1. Dalla barra dei menu, passare a **Visualizza** > **strumento Windows** > **Azure Explorer**.

   ![IntelliJ IDEA Mostra Azure Explorer](./media/apache-spark-intellij-tool-plugin/show-azure-explorer1.png)

2. Da Azure Explorer, fare clic con il pulsante destro del mouse sul nodo **Azure** e quindi scegliere **Sign In** (Accedi).

   ![IntelliJ IDEA: fare clic con il pulsante destro del mouse su Azure in Azure Explorer](./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png)

3. Nella finestra di dialogo **Azure Sign In** (Accesso ad Azure) scegliere **Accesso dispositivo** e quindi selezionare **Accedi**.

    !["IntelliJ IDEA Azure Login Device](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer2.png)

4. Nella finestra di dialogo **Azure Device Login** (Accesso dispositivo Azure) fare clic su **Copy&Open** (Copia e apri).

   ![' IntelliJ IDEA login dispositivo Azure](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer5.png)

5. Nell'interfaccia del browser incollare il codice e quindi fare clic su **Avanti**.

   ![Finestra di dialogo ' Microsoft Enter code per HDI '](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer6.png)

6. Immettere le credenziali di Azure e quindi chiudere il browser.

   ![' Microsoft immettere la finestra di dialogo di posta elettronica per HDI '](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer7.png)

7. Dopo l'accesso, la finestra di dialogo **Selezionare le sottoscrizioni** elenca tutte le sottoscrizioni di Azure associate alle credenziali. Selezionare la sottoscrizione e quindi il pulsante **Aggiorna**.

    ![Finestra di dialogo Seleziona sottoscrizioni](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

8. Da **Azure Explorer** espandere **HDInsight** per visualizzare i cluster HDInsight Spark disponibili nelle sottoscrizioni.

    ![IntelliJ IDEA: visualizzazione principale di Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer3.png)

9. Per visualizzare le risorse, ad esempio gli account di archiviazione, associate al cluster, è possibile espandere ancora un nodo di tipo nome del cluster.

    ![Account di archiviazione in Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer4.png)

### <a name="link-a-cluster"></a>Collegare un cluster

È possibile collegare un cluster HDInsight usando lo username gestito di Apache Ambari. Analogamente, è possibile collegare un cluster HDInsight aggiunto al dominio tramite il dominio e il nome utente, come `user1@contoso.com`. È anche possibile collegare il cluster del servizio Livy.

1. Dalla barra dei menu, passare a **Visualizza** > **strumento Windows** > **Azure Explorer**.

1. Da Azure Explorer, fare clic con il pulsante destro del mouse sul nodo **HDInsight** e quindi scegliere **Link A Cluster** (collegare un cluster).

   ![Menu di scelta rapida per collegare un cluster in Azure Explorer](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. Le opzioni disponibili nella finestra **Link A Cluster** (collegare un cluster) variano a seconda di quale valore si seleziona dall'elenco a discesa **Link Resource Type** (tipo di risorsa di collegamento).  Immettere i valori e quindi selezionare **OK**.

    * **Cluster HDInsight**  
  
        |Proprietà |Valore |
        |----|----|
        |Tipo di risorsa di collegamento|Selezionare **HDInsight Cluster** nell'elenco a discesa.|
        |Nome/URL del cluster| Immettere il nome del cluster.|
        |Tipo di autenticazione| Lasciare come **autenticazione di base**|
        |Nome utente| Immettere il nome utente del cluster, il cui valore predefinito è admin.|
        |Password| Immettere la password per il nome utente.|

        ![IntelliJ IDEA: finestra di dialogo per collegare un cluster](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Livy Service**  
  
        |Proprietà |Valore |
        |----|----|
        |Tipo di risorsa di collegamento|Selezionare **Livy Service** nell'elenco a discesa.|
        |Livy Endpoint| Inserire Livy Endpoint|
        |Cluster Name| Immettere il nome del cluster.|
        |Yarn Endpoint|facoltativo.|
        |Tipo di autenticazione| Lasciare come **autenticazione di base**|
        |Nome utente| Immettere il nome utente del cluster, il cui valore predefinito è admin.|
        |Password| Immettere la password per il nome utente.|

        ![IntelliJ IDEA: finestra di dialogo per collegare un cluster Livy](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

1. È possibile visualizzare il cluster collegato dal nodo **HDInsight**.

   ![Cluster collegato in Azure Explorer 1](./media/apache-spark-intellij-tool-plugin/hdinsight-linked-cluster.png)

1. È inoltre possibile scollegare un cluster in **Azure Explorer** (Esplora Azure).

   ![Scollegamento del cluster in Azure Explorer](./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Eseguire un'applicazione Spark in Scala in un cluster HDInsight Spark

Dopo aver creato un'applicazione Scala, è possibile inviarla al cluster.

1. Da Project (Progetto) passare a **myApp** > **src** > **main** > **scala** > **myApp**.  Fare clic con il pulsante destro del mouse su **myApp** e selezionare **Submit Spark Application** (probabilmente sarà disponibile nella parte inferiore dell'elenco).

      ![Comando di invio dell'applicazione Spark a HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. Nella finestra di dialogo **Invia applicazione Spark** selezionare **1. Spark in HDInsight**.

3. Nella finestra **Modifica configurazione** specificare i valori seguenti e quindi selezionare **OK**:

    |Proprietà |valore |
    |----|----|
    |Cluster Spark (solo Linux)|Selezionare il cluster HDInsight Spark in cui eseguire l'applicazione.|
    |Selezionare un artefatto da inviare|Lasciare l'impostazione predefinita.|
    |Nome della classe principale|Il valore predefinito corrisponde alla classe principale del file selezionato. È possibile modificare la classe selezionando i puntini di sospensione ( **...** ) e scegliendo una classe diversa.|
    |Configurazioni del processo|È possibile modificare le chiavi e i valori predefiniti. Per altre informazioni, vedere [Apache Livy REST API](https://livy.incubator.apache.org/docs/latest/rest-api.html) (API REST di Apache Livy).|
    |Argomenti della riga di comando|È possibile immettere gli argomenti divisi da uno spazio per la classe principale, se necessario.|
    |Referenced Jars (file JAR di riferimento) e Referenced Files (file di riferimento)|È possibile immettere i percorsi per file e jar di riferimento, se presenti. È anche possibile selezionare i file nel file system virtuale di Azure, che attualmente supporta solo cluster di Azure Data Lake Store Gen 2. Per altre informazioni, [Apache Spark configurazione](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Vedere anche [Come caricare le risorse nel cluster](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md).|
    |Archivio di caricamento del processo|Espandere per visualizzare le opzioni aggiuntive.|
    |Tipo di archiviazione|Selezionare **Usare Azure Blob per caricare** nell'elenco a discesa.|
    |Account di archiviazione|Immettere l'account di archiviazione.|
    |Chiave di archiviazione|Immettere la chiave di archiviazione.|
    |Contenitore di archiviazione|Selezionare il contenitore di archiviazione dall'elenco a discesa una volta immessi **Account di archiviazione** e **chiave di archiviazione**.|

    ![Finestra di dialogo Spark Submission (Invio Spark)](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

4. Fare clic su **SparkJobRun** per inviare il progetto al cluster selezionato. La scheda **Remote Spark Job in Cluster** (Processo Spark remoto nel cluster) visualizza lo stato dell'esecuzione del processo, nella parte inferiore. È possibile arrestare l'applicazione facendo clic sul pulsante rosso.

    ![Finestra per l'invio di Apache Spark](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Eseguire il debug di applicazioni Apache Spark in modalità locale o remota in un cluster HDInsight

Si consiglia anche di usare un altro modo per inviare l'applicazione Spark al cluster. È ad esempio possibile configurare i parametri nell'IDE **Run/Debug configurations** (Esegui/Esegui il debug delle configurazioni). Vedere [eseguire il debug di applicazioni Apache Spark in locale o in remoto in un cluster HDInsight con Azure Toolkit for IntelliJ tramite SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Accedere e gestire i cluster HDInsight Spark tramite il Toolkit di Azure per IntelliJ

È possibile eseguire diverse operazioni usando Azure Toolkit for IntelliJ.  La maggior parte delle operazioni viene avviata da **Azure Explorer**.  Dalla barra dei menu, passare a **Visualizza** > **strumento Windows** > **Azure Explorer**.

### <a name="access-the-job-view"></a>Accedere alla visualizzazione del processo

1. Da Azure Explorer passare a **HDInsight**  >  \<Your Cluster>  >  **Jobs**.

    ![IntelliJ: nodo per la visualizzazione dei processi in Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-job-view-node.png)

2. Nel riquadro destro la scheda **Spark Job View** (Visualizzazione processi Spark) visualizza tutte le applicazioni eseguite nel cluster. Selezionare il nome dell'applicazione per cui si vogliono visualizzare altri dettagli.

    ![Visualizzazione dei processi Spark: dettagli dell'applicazione](./media/apache-spark-intellij-tool-plugin/intellij-view-job-logs.png)

3. Per visualizzare le informazioni di base sui processi in esecuzione, passare il mouse sopra il grafico relativo al processo. Per visualizzare i grafici sulle fasi e le informazioni generate da ogni processo, selezionare un nodo nel grafico relativo al processo.

    ![Visualizzazione dei processi Spark: dettagli delle fasi del processo](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Per visualizzare i log di uso più frequente, ad esempio *Driver Stderr*, *Driver Stdout* e *Directory Info*, selezionare la scheda **Log**.

    ![Visualizzazione dei processi Spark: dettagli dei log](./media/apache-spark-intellij-tool-plugin/intellij-job-log-info.png)

5. È possibile visualizzare l'interfaccia utente della cronologia di Spark e l'interfaccia utente di YARN (a livello di applicazione). Selezionare un collegamento nella parte superiore della finestra.

### <a name="access-the-spark-history-server"></a>Accedere al Server cronologia Spark

1. Da Azure Explorer espandere **HDInsight**, fare clic con il pulsante destro del mouse sul nome del cluster Spark e quindi scegliere **Open Spark History UI** (Apri UI cronologia Spark).  
2. Quando richiesto, immettere le credenziali dell'amministratore del cluster, specificate durante la configurazione del cluster.

3. Nel dashboard del server della cronologia di Spark è possibile usare il nome dell'applicazione per cercare l'applicazione di cui è appena stata completata l'esecuzione. Nel codice precedente impostare il nome dell'applicazione usando `val conf = new SparkConf().setAppName("myApp")`. Il nome dell'applicazione Spark è **MyApp**.

### <a name="start-the-ambari-portal"></a>Avviare il portale di Ambari

1. Da Azure Explorer espandere **HDInsight**, fare clic con il pulsante destro del mouse sul nome del cluster Spark e quindi scegliere **Open Cluster Management Portal (Ambari)** (Apri il portale di gestione cluster - Ambari).  

2. Quando richiesto, immettere le credenziali dell'amministratore per il cluster. Queste credenziali sono state specificate durante il processo di configurazione del cluster.

### <a name="manage-azure-subscriptions"></a>Gestire le sottoscrizioni di Azure

Per impostazione predefinita, il Toolkit di Azure per IntelliJ elenca i cluster Spark di tutte le sottoscrizioni di Azure. Se necessario, è possibile specificare le sottoscrizioni a cui si vuole accedere.  

1. Da Azure Explorer fare clic con il pulsante destro del mouse sul nodo radice **Azure** e quindi scegliere **Scegli sottoscrizioni**.  

2. Dalla finestra **Seleziona sottoscrizioni**, deselezionare le caselle di controllo accanto alla sottoscrizione alla quale non si vuole accedere e quindi selezionare **Chiudi**.

## <a name="spark-console"></a>Console Spark

È possibile eseguire la console locale Spark (Scala) o eseguire la console della sessione Spark Livy interattiva (Scala).

### <a name="spark-local-consolescala"></a>Console locale Spark (Scala)

Assicurarsi di aver soddisfatto il prerequisito relativo al file WINUTILS.EXE.

1. Dalla barra dei menu passare a **Run** > **Edit Configurations...** (Esegui > Modifica configurazioni).

2. Dalla finestra **Eseguire configurazioni di debug**, nel riquadro a sinistra, passare a **Apache Spark in HDInsight** > **[Spark in HDInsight] myApp**.

3. Nella finestra principale selezionare la **`Locally Run`** scheda.

4. Specificare i valori seguenti e quindi selezionare **OK**:

    |Proprietà |valore |
    |----|----|
    |Job main class (Classe principale del processo)|Il valore predefinito corrisponde alla classe principale del file selezionato. È possibile modificare la classe selezionando i puntini di sospensione ( **...** ) e scegliendo una classe diversa.|
    |Variabili di ambiente|Verificare che il valore di HADOOP_HOME sia corretto.|
    |WINUTILS.exe location (Percorso di WINUTILS.exe)|Assicurarsi che il percorso sia corretto.|

    ![Impostazione della configurazione nella console locale](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. Da Project (Progetto) passare a **myApp** > **src** > **main** > **scala** > **myApp**.  

6. Dalla barra dei menu passare a **Tools** > **Spark Console** > **Run Spark Local Console(Scala)** (Strumenti > Console Spark > Esegui console locale Spark - Scala).

7. Potrebbero venire visualizzate due finestre di dialogo in cui viene chiesto se si vuole correggere automaticamente le dipendenze. In caso affermativo, selezionare **Auto Fix** (Correggi automaticamente).

    ![IntelliJ IDEA: finestra di dialogo per correzione automatica Spark 1](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix1.png)

    ![IntelliJ IDEA: finestra di dialogo per correzione automatica Spark 2](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix2.png)

8. La console dovrebbe essere simile all'immagine seguente. Nella finestra della console digitare `sc.appName` e quindi premere CTRL+INVIO.  Verrà visualizzato il risultato. È possibile chiudere la console locale facendo clic sul pulsante rosso.

    ![IntelliJ IDEA: risultato nella console locale](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Console della sessione Spark Livy interattiva (Scala)

1. Dalla barra dei menu passare a **Run** > **Edit Configurations...** (Esegui > Modifica configurazioni).

2. Dalla finestra **Eseguire configurazioni di debug**, nel riquadro a sinistra, passare a **Apache Spark in HDInsight** > **[Spark in HDInsight] myApp**.

3. Nella finestra principale selezionare la **`Remotely Run in Cluster`** scheda.

4. Specificare i valori seguenti e quindi selezionare **OK**:

    |Proprietà |valore |
    |----|----|
    |Cluster Spark (solo Linux)|Selezionare il cluster HDInsight Spark in cui eseguire l'applicazione.|
    |Nome della classe principale|Il valore predefinito corrisponde alla classe principale del file selezionato. È possibile modificare la classe selezionando i puntini di sospensione ( **...** ) e scegliendo una classe diversa.|

    ![Impostazione della configurazione nella console interattiva](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. Da Project (Progetto) passare a **myApp** > **src** > **main** > **scala** > **myApp**.  

6. Dalla barra dei menu passare a **Tools** > **Spark Console** > **Run Spark Livy Interactive Session Console(Scala)** (Strumenti > Console Spark > Esegui console della sessione Spark Livy interattiva -Scala).

7. La console dovrebbe essere simile all'immagine seguente. Nella finestra della console digitare `sc.appName` e quindi premere CTRL+INVIO.  Verrà visualizzato il risultato. È possibile chiudere la console locale facendo clic sul pulsante rosso.

    ![IntelliJ IDEA: risultato nella console interattiva](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Send Selection to Spark Console (Invia selezione alla console Spark)

È utile prevedere il risultato dello script inviando una parte di codice alla console locale o alla console di sessione interattiva Livy (Scala). È possibile evidenziare il codice nel file Scala e quindi fare clic con il pulsante destro del mouse su **Send Selection To Spark Console** (Invia selezione alla console Spark). Il codice selezionato verrà inviato alla console. Il risultato verrà visualizzato dopo il codice nella console. La console controllerà gli errori, se presenti.  

   ![Send Selection to Spark Console (Invia selezione alla console Spark)](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Eseguire l'integrazione con broker di identità di HDInsight

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Connettersi al cluster ESP HDInsight con ID Broker (HIB)

È possibile seguire la procedura normale per accedere alla sottoscrizione di Azure per connettersi al cluster ESP HDInsight con ID Broker (HIB). Dopo l'accesso, l'elenco cluster verrà visualizzato in Azure Explorer. Per altre istruzioni, vedere [Connettersi al cluster HDInsight](#connect-to-your-hdinsight-cluster).

### <a name="run-a-spark-scala-application-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Eseguire un'applicazione Spark in scala in un cluster ESP HDInsight con ID Broker (HIB)

È possibile seguire la procedura normale per inviare il processo al cluster ESP HDInsight con ID Broker (HIB). Per altre istruzioni, vedere [Eseguire un'applicazione Spark in Scala in un cluster HDInsight Spark](#run-a-spark-scala-application-on-an-hdinsight-spark-cluster).

I file necessari vengono caricati in una cartella denominata con l'account di accesso ed è possibile visualizzare il percorso di caricamento nel file di configurazione.

   ![Percorso di caricamento nella configurazione](./media/apache-spark-intellij-tool-plugin/upload-path-in-the-configuration.png)

### <a name="spark-console-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Console di Spark in un cluster HDInsight ESP con ID Broker (HIB)

È possibile eseguire la console locale Spark (scala) o eseguire Spark Livio Interactive session Console (scala) in un cluster ESP HDInsight con ID Broker (HIB). Per altre istruzioni, vedere [Console Spark](#spark-console).

   > [!NOTE]  
   > Per il cluster ESP HDInsight con broker di identità, il [collegamento di un cluster](#link-a-cluster) e il [debug di applicazioni Apache Spark in remoto](#debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster) non sono attualmente supportati.

## <a name="reader-only-role"></a>Ruolo di sola lettura

Quando gli utenti inviano processi a un cluster con autorizzazione di sola lettura, le credenziali di Ambari sono obbligatorie.

### <a name="link-cluster-from-context-menu"></a>Collegare un cluster dal menu di scelta rapida

1. Accedere con l'account di un ruolo di sola lettura.

2. Da **Azure Explorer** espandere **HDInsight** per visualizzare i cluster HDInsight Spark disponibili nella sottoscrizione. I cluster contrassegnati da **"Role:Reader"** (Ruolo:Lettore) hanno autorizzazioni di ruolo di sola lettura.

    ![' IntelliJ Azure Explorer Role: Reader '](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer15.png)

3. Fare clic con il pulsante destro del mouse sul cluster con autorizzazione di ruolo di sola lettura. Selezionare **Link this cluster** (Collega questo cluster) dal menu di scelta rapida per collegare il cluster. Immettere il nome utente e la password di Ambari.

    ![IntelliJ: collegamento del cluster in Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer11.png)

4. Se il cluster è stato collegato correttamente, HDInsight viene aggiornato.
   La fase del cluster diventerà Linked (Collegato).
  
    ![IntelliJ: finestra di dialogo di Azure Explorer con collegamento completato](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Collegare un cluster espandendo il nodo Jobs (Processi)

1. Fare clic sul nodo **Jobs** (Processi). Verrà visualizzata la finestra **Cluster Job Access Denied** (Accesso negato al cluster Jobs).

2. Fare clic su **Link this cluster** (Collega questo cluster) per collegare il cluster.

    ![finestra di dialogo accesso processo cluster negato](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer9.png)

### <a name="link-cluster-from-rundebug-configurations-window"></a>Collegare un cluster dalla finestra Run/Debug Configurations (Configurazioni di esecuzione/debug)

1. Creare una configurazione di HDInsight e quindi selezionare **Remotely Run in Cluster** (Esecuzione remota nel cluster).

2. Selezionare un cluster con autorizzazione di ruolo di sola lettura per **Spark clusters(Linux only)** (Spark cluster (solo Linux)). Viene visualizzato un messaggio di avviso. È possibile fare clic su **collega questo cluster** per collegare il cluster.

   ![IntelliJ IDEA: creazione della configurazione di esecuzione/debug](./media/apache-spark-intellij-tool-plugin/create-configuration.png)

### <a name="view-storage-accounts"></a>Visualizzare gli account di archiviazione

* Per i cluster con autorizzazione di ruolo di sola lettura fare clic sul nodo **Storage Accounts** (Account di archiviazione). Verrà visualizzata la finestra **Storage Access Denied** (Accesso negato alla risorsa di archiviazione). È possibile fare clic su **Apri Azure Storage Explorer** per aprire Storage Explorer.

   ![' IntelliJ IDEA storage accesso negato '](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer14.png)

   ![IntelliJ IDEA: pulsante in finestra di dialogo di accesso negato alla risorsa di archiviazione](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer10.png)

* Per i cluster collegati fare clic sul nodo **Storage Accounts** (Account di archiviazione). Verrà visualizzata la finestra **Storage Access Denied** (Accesso negato alla risorsa di archiviazione). È possibile fare clic su **Apri Azure Storage Explorer** per aprire Storage Explorer.

   ![' IntelliJ IDEA Storage Access Denied2'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer13.png)

   ![IntelliJ IDEA: pulsante in finestra di dialogo di accesso negato alla risorsa di archiviazione 2](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer12.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Convertire le applicazioni IntelliJ IDEA esistenti per usare il Toolkit di Azure per IntelliJ

È possibile convertire le applicazioni Spark Scala esistenti create in IntelliJ IDEA per renderle compatibili con Azure Toolkit for IntelliJ. È possibile usare il plug-in per inviare le applicazioni a un cluster HDInsight Spark.

1. Per un'applicazione Spark scala esistente creata tramite IntelliJ IDEA, aprire il `.iml` file associato.

2. Al livello radice, è un elemento **modulo** come il testo seguente:

    ```xml
    <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
    ```

   Modificare l'elemento da aggiungere in `UniqueKey="HDInsightTool"` modo che l'elemento **modulo** appaia come il testo seguente:

    ```xml
    <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
    ```

3. Salvare le modifiche. L'applicazione dovrebbe ora essere compatibile con il Toolkit di Azure per IntelliJ. È possibile verificarlo facendo clic con il pulsante destro del mouse sul nome del progetto in Progetti. Nel menu a comparsa viene ora visualizzata l'opzione **Submit Spark Application to HDInsight**(Invia applicazione Spark a HDInsight).

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare il cluster creato con i passaggi seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Nella casella **Ricerca** in alto digitare **HDInsight**.

1. Selezionare **Cluster HDInsight** in **Servizi**.

1. Nell'elenco dei cluster HDInsight visualizzato selezionare il **...** accanto al cluster creato per questo articolo.

1. Selezionare **Elimina**. Selezionare **Sì**.

![portale di Azure Elimina il cluster HDInsight](./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png "Eliminare un cluster HDInsight")

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come usare il plug-in Azure Toolkit for IntelliJ per sviluppare applicazioni Apache Spark scritte in [scala](https://www.scala-lang.org/). Quindi inviarli a un cluster HDInsight Spark direttamente dalla Integrated Development Environment IntelliJ (IDE). Passare all'articolo successivo per scoprire come eseguire il pull dei dati registrati in Apache Spark in uno strumento di analisi BI come Power BI.

> [!div class="nextstepaction"]
> [Analizzare i dati Apache Spark tramite Power BI](apache-spark-use-bi-tools.md)