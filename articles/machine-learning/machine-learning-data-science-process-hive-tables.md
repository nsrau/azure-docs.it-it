<properties
	pageTitle="Inviare query Hive ai cluster Hadoop in Advanced Analytics Process and Technology | Microsoft Azure"
	description="Elaborare i dati delle tabelle Hive usando query Hive."
	services="machine-learning"
	documentationCenter=""
	authors="hangzh-msft"
	manager="paulettm" 
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/29/2015"
	ms.author="hangzh;bradsev" />

#<a name="heading"></a> Inviare query Hive ai cluster Hadoop di HDInsight in Advanced Analytics Process and Technology 

Questo documento descrive le differenti modalità di invio delle query Hive ai cluster Hadoop gestiti dal servizio HDInsight in Azure. Questa attività fa parte di ADAPT (Advanced Analytics Process and Technology), un processo fornito da Azure Machine Learning. Il documento illustra inoltre due attività di gestione dei dati: creazione di funzionalità ed esplorazione dei dati. Descrive infine query Hive generiche che illustrano come esplorare i dati o creare funzionalità usando Hive in un cluster Hadoop del servizio HDInsight di Azure. Tali query Hive usano le funzioni definite dall'utente fornite.

Nell'<a href="https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts" target="_blank">archivio GitHub</a> sono inoltre disponibili alcuni esempi di query specifiche per gli scenari relativi ai dati dei <a href="http://chriswhong.com/open-data/foil_nyc_taxi/" target="_blank">tragitti dei taxi di NYC</a>. Tali query dispongono già di un determinato schema dei dati e possono essere inviate e usate immediatamente.

Nella parte finale del documento, vengono descritti i parametri che gli utenti possono impostare per migliorare le prestazioni delle query Hive.

## Prerequisiti
Questo articolo presuppone che l'utente abbia:

* Creato un account di archiviazione di Azure. Per istruzioni, vedere [Creare un account di archiviazione di Azure](../hdinsight-get-started.md#storage).
* Eseguito il provisioning di un cluster Hadoop personalizzato con il servizio HDInsight. Per istruzioni, vedere [Personalizzazione di cluster Hadoop di Azure HDInsight per l'analisi scientifica dei dati](machine-learning-data-science-customize-hadoop-cluster.md).
* Caricato i dati nelle tabelle Hive dei cluster Hadoop di Azure HDInsight. Se questa operazione non è stata effettuata, seguire le istruzioni riportate in [Creazione e caricamento di dati nelle tabelle Hive](machine-learning-data-science-move-hive-tables.md) per caricare innanzitutto i dati nelle tabelle Hive.
* Abilitato l'accesso remoto al cluster. Per istruzioni, vedere [Accesso al nodo head del cluster Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#headnode).


## <a name="submit"></a>Come inviare query Hive
È possibile inviare query Hive mediante:

* Riga di comando di Hadoop presente nel nodo head del cluster
* IPython Notebook
* Editor Hive
* Script di Azure PowerShell

Le query Hive sono simili a SQL. Gli utenti che conoscono SQL possono trovare utile il <a href="http://hortonworks.com/wp-content/uploads/downloads/2013/08/Hortonworks.CheatSheet.SQLtoHive.pdf" target="_blank">foglio informativo relativo all'uso di Hive per gli utenti di SQL</a>.

Quando si invia una query Hive, è possibile controllare la destinazione dell'output, sia questo visualizzato sullo schermo o incluso in un file locale nel nodo head o in un BLOB di Azure.

### Attraverso la console della riga di comando di Hadoop nel nodo head del cluster Hadoop

Se la query è complessa e si inviano le query Hive direttamente dal nodo head del cluster Hadoop, il completamento dell'operazione sarà più veloce rispetto a quando si inviano usando l'editor di Hive o gli script di Azure PowerShell.

Accedere al nodo head del cluster Hadoop, aprire la riga di comando presente nel desktop del nodo head e inserire il comando.

    cd %hive_home%\bin

Gli utenti possono inviare le query Hive nella console della riga di comando di Hadoop in tre modi:

* Direttamente dalla riga di comando di Hadoop
* Mediante i file con estensione hql
* Dalla console dei comandi di Hive

#### Inviare query Hive direttamente dalla riga di comando di Hive

Gli utenti possono eseguire comandi simili al seguente

	hive -e "<your hive query>;

per inviare query Hive semplici direttamente alla riga di comando di Hive. In questo esempio, la casella rossa evidenzia il comando che consente di inviare la query Hive, mentre quella verde evidenzia l'output della query.

![Creare un'area di lavoro][10]

#### Inviare query nei file con estensione hql

Quando la query Hive è più complessa e dispone di numerose righe, l'operazione di modifica delle query nella riga di comando di Hadoop o nella console dei comandi di Hive non è molto pratica. Pertanto, è possibile usare un editor di testo nel nodo head del cluster Hadoop e salvare le query Hive in un file con estensione hql da inserire nella directory locale del nodo head. Quindi la query Hive nel file con estensione hql può essere inviata usando l'argomento `-f` nel comando `hive` come segue:

	`hive -f "<path to the .hql file>"`


#### Eliminare la visualizzazione relativa allo stato di avanzamento delle query Hive sullo schermo

Per impostazione predefinita, dopo aver inviato una query Hive nella riga di comando di Hadoop, sullo schermo viene visualizzato lo stato di avanzamento del processo di mapping e di riduzione. Per eliminare la stampa della schermata di avanzamento del processo di mapping e riduzione, è possibile usare l'argomento `-S` (si applica la distinzione da maiuscole e minuscole) nella riga di comando nel modo seguente:

	hive -S -f "<path to the .hql file>"
	hive -S -e "<Hive queries>"

#### Inviare query Hive nella console dei comandi di Hive

Gli utenti possono anche immettere la console dei comandi di Hive eseguendo il comando `hive` nella riga di comando di Hadoop e quindi inviare query Hive dalla console dei comandi di Hive al prompt **hive>**. Di seguito è fornito un esempio.

![Creare un'area di lavoro][11]

In questo esempio, le due caselle rosse evidenziano i comandi usati per inserire la console dei comandi di Hive e la query Hive inviata nella console dei comandi. La casella verde evidenzia l'output dalla query Hive.

Negli esempi precedenti, i risultati della query Hive vengono visualizzati direttamente sullo schermo. Gli utenti possono anche salvare l'output su un file locale nel nodo head o in un BLOB di Azure. In seguito, gli utenti possono usare altri strumenti per analizzare ulteriormente l'output della query Hive.

#### Restituire i risultati delle query Hive in un file locale.

Per restituire i risultati delle query Hive in una directory locale nel nodo head, gli utenti devono inviare la query Hive nella riga di comando di Hadoop come indicato di seguito:

	`hive -e "<hive query>" > <local path in the head node>`


#### Restituire i risultati delle query Hive in un BLOB di Azure

Gli utenti possono anche restituire i risultati della query Hive in un BLOB di Azure, nel contenitore predefinito del cluster Hadoop. La query Hive che consente di eseguire questa operazione ha un aspetto analogo al seguente:

	insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

Nell'esempio seguente, l'output della query Hive viene scritto in una directory del BLOB `queryoutputdir` nel contenitore predefinito del cluster Hadoop. In questo caso, è necessario fornire solo il nome della directory e non anche quello del BLOB. Verrà generato un errore se si specificano i nomi della directory e del BLOB, ad esempio **wasb:///queryoutputdir/queryoutput.txt*.

![Creare un'area di lavoro][13]

L'output della query Hive può essere visualizzato nell'archiviazione BLOB, aprendo il contenitore predefinito del cluster Hadoop tramite lo strumento Esplora archivi Azure (o uno equivalente). È possibile applicare il filtro (evidenziato nella casella rossa) se si desidera recuperare soltanto un BLOB che contiene determinate lettere nei nomi.

![Creare un'area di lavoro][14]

### Attraverso i comandi dell'editor Hive o di Azure PowerShell

Gli utenti possono usare la Console di query (editor Hive) inserendo l'URL del modulo

nome cluster *https://&#60;Hadoop>.azurehdinsight.net/Home/HiveEditor*

in un Web browser. Si noti che verrà richiesto di immettere le credenziali del cluster Hadoop per l'accesso. In alternativa, [Inviare processi Hive tramite PowerShell](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell).


##<a name="hive-dataexploration"></a>Esplorazione dei dati
Di seguito sono riportati alcuni script Hive di esempio che possono essere usati per esplorare i dati nelle tabelle Hive.

1. Visualizzare il numero di osservazioni per partizione `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. Visualizzare il numero di osservazioni per giorno `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`

3. Visualizzare i livelli in una colonna di categoria `SELECT  distinct <column_name> from <databasename>.<tablename>`

4. Visualizzare il numero di livelli in una combinazione di due colonne di categoria `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. Visualizzare la distribuzione per colonne numeriche `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. Estrarre i record dall'unione di due tabelle

	    SELECT
			a.<common_columnname1> as <new_name1>,
			a.<common_columnname2> as <new_name2>,
    		a.<a_column_name1> as <new_name3>,
    		a.<a_column_name2> as <new_name4>,
    		b.<b_column_name1> as <new_name5>,
    		b.<b_column_name2> as <new_name6>
    	FROM
    		(
    		SELECT <common_columnname1>,
    			<common_columnname2>,
				<a_column_name1>,
				<a_column_name2>,
			FROM <databasename>.<tablename1>
			) a
			join
			(
			SELECT <common_columnname1>,
    			<common_columnname2>,
				<b_column_name1>,
				<b_column_name2>,
			FROM <databasename>.<tablename2>
			) b
			ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

##<a name="hive-featureengineering"></a>Creazione di funzionalità

In questa sezione viene descritto come creare funzionalità tramite le query Hive.

> [AZURE.NOTE]Le query Hive di esempio riportate presuppongono che i dati siano stati caricati nelle tabelle Hive dei cluster Hadoop di Azure HDInsight. Se questa operazione non è stata effettuata, seguire le istruzioni riportate in [Creazione e caricamento di dati nelle tabelle Hive](machine-learning-data-science-hive-tables.md) per caricare innanzitutto i dati nelle tabelle Hive.

Dopo aver creato le funzionalità aggiuntive, è possibile aggiungerle come colonne alla tabella esistente oppure creare una nuova tabella con le funzionalità aggiuntive e la chiave primaria, che quindi può essere unita alla tabella originale.

1. [Creazione di funzionalità basata sulla frequenza](#hive-frequencyfeature)
2. [Rischi di variabili di categoria nella classificazione binaria](#hive-riskfeature)
3. [Estrazione delle funzionalità dal campo Datetime](#hive-datefeatures)
4. [Estrazione delle funzionalità dal campo Text](#hive-textfeatures)
5. [Calcolo della distanza tra le coordinate GPS](#hive-gpsdistance)

###<a name="hive-frequencyfeature"></a>Creazione di funzionalità basata sulla frequenza

Spesso, può essere utile calcolare le frequenze relative ai livelli di una variabile di categoria o le frequenze di alcune combinazioni di livelli di più variabili di categoria. Per calcolare tali frequenze, gli utenti possono usare lo script seguente:

		select
			a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
		from
		(
			select
				<column_name1>,<column_name2>, count(*) as sub_count
			from <databasename>.<tablename> group by <column_name1>, <column_name2>
		)a
		order by frequency desc;


###<a name="hive-riskfeature"></a>Rischi di variabili di categoria nella classificazione binaria

Nella classificazione binaria, se i modelli usati accettano soltanto funzionalità numeriche, è necessario convertire le variabili di categoria non numeriche in funzionalità numeriche. È possibile eseguire questa operazione sostituendo tutti i livelli non numerici con un rischio numerico. In questa sezione, vengono descritte alcune query Hive generiche che consentono di calcolare i valori di rischio (disparità di log) di una variabile di categoria.


	    set smooth_param1=1;
	    set smooth_param2=20;
	    select
	    	<column_name1>,<column_name2>,
			ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
	    from
	    	(
	    	select
	    		<column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
	    	from
	    		(
	    		select
	    			<column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
	    		from <databasename>.<tablename>
	    		)a
	    	group by <column_name1>, <column_name2>
	    	)b

In questo esempio, le variabili `smooth_param1` e `smooth_param2` vengono impostate per arrotondare i valori di rischio calcolati dai dati. Rischi devono essere compresi in un intervallo di -Inf e Inf. Un rischio > 0 indica che la probabilità che la destinazione sia pari a 1 è maggiore di 0,5.

Dopo aver calcolato la tabella dei rischi, gli utenti possono assegnare i valori di rischio a una tabella unendola a quella dei rischi. La query Hive che viene aggiunta è stata descritta nella sezione precedente.

###<a name="hive-datefeatures"></a>Estrazione delle funzionalità dal campo Datetime

In Hive è disponibile un set di funzioni definite dall'utente per elaborare i campi Datetime. In Hive, il formato data/ora predefinito è "aaaa-MM-gg 00:00:00" (ad esempio, "1970-01-01 12:21:32"). In questa sezione, vengono riportati esempi che consentono di estrarre il giorno del mese, il mese da un campo Datetime e altri esempi che consentono di convertire una stringa data/ora in formato diverso rispetto a quello predefinito da una stringa data/ora con formato predefinito.

    	select day(<datetime field>), month(<datetime field>)
		from <databasename>.<tablename>;

Questa query Hive presuppone che *&#60;datetime field>* sia nel formato di data/ora predefinito.

Se un campo data/ora non è nel formato predefinito, è necessario convertire innanzitutto il campo Datetime in indicatore data/ora Unix e convertire quest'ultimo in una stringa data/ora nel formato predefinito. Quando la stringa data/ora è nel formato predefinito, è possibile applicare le funzioni data/ora incorporate e definite dall'utente al fine di estrarre le funzionalità.

		select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
		from <databasename>.<tablename>;

In questa query, se *&#60;datetime field>* ha il modello *03/26/2015 12:04:39*, *'&#60;pattern of the datetime field>'* deve essere `'MM/dd/yyyy HH:mm:ss'`. Per eseguire il test, gli utenti possono eseguire

		select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
		from hivesampletable limit 1;

In questa query, *hivesampletable* presenta tutti i cluster Hadoop di Azure HDInsight per impostazione predefinita quando viene effettuato il provisioning dei cluster.


###<a name="hive-textfeatures"></a>Estrazione delle funzionalità dal campo Text

Quando la tabella Hive dispone di un campo di testo con una stringa di parole delimitate da spazi, la seguente query consente di estrarre la lunghezza della stringa e il numero di parole contenuto.

    	select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
		from <databasename>.<tablename>;

###<a name="hive-gpsdistance"></a>Calcolo della distanza tra le coordinate GPS

La query descritta in questa sezione può essere applicata direttamente ai dati relativi ai tragitti dei taxi di NYC. Lo scopo della query è quello di dimostrare come applicare una funzione matematica incorporata in Hive, al fine di creare funzionalità.

I campi usati in questa query sono coordinate GPS relative ai luoghi in cui si sale e scende dal taxi, denominati *pickup\_longitude*, *pickup\_latitude*, *dropoff\_longitude* e *dropoff\_latitude*. Le query che consentono di calcolare la distanza diretta tra questi due punti sono:

		set R=3959;
		set pi=radians(180);
		select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
			${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
			*${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
			*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
			/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
			+cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
			pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
		from nyctaxi.trip
		where pickup_longitude between -90 and 0
		and pickup_latitude between 30 and 90
		and dropoff_longitude between -90 and 0
		and dropoff_latitude between 30 and 90
		limit 10;

Le equazioni matematiche per calcolare la distanza tra due coordinate GPS sono riportate nel sito <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Movable Type Scripts</a> creato da Peter Lapisu. In JavaScript la funzione `toRad()` è semplicemente *lat\_or\_lon*pi/180* e consente di convertire i gradi in radianti. Qui *lat\_or\_lon* rappresenta la latitudine o la longitudine. Dal momento che Hive non fornisce la funzione `atan2`, ma `atan`, la funzione `atan2` viene implementata dalla funzione `atan` nella query Hive precedente in base alla definizione fornita su <a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Creare un'area di lavoro][1]

Nella sezione **Funzionalità integrate** del <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Wiki su Hive di Apache</a> è disponibile un elenco completo relativo alle funzioni definite dall'utente e incorporate di Hive.

## <a name="tuning"></a> Argomento avanzato: Ottimizzare i parametri Hive per migliorare la velocità delle query

Le impostazioni predefinite per i parametri del cluster Hive potrebbero non essere adatte alle query Hive e ai dati elaborati dalle query. In questa sezione, vengono illustrati alcuni parametri che gli utenti possono regolare per migliorare le prestazioni delle query Hive. Gli utenti devono aggiungere la query di regolazione del parametro prima della query di elaborazione dei dati.

1. **Spazio dell'heap di Java**: per le query che comportano l'unione di set di dati di grandi dimensioni o l'elaborazione di record estesi, un errore comune è quello di **esaurire lo spazio dell'heap**. Questo può essere ottimizzato mediante l'impostazione di parametri *mapreduce.map.java.opts* e *mapreduce.task.io.sort.mb* sui valori desiderati. Di seguito è fornito un esempio:

		set mapreduce.map.java.opts=-Xmx4096m;
		set mapreduce.task.io.sort.mb=-Xmx1024m;


	Questo parametro consente di allocare 4 GB di memoria nello spazio dell'heap di Java e facilita l'ordinamento poiché rende disponibile maggiore quantità di memoria. Si consiglia di regolare queste allocazioni se si verificano errori di processo relativi allo spazio dell'heap.

2. **Dimensione di blocco per DFS**: questo parametro consente di impostare la quantità minima relativa all'unità di dati che il file system è in grado di memorizzare. Ad esempio, se la dimensione di blocco per DFS è di 128 MB, tutti i dati con dimensione inferiore o pari a 128 MB vengono memorizzati in un blocco singolo; al contrario, ai dati con dimensione superiore a 128 MB vengono assegnati ulteriori blocchi. Se si sceglie una dimensione di blocco molto limitata si causano sovraccarichi in Hadoop, dal momento che il nodo del nome deve elaborare molte più richieste al fine di rilevare il blocco che fa riferimento al file. Un'impostazione consigliata per dati con dimensioni in gigabyte (o più grandi) è:

		set dfs.block.size=128m;

3. **Ottimizzazione delle operazioni di unione in Hive**: anche se le operazioni nel framework di mapping/riduzione avvengono solitamente nella fase di riduzione, è possibile talvolta ottenere dei miglioramenti pianificando le operazioni di unione nella fase di mapping (detta anche "mapjoin"). Per indirizzare Hive a tale scopo, quando possibile, impostare:

		set hive.auto.convert.join=true;

4. **Specifica del numero di mapper in Hive**: mentre Hadoop consente all'utente di impostare il numero di riduttori, il numero di mapper generalmente non viene impostato dall'utente. Un espediente che offre un certo livello di controllo su questo numero consiste nello scegliere le variabili di Hadoop, *mapred.min.split.size* e *mapred.max.split.size* come dimensione di ogni attività di mapping determinata da:

		num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

	In genere, il valore predefinito di *mapred.min.split.size* è 0, quello di *mapred.max.split.size* è **Long.MAX** e quello di *dfs.block.size* è 64 MB. Come è evidente, stabilita la dimensione dei dati, regolare i parametri "impostandoli" consente all'utente di scegliere il numero di mapper da usare.

5. Di seguito, sono riportate altre **opzioni avanzate** che consentono di ottimizzare le prestazioni di Hive. Tali opzioni consentono di impostare la quantità di memoria allocata per le attività di mapping e di riduzione e possono essere utili nella modifica delle prestazioni. Tenere presente che *mapreduce.reduce.memory.mb* non può essere superiore alla dimensione della memoria fisica di ogni nodo di lavoro presente nel cluster Hadoop.

		set mapreduce.map.memory.mb = 2048;
		set mapreduce.reduce.memory.mb=6144;
		set mapreduce.reduce.java.opts=-Xmx8192m;
		set mapred.reduce.tasks=128;
		set mapred.tasktracker.reduce.tasks.maximum=128;

[1]: ./media/machine-learning-data-science-process-hive-tables/atan2new.png
[10]: ./media/machine-learning-data-science-process-hive-tables/run-hive-queries-1.png
[11]: ./media/machine-learning-data-science-process-hive-tables/run-hive-queries-2.png
[12]: ./media/machine-learning-data-science-process-hive-tables/output-hive-results-1.png
[13]: ./media/machine-learning-data-science-process-hive-tables/output-hive-results-2.png
[14]: ./media/machine-learning-data-science-process-hive-tables/output-hive-results-3.png
[15]: ./media/machine-learning-data-science-process-hive-tables/run-hive-queries-3.png
 

<!---HONumber=August15_HO6-->