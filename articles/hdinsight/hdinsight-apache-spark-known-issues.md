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
	ms.date="02/17/2016" 
	ms.author="nitinme"/>

# Problemi noti di Apache Spark in Azure HDInsight (Linux)

Questo documento elenca tutti i problemi noti relativi all'anteprima pubblica di HDInsight Spark.

##Livy perde la sessione interattiva
 
**Sintomo:**

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

**Sintomo:**
 
Il server cronologia Spark non viene avviato automaticamente dopo la creazione di un cluster.

**Soluzione:**

Avviare manualmente il server cronologia da Ambari.

##Errore durante il caricamento di notebook di maggiori dimensioni

**Sintomo:**

Potrebbe essere visualizzato l'errore **`Error loading notebook`** quando si caricano i notebook di maggiori dimensioni.

**Soluzione:**

Se viene visualizzato questo errore, non significa che i dati sono danneggiati o persi. I notebook sono ancora disponibili su disco in `/var/lib/jupyter` ed è possibile usare SSH nel cluster per accedervi. Si possono copiare i notebook dal cluster nel computer locale (tramite SCP o WinSCP) come backup per evitare la perdita di dati importanti del notebook. È quindi possibile usare SSH per il tunneling al nodo head sulla porta 8001 e accedere a Jupyter senza passare attraverso il gateway. Qui è possibile cancellare l'output del notebook e salvarlo di nuovo per ridurne le dimensioni.

Per evitare questo errore in futuro, è necessario seguire alcune procedure consigliate:

* È importante mantenere ridotte le dimensioni del notebook. L'output dei processi Spark inviato a Jupyter viene salvato in modo permanente nel notebook. Con Jupyter è in genere consigliabile evitare di eseguire `.collect()` su RDD o frame di dati di grandi dimensioni. Se si vuole visualizzare il contenuto di un RDD, considerare invece la possibilità di eseguire `.take()` o `.sample()` per evitare la crescita eccessiva dell'output.
* Quando si salva un notebook, cancellare anche tutte le celle di output per ridurre le dimensioni.



##L'avvio iniziale del notebook richiede più tempo del previsto 

**Sintomo:**

La prima istruzione nel notebook di Jupyter tramite magic Spark potrebbe richiedere più di un minuto.

**Spiegazione:**
 
Ciò accade quando viene eseguita la prima cella di codice. In background viene avviata la configurazione della sessione e vengono impostati i contesti Spark, SQL e Hive. La prima istruzione viene eseguita dopo l'impostazione di questi contesti, dando l'impressione che l'esecuzione dell'istruzione impieghi molto tempo.

##Timeout del notebook di Jupyter durante la creazione della sessione

**Sintomo:**

Quando il cluster Spark esaurisce le risorse, si verificherà il timeout dei kernel Spark e Pyspark nel notebook di Jupyter quando si cerca di creare la sessione.

**Soluzioni:**

1. Liberare alcune risorse nel cluster Spark nei modi seguenti:

    - Arrestando altri notebook Spark selezionando il menu Close and Halt o facendo clic su Shutdown nel notebook.
    - Arrestando altre applicazioni Spark da YARN.

2. Riavviare il notebook che si stava cercando di avviare. Ora dovrebbero essere disponibili risorse sufficienti per creare una sessione.

## Problema di autorizzazioni nella directory log Spark 

**Sintomo:**
 
Quando hdiuser invia un processo con spark-submit, si verifica un errore java.io.FileNotFoundException: /var/log/spark/sparkdriver\_hdiuser.log (autorizzazione negata) e il log del driver non viene scritto.

**Soluzione:**
 
1. Aggiungere hdiuser al gruppo Hadoop. 
2. Indicare 777 autorizzazioni in /var/log/spark dopo la creazione del cluster. 
3. Aggiornare il percorso del log Spark tramite Ambari in modo che corrisponda a una directory con 777 autorizzazioni.  
4. Eseguire spark-submit come sudo. 

##Vedere anche

- [Panoramica: Apache Spark in Azure HDInsight (Linux)](hdinsight-apache-spark-overview.md)
- [Introduzione: eseguire il provisioning di Apache Spark in Azure HDInsight (Linux) ed eseguire query interattive usando Spark SQL](hdinsight-apache-spark-jupyter-spark-sql.md)

<!---HONumber=AcomDC_0224_2016-->