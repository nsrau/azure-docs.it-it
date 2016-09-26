 <properties
	pageTitle="Creare applicazioni Spark in Scala tramite gli strumenti HDInsight nel Toolkit di Azure per Eclipse | Microsoft Azure"
	description="Informazioni su come creare un'applicazione Scala autonoma da eseguire nei cluster HDInsight Spark."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>  

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/30/2016"
	ms.author="nitinme"/>  


# Usare gli strumenti HDInsight nel Toolkit di Azure per Eclipse per creare applicazioni Spark per il cluster HDInsight Spark Linux

Questo articolo contiene istruzioni dettagliate sullo sviluppo di applicazioni Spark scritte in Scala e il relativo invio a un cluster HDInsight Spark usando gli strumenti HDInsight nel Toolkit di Azure per Eclipse. È possibile usare gli strumenti in diversi modi:

* Per sviluppare e inviare un'applicazione Spark in Scala in un cluster HDInsight Spark
* Per accedere alle risorse cluster HDInsight Spark di Azure
* Per sviluppare ed eseguire un'applicazione Spark in Scala localmente

>[AZURE.IMPORTANT] Questo strumento può essere usato per creare e inviare applicazioni solo per un cluster HDInsight Spark in Linux.


##Prerequisiti

* Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un cluster Apache Spark in HDInsight Linux. Per istruzioni, vedere l'articolo relativo alla [creazione di cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

* Oracle Java Development Kit versione 7 e 8.
	* **Java SDK 7** viene usato per la compilazione di progetti Spark dal momento che i cluster HDInsight supportano Java versione 7. È possibile scaricare Java SDK 7 da [qui](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html).
	* **Java SDK 8** viene usato per il runtime dell'ambiente IDE Eclipse. È possibile scaricarlo [qui](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

* Ambiente IDE Eclipse. Questo articolo usa Eclipse Neon. Per installarlo, fare clic [qui](https://www.eclipse.org/downloads/).

* Ambiente IDE Scala per Eclipse.
	* **In caso di installazione di ambiente IDE Eclipse**, è possibile aggiungere il plug-in IDE Scala tramite**Help** (Guida) -> **Install New Software** (Installa nuovo software) e aggiungere [http://download.scala-ide.org/sdk/lithium/e44/scala211/stable/site](http://download.scala-ide.org/sdk/lithium/e44/scala211/stable/site) come origine per il download del plug-in Scala per Eclipse.
	* **Se l'ambiente IDE Eclipse non è installato**, è possibile installare l'ambiente IDE Scala direttamente da [qui](http://scala-ide.org/download/sdk.html). È possibile scaricare il file ZIP da questo collegamento, estrarlo, passare alla cartella **/eclipse** e quindi eseguire il file **eclipse.exe** da tale posizione.
	
	>[AZURE.NOTE] I passaggi in questo documento si basano sull'uso dell'ambiente IDE Eclipse con il plug-in Scala installato.

* Spark SDK. È possibile scaricarlo [qui](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409).

* Installare e(fx)clipse da [https://www.eclipse.org/efxclipse/install.html](https://www.eclipse.org/efxclipse/install.html).


## Installare gli strumenti HDInsight nel Toolkit di Azure per Eclipse

Gli strumenti HDInsight per Eclipse sono disponibili come parte del Toolkit di Azure per Eclipse. Per istruzioni su come installare Azure Toolkit, vedere [Installazione di Azure Toolkit per Eclipse](../azure-toolkit-for-eclipse-installation.md).

## Accedere alla propria sottoscrizione di Azure

1. Avviare Eclipse IDE e aprire Azure Explorer. Nel menu **Window** (Finestra) nell'IDE fare clic su **Show View** (Mostra visualizzazione) e quindi fare clic su **Other** (Altro). Nella finestra di dialogo visualizzata espandere **Azure**, fare clic su **Azure Explorer** e quindi fare clic su **OK**.

	![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-1.png)  

2. In **Azure Explorer** fare clic con il pulsante destro del mouse sul nodo **Azure** e scegliere **Manage Subscriptions** (Gestisci sottoscrizioni).

3. Nella finestra di dialogo **Manage Subscriptions** (Gestisci sottoscrizioni) fare clic su **Sign in** (Accedi) e immettere le credenziali di Azure.

	![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-2.png)

4. Dopo l'accesso, la finestra di dialogo **Manage Subscriptions** (Gestisci sottoscrizioni) elenca tutte le sottoscrizioni di Azure associate alle credenziali. Fare clic su **Close** (Chiudi) nella finestra di dialogo.

5. Nella scheda Azure Explorer espandere **HDInsight** per visualizzare i cluster HDInsight Spark nella sottoscrizione.

	![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-3.png)

6. È possibile espandere ulteriormente un nodo del nome cluster per vedere le risorse, ad esempio gli account di archiviazione, associate al cluster.

	![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-4.png)  

## Configurare un progetto Spark in Scala per un cluster HDInsight Spark

1. Nell'area di lavoro dell'ambiente IDE Eclipse fare clic su **File**, quindi su **New** (Nuovo) e infine su **Project** (Progetto).

2. Nella procedura guidata **New Project** (Nuovo progetto) espandere **HDInsight**, selezionare **Spark on HDInsight (Scala)** (Spark in HDInsight (Scala)) e quindi fare clic su **Next** (Avanti).

	![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)  

3. Nella finestra di dialogo **New HDInsight Scala Project** (Nuovo progetto HDInsight Scala) immettere/selezionare i valori come illustrato nell'immagine seguente e quindi fare clic su **Next** (Avanti).

	![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)

	* Immettere un nome per il progetto.
	* Verificare che nella casella **JRE** l'opzione **Use an execution environment JRE** (Usa un ambiente di esecuzione JRE) sia impostata su **JavaSE-1.7**.
	* Assicurarsi che Spark SDK sia impostato per il percorso in cui è stato scaricato l'SDK. Il collegamento al percorso di download è incluso nella sezione [Prerequisiti](#prerequisites) precedentemente illustrata in questo argomento. È anche possibile scaricare l'SDK dal collegamento incluso in questa finestra di dialogo, come illustrato nell'immagine precedente.

4. Nella successiva finestra di dialogo fare clic sulla scheda **Libraries** (Librerie) e quindi fare doppio clic su **JRE System Library [JavaSE-1.7]** (Libreria sistema JRE [JavaSE-1.7]).

	![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-4.png)  

5. Nella finestra di dialogo **Edit Library** (Modifica libreria) verificare che **Execution Environment** (Ambiente di esecuzione) sia impostato su **JavaSE-1.7(jdk1.7.0\_79)**. Se non è disponibile come opzione, seguire la procedura seguente.

	1. Selezionare l'opzione **Alternate JRE** (JRE alternativo) e vedere se **JavaSE-1.7(jdk1.7.0\_79)** è disponibile.
	2. In caso contrario, fare clic sul pulsante **Installed JREs** (JRE installati).

		  ![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-5.png)

	3. Nella finestra di dialogo **Installed JREs** (JRE installati) fare clic su **Add** (Aggiungi).

		  ![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-6.png)  

	4. Nella finestra di dialogo **JRE Type** (Tipo JRE) selezionare **Standard VM** (VM standard) e quindi fare clic su **Next** (Avanti)

		  ![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-7.png)

	5. Nella finestra di dialogo **JRE Definition** (Definizione JRE) fare clic su Directory, quindi passare al percorso per l'installazione di JDK 7 e selezionare la cartella radice per **jdk1.7.0\_79**.

		  ![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-8.png)  

	6. Fare clic su **Fine**. Nella finestra di dialogo **Installed JREs** (JRE installati) selezionare l'ambiente JRE appena aggiunto e quindi fare clic su **OK**.

		   ![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-9.png)  

	7. L'ambiente JRE appena aggiunto deve essere elencato per **Execution Environment** (Ambiente di esecuzione). Fare clic su **Fine**.

	  	   ![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-10.png)  

6. Tornando nella scheda **Libraries** (Librerie) fare doppio clic su **Scala Library Container[2.11.8]** (Contenitore della libreria Scala [2.11.8]). Nella finestra di dialogo **Edit Library** (Modifica libreria) selezionare **Fixed Scala Library container:2.10.6** (Contenitore libreria Scala fissa: 2.10.6).

	![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-11.png)  

	Fare clic su **Finish** (Fine) fino a chiudere la finestra di dialogo delle impostazioni di progetto.

## Eseguire un'applicazione Scala in un cluster HDInsight Spark

1. Nell'ambiente IDE Eclipse già aperto, in **Package Explorer**, espandere il progetto creato in precedenza, fare clic con il pulsante destro del mouse su **src**, selezionare **New** (Nuovo) e quindi fare clic su **Other** (Altro).

2. Nella finestra di dialogo **Select a wizard** (Seleziona una procedura guidata) espandere **Scala Wizards** (Procedure guidate Scala), fare clic su **Scala Object** (Oggetto Scala) e quindi su **Next** (Avanti).

	![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)

3. Nella finestra di dialogo **Create New File** (Crea nuovo file) immettere un nome per l'oggetto e quindi fare clic su **Finish** (Fine).

	![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)  

4. Incollare il codice seguente nell'editor di testo.

		import org.apache.spark.SparkConf
		import org.apache.spark.SparkContext
	
		object MyClusterApp{
		  def main (arg: Array[String]): Unit = {
		    val conf = new SparkConf().setAppName("MyClusterApp")
		    val sc = new SparkContext(conf)
		
		    val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		    //find the rows which have only one digit in the 7th column in the CSV
		    val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
		
		    rdd1.saveAsTextFile("wasbs:///HVACOut")
		  }		
		}


5. Eseguire l'applicazione in un cluster HDInsight Spark.

	1. In **Package Explorer** fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Submit Spark Application to HDInsight** (Invia applicazione Spark a HDInsight).

	2. Nella finestra di dialogo **Spark Submission** (Invio Spark) specificare i valori seguenti.

		* Per **Cluster Name** (Nome cluster) selezionare il cluster HDInsight Spark in cui eseguire l'applicazione.

		* È necessario selezionare un elemento nel progetto Eclipse oppure selezionarne uno dal disco rigido.

		* Nella casella di testo **Main class name** (Nome classe principale) immettere il nome dell'oggetto specificato nel codice (vedere la figura seguente).

			![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)  

		* Poiché il codice dell'applicazione in questo esempio non richiede argomenti della riga di comando e non fa riferimento a JAR o file, è possibile lasciare vuote le rimanenti caselle di testo.

		* Fare clic su **Submit**.

	3. Nella scheda **Spark Submission** (Invio Spark) verrà visualizzato lo stato di avanzamento. È possibile arrestare l'applicazione facendo clic sul pulsante rosso nella finestra "Spark Submission" (Invio Spark). È inoltre possibile visualizzare i log per questa esecuzione dell'applicazione specifica facendo clic sull'icona del mondo (indicata dalla casella blu nell'immagine).

        ![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)  

    La sezione successiva illustrerà come accedere all'output del processo tramite gli strumenti HDInsight nel Toolkit di Azure per Eclipse.


## Accedere e gestire i cluster HDInsight Spark tramite gli strumenti HDInsight nel Toolkit di Azure per Eclipse.

È possibile eseguire una serie di operazioni tramite gli strumenti HDInsight.

### Accedere al contenitore di archiviazione per il cluster

1. In Azure Explorer espandere il nodo radice **HDInsight** per visualizzare un elenco di cluster HDInsight Spark disponibili.

3. Espandere il nome del cluster per visualizzare l'account di archiviazione e il contenitore di archiviazione predefinito per il cluster.

	![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-5.png)

4. Fare clic sul nome del contenitore di archiviazione associato al cluster. Nel riquadro a destra verrà visualizzata una cartella denominata **HVACOut**. Fare doppio clic per aprire la cartella. Verranno visualizzati i file **part-***. Aprire uno dei file per visualizzare l'output dell'applicazione.

### Accedere a Spark History Server

1. In **Azure Explorer** fare clic con il pulsante destro del mouse sul nome del cluster Spark e quindi scegliere **Open Spark History UI** (Apri UI cronologia Spark). Quando richiesto, immettere le credenziali per il cluster. È necessario specificare le credenziali durante il provisioning del cluster.

2. Nel dashboard di Spark History Server è possibile cercare l'applicazione di cui è appena stata completata l'esecuzione usando il nome dell'applicazione. Nel codice precedente impostare il nome dell'applicazione usando `val conf = new SparkConf().setAppName("MyClusterApp")`. Il nome dell'applicazione Spark è **MyClusterApp**.

### Avviare il portale di Ambari

In **Azure Explorer** fare clic con il pulsante destro del mouse sul nome del cluster Spark e quindi scegliere **Open Cluster Management Portal (Ambari)** (Apri portale di gestione cluster (Ambari)). Quando richiesto, immettere le credenziali per il cluster. È necessario specificare le credenziali durante il provisioning del cluster.

### Gestire le sottoscrizioni di Azure

Per impostazione predefinita, gli strumenti HDInsight nel Toolkit di Azure per Eclipse elencano i cluster Spark di tutte le sottoscrizioni di Azure. Se necessario, è possibile specificare le sottoscrizioni per cui si vuole accedere al cluster. In **Azure Explorer** fare clic con il pulsante destro del mouse sul nodo radice **Azure** e scegliere **Manage Subscriptions** (Gestisci sottoscrizioni). Nella finestra di dialogo deselezionare le caselle di controllo della sottoscrizione alla quale non si vuole accedere e quindi fare clic su **Close** (Chiudi). È anche possibile fare clic su **Sign Out** (Disconnetti) per uscire dalla sessione di sottoscrizione di Azure.


## Eseguire un'applicazione Spark in Scala localmente

È possibile usare gli strumenti HDInsight nel Toolkit di Azure per Eclipse per eseguire applicazioni Spark in Scala localmente nella workstation. Tali applicazioni in genere non richiedono l'accesso alle risorse del cluster come il contenitore di archiviazione e possono essere eseguite e testate localmente.

### Prerequisito

Quando si esegue l'applicazione Spark in Scala locale in un computer Windows, è possibile che venga restituita un'eccezione, come spiegato in [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356), che si verifica a causa di un file **WinUtils.exe** mancante in Windows. Per risolvere questo errore, è necessario [scaricare il file eseguibile da qui](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) in un percorso come **C:\\WinUtils\\bin**. È quindi necessario aggiungere una variabile di ambiente **HADOOP\_HOME** e impostare il valore della variabile su **C\\WinUtils**.

### Eseguire un'applicazione Spark in Scala locale	 

1. Avviare Eclipse e creare un nuovo progetto. Nella finestra di dialogo del nuovo progetto selezionare le opzioni seguenti e quindi fare clic su **Next**.

	![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)

	* Selezionare **HDInsight** nel riquadro sinistro.
	* Selezionare **Spark on HDInsight Local Run Sample (Scala)** (Spark su esecuzione di esempio locale HDInsight - Scala) nel riquadro destro.
	* Fare clic su **Next**.

2. Per fornire i dettagli del progetto, seguire i passaggi da 3 a 6, come illustrato nella sezione precedente [Configurare un progetto di un'applicazione Spark in Scala per un cluster HDInsight Spark] (#set-up-a-spark-scala-application-project-for-an-hdinsight-spark cluster).

3. Il modello aggiunge un codice di esempio (**LogQuery**) sotto la cartella **src** che è possibile eseguire in locale nel computer in uso.

	![Risultato dell'esecuzione locale dell'applicazione Spark](./media/hdinsight-apache-spark-eclipse-tool-plugin/local-app.png)  

4.  Fare clic con il pulsante destro del mouse sull'applicazione **LogQuery**, puntare a **Run As** (Esegui come) e quindi fare clic su **1 Scala Application** (1 applicazione Scala). Verrà visualizzato un output simile al seguente nella scheda **Console** in basso.

	![Risultato dell'esecuzione locale dell'applicazione Spark](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)  

## Commenti e suggerimenti e problemi noti

Anche se è previsto il supporto della visualizzazione diretta degli output di Spark in futuro, questa funzionalità non è al momento disponibile.

Per eventuali commenti o suggerimenti oppure se vengono riscontrati problemi durante l'utilizzo di questo strumento, inviare un messaggio di posta elettronica all'indirizzo hdivstool@microsoft.com.

## <a name="seealso"></a>Vedere anche


* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)

### Scenari

* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)

* [Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark con Machine Learning: usare Spark in HDInsight per prevedere i risultati del controllo degli alimenti](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)

* [Analisi dei log del sito Web mediante Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Creare ed eseguire applicazioni

* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Eseguire processi in modalità remota in un cluster Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### Strumenti ed estensioni

* [Usare gli strumenti HDInsight nel Toolkit di Azure per IntelliJ per creare e inviare applicazioni Spark in Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Use HDInsight Tools in Azure Toolkit for IntelliJ to debug Spark applications remotely (Usare gli strumenti HDInsight nel Toolkit di Azure per IntelliJ per eseguire il debug di applicazioni Spark in remoto)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Usare pacchetti esterni con i notebook Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Gestire risorse

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0914_2016-->