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
	ms.date="12/22/2015" 
	ms.author="jgao"/>

# Problemi noti di Apache Spark in Azure HDInsight (Linux)

Questo documento elenca tutti i problemi noti relativi all'anteprima pubblica di Spark.

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

##L'avvio iniziale del notebook richiede più tempo del previsto 

**Sintomo:**

La prima istruzione nel notebook di Jupyter tramite Spark Magic potrebbe richiedere più di un minuto.

**Soluzione:**
 
Non sono disponibili soluzioni. A volte è necessario un minuto.

##Impossibile personalizzare le configurazioni principali/di memoria

**Sintomo:**
 
Non è possibile specificare le configurazioni principali/di memoria tranne il valore predefinito dai kernel Spark/Pyspark.

**Soluzione:**
 
La funzionalità verrà resa disponibile.

##Timeout del notebook di Jupyter durante la creazione della sessione

**Sintomo:**

Quando il cluster Spark esaurisce le risorse, si verificherà il timeout dei kernel Spark e Pyspark nel notebook di Jupyter quando si cerca di creare la sessione. Soluzioni:

1. Liberare alcune risorse nel cluster Spark nei modi seguenti:

    - Arrestare altri notebook di Spark selezionando il menu Close and Halt o facendo clic su Shutdown nel notebook.
    - Arrestare altre applicazioni Spark da YARN.

2. Riavviare il notebook che si stava cercando di avviare. Ora dovrebbero essere disponibili risorse sufficienti per creare una sessione.

##Problema di formattazione dei risultati di output del notebook

**Sintomo:**
 
La formattazione dei risultati di output del notebook non è corretta dopo l'esecuzione di una cella dai kernel di Jupyter Spark e Pyspark. Sono inclusi i risultati corretti delle esecuzioni delle celle, oltre alle tracce dello stack Spark o altri errori.

**Soluzione:**
 
Questo problema verrà risolto in una versione futura.

##Errori di digitazione nei notebook di esempio
 
- **Notebook Python 4 (analizzare i log con Spark usando una libreria personalizzata)**

    "Si supponga di copiarlo su wasb:///example/data/iislogparser.py" dovrebbe essere "Si supponga di copiarlo su wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py"".

- **Notebook Python 5 (Spark con Machine Learning: analisi predittiva dei dati del controllo degli alimenti tramite MLLib)**

    "Una visualizzazione rapida aiuta a riflettere sulla distribuzione di questi risultati" contiene codice non corretto che non verrà eseguito. Va modificato come segue:

        countResults = df.groupBy('results').count().withColumnRenamed('count', 'cnt').collect() 
        labels = [row.results for row in countResults] 
        sizes = [row.cnt for row in countResults] 
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral'] 
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors) plt.axis('equal') 
        
- **Notebook Python 5 (Spark con Machine Learning: analisi predittiva dei dati del controllo degli alimenti tramite MLLib)**

    Il commento finale afferma che il tasso di falsi negativi e il tasso di falsi positivi sono rispettivamente 12,6% e % 16,0. Questi numeri non sono corretti: eseguire il codice per visualizzare il grafico a torta con le percentuali corrette.

- **Notebook Python 6 e 7**

    La prima cella non riesce a registrare il metodo sc.stop() da chiamare quando viene chiuso il notebook. In alcuni casi questo potrebbe causare una perdita di risorse Spark. Per evitare che questo si verifichi, verificare di eseguire l'importazione di atexit; atexit.register(lambda: sc.stop()) in questi notebook prima di arrestarli. Se si perdono accidentalmente le risorse, seguire le istruzioni sopra riportate per terminare le applicazioni YARN perse.
     
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

<!---HONumber=AcomDC_1223_2015-->