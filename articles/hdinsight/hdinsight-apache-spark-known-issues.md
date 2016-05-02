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
	ms.date="04/14/2016" 
	ms.author="nitinme"/>

# Problemi noti di Apache Spark su HDInsight Linux (anteprima)

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

### Non è possibile scaricare i notebook Jupyter nel formato ipynb

Se si esegue la versione più recente dei notebook Jupyter per HDInsight Spark e si tenta di scaricare una copia del notebook in formato **ipynb** dall'interfaccia utente del notebook Jupyter, si potrebbe visualizzare un errore interno del server.

**Soluzione:**

1.	Un metodo efficace è scaricare il notebook in un formato diverso da ipynb, ad esempio con estensione txt.  
2.	Se occorre il file ipynb, è possibile scaricarlo dal contenitore del cluster nell'account di archiviazione nel percorso **/HdiNotebooks**. Ciò vale solo per la versione più recente dei notebook Jupyter per HDInsight, che supporta i backup del notebook nell'account di archiviazione. Questo problema non si verifica con le versioni precedenti dei notebook Jupyter per HDInsight Spark.


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

### Il ripristino del checkpoint potrebbe non riuscire

È possibile creare i checkpoint nei notebook Jupyter per ripristinare una versione precedente del notebook. Se però lo stato corrente del notebook ha una query SQL con visualizzazione automatica, il ripristino di un checkpoint memorizzato in precedenza potrebbe provocare un errore.

##Vedere anche

- [Panoramica: Apache Spark in Azure HDInsight (Linux)](hdinsight-apache-spark-overview.md)
- [Introduzione: eseguire il provisioning di Apache Spark in Azure HDInsight (Linux) ed eseguire query interattive usando Spark SQL](hdinsight-apache-spark-jupyter-spark-sql.md)

<!---HONumber=AcomDC_0420_2016-->