---
title: 'Azure Toolkit for Eclipse: Creare applicazioni Scala per HDInsight Spark | Microsoft Docs'
description: Usare gli strumenti HDInsight in Azure Toolkit for Eclipse per creare applicazioni Spark scritte in Scala e inoltrarle a un cluster HDInsight Spark direttamente dall&quot;IDE di Eclipse.
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f6c79550-5803-4e13-b541-e86c4abb420b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: d83e84e8ea2158ecf6c8b966732ca1d8961389e0
ms.contentlocale: it-it
ms.lasthandoff: 05/18/2017


---
# <a name="use-azure-toolkit-for-eclipse-to-create-spark-applications-for-an-hdinsight-cluster"></a>Usare Azure Toolkit for Eclipse per creare applicazioni Spark per un cluster HDInsight

Usare gli strumenti HDInsight in Azure Toolkit for Eclipse per sviluppare applicazioni Spark scritte in Scala e inoltrarle a un cluster Azure HDInsight Spark direttamente dall'IDE Eclipse. È possibile usare gli strumenti di HDInsight in diversi modi:

* Per sviluppare e inviare un'applicazione Spark in Scala in un cluster HDInsight Spark
* Per accedere alle risorse cluster HDInsight Spark di Azure
* Per sviluppare ed eseguire un'applicazione Spark in Scala localmente

> [!IMPORTANT]
> Questo strumento può essere usato per creare e inviare applicazioni solo per un cluster HDInsight Spark in Linux.
> 
> 

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo relativo alla [creazione di cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Oracle Java Development Kit versione 8, usato per il runtime IDE Eclipse. È possibile scaricarlo dal [sito Web di Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Ambiente IDE Eclipse. Questo articolo usa Eclipse Neon. È possibile installarlo dal [sito Web di Eclipse](https://www.eclipse.org/downloads/).
* Ambiente IDE Scala per Eclipse. 
  
  * **Se è installato l'ambiente IDE Eclipse**, è possibile aggiungere il plug-in IDE Scala scegliendo **Help** > **Install New SoftWare** (? > Installa nuovo software) e aggiungendo [http://download.scala-ide.org/sdk/lithium/e46/scala211/stable/site](http://download.scala-ide.org/sdk/lithium/e46/scala211/stable/site) come origine per il download del plug-in Scala per Eclipse. 
  * **Se l'ambiente IDE Eclipse non è installato**, è possibile installare l'ambiente IDE Scala direttamente dal [sito Web di Scala](http://scala-ide.org/download/sdk.html). Scaricare il file ZIP, estrarlo, passare alla cartella **/eclipse** e quindi eseguire il file **eclipse.exe** da tale posizione.
    
    > [!NOTE]
    > I passaggi in questo articolo si basano sull'uso dell'ambiente IDE Eclipse con il plug-in Scala installato.
    > 
    > 
* Spark SDK. È possibile scaricarlo da [GitHub](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409).
* e(fx)clipse. È possibile installarlo dalla [pagina di download del sito Web di Eclipse](https://www.eclipse.org/efxclipse/install.html).

## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Installare gli strumenti HDInsight nel Toolkit di Azure per Eclipse
Gli strumenti HDInsight per Eclipse sono disponibili come parte di Azure Toolkit for Eclipse. Le istruzioni di installazione sono disponibili in [Installazione di Azure Toolkit for Eclipse](../azure-toolkit-for-eclipse-installation.md).

## <a name="sign-in-to-your-azure-subscription"></a>Accedere alla sottoscrizione di Azure.
1. Avviare l'IDE di Eclipse e aprire Azure Explorer. Nel menu **Window** (Finestra) fare clic su **Show View** (Mostra visualizzazione) e quindi su **Other** (Altro). Nella finestra di dialogo visualizzata espandere **Azure**, fare clic su **Azure Explorer** e quindi su **OK**.

    ![Finestra di dialogo Show View (Mostra visualizzazione)](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-1.png)
2. Fare clic con il pulsante destro del mouse sul nodo **Azure** e quindi su **Sign in** (Accedi).
3. Nella finestra di dialogo di **accesso ad Azure** scegliere il metodo di autenticazione, fare clic su **Sign in** (Accedi) e immettere le credenziali di Azure.
   
    ![Finestra di dialogo di accesso di Azure](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-2.png)
4. Dopo l'accesso, la finestra di dialogo **Selezionare le sottoscrizioni** elenca tutte le sottoscrizioni di Azure associate alle credenziali. Fare clic su **Seleziona** per chiudere la finestra di dialogo.

    ![Finestra di dialogo Selezionare le sottoscrizioni](./media/hdinsight-apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
5. Nella scheda **Azure Explorer** espandere **HDInsight** per visualizzare i cluster HDInsight Spark nella sottoscrizione.
   
    ![Cluster HDInsight Spark in Azure Explorer](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-3.png)
6. È possibile espandere ancora un nodo del nome cluster per vedere le risorse, ad esempio gli account di archiviazione, associate al cluster.
   
    ![Espansione di un nome cluster per vedere le risorse](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Configurare un progetto Spark in Scala per un cluster HDInsight Spark

1. Nell'area di lavoro dell'ambiente IDE Eclipse fare clic su **File**, quindi su **New** (Nuovo) e infine su **Project** (Progetto). 
2. Nella procedura guidata New Project (Nuovo progetto) espandere **HDInsight**, selezionare **Spark on HDInsight (Scala)** (Spark in HDInsight - Scala) e quindi fare clic su **Next** (Avanti).

    ![Selezione del progetto Spark on HDInsight (Scala)](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
3. Nella finestra di dialogo **New HDInsight Scala Project** (Nuovo progetto HDInsight Scala) specificare i valori seguenti e fare clic su **Next** (Avanti):
   * Immettere un nome per il progetto.
   * Nell'area **JRE** verificare che l'opzione **Use an execution environment JRE** (Usa un ambiente di esecuzione JRE) sia impostata su **JavaSE-1.7**.
   * Assicurarsi che Spark SDK sia impostato sul percorso in cui è stato scaricato l'SDK. Il collegamento al percorso di download è incluso nella sezione [Prerequisiti](#prerequisites) illustrata in precedenza in questo articolo. È anche possibile scaricare l'SDK dal collegamento incluso nella finestra di dialogo.

    ![Finestra di dialogo New HDInsight Scala Project (Nuovo progetto HDInsight Scala)](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
4.    Nella finestra di dialogo successiva fare clic sulla scheda **Libraries (Librerie)**, mantenere i valori predefiniti e quindi fare clic su **Finish** (Fine). 
   
    ![Scheda Libraries (Librerie)](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-4.png)


## <a name="run-a-spark-scala-application-on-an-azure-data-lake-store-cluster"></a>Eseguire un'applicazione Spark Scala in un cluster Azure Data Lake Store
Se si vuole inviare un'applicazione ad Azure Data Lake Store, è necessario selezionare la modalità **Interactive** (Interattivo) durante l'accesso ad Azure. 

   ![Opzione Interactive (Interattivo) all'accesso](./media/hdinsight-apache-spark-eclipse-tool-plugin/Interactive-Authentication.png)

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Creare un'applicazione Scala per un cluster HDInsight Spark

1. Nell'ambiente IDE Eclipse in Package Explorer (Esplora pacchetti) espandere il progetto creato in precedenza, fare clic con il pulsante destro del mouse su **src**, scegliere **New** (Nuovo) e quindi fare clic su **Other** (Altro).
2. Nella finestra di dialogo **Select a wizard** (Seleziona una procedura guidata) espandere **Scala Wizards** (Procedure guidate Scala) e fare clic su **Scala Object** (Oggetto Scala) e quindi su **Next** (Avanti).
   
    ![Finestra di dialogo Select a wizard (Seleziona una procedura guidata)](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
3. Nella finestra di dialogo **Create New File** (Crea nuovo file) immettere un nome per l'oggetto e quindi fare clic su **Finish** (Fine).
   
    ![Finestra di dialogo Create New File (Crea nuovo file)](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
4. Incollare il codice seguente nell'editor di testo:
   
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
5. Eseguire l'applicazione in un cluster HDInsight Spark:
   
   1. In Package Explorer (Esplora pacchetti) fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Submit Spark Application to HDInsight** (Invia applicazione Spark a HDInsight).        
   2. Nella finestra di dialogo **Spark Submission** (Invio Spark) specificare i valori seguenti e fare clic su **Submit** (Invia):
      
      * Per **Cluster Name**(Nome cluster) selezionare il cluster HDInsight Spark in cui eseguire l'applicazione.
      * Selezionare un elemento nel progetto Eclipse oppure nel disco rigido.
      * Nella casella di testo **Main class name** (Nome classe principale) immettere il nome dell'oggetto specificato nel codice.
      * Poiché il codice dell'applicazione in questo esempio non richiede argomenti della riga di comando e non fa riferimento a JAR o file, è possibile lasciare vuote le rimanenti caselle di testo.
        
       ![Finestra di dialogo Spark Submission (Invio Spark)](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
   3. Nella scheda **Spark Submission** (Invio Spark) verrà visualizzato lo stato di avanzamento. È possibile arrestare l'applicazione facendo clic sul pulsante rosso nella finestra **Spark Submission** (Invio Spark). È inoltre possibile visualizzare i log per questa esecuzione dell'applicazione specifica facendo clic sull'icona del mondo (indicata dalla casella blu nell'immagine).
      
       ![Finestra Spark Submission (Invio Spark)](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)
      
## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Accedere e gestire i cluster HDInsight Spark con gli strumenti HDInsight in Azure Toolkit for Eclipse
È possibile eseguire varie operazioni con gli strumenti HDInsight, tra cui accedere all'output dei processi.

### <a name="access-the-storage-container-for-the-cluster"></a>Accedere al contenitore di archiviazione per il cluster
1. In Azure Explorer espandere il nodo radice **HDInsight** per visualizzare un elenco di cluster HDInsight Spark disponibili.
2. Espandere il nome del cluster per visualizzare l'account di archiviazione e il contenitore di archiviazione predefinito per il cluster.
   
    ![Account di archiviazione e contenitore di archiviazione predefinito](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-5.png)
3. Fare clic sul nome del contenitore di archiviazione associato al cluster. Nel riquadro destro fare doppio clic sulla cartella **HVACOut**. Aprire uno dei file **part-** per visualizzare l'output dell'applicazione.

### <a name="access-the-spark-history-server"></a>Accedere al Server cronologia Spark
1. In Azure Explorer fare clic con il pulsante destro del mouse sul nome del cluster Spark e quindi scegliere **Open Spark History UI** (Apri UI cronologia Spark). Quando richiesto, immettere le credenziali dell'amministratore per il cluster. È necessario specificare le credenziali durante il provisioning del cluster.
2. Nel dashboard del Server cronologia Spark è possibile usare il nome dell'applicazione per cercare l'applicazione di cui è appena stata completata l'esecuzione. Nel codice precedente impostare il nome dell'applicazione usando `val conf = new SparkConf().setAppName("MyClusterApp")`. Il nome dell'applicazione Spark è **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Avviare il portale di Ambari
1. In Azure Explorer fare clic con il pulsante destro del mouse sul nome del cluster Spark e quindi scegliere **Open Cluster Management Portal (Ambari)** (Apri portale di gestione cluster - Ambari). 
2. Quando richiesto, immettere le credenziali dell'amministratore per il cluster. È necessario specificare le credenziali durante il provisioning del cluster.

### <a name="manage-azure-subscriptions"></a>Gestire le sottoscrizioni di Azure
Per impostazione predefinita, gli strumenti HDInsight in Azure Toolkit for Eclipse elencano i cluster Spark di tutte le sottoscrizioni di Azure. Se necessario, è possibile specificare le sottoscrizioni per cui si vuole accedere al cluster. 

1. In Azure Explorer fare clic con il pulsante destro del mouse sul nodo radice **Azure** e quindi scegliere **Gestisci sottoscrizioni**. 
2. Nella finestra di dialogo deselezionare le caselle di controllo della sottoscrizione alla quale non si vuole accedere e quindi fare clic su **Chiudi**. È anche possibile fare clic su **Esci** per uscire dalla sessione di sottoscrizione di Azure.

## <a name="run-a-spark-scala-application-locally"></a>Eseguire un'applicazione Spark in Scala localmente
È possibile usare gli strumenti HDInsight in Azure Toolkit for Eclipse per eseguire applicazioni Spark in Scala localmente nella workstation. Tali applicazioni in genere non richiedono l'accesso a risorse del cluster quali il contenitore di archiviazione e possono essere eseguite e testate localmente.

### <a name="prerequisite"></a>Prerequisito
Quando si esegue l'applicazione Spark Scala locale in un computer Windows, potrebbe essere restituita un'eccezione, come spiegato in [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356), che si verifica a causa di un file **WinUtils.exe** mancante in Windows. 

Per risolvere questo errore è necessario [scaricare il file eseguibile](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) in un percorso come **C:\WinUtils\bin**. È quindi necessario aggiungere una variabile di ambiente **HADOOP_HOME** e impostare il valore della variabile su **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Eseguire un'applicazione Spark in Scala locale
1. Avviare Eclipse e creare un progetto. Nella finestra di dialogo **New Project** (Nuovo progetto) selezionare le opzioni seguenti e quindi fare clic su **Next** (Avanti).
   
   * Selezionare **HDInsight** nel riquadro sinistro.
   * Selezionare **Spark on HDInsight Local Run Sample (Scala)** (Esecuzione locale di esempio Spark in HDInsight - Scala) nel riquadro destro.

    ![Finestra di dialogo Nuovo progetto](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
2. Per specificare i dettagli del progetto, seguire i passaggi da 3 a 6 illustrati nella sezione precedente [Configurare un progetto Spark in Scala per un cluster HDInsight Spark](#set-up-a-spark-scala-project-for-an-hdinsight-spark cluster).
3. Il modello aggiunge un codice di esempio (**LogQuery**) sotto la cartella **src** eseguibile in locale nel computer in uso.
   
    ![Percorso di LogQuery](./media/hdinsight-apache-spark-eclipse-tool-plugin/local-app.png)
4. Fare clic con il pulsante destro del mouse sull'applicazione **LogQuery**, scegliere **Run As** (Esegui come) e quindi fare clic su **1 Scala Application** (1 applicazione Scala). Verrà visualizzato un output simile al seguente nella scheda **Console** in basso:
   
   ![Risultato dell'esecuzione locale dell'applicazione Spark](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="feedback-and-known-issues"></a>Commenti, suggerimenti e problemi noti
Il supporto della visualizzazione diretta degli output di Spark non è al momento disponibile.

Per eventuali commenti o suggerimenti oppure se si riscontrano problemi nell'uso di questo strumento, inviare un messaggio di posta elettronica all'indirizzo hdivstool@microsoft.com.

## <a name="seealso"></a>Vedere anche
* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)

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
* [Usare Azure Toolkit for IntelliJ per creare e inviare applicazioni Spark in Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Spark](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gestione delle risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-job-debugging.md)


