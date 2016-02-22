 <properties 
	pageTitle="Creare applicazioni Spark in Scala tramite il plug-in di HDInsight per IntelliJ IDEA | Microsoft Azure" 
	description="Informazioni su come creare un'applicazione Scala autonoma da eseguire nei cluster HDInsight Spark." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2016" 
	ms.author="nitinme"/>


# Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark Scala (Linux)

Questo articolo contiene istruzioni dettagliate sullo sviluppo di applicazioni Spark scritte in Scala e il relativo invio a un cluster HDInsight Spark usando il plug-in di HDInsight per IntelliJ IDEA. È possibile usare il plug-in diversi modi:

* Per sviluppare e inviare un'applicazione Spark in Scala in un cluster HDInsight Spark
* Per accedere alle risorse cluster HDInsight Spark di Azure
* Per sviluppare ed eseguire un'applicazione Spark in Scala localmente

>[AZURE.IMPORTANT] Questo strumento può essere usato per creare e inviare applicazioni solo per un cluster HDInsight Spark in Linux.


**Prerequisiti**

* Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark in HDInsight Linux. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Oracle Java Development Kit. Per installarlo, fare clic [qui](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IntelliJ IDEA. In questo articolo viene usata la versione 15.0.1. Per installarlo, fare clic [qui](https://www.jetbrains.com/idea/download/). 


## Installare il plug-in Scala per IntelliJ IDEA

Se durante l'installazione di IntelliJ IDEA non è stata richiesta l'abilitazione del plug-in Scala, avviare IntelliJ IDEA e completare i passaggi seguenti per installare il plug-in:

1. Avviare IntelliJ IDEA e dalla schermata iniziale fare clic su **Configure** e quindi fare clic su **Plugins**.

	![Abilitazione di plug-in Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/enable-scala-plugin.png)

2. Nella schermata successiva fare clic su **Install JetBrains plugin** nell'angolo inferiore sinistro. Nella finestra di dialogo **Browse JetBrains Plugins** che si apre cercare Scala e quindi fare clic su **Install**.

	![Installazione di plug-in Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/install-scala-plugin.png)

3. Dopo l'installazione del plug-in, verrà richiesto di riavviare l'ambiente IDE. Per ora è possibile ignorare questo passaggio.

## Installare il plug-in degli strumenti HDInsight per IntelliJ IDEA

1. Nella schermata iniziale di IntelliJ IDEA fare clic su **Configure** e quindi nuovamente su **Plugins**.

2. Nella schermata successiva fare clic su **Browse Repositories** nell'angolo inferiore sinistro. Nella finestra di dialogo visualizzata **Browse Repositories** cercare **HDInsight**, selezionare **Microsoft Azure HDInsight Tools for IntelliJ** e quindi fare clic su **Install**.

3. Quando richiesto, fare clic sul pulsante **Restart IntelliJ IDEA** per riavviare l'ambiente IDE.

## Eseguire un'applicazione Spark in Scala in un cluster HDInsight Spark

1. Avviare IntelliJ IDEA e creare un nuovo progetto. Nella finestra di dialogo del nuovo progetto selezionare le opzioni seguenti e quindi fare clic su **Next**.

	![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

	* Fare clic su **HDInsight** nel riquadro di sinistra.
	* Fare clic su **Spark on HDInsight (Scala)** nel riquadro di destra.
	* Fare clic su **Next**.

2. Nella finestra successiva specificare i dettagli del progetto.

	![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)

	* Specificare un nome per il progetto e il relativo percorso.
	* Per **Project SDK** assicurarsi di specificare una versione di Java successiva alla 7.
	* Per **Scala SDK** fare clic su **Create**, quindi su **Download** e infine selezionare la versione di Scala da usare. **Assicurarsi di non usare la versione 2.11.x**. In questo esempio viene usata la versione **2.10.6**.
	
		![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* Per **Spark SDK** scaricare e usare l'SDK da [qui](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409).
	* Fare clic su **Finish**.

3. Definire la struttura del progetto per creare un elemento (file con estensione jar in pacchetto) che alla fine conterrà il codice che viene eseguito nel cluster.

	1. Nel menu **File** fare clic su **Project Structure**.
	2. Nella finestra di dialogo **Project Structure** fare clic su **Artifacts** e quindi sul segno più. Nella finestra di dialogo popup fare clic su **JAR** e quindi su **Empty**.

		![Creazione di un file con estensione jar](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-1.png)

	3. Immettere un nome per il file con estensione jar (ad esempio **MyClusterApp**). Nel riquadro di elementi disponibili fare clic con il pulsante destro del mouse su **'MyClusterApp' compile output** e quindi fare clic su **Put into Output Root**.

		![Creazione di un file con estensione jar](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-2.png)
	
	4. Fare clic su **Apply** e quindi su **OK**.

4. Aggiungere il codice sorgente dell'applicazione.

	1. In **Project Explorer** fare clic con il pulsante destro del mouse su **src**, scegliere **New** e quindi fare clic su **Scala class**.

		![Aggiunta di un codice sorgente](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

	2. Nella finestra di dialogo **Create New Scala Class** immettere un nome, selezionare **Object** per il campo **Kind** e quindi fare clic su **OK**.

		![Aggiunta di un codice sorgente](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

	3. Nel file **MyClusterApp.scala** incollare il codice seguente. Questo codice legge i dati dal file HVAC.csv, disponibile in tutti i cluster HDInsight Spark, recupera le righe che hanno solo una cifra nella settima colonna del file con estensione e scrive l'output di **/HVACOut** nel contenitore di archiviazione predefinito per il cluster.


			import org.apache.spark.SparkConf
			import org.apache.spark.SparkContext
			
			object MyClusterApp{
			  def main (arg: Array[String]): Unit = {
			    val conf = new SparkConf().setAppName("MyClusterApp")
			    val sc = new SparkContext(conf)
			
			    val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
			
			    //find the rows which have only one digit in the 7th column in the CSV
				val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
			
			    rdd1.saveAsTextFile("wasb:///HVACOut")
			  }
			
			}

5. Eseguire l'applicazione in un cluster HDInsight Spark.

	1. In **Project Explorer** fare clic con il pulsante destro del mouse sul nome del progetto e quindi selezionare **Submit Spark Application to HDInsight**.

		![Invio di un'applicazione Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

	2. Verrà richiesto di immettere le credenziali della sottoscrizione di Azure. Nella finestra di dialogo **Spark Submission** specificare i valori seguenti.

		![Invio di un'applicazione Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

		* Per **Spark clusters (Linux only)**, selezionare il cluster HDInsight Spark in cui eseguire l'applicazione.

		* Nell'elenco a discesa **Build Artifacts** vengono elencati il nome del file con estensione jar specificato nei passaggi precedenti.

		* Nella casella di testo **Main class name** fare clic sui puntini di sospensione (![puntini di sospensione](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png)), selezionare la classe principale nel codice sorgente dell'applicazione e quindi fare clic su **OK**.

			![Invio di un'applicazione Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)
	
		* Poiché il codice dell'applicazione in questo esempio non richiede argomenti della riga di comando e non fa riferimento a JAR o file, è possibile lasciare vuote le rimanenti caselle di testo.
	
		* Fare clic su **Submit**.

	3. Nella scheda **Spark Submission** nella parte inferiore della finestra verrà visualizzato lo stato di avanzamento.

	La sezione successiva illustrerà come accedere all'output del processo tramite il plug-in HDInsight per IntelliJ IDEA.


## Accedere e gestire i cluster HDInsight Spark tramite il plug-in HDInsight per IntelliJ

È possibile eseguire una serie di operazioni tramite il plug-in HDInsight.

### Accedere al contenitore di archiviazione per il cluster

1. Nel menu **View** selezionare **Tool Windows** e quindi fare clic su **HDInsight Explorer**. Se richiesto, immettere le credenziali per accedere alla sottoscrizione di Azure.

2. Espandere il nodo radice **HDInsight** per visualizzare un elenco di cluster HDInsight Spark disponibili.

3. Espandere il nome del cluster per visualizzare l'account di archiviazione e il contenitore di archiviazione predefinito per il cluster.

	![Accesso all'archiviazione cluster](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-access-storage.png)

4. Fare clic sul nome del contenitore di archiviazione associato al cluster. Nel riquadro di destra verrà visualizzata una cartella denominata **HVACOut**. Fare doppio clic per aprire la cartella e verranno visualizzati i file **part-***. Aprire uno dei file per visualizzare l'output dell'applicazione.

### Accedere a Spark History Server

1. In **HDInsight Explorer** fare clic con il pulsante destro del mouse sul nome del cluster Spark e quindi scegliere **Open Spark History UI**. Quando richiesto, immettere le credenziali per il cluster. È necessario specificare le credenziali durante il provisioning del cluster.

2. Nel dashboard di Spark History Server è possibile cercare l'applicazione di cui è appena stata completata l'esecuzione usando il nome dell'applicazione. Nel codice precedente impostare il nome dell'applicazione usando `val conf = new SparkConf().setAppName("MyClusterApp")`. Il nome dell'applicazione Spark era **MyClusterApp**.

### Avviare il portale di Ambari

In **HDInsight Explorer** fare clic con il pulsante destro del mouse sul nome del cluster Spark e quindi scegliere **Open Cluster Management Portal (Ambari)**. Quando richiesto, immettere le credenziali per il cluster. È necessario specificare le credenziali durante il provisioning del cluster.

### Gestire le sottoscrizioni di Azure

Per impostazione predefinita, nel plug-in HDInsight sono elencati i cluster Spark di tutte le sottoscrizioni di Azure. Se necessario, è possibile specificare le sottoscrizioni per cui si vuole accedere al cluster. In **HDInsight Explorer** fare clic con il pulsante destro del mouse sul nodo radice **HDInsight** e quindi scegliere **Manage Subscriptions**. Nella finestra di dialogo deselezionare le caselle di controllo della sottoscrizione alla quale non si vuole accedere e quindi fare clic su **Close**. È inoltre possibile fare clic su **Sign Out** per disconnettersi dalla sottoscrizione di Azure.


## Eseguire un'applicazione Spark in Scala localmente

È possibile usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per eseguire applicazioni Spark in Scala localmente nella workstation. Tali applicazioni in genere non richiedono l'accesso alle risorse del cluster come il contenitore di archiviazione e possono essere eseguite e testate localmente.

### Prerequisito

Quando si esegue l'applicazione Spark in Scala localmente in un computer Windows, è possibile che venga restituita un'eccezione, come spiegato in [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356), che si verifica a causa di un file WinUtils.exe mancante in Windows. Per risolvere questo errore, è necessario [scaricare il file eseguibile da qui](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) in un percorso simile a **C:\\WinUtils\\bin**. È quindi necessario aggiungere una variabile di ambiente **HADOOP\_HOME** e impostare il valore della variabile su **C\\WinUtils**.

### Eseguire un'applicazione Spark in Scala locale	 

1. Avviare IntelliJ IDEA e creare un nuovo progetto. Nella finestra di dialogo del nuovo progetto selezionare le opzioni seguenti e quindi fare clic su **Next**.

	![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

	* Fare clic su **HDInsight** nel riquadro di sinistra.
	* Fare clic su **Spark on HDInsight (Scala)** nel riquadro di destra.
	* Fare clic su **Next**.

2. Nella finestra successiva specificare i dettagli del progetto.

	![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-local-project-details.png)

	* Specificare un nome per il progetto e il relativo percorso.
	* Per **Project SDK** assicurarsi di specificare una versione di Java successiva alla 7.
	* Per **Scala SDK** fare clic su **Create**, quindi su **Download** e infine selezionare la versione di Scala da usare. **Assicurarsi di non usare la versione 2.11.x**. In questo esempio viene usata la versione **2.10.6**.
	
		![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* Per **Spark SDK** scaricare e usare l'SDK da [qui](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409).
	* Fare clic su **Finish**.

3. Definire la struttura del progetto per creare un elemento (file con estensione jar in pacchetto) che alla fine conterrà il codice che viene eseguito nel cluster.

	1. Nel menu **File** fare clic su **Project Structure**.
	2. Nella finestra di dialogo **Project Structure** fare clic su **Artifacts** e quindi sul segno più. Nella finestra di dialogo popup fare clic su **JAR** e quindi su **Empty**.

		![Creazione di un file con estensione jar](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-1.png)

	3. Immettere un nome per il file con estensione jar (ad esempio **MyLocalApp**). Nel riquadro Available Elements fare clic con il pulsante destro del mouse su **'MyLocalApp' compile output** e quindi fare clic su **Put into Output Root**.

		![Creazione di un file con estensione jar](./media/hdinsight-apache-spark-intellij-tool-plugin/create-local-jar-2.png)
	
	4. Fare clic su **Apply** e quindi su **OK**.

4. Aggiungere il codice sorgente dell'applicazione.

	1. In **Project Explorer** fare clic con il pulsante destro del mouse su **src**, scegliere **New** e quindi fare clic su **Scala class**.

		![Aggiunta di un codice sorgente](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-local-scala-code.png)

	2. Nella finestra di dialogo **Create New Scala Class** immettere un nome, selezionare **Object** per il campo **Kind** e quindi fare clic su **OK**.

		![Aggiunta di un codice sorgente](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-local-scala-code-object.png)

	3. Nel file **MyLocalApp.scala** incollare il codice seguente. Questo codice legge un file di testo di input di esempio nel computer e stampa il numero di righe nel file di testo contenenti i caratteri "a" e "b".


			import org.apache.spark.SparkContext
			import org.apache.spark.SparkContext._
			import org.apache.spark.SparkConf
			
			object MyLocalApp {
			  def main(args: Array[String]) {
			    val logFile = "C:/users/nitinme/Desktop/commands.txt" // Should be some file on your system
			    val conf = new SparkConf().setAppName("MyLocalApp").setMaster("local[2]")
			    val sc = new SparkContext(conf)
			    val logData = sc.textFile(logFile, 2).cache()
			    val numAs = logData.filter(line => line.contains("a")).count()
			    val numBs = logData.filter(line => line.contains("b")).count()
			    println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
			  }
			}

5. Eseguire l'applicazione localmente nella workstation. Selezionare **Run 'MyLocalApp'** dal menu **Run**. Verrà visualizzato un output simile al seguente nella scheda **Run** in basso.

		...
		...
		Lines with a: 4, Lines with b: 4
		...
		...
		16/02/01 15:04:05 INFO SparkContext: Successfully stopped SparkContext
		16/02/01 15:04:05 INFO ShutdownHookManager: Shutdown hook called
		16/02/01 15:04:05 INFO ShutdownHookManager: Deleting directory C:\Users\nitinme\AppData\Local\Temp\spark-618dee33-45a3-4bce-a8fc-bf85663133b3
		
		Process finished with exit code 0


## Convertire applicazioni IntelliJ IDEA esistenti per usare il plug-in dello strumento HDInsight

È inoltre possibile convertire le applicazioni Spark in Scala esistenti create in IntelliJ IDEA per renderle compatibili con il plug-in dello strumento HDInsight. In questo modo sarà possibile usare lo strumento per inviare le applicazioni a un cluster HDInsight Spark. A questo scopo, seguire questa procedura:

1. Per un'applicazione Spark in Scala esistente creata tramite IntelliJ IDEA, aprire il file IML associato.
2. A livello di radice verrà visualizzato un elemento **module** simile al seguente:

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

3. Modificare l'elemento per aggiungere `UniqueKey="HDInsightTool"` in modo che l'elemento **module** sia simile al seguente:

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

4. Salvare le modifiche. L'applicazione dovrebbe ora essere compatibile con il plug-in dello strumento HDInsight. È possibile verificarlo facendo clic con il pulsante destro del mouse sul nome del progetto in Project Explorer. Nel menu a comparsa viene ora visualizzata l'opzione **Submit Spark Application to HDInsight**.

## <a name="seealso"></a>Vedere anche


* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)

### Scenari

* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)

* [Spark con Machine Learning: usare Spark in HDInsight per l'analisi della temperatura dell'edificio mediante dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark con Machine Learning: usare Spark in HDInsight per prevedere i risultati del controllo degli alimenti](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)

* [Analisi dei log del sito Web mediante Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Creare ed eseguire applicazioni

* [Eseguire processi in modalità remota in un cluster Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### Strumenti ed estensioni

* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Gestire le risorse

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0211_2016-->