---
title: 'Azure Toolkit for Eclipse: Create Scala apps for HDInsight Spark'
description: Usare gli strumenti di HDInsight in Azure Toolkit for Eclipse per creare applicazioni Spark scritte in Scala e inoltrarle a un cluster HDInsight Spark direttamente dall'IDE di Eclipse.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/13/2019
ms.openlocfilehash: 07ed22879180d8126711eba9af0a2cac5b7b2953
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272110"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Usare Azure Toolkit for Eclipse per creare applicazioni Apache Spark per un cluster HDInsight

Usare gli strumenti HDInsight in Azure Toolkit for [Eclipse](https://www.eclipse.org/) per sviluppare applicazioni [Apache Spark](https://spark.apache.org/) scritte in [Scala](https://www.scala-lang.org/) e inoltrarle a un cluster Azure HDInsight Spark direttamente dall'IDE Eclipse. È possibile usare gli strumenti di HDInsight in diversi modi:

* Per sviluppare e inviare un'applicazione Spark in Scala in un cluster HDInsight Spark.
* Per accedere alle risorse cluster HDInsight Spark di Azure.
* Per sviluppare ed eseguire un'applicazione Spark in Scala localmente.

## <a name="prerequisites"></a>Prerequisiti

* Cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Java Developer Kit (JDK) versione 8](https://aka.ms/azure-jdks).

* [Eclipse IDE](https://www.eclipse.org/downloads/). In questo articolo viene utilizzato l'IDE Eclipse per gli sviluppatori Java.

## <a name="install-required-plug-ins"></a>Installare i plug-in necessari

### <a name="install-azure-toolkit-for-eclipse"></a>Installare Azure Toolkit for Eclipse

Le istruzioni di installazione sono disponibili in [Installazione di Azure Toolkit for Eclipse](https://docs.microsoft.com/azure/java/eclipse/azure-toolkit-for-eclipse-installation).

### <a name="install-the-scala-plug-in"></a>Installare il plug-in di Scala

Quando si apre Eclipse, Strumenti di HDInsight rileva automaticamente se è installato il plug-in Scala. Selezionare **OK** per continuare, quindi seguire le istruzioni per installare il plug-in del Marketplace di Eclipse. Riavviare l'IDE al termine dell'installazione.

![Installazione automatica del plug-in di Scala](./media/apache-spark-eclipse-tool-plugin/auto-installation-scala1.png)

### <a name="confirm-plug-ins"></a>Confermare i plug-in

1. Passare ad **Help** > **Eclipse Marketplace...**.

1. Selezionare la scheda **Installati**.

1. Dovresti vedere almeno:
    * Azure Toolkit per \<la versione Eclipse>.
    * La versione \<IDE Scala>.

## <a name="sign-in-to-your-azure-subscription"></a>Accedere alla sottoscrizione di Azure.

1. Avviare Eclipse IDE.

1. Vai alla **finestra** >  **Mostra vista** > **Altro...**  >  **Accedi..**.

1. Nella finestra di dialogo **Mostra visualizzazione** passare ad **Azure** > **Azure Explorer**e quindi selezionare **Apri**.

   ![Apache Spark Eclipse mostra la vista](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer1.png)

1. In **Esplora risorse**fare clic con il pulsante destro del mouse sul nodo **Azure** e quindi scegliere **Accedi**.

1. Nella finestra di dialogo Accesso di **Azure** scegliere il metodo di autenticazione, selezionare **Accedi**e completare il processo di accesso.

   ![Segno di Azure Apache Spark Eclipse](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer2.png)

1. Dopo aver eseguito l'accesso, nella finestra di dialogo **Sottoscrizioni** sono elencate tutte le sottoscrizioni di Azure associate alle credenziali. Premere **Seleziona** per chiudere la finestra di dialogo.

   ![Finestra di dialogo Selezionare le sottoscrizioni](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)

1. Da **Azure Explorer**passare ad **Azure** >  **HDInsight** per visualizzare i cluster HDInsight Spark nella sottoscrizione.

   ![HDInsight Spark clusters in Azure Explorer3](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer3.png)

1. È possibile espandere ancora un nodo del nome cluster per vedere le risorse, ad esempio gli account di archiviazione, associate al cluster.

   ![Espansione di un nome cluster per vedere le risorse](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer4.png)

## <a name="link-a-cluster"></a>Collegare un cluster

È possibile collegare un normale cluster usando lo username gestito di Ambari. Analogamente, è possibile collegare un cluster HDInsight aggiunto al dominio tramite il dominio e il nome utente, come `user1@contoso.com`.

1. In **Azure Explorer**fare clic con il pulsante destro del mouse su **HDInsight**e scegliere **Collega un cluster**.

   ![Menu del cluster di collegamenti di Azure ExplorerAzure Explorer link cluster menu](./media/apache-spark-eclipse-tool-plugin/link-a-cluster-context-menu.png)

1. Immettere **Nome cluster**, **Nome utente**e **Password**, quindi scegliere **OK**. Facoltativamente, immettere l'account di archiviazione e la chiave di archiviazione e quindi selezionare il contenitore di archiviazione per Storage Explorer in modo da usare la visualizzazione struttura ad albero a sinistra.

   ![Finestra di dialogo del cluster Collega nuovo cluster HDInsight](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog1.png)

   > [!NOTE]  
   > Vengono usati la chiave di archiviazione, il nome utente e la password collegati se il cluster ha eseguito l'accesso alla sottoscrizione di Azure e ha collegato un cluster.
   > ![Account di archiviazione in Azure Explorer](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)
   >
   > Per l'utente solo per la tastiera, quando lo stato attivo corrente è su **Chiave di archiviazione**, è necessario utilizzare CTRL , **TAB** per concentrarsi sul campo successivo nella finestra di dialogo.

1. È possibile visualizzare il cluster collegato in **HDInsight**. È ora possibile inviare un'applicazione al cluster collegato.

   ![Cluster collegato hdi di Azure ExplorerAzure Explorer hdi linked cluster](./media/apache-spark-eclipse-tool-plugin/hdinsight-linked-cluster.png)

1. È inoltre possibile scollegare un cluster in **Azure Explorer** (Esplora Azure).

   ![Scollegamento del cluster in Azure Explorer](./media/apache-spark-eclipse-tool-plugin/hdi-unlinked-cluster.png)

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Configurare un progetto Spark in Scala per un cluster HDInsight Spark

1. Nell'area di lavoro IDE eclipse selezionare **File** > **Nuovo** > **progetto...**.

1. Nella procedura guidata **Nuovo progetto** selezionare **HDInsight Project** > **Spark in HDInsight (Scala)**. Quindi selezionare **Avanti**.

   ![Selezione del progetto Spark on HDInsight (Scala)](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)

1. Nella finestra di dialogo **New HDInsight Scala Project** (Nuovo progetto HDInsight Scala) specificare i valori seguenti e selezionare su **Next** (Avanti):
   * Immettere un nome per il progetto.
   * Nell'area **JRE** verificare che l'opzione **Use an execution environment JRE** (Usa un ambiente di esecuzione JRE) sia impostata su **JavaSE-1.7** o versioni successive.
   * Nell'area **Spark Library** (Libreria Spark) è possibile scegliere l'opzione **Use Maven to configure Spark SDK** (Usa Maven per configurare l'SDK di Spark).  Lo strumento integra la versione corretta dell'SDK di Spark e Scala. È anche possibile scegliere **Aggiungi Spark SDK manualmente** opzione, scaricare e aggiungere Spark SDK manualmente.

   ![Finestra di dialogo New HDInsight Scala Project (Nuovo progetto HDInsight Scala)](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)

1. Nella finestra di dialogo successiva esaminare i dettagli e quindi selezionare **Fine**.

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Creare un'applicazione Scala per un cluster HDInsight Spark

1. In **Esplora pacchetti**espandere il progetto creato in precedenza. Fare clic con il pulsante destro del mouse su **src**, selezionare **Nuovo** > **altro...**.

1. Nella finestra di dialogo **Seleziona procedura guidata** selezionare Scala **Wizards** > **Scala Object**. Quindi selezionare **Avanti**.

   ![Selezionare una procedura guidata Crea un oggetto Scala](./media/apache-spark-eclipse-tool-plugin/create-scala-project1.png)

1. Nella finestra di dialogo **Create New File** (Crea nuovo file) immettere un nome per l'oggetto e quindi selezionare **Finish** (Fine). Si aprirà un editor di testo.

   ![Creazione guidata nuovo file Crea nuovo file](./media/apache-spark-eclipse-tool-plugin/create-scala-project2.png)

1. Nell'editor di testo, sostituire il contenuto corrente con il codice seguente:

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object MyClusterApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("MyClusterApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }
    }
    ```

1. Eseguire l'applicazione in un cluster HDInsight Spark:

   a. In Package Explorer (Esplora pacchetti) fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Submit Spark Application to HDInsight** (Invia applicazione Spark a HDInsight).

   b. Nella finestra di dialogo **Invio spark** specificare i valori seguenti e quindi selezionare **Invia:**

   * Per **Cluster Name**(Nome cluster) selezionare il cluster HDInsight Spark in cui eseguire l'applicazione.
   * Selezionare un elemento nel progetto Eclipse oppure nel disco rigido. Il valore predefinito dipende dall'elemento su cui si fa clic con il pulsante destro del mouse da Package Explorer (Esplora pacchetti).
   * Nell'elenco a discesa **Main class name** (Nome classe principale) l'invio guidato mostra tutti i nomi di oggetto del progetto. Selezionare o inserire quello che si vuole eseguire. Se si seleziona un elemento da un disco rigido, è necessario immettere manualmente il nome della classe principale. 
   * Poiché il codice dell'applicazione in questo esempio non richiede argomenti della riga di comando o file di riferimento di riferimento, è possibile lasciare vuote le caselle di testo rimanenti.

     ![Finestra di dialogo Invio Apache Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-project3.png)

1. Nella scheda **Spark Submission** (Invio Spark) verrà visualizzato lo stato di avanzamento. È possibile arrestare l'applicazione selezionando il pulsante rosso nella finestra **Spark Submission** (Invio Spark). È anche possibile visualizzare i log per questa esecuzione dell'applicazione specifica selezionando l'icona del mondo, indicata dalla casella blu nell'immagine.

   ![Finestra per l'invio di Apache Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-project4.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Accedere e gestire i cluster HDInsight Spark con gli strumenti HDInsight in Azure Toolkit for Eclipse

È possibile eseguire varie operazioni con gli strumenti HDInsight, tra cui accedere all'output dei processi.

### <a name="access-the-job-view"></a>Accedere alla visualizzazione del processo

1. In **Azure Explorer**espandere **HDInsight**, quindi il nome del cluster Spark e infine selezionare **Processi**.

   ![Nodo della visualizzazione processo Eclipse di Azure ExplorerAzure Explorer Eclipse job view node](./media/apache-spark-eclipse-tool-plugin/eclipse-job-view-node.png)

1. Selezionare il nodo **Processi** . Se la versione di Java è precedente alla versione **1.8**, gli strumenti di HDInsight inviano un promemoria automatico per l'installazione del plug-in **E(fx)clipse**. Selezionare **OK** per continuare, quindi seguire la procedura guidata per installare il plug-in dal Marketplace di Eclipse e riavviare Eclipse.

   ![Installare il plugin mancante E(fx)clipse](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. Aprire la visualizzazione del processo dal nodo **Processi**. Nel riquadro destro la scheda **Spark Job View** (Visualizzazione processi Spark) visualizza tutte le applicazioni eseguite nel cluster. Selezionare il nome dell'applicazione per cui si vogliono visualizzare altri dettagli.

   ![Apache Eclipse visualizza i dettagli dei log dei processi](./media/apache-spark-eclipse-tool-plugin/eclipse-view-job-logs.png)

   È quindi possibile intraprendere una delle seguenti operazioni:

   * Passare il mouse sul grafico del processo. Mostra le informazioni di base sul processo in esecuzione. Selezionare il grafico del processo: vengono visualizzate le fasi e le informazioni generate da ogni processo.

     ![Informazioni sulla fase del grafico del processo Apache Spark](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Selezionare la scheda **Log** per visualizzare i log di uso più frequente, ad esempio **Driver Stderr**, **Driver Stdout** e **Directory Info**.

     ![Informazioni del registro processi di Apache Spark Eclipse](./media/apache-spark-eclipse-tool-plugin/eclipse-job-log-info.png)

   * Aprire l'interfaccia utente della cronologia di Spark e l'interfaccia utente di Apache Hadoop YARN, a livello di applicazione, selezionando i rispettivi collegamenti ipertestuali nella parte superiore della finestra.

### <a name="access-the-storage-container-for-the-cluster"></a>Accedere al contenitore di archiviazione per il cluster

1. In Azure Explorer espandere il nodo radice **HDInsight** per visualizzare un elenco di cluster HDInsight Spark disponibili.

1. Espandere il nome del cluster per visualizzare l'account di archiviazione e il contenitore di archiviazione predefinito per il cluster.

   ![Account di archiviazione e contenitore di archiviazione predefinito](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer5.png)

1. Fare clic sul nome del contenitore di archiviazione associato al cluster. Nel riquadro destro fare doppio clic sulla cartella **HVACOut**. Aprire uno dei file **part-** per visualizzare l'output dell'applicazione.

### <a name="access-the-spark-history-server"></a>Accedere al Server cronologia Spark

1. In Azure Explorer fare clic con il pulsante destro del mouse sul nome del cluster Spark e quindi scegliere **Open Spark History UI** (Apri UI cronologia Spark). Quando richiesto, immettere le credenziali dell'amministratore per il cluster. Queste sono state specificate durante il provisioning del cluster.

1. Nel dashboard del Server cronologia Spark è possibile usare il nome dell'applicazione per cercare l'applicazione di cui è appena stata completata l'esecuzione. Nel codice precedente impostare il nome dell'applicazione usando `val conf = new SparkConf().setAppName("MyClusterApp")`. Il nome dell'applicazione Spark era **MyClusterApp**.

### <a name="start-the-apache-ambari-portal"></a>Avviare il portale di Apache Ambari

1. In Azure Explorer fare clic con il pulsante destro del mouse sul nome del cluster Spark e quindi scegliere **Open Cluster Management Portal (Ambari)** (Apri portale di gestione cluster - Ambari).

1. Quando richiesto, immettere le credenziali dell'amministratore per il cluster. Queste sono state specificate durante il provisioning del cluster.

### <a name="manage-azure-subscriptions"></a>Gestire le sottoscrizioni di Azure

Per impostazione predefinita, gli strumenti di HDInsight in Azure Toolkit for Eclipse elencano i cluster Spark di tutte le sottoscrizioni di Azure. Se necessario, è possibile specificare le sottoscrizioni per cui si vuole accedere al cluster.

1. In Azure Explorer fare clic con il pulsante destro del mouse sul nodo radice **Azure** e quindi scegliere **Gestisci sottoscrizioni**.

1. Nella finestra di dialogo deselezionare le caselle di controllo della sottoscrizione alla quale non si vuole accedere e quindi fare clic su **Chiudi**. È anche possibile selezionare **Esci** per uscire dalla sessione di sottoscrizione di Azure.

## <a name="run-a-spark-scala-application-locally"></a>Eseguire un'applicazione Spark in Scala localmente

È possibile usare gli strumenti HDInsight in Azure Toolkit for Eclipse per eseguire applicazioni Spark in Scala localmente nella workstation. Tali applicazioni in genere non richiedono l'accesso a risorse del cluster quali il contenitore di archiviazione e possono essere eseguite e testate localmente.

### <a name="prerequisite"></a>Prerequisito

Quando si esegue l'applicazione Spark Scala locale in un computer Windows, potrebbe essere restituita un'eccezione, come spiegato in [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356), che si verifica a causa di un file **WinUtils.exe** mancante in Windows.

Per risolvere l'errore, è necessario [che Winutils.exe](https://github.com/steveloughran/winutils) si trovi in un percorso quale **C:,** quindi aggiungere la variabile di ambiente **HADOOP_HOME** e impostare il valore della variabile su **C .**

### <a name="run-a-local-spark-scala-application"></a>Eseguire un'applicazione Spark in Scala locale

1. Avviare Eclipse e creare un progetto. Nella finestra di dialogo **New Project** (Nuovo progetto) selezionare le opzioni seguenti e quindi fare clic su **Next** (Avanti).

1. Nella procedura guidata **Nuovo progetto** selezionare **HDInsight Project** > **Spark on HDInsight Local Run Sample (Scala)**. Quindi selezionare **Avanti**.

   ![Nuovo progetto seleziona una finestra di dialogo della procedura guidata](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)

1. Per specificare i dettagli del progetto, seguire i passaggi da 3 a 6 illustrati nella sezione precedente [Configurare un progetto Spark in Scala per un cluster HDInsight Spark](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. Il modello aggiunge un codice di esempio (**LogQuery**) sotto la cartella **src** eseguibile in locale nel computer in uso.

   ![Posizione dell'applicazione scala locale LogQuery](./media/apache-spark-eclipse-tool-plugin/local-scala-application.png)

1. Fare clic con il pulsante destro del mouse su **LogQuery.scala** e **scegliere Esegui come** > **applicazione Scala 1**. Un output come questo viene mostrato nella scheda **Console**:

   ![Risultato dell'esecuzione locale dell'applicazione Spark](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="reader-only-role"></a>Ruolo di sola lettura

Quando gli utenti inviano processi a un cluster con autorizzazione di sola lettura, le credenziali di Ambari sono obbligatorie.

### <a name="link-cluster-from-context-menu"></a>Collegare un cluster dal menu di scelta rapida

1. Accedere con l'account di un ruolo di sola lettura.

2. Da **Azure Explorer** espandere **HDInsight** per visualizzare i cluster HDInsight Spark disponibili nella sottoscrizione. I cluster contrassegnati da **"Role:Reader"** (Ruolo:Lettore) hanno autorizzazioni di ruolo di sola lettura.

    ![Cluster Spark HDInsight nel lettore di ruoli di Azure ExplorerHDInsight Spark spark clusters in Azure Explorer role reader](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer6.png)

3. Fare clic con il pulsante destro del mouse sul cluster con autorizzazione di ruolo di sola lettura. Selezionare **Link this cluster** (Collega questo cluster) dal menu di scelta rapida per collegare il cluster. Immettere il nome utente e la password Ambari.

    ![Collegamento HDInsight Spark Spark in Azure ExplorerHDInsight Spark clusters in Azure Explorer link](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer7.png)

4. Se il cluster è stato collegato correttamente, HDInsight viene aggiornato.
   La fase del cluster diventerà Linked (Collegato).
  
    ![Cluster Spark HDInsight in Azure Explorer collegatiHDInsight Spark clusters in Azure Explorer linked](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Collegare un cluster espandendo il nodo Jobs (Processi)

1. Fare clic sul nodo **Jobs** (Processi). Verrà visualizzata la finestra **Cluster Job Access Denied** (Accesso negato al cluster Jobs).

2. Fare clic su **Link this cluster** (Collega questo cluster) per collegare il cluster.

    ![HDInsight Spark clusters in Azure Explorer9](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer9.png)

### <a name="link-cluster-from-spark-submission-window"></a>Collegare il cluster dalla finestra Invio SparkLink cluster from Spark Submission window

1. Creare un progetto HDInsight.Create an HDInsight Project.

2. Fare clic con il pulsante destro del mouse sul pacchetto. Selezionare **quindi Invia applicazione Spark a HDInsight**.

   ![Invio di cluster Spark HDInsight in Azure ExplorerHDInsight Spark clusters in Azure Explorer submit](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer11.png)

3. Selezionare un cluster, che dispone dell'autorizzazione del ruolo Solo lettura per **Nome cluster**. Viene visualizzato un messaggio di avviso. È possibile fare clic su **Collega il cluster** per collegare il cluster.

   ![I cluster Spark HDInsight in Azure Explorer collegano questo](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer15.png)

### <a name="view-storage-accounts"></a>Visualizzare gli account di archiviazione

* Per i cluster con autorizzazione di ruolo di sola lettura fare clic sul nodo **Storage Accounts** (Account di archiviazione). Verrà visualizzata la finestra **Storage Access Denied** (Accesso negato alla risorsa di archiviazione).

   ![Cluster Spark HDInsight nello spazio di archiviazione di Azure ExplorerHDInsight Spark clusters in Azure Explorer storage](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer13.png)

   ![Cluster Spark HDInsight in Azure Explorer negatiHDInsight Spark clusters in Azure Explorer denied](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer12.png)

* Per i cluster collegati fare clic sul nodo **Storage Accounts** (Account di archiviazione). Verrà visualizzata la finestra **Storage Access Denied** (Accesso negato alla risorsa di archiviazione).

   ![Cluster Spark HDInsight in Azure Explorer negati2HDInsight Spark clusters in Azure Explorer denied2](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer14.png)

## <a name="known-problems"></a>Problemi noti

Quando si utilizza **il cluster di collegamento A**, è consigliabile fornire le credenziali di archiviazione.

![cluster di collegamento con eclissi delle credenziali di archiviazione](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

Esistono due modalità per inviare i processi. Se vengono fornite le credenziali di archiviazione, per inviare il processo verrà usata la modalità batch. In caso contrario, verrà usata la modalità interattiva. Se il cluster è occupato, è possibile che venga visualizzato l'errore riportato di seguito.

![Errore Eclipse in caso di cluster occupato](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png "Errore Eclipse in caso di cluster occupato")

![eclissi ottiene errore quando cluster occupato filato](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png "eclissi ottiene errore quando cluster occupato filato")

## <a name="see-also"></a>Vedere anche

* [Panoramica: Apache Spark su Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenari

* [Apache Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark con Machine Learning: utilizzare Spark in HDInsight per prevedere i risultati di un controllo alimentare](apache-spark-machine-learning-mllib-ipython.md)
* [Analisi dei log del sito Web con Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Creazione ed esecuzione di applicazioni

* [Creare un'applicazione autonoma con Scala](apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni

* [Usare Azure Toolkit for IntelliJ per creare e inviare applicazioni Spark in Scala](apache-spark-intellij-tool-plugin.md)
* [Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark tramite VPN](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark tramite SSH](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usare i notebook di Apache Zeppelin con un cluster Apache Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Apache Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gestione delle risorse

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)
