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
	ms.date="05/18/2016"
	ms.author="nitinme"/>


# Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per creare applicazioni Spark scritte in Scala per il cluster HDInsight Spark Linux (anteprima)

Questo articolo contiene istruzioni dettagliate sullo sviluppo di applicazioni Spark scritte in Scala e il relativo invio a un cluster HDInsight Spark usando il plug-in di HDInsight per IntelliJ IDEA. È possibile usare il plug-in diversi modi:

* Per sviluppare e inviare un'applicazione Spark in Scala in un cluster HDInsight Spark
* Per accedere alle risorse cluster HDInsight Spark di Azure
* Per sviluppare ed eseguire un'applicazione Spark in Scala localmente

Per iniziare, è anche possibile guardare un video disponibile [qui](https://mix.office.com/watch/1nqkqjt5xonza).

>[AZURE.IMPORTANT] Questo strumento può essere usato per creare e inviare applicazioni solo per un cluster HDInsight Spark in Linux.


**Prerequisiti**

* Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark in HDInsight Linux. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Oracle Java Development Kit. Per installarlo, fare clic [qui](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IntelliJ IDEA. In questo articolo viene usata la versione 15.0.1. Per installarlo, fare clic [qui](https://www.jetbrains.com/idea/download/).

## Installare il plug-in degli strumenti HDInsight per IntelliJ IDEA

1. Nella schermata iniziale di IntelliJ IDEA fare clic su **Configure** e quindi su **Plugins**.

2. Nella schermata successiva fare clic su **Browse Repositories** nell'angolo inferiore sinistro. Nella finestra di dialogo visualizzata **Browse Repositories** cercare **HDInsight**, selezionare **Microsoft Azure HDInsight Tools for IntelliJ (Preview)** e quindi fare clic su **Install**. Il plug-in include una dipendenza dal plug-in Scala, di conseguenza se il plug-in Scala non è installato, verrà richiesto di installarlo.

	![Installazione di plug-in HDInsight](./media/hdinsight-apache-spark-intellij-tool-plugin/install-hdinsight-plugin.png)

3. Quando richiesto, fare clic sul pulsante **Restart IntelliJ IDEA** per riavviare l'ambiente IDE.


## Eseguire un'applicazione Spark in Scala in un cluster HDInsight Spark

1. Avviare IntelliJ IDEA e creare un nuovo progetto. Nella finestra di dialogo del nuovo progetto selezionare le opzioni seguenti e quindi fare clic su **Next**.

	![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

	* Selezionare **HDInsight** nel riquadro sinistro.
	* Selezionare **Spark on HDInsight (Scala)** nel riquadro destro.
	* Fare clic su **Avanti**.

2. Nella finestra successiva specificare i dettagli del progetto.

	* Specificare un nome per il progetto e il relativo percorso.
	* Per **Project SDK** assicurarsi di specificare una versione di Java successiva alla 7.
	* Per **Scala SDK** fare clic su **Create**, quindi su **Download** e infine selezionare la versione di Scala da usare. **Assicurarsi di non usare la versione 2.11.x**. In questo esempio viene usata la versione **2.10.6**.

		![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* Per **Spark SDK** scaricare e usare l'SDK da [qui](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409). È anche possibile ignorare questo passaggio e usare il [repository Maven per Spark](http://mvnrepository.com/search?q=spark), assicurandosi tuttavia di aver installato il repository Maven corretto per sviluppare le applicazioni Spark. Ad esempio, è necessario verificare di aver installato la parte Spark Streaming, se si usa Spark Streaming e di usare il repository contrassegnato come Scala 2.10. Non usare il repository Scala 2.11.

		![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)

	* Fare clic su **Finish**.

3. Il progetto Spark creerà automaticamente un elemento. Per crearne uno personalizzato, seguire questa procedura:

	1. Nel menu **File** fare clic su **Project Structure**.
	2. Nella finestra di dialogo **Project Structure** fare clic su **Artifacts** e quindi sul segno più. Nella finestra di dialogo popup fare clic su **JAR** e quindi su **Empty**.

		![Creazione di un file con estensione jar](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-1.png)

	3. Immettere un nome per il file JAR, ad esempio **MyClusterApp**. Nel riquadro Available Elements fare clic con il pulsante destro del mouse su **'MyClusterApp' compile output** e quindi scegliere **Put into Output Root**.

		![Creazione di un file con estensione jar](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-2.png)

	4. Fare clic su **Apply** e quindi su **OK**.

4. Aggiungere il codice sorgente dell'applicazione.

	1. In **Project Explorer** fare clic con il pulsante destro del mouse su **src**, scegliere **New** e quindi fare clic su **Scala class**.

		![Aggiunta di un codice sorgente](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

	2. Nella finestra di dialogo **Create New Scala Class** immettere un nome, selezionare **Object** per il campo **Kind** e quindi fare clic su **OK**.

		![Aggiunta di un codice sorgente](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

	3. Nel file **MyClusterApp.scala** incollare il codice seguente. Questo codice legge i dati dal file HVAC.csv, disponibile in tutti i cluster HDInsight Spark, recupera le righe che hanno solo una cifra nella settima colonna del file CSV e scrive l'output in **/HVACOut** nel contenitore di archiviazione predefinito per il cluster.


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

	1. In **Project Explorer** fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Submit Spark Application to HDInsight**.

		![Invio di un'applicazione Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

	2. Verrà richiesto di immettere le credenziali della sottoscrizione di Azure. Nella finestra di dialogo **Spark Submission** specificare i valori seguenti.

		![Invio di un'applicazione Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

		* Per **Spark clusters (Linux only)**, selezionare il cluster HDInsight Spark in cui eseguire l'applicazione.

		* È necessario selezionare un elemento nel progetto IntelliJ oppure selezionarne uno dal disco rigido.

		* Nella casella di testo **Main class name** fare clic sui puntini di sospensione (![puntini di sospensione](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png)), selezionare la classe principale nel codice sorgente dell'applicazione e quindi fare clic su **OK**.

			![Invio di un'applicazione Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

		* Poiché il codice dell'applicazione in questo esempio non richiede argomenti della riga di comando e non fa riferimento a JAR o file, è possibile lasciare vuote le rimanenti caselle di testo.

		* Fare clic su **Submit**.

	3. Nella scheda **Spark Submission** nella parte inferiore della finestra verrà visualizzato lo stato di avanzamento. È anche possibile arrestare l'applicazione facendo clic sul pulsante rosso nella finestra "Spark Submission".

        ![Risultato dell'applicazione Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

    La sezione successiva illustrerà come accedere all'output del processo tramite il plug-in HDInsight per IntelliJ IDEA.


## Accedere e gestire i cluster HDInsight Spark tramite il plug-in HDInsight per IntelliJ

È possibile eseguire una serie di operazioni tramite il plug-in HDInsight.

### Accedere al contenitore di archiviazione per il cluster

1. Nel menu **View** selezionare **Tool Windows** e quindi fare clic su **HDInsight Explorer**. Se richiesto, immettere le credenziali per accedere alla sottoscrizione di Azure.

2. Espandere il nodo radice **HDInsight** per visualizzare un elenco di cluster HDInsight Spark disponibili.

3. Espandere il nome del cluster per visualizzare l'account di archiviazione e il contenitore di archiviazione predefinito per il cluster.

	![Accesso all'archiviazione cluster](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-access-storage.png)

4. Fare clic sul nome del contenitore di archiviazione associato al cluster. Nel riquadro a destra verrà visualizzata una cartella denominata **HVACOut**. Fare doppio clic per aprire la cartella. Verranno visualizzati i file **part-***. Aprire uno dei file per visualizzare l'output dell'applicazione.

### Accedere a Spark History Server

1. In **HDInsight Explorer** fare clic con il pulsante destro del mouse sul nome del cluster Spark e quindi scegliere **Open Spark History UI**. Quando richiesto, immettere le credenziali per il cluster. È necessario specificare le credenziali durante il provisioning del cluster.

2. Nel dashboard di Spark History Server è possibile cercare l'applicazione di cui è appena stata completata l'esecuzione usando il nome dell'applicazione. Nel codice precedente impostare il nome dell'applicazione usando `val conf = new SparkConf().setAppName("MyClusterApp")`. Il nome dell'applicazione Spark è **MyClusterApp**.

### Avviare il portale di Ambari

In **HDInsight Explorer** fare clic con il pulsante destro del mouse sul nome del cluster Spark e quindi scegliere **Open Cluster Management Portal (Ambari)**. Quando richiesto, immettere le credenziali per il cluster. È necessario specificare le credenziali durante il provisioning del cluster.

### Gestire le sottoscrizioni di Azure

Per impostazione predefinita, nel plug-in HDInsight sono elencati i cluster Spark di tutte le sottoscrizioni di Azure. Se necessario, è possibile specificare le sottoscrizioni per cui si vuole accedere al cluster. In **HDInsight Explorer** fare clic con il pulsante destro del mouse sul nodo radice **HDInsight** e quindi scegliere **Manage Subscriptions**. Nella finestra di dialogo deselezionare le caselle di controllo della sottoscrizione alla quale non si vuole accedere e quindi fare clic su **Close**. È anche possibile fare clic su **Sign Out** per disconnettersi dalla sottoscrizione di Azure.


## Eseguire un'applicazione Spark in Scala localmente

È possibile usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per eseguire applicazioni Spark in Scala localmente nella workstation. Tali applicazioni in genere non richiedono l'accesso alle risorse del cluster come il contenitore di archiviazione e possono essere eseguite e testate localmente.

### Prerequisito

Quando si esegue l'applicazione Spark in Scala locale in un computer Windows, è possibile che venga restituita un'eccezione, come spiegato in [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356), che si verifica a causa di un file WinUtils.exe mancante in Windows. Per risolvere questo errore, è necessario [scaricare il file eseguibile da qui](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) in un percorso simile a **C:\\WinUtils\\bin**. È quindi necessario aggiungere una variabile di ambiente **HADOOP\_HOME** e impostare il valore della variabile su **C\\WinUtils**.

### Eseguire un'applicazione Spark in Scala locale	 

1. Avviare IntelliJ IDEA e creare un nuovo progetto. Nella finestra di dialogo del nuovo progetto selezionare le opzioni seguenti e quindi fare clic su **Next**.

	![Creazione di un'applicazione Spark in Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)

	* Selezionare **HDInsight** nel riquadro sinistro.
	* Selezionare **Spark on HDInsight Local Run Sample (Scala)** nel riquadro destro.
	* Fare clic su **Avanti**.

2. Per configurare le impostazioni necessarie, seguire la procedura illustrata in "Eseguire un'applicazione Spark in Scala in un cluster HDInsight Spark".

3. Eseguire l'applicazione localmente nella workstation. Fare clic con il pulsante destro del mouse sull'applicazione Scala e quindi scegliere **"Run 'LogQuery'"**. Verrà visualizzato un output simile al seguente nella scheda **Run** in basso.

	![Risultato dell'esecuzione locale dell'applicazione Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## Convertire applicazioni IntelliJ IDEA esistenti per usare il plug-in dello strumento HDInsight

È inoltre possibile convertire le applicazioni Spark in Scala esistenti create in IntelliJ IDEA per renderle compatibili con il plug-in dello strumento HDInsight. In questo modo sarà possibile usare lo strumento per inviare le applicazioni a un cluster HDInsight Spark. A questo scopo, seguire questa procedura:

1. Per un'applicazione Spark in Scala esistente creata tramite IntelliJ IDEA, aprire il file IML associato.
2. A livello di radice verrà visualizzato un elemento **module** simile al seguente:

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

3. Modificare l'elemento per aggiungere `UniqueKey="HDInsightTool"` in modo che l'elemento **module** sia simile al seguente:

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

4. Salvare le modifiche. L'applicazione dovrebbe ora essere compatibile con il plug-in dello strumento HDInsight. È possibile verificarlo facendo clic con il pulsante destro del mouse sul nome del progetto in Project Explorer. Nel menu a comparsa viene ora visualizzata l'opzione **Submit Spark Application to HDInsight**.


## Risoluzione dei problemi

### Errore relativo all'uso di dimensioni heap maggiori nell'esecuzione locale

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

Questi errori si verificano perché le dimensioni dell'heap non sono sufficientemente grandi per l'esecuzione di Spark, poiché Spark richiede almeno 471 MB. Per altre informazioni dettagliate, vedere [SPARK 12081](https://issues.apache.org/jira/browse/SPARK-12081). Una soluzione semplice consiste nell'uso di un SDK per Java a 64 bit. È anche possibile modificare le impostazioni JVM in IntelliJ aggiungendo le opzioni seguenti:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Risultato dell'esecuzione locale dell'applicazione Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/change-heap-size.png)

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

* [Eseguire processi in modalità remota in un cluster Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### Strumenti ed estensioni

* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Gestire le risorse

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0601_2016-->