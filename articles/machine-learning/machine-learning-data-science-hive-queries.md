<properties 
	pageTitle="Inviare query Hive ai cluster Hadoop nel Cortana Analytics Process | Microsoft Azure" 
	description="Elaborazione dati tabelle Hive" 
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
	ms.date="10/18/2015" 
	ms.author="hangzh;bradsev" />

#<a name="heading"></a> Inviare query Hive ai cluster Hadoop di HDInsight nel Cortana Analytics Process

Questo documento descrive le differenti modalità di invio delle query Hive ai cluster Hadoop gestiti dal servizio HDInsight in Azure. È possibile inviare query Hive mediante:

* Riga di comando di Hadoop presente nel nodo head del cluster
* IPython Notebook 
* Editor Hive
* Script di Azure PowerShell 

Vengono fornite query Hive generiche che possono essere usate per esplorare i dati o per generare funzionalità che usano le funzioni definite dall'utente di Hive (UDF).

Esempi di query specifiche per scenari relativi ai [dati delle corse dei taxi di NYC](http://chriswhong.com/open-data/foil_nyc_taxi/) vengono inoltre forniti nell'[archivio GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Tali query dispongono già di un determinato schema dei dati e possono essere inviate e usate immediatamente per questo scenario.

Nella parte finale del documento, vengono descritti i parametri che gli utenti possono impostare per migliorare le prestazioni delle query Hive.

## Prerequisiti
Questo articolo presuppone che l'utente abbia:
 
* Creato un account di archiviazione di Azure. Per istruzioni per questa attività, vedere [Creare un account di archiviazione di Azure](../hdinsight-get-started.md#storage). 
* Eseguito il provisioning di un cluster Hadoop con il servizio HDInsight. Per istruzioni, vedere [Provisioning di un cluster HDInsight](../hdinsight-get-started.md#provision).
* Caricati i dati nelle tabelle Hive nei cluster Hadoop di Azure HDInsight. Se questa operazione non è stata effettuata, seguire le istruzioni riportate in [Creazione e caricamento di dati nelle tabelle Hive](machine-learning-data-science-hive-tables.md) per caricare prima di tutto i dati nelle tabelle Hive.
* Abilitato l'accesso remoto al cluster. Per istruzioni, vedere [Accesso al nodo head del cluster Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#remoteaccess). 


## <a name="submit"></a>Come inviare query Hive

1. [Inviare le query Hive attraverso la riga di comando di Hadoop nel nodo head del cluster Hadoop](#headnode)
2. [Inviare le query Hive con l'editor Hive](#hive-editor)
3. [Inviare le query Hive con i comandi di Azure PowerShell](#ps)
 
###<a name="headnode"></a> 1. Inviare le query Hive attraverso la riga di comando di Hadoop nel nodo head del cluster Hadoop

Se la query Hive è complessa e la si invia direttamente al nodo head del cluster Hadoop, il completamento dell'operazione sarà più rapido rispetto a quando l'invio viene effettuato con l'editor Hive o gli script di Azure PowerShell.

Accedere al nodo head del cluster Hadoop, aprire la riga di comando di Hadoop sul desktop del nodo head e immettere il comando `cd %hive_home%\bin`.

Gli utenti dispongono di tre modi per inviare query Hive nella riga di comando di Hadoop:

* Direttamente
* Mediante i file con estensione hql
* Con la console dei comandi di Hive

#### Inviare query Hive direttamente nella riga di comando di Hadoop 

Gli utenti possono eseguire comandi come `hive -e "<your hive query>;` per inviare query Hive semplici direttamente nella riga di comando di Hadoop. In questo esempio, la casella rossa evidenzia il comando che consente di inviare la query Hive, mentre quella verde evidenzia l'output della query.

![Creare un'area di lavoro][10]

#### Inviare query nei file con estensione hql

Quando la query Hive è più complicata e presenta più righe, modificare le query nella riga di comando o nella console dei comandi di Hive non è pratico. Un'alternativa consiste nell'usare un editor di testo nel nodo head del cluster Hadoop per salvare le query Hive in un file con estensione hql in una directory locale del nodo head. Quindi la query Hive nel file con estensione hql può essere inviata usando l'argomento `-f` nel modo seguente:
	
	`hive -f "<path to the .hql file>"`

![Creare un'area di lavoro][15]


**Eliminare la visualizzazione relativa allo stato di avanzamento delle query Hive sullo schermo**

Per impostazione predefinita, dopo l'invio della query Hive nella riga di comando di Hadoop, lo stato di avanzamento del processo di mapping e riduzione verrà stampato sullo schermo. Per eliminare la stampa della schermata di avanzamento del processo di mapping e riduzione, è possibile usare un argomento `-S` ("S" in lettere maiuscole) nella riga di comando nel modo seguente:

	hive -S -f "<path to the .hql file>"
	hive -S -e "<Hive queries>"

#### Inviare query Hive nella console dei comandi di Hive

Gli utenti possono innanzitutto immettere la console dei comandi di Hive eseguendo il comando `hive` nella riga di comando di Hadoop, quindi inviare query Hive nella console dei comandi di Hive. Di seguito è fornito un esempio. In questo esempio, le due caselle rosse evidenziano i comandi usati per inserire la console dei comandi di Hive e la query Hive inviata nella console dei comandi. La casella verde evidenzia l'output dalla query Hive.

![Creare un'area di lavoro][11]

Negli esempi precedenti, i risultati della query Hive vengono visualizzati direttamente sullo schermo. Gli utenti possono anche salvare l'output su un file locale nel nodo head o in un BLOB di Azure. Quindi, gli utenti possono usare altri strumenti per analizzare ulteriormente l'output delle query Hive.

**Restituire i risultati delle query Hive in un file locale.**

Per restituire i risultati delle query Hive in una directory locale nel nodo head, gli utenti devono inviare la query Hive nella riga di comando di Hadoop come indicato di seguito:

	`hive -e "<hive query>" > <local path in the head node>`

Nell'esempio seguente, l'output della query Hive viene scritto in un file `hivequeryoutput.txt` all'interno della directory `C:\apps\temp`.

![Creare un'area di lavoro][12]

**Restituire i risultati delle query Hive in un BLOB di Azure**

Gli utenti possono anche restituire i risultati della query Hive in un BLOB di Azure, nel contenitore predefinito del cluster Hadoop. La query Hive deve avere l'aspetto seguente:

	`insert overwrite directory wasb:///<directory within the default container> <select clause from ...>`

Nell'esempio seguente, l'output della query Hive viene scritto in una directory del BLOB `queryoutputdir` nel contenitore predefinito del cluster Hadoop. In questo caso, è sufficiente fornire il nome della directory, senza il nome del BLOB. Verrà generato un errore se si specificano i nomi della directory e del BLOB, ad esempio `wasb:///queryoutputdir/queryoutput.txt`.

![Creare un'area di lavoro][13]

Se si apre il contenitore predefinito del cluster Hadoop usando strumenti come Esplora archivi di Azure, verrà visualizzato l'output della query Hive come indicato di seguito. È possibile applicare il filtro (evidenziato da una casella rossa) per recuperare solo il BLOB con le lettere specificate nei nomi.

![Creare un'area di lavoro][14]

###<a name="hive-editor"></a> 2. Inviare le query Hive con l'editor Hive

Gli utenti possono inoltre usare la Console Query (Editor Hive) immettendo l'URL in un browser Web `https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor` (verrà richiesto di immettere le credenziali del cluster Hadoop per accedere).

###<a name="ps"></a> 3. Inviare le query Hive con i comandi di Azure PowerShell

Gli utenti possono usare anche PowerShell per inviare le query Hive. Per istruzioni, vedere [Invio di processi Hive tramite PowerShell](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell).

## <a name="explore"></a> Esplorazione dei dati, progettazione delle funzionalità e ottimizzazione dei parametri Hive

In questa sezione, vengono descritte le attività di gestione dei dati seguenti usando Hive nei cluster Hadoop in Azure HDInsight:

1. [Esplorazione dei dati](#hive-dataexploration)
2. [Creazione di funzionalità](#hive-featureengineering)

> [AZURE.NOTE]Le query Hive di esempio presuppongono che i dati siano stati caricati nelle tabelle Hive nei cluster Hadoop di Azure HDInsight. Se questa operazione non è stata effettuata, seguire le istruzioni riportate in [Creazione e caricamento di dati nelle tabelle Hive](machine-learning-data-science-hive-tables.md) per caricare innanzitutto i dati nelle tabelle Hive.

###<a name="hive-dataexploration"></a>Esplorazione dei dati
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

###<a name="hive-featureengineering"></a>Creazione di funzionalità

In questa sezione verranno descritti i modi per creare funzionalità tramite query Hive:
  
1. [Creazione di funzionalità basata sulla frequenza](#hive-frequencyfeature)
2. [Rischi di variabili di categoria nella classificazione binaria](#hive-riskfeature)
3. [Estrazione delle funzionalità dal campo Datetime](#hive-datefeature)
4. [Estrazione delle funzionalità dal campo Text](#hive-textfeature)
5. [Calcolo della distanza tra le coordinate GPS](#hive-gpsdistance)

> [AZURE.NOTE]Dopo avere creato le funzionalità aggiuntive, è possibile aggiungerle come colonne alla tabella esistente o creare una nuova tabella con le funzionalità aggiuntive e la chiave primaria, che possono quindi essere unite con la tabella originale.

####<a name="hive-frequencyfeature"></a> Creazione di funzionalità basata sulla frequenza

Talvolta è utile per calcolare le frequenze dei livelli di una variabile di categoria o le frequenze di combinazioni di livelli di più variabili di categoria. Gli utenti possono usare lo script seguente per calcolare le frequenze:

		select 
			a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
		from
		(
			select 
				<column_name1>,<column_name2>, count(*) as sub_count 
			from <databasename>.<tablename> group by <column_name1>, <column_name2>
		)a
		order by frequency desc;
	

####<a name="hive-riskfeature"></a> Rischi di variabili di categoria nella classificazione binaria

Nella classificazione binaria, a volte è necessario convertire le variabili di categoria non numeriche in funzionalità numeriche sostituendo i livelli non numerici con rischi numerici, poiché alcuni modelli potrebbero richiedere solo funzionalità numeriche. In questa sezione vengono illustrate alcune query Hive generiche per calcolare i valori di rischio (disparità di log) di una variabile di categoria.


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

In questo esempio, le variabili `smooth_param1` e `smooth_param2` vengono impostate per arrotondare i valori di rischio calcolati dai dati. I rischi sono compresi tra -Inf e Inf. Risks>0 rappresenta la probabilità che la destinazione uguale a 1 è maggiore di 0,5.

Dopo aver calcolato la tabella dei rischi, gli utenti possono assegnare i valori di rischio a una tabella unendola a quella dei rischi. Il join della query Hive è stato descritto nella sezione precedente.

####<a name="hive-datefeature"></a> Estrazione delle funzionalità dal campo Datetime

Hive presenta un set di UDF per l'elaborazione dei campi Data/ora. In Hive, il formato di data/ora predefinito è "aaaa-MM-gg 00:00:00" (ad esempio, "1970-01-01 12:21:32"). In questa sezione, sono riportati esempi di estrazione del giorno del mese e del mese da un campo Data/ora ed esempi di conversione di una stringa Data/ora in un formato diverso da quello predefinito in una stringa Data/ora in formato predefinito.

    	select day(<datetime field>), month(<datetime field>) 
		from <databasename>.<tablename>;

Questa query Hive presuppone che `<datetime field>` sia nel formato di data/ora predefinito.

Se un campo Data/ora non è nel formato predefinito, è necessario innanzitutto convertire il campo datetile in un timestamp Unix, quindi convertire il timestamp Unix in una stringa Data/ora nel formato predefinito. Dopo che la data/ora sono espresse nel formato predefinito, gli utenti possono applicare gli UDF di data/ora incorporate per estrarre funzionalità.

		select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
		from <databasename>.<tablename>;

In questa query , se `<datetime field>` dispone di un criterio quale `03/26/2015 12:04:39`, il campo `'<pattern of the datetime field>'` deve essere `'MM/dd/yyyy HH:mm:ss'`. Per eseguire il test, gli utenti possono eseguire

		select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
		from hivesampletable limit 1;

In questa query, `hivesampletable` presenta tutti i cluster Hadoop di Azure HDInsight per impostazione predefinita quando viene effettuato il provisioning dei cluster.


####<a name="hive-textfeature"></a> Estrazione delle funzionalità dal campo Text

Supponendo che la tabella Hive presenti un campo di testo che è una stringa di parole separate da spazi, la seguente query consente di estrarre la lunghezza della stringa e il numero di parole nella stringa.

    	select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num 
		from <databasename>.<tablename>;

####<a name="hive-gpsdistance"></a> Calcolo della distanza tra le coordinate GPS

La query descritta in questa sezione può essere applicata direttamente ai dati di viaggio dei taxi di NYC. Lo scopo di questa query è mostrare il modo in cui applicare le funzioni matematiche incorporare in Hive per creare funzionalità.

I campi usati in questa query sono coordinate GPS relative ai luoghi in cui si sale e scende dal taxi, denominati pickup\_longitude, pickup\_latitude, dropoff\_longitude e dropoff\_latitude. Le query per calcolare la distanza diretta tra le coordinate dei luoghi in cui si sale e scende sono:

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

Le equazioni matematiche per calcolare la distanza tra due coordinate GPS sono riportate nel sito [Movable Type Scripts](http://www.movable-type.co.uk/scripts/latlong.html) creato da Peter Lapisu. In JavaScript la funzione toRad() è semplicemente `lat_or_lon*pi/180` e consente di convertire i gradi in radianti. Qui `lat_or_lon` rappresenta la latitudine o la longitudine. Dal momento che Hive non fornisce la funzione `atan2`, ma `atan`, la funzione `atan2` viene implementata dalla funzione `atan` nella query Hive precedente in base alla definizione fornita su [Wikipedia](http://en.wikipedia.org/wiki/Atan2).

![Creare un'area di lavoro][1]

Un elenco completo di UDF di Hive incorporate è disponibile nel [Manuale sul linguaggio delle UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions).

## <a name="tuning"></a> Argomento avanzato: Ottimizzare i parametri Hive per migliorare la velocità delle query

Le impostazioni predefinite dei parametri del cluster Hive potrebbero non essere adatte per le query Hive e per i dati che le query elaborano. In questa sezione verranno esaminati alcuni parametri che gli utenti possono ottimizzare per migliorare le prestazioni delle query Hive. Gli utenti devono aggiungere la query di regolazione del parametro prima della query di elaborazione dei dati.

1. Spazio dell'heap di Java: nel caso di query che consentono di unire set di dati di grandi dimensioni o di elaborare record estesi, un errore comune è quello di **esaurire lo spazio dell'heap**. Quest'ultimo può essere regolato impostando i parametri `mapreduce.map.java.opts` e `mapreduce.task.io.sort.mb` sui valori desiderati. Di seguito è fornito un esempio:

		set mapreduce.map.java.opts=-Xmx4096m;
		set mapreduce.task.io.sort.mb=-Xmx1024m;
	

	Questo parametro consente di allocare 4 GB di memoria nello spazio dell'heap di Java e facilita l'ordinamento poiché rende disponibile maggiore quantità di memoria. È consigliabile provarlo per vedere se si verificano errori relativi alla non riuscita dei processi correlati allo spazio heap.

2. Dimensione di blocco per DFS: questo parametro consente di impostare la quantità minima relativa all'unità di dati che il file system è in grado di memorizzare. Ad esempio, se la dimensione di blocco per DFS è di 128 MB, tutti i dati con dimensione inferiore o pari a 128 MB vengono memorizzati in un blocco singolo; al contrario, ai dati con dimensione superiore a 128 MB vengono assegnati ulteriori blocchi. Se si sceglie una dimensione di blocco molto limitata si causano sovraccarichi in Hadoop, dal momento che il nodo del nome deve elaborare molte più richieste al fine di rilevare il blocco che fa riferimento al file. Un'impostazione consigliata per dati con dimensioni in gigabyte (o più grandi) è:

		set dfs.block.size=128m;

3. Ottimizzazione delle operazioni di join in Hive: mentre le operazioni di join nel framework di mapping e riduzione vengono generalmente eseguite in fase di riduzione, alcune volte, possono essere ottenuti enormi vantaggi pianificando i join nella fase di mapping (detti anche "mapjoin"). Per indirizzare Hive a tale scopo, quando possibile, impostare:

		set hive.auto.convert.join=true;

4. Specifica del numero di mapper in Hive: mentre Hadoop consente all'utente di impostare il numero di riduttori, il numero di mapper generalmente non viene impostato dall'utente. Un espediente che offre un certo livello di controllo su questo numero consiste nello scegliere le variabili di hadoop, mapred.min.split.size e mapred.max.split.size. Le dimensioni di ogni attività di mapping sono determinate da:

		num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

	Generalmente, il valore predefinito di mapred.min.split.size è 0, quello di mapred.max.split.size è Long.MAX e quello di dfs.block.size è 64 MB. Come è evidente, stabilita la dimensione dei dati, regolare i parametri "impostandoli" consente all'utente di scegliere il numero di mapper da usare.

5. Di seguito, sono riportate altre opzioni avanzate che consentono di ottimizzare le prestazioni di Hive. Consentono l'impostazione della memoria destinata al mapping e riducono le attività e possono essere utili per regolare le prestazioni. Tenere presente che `mapreduce.reduce.memory.mb` non può essere superiore alla dimensione della memoria fisica di ogni nodo di lavoro presente nel cluster Hadoop.

		set mapreduce.map.memory.mb = 2048;
		set mapreduce.reduce.memory.mb=6144;
		set mapreduce.reduce.java.opts=-Xmx8192m;
		set mapred.reduce.tasks=128;
		set mapred.tasktracker.reduce.tasks.maximum=128;

[1]: ./media/machine-learning-data-science-hive-queries/atan2new.png
[10]: ./media/machine-learning-data-science-hive-queries/run-hive-queries-1.png
[11]: ./media/machine-learning-data-science-hive-queries/run-hive-queries-2.png
[12]: ./media/machine-learning-data-science-hive-queries/output-hive-results-1.png
[13]: ./media/machine-learning-data-science-hive-queries/output-hive-results-2.png
[14]: ./media/machine-learning-data-science-hive-queries/output-hive-results-3.png
[15]: ./media/machine-learning-data-science-hive-queries/run-hive-queries-3.png


 

<!---HONumber=AcomDC_1217_2015-->