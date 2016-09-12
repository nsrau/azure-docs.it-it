<properties 
	pageTitle="Problemi noti di Apache Spark in HDInsight | Microsoft Azure" 
	description="Problemi noti di Apache Spark in Azure HDInsight." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/25/2016" 
	ms.author="nitinme"/>

# Problemi noti del cluster Apache Spark in HDInsight Linux

Questo documento elenca tutti i problemi noti relativi all'anteprima pubblica di HDInsight Spark.

##Livy perde la sessione interattiva
 
Quando Livy viene riavviato con una sessione interattiva (da Ambari oppure a causa del riavvio della macchina virtuale con nodo head 0) ancora attiva, una sessione interattiva andrà persa. Per questo motivo, i nuovi processi possono rimanere bloccati in stato Accettato e non possono essere avviati.

**Soluzione:**

Seguire questa procedura per risolvere il problema:

1. Eseguire SSH nel nodo head.
2. Eseguire il comando seguente per trovare gli ID applicazione dei processi interattivi avviati tramite Livy.

        yarn application –list

    I nomi di processo predefiniti corrispondono a Livy se i processi sono stati avviati con una sessione interattiva Livy senza specificare nomi espliciti. Per la sessione Livy avviata dal notebook di Jupyter, il nome del processo verrà avviato con remotesparkmagics\_*.

3. Eseguire il comando seguente per terminare questi processi.

        yarn application –kill <Application ID>

Verranno avviati nuovi processi.

##Il server cronologia Spark non viene avviato 

Il server cronologia Spark non viene avviato automaticamente dopo la creazione di un cluster.

**Soluzione:**

Avviare manualmente il server cronologia da Ambari.

## Problema di autorizzazioni nella directory log Spark 

Quando hdiuser invia un processo con spark-submit, si verifica un errore java.io.FileNotFoundException: /var/log/spark/sparkdriver\_hdiuser.log (autorizzazione negata) e il log del driver non viene scritto.

**Soluzione:**
 
1. Aggiungere hdiuser al gruppo Hadoop.
2. Indicare 777 autorizzazioni in /var/log/spark dopo la creazione del cluster.
3. Aggiornare il percorso del log Spark tramite Ambari in modo che corrisponda a una directory con 777 autorizzazioni.
4. Eseguire spark-submit come sudo.

## Problemi relativi ai notebook Jupyter

Seguito alcuni problemi noti relativi ai notebook Jupyter.


### Notebook con nomi di file contenenti caratteri non ASCII

I notebook Jupyter utilizzabili nei cluster HDInsight Spark non devono contenere nei nomi di file caratteri non ASCII. Se si tenta di caricare tramite l'interfaccia utente Jupyter un file con un nome di file non ASCII, l'operazione si interromperà senza avvisi. Questo significa che Jupyter non consentirà di caricare il file, ma non genererà un errore visibile.

### Errore durante il caricamento di notebook di maggiori dimensioni

Quando si caricano notebook di maggiori dimensioni, potrebbe comparire l'errore **`Error loading notebook`**.

**Soluzione:**

Se viene visualizzato questo errore, non significa che i dati sono danneggiati o persi. I notebook sono ancora disponibili su disco in `/var/lib/jupyter` ed è possibile usare SSH nel cluster per accedervi. Si possono copiare i notebook dal cluster nel computer locale (tramite SCP o WinSCP) come backup per evitare la perdita di dati importanti del notebook. È quindi possibile usare SSH per il tunneling al nodo head sulla porta 8001 e accedere a Jupyter senza passare attraverso il gateway. Qui è possibile cancellare l'output del notebook e salvarlo di nuovo per ridurne le dimensioni.

Per evitare questo errore in futuro, è necessario seguire alcune procedure consigliate:

* È importante mantenere ridotte le dimensioni del notebook. L'output dei processi Spark inviato a Jupyter viene salvato in modo permanente nel notebook. Con Jupyter in genere è consigliabile evitare di eseguire `.collect()` su RDD o frame di dati di grandi dimensioni. Se si vuole visualizzare il contenuto di un RDD, considerare invece la possibilità di eseguire `.take()` o `.sample()` per evitare la crescita eccessiva dell'output.
* Quando si salva un notebook, cancellare anche tutte le celle di output per ridurre le dimensioni.

### L'avvio iniziale del notebook richiede più tempo del previsto 

La prima istruzione del codice nel notebook di Jupyter tramite il magic Spark potrebbe richiedere più di un minuto.

**Spiegazione:**
 
Ciò accade quando viene eseguita la prima cella di codice. In background viene avviata la configurazione della sessione e vengono impostati i contesti Spark, SQL e Hive. La prima istruzione viene eseguita dopo l'impostazione di questi contesti, dando l'impressione che l'esecuzione dell'istruzione impieghi molto tempo.

### Timeout del notebook di Jupyter durante la creazione della sessione

Quando il cluster Spark esaurisce le risorse, si verificherà il timeout dei kernel Spark e Pyspark nel notebook di Jupyter quando si cerca di creare la sessione.

**Soluzioni:**

1. Liberare alcune risorse nel cluster Spark nei modi seguenti:

    - Arrestando altri notebook Spark selezionando il menu Close and Halt o facendo clic su Shutdown nel notebook.
    - Arrestando altre applicazioni Spark da YARN.

2. Riavviare il notebook che si stava cercando di avviare. Ora dovrebbero essere disponibili risorse sufficienti per creare una sessione.

##Vedere anche

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

* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark in Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Spark in remoto)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Usare pacchetti esterni con i notebook Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Gestire risorse

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0831_2016-->