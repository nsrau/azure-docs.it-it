<properties 
	pageTitle="Creare e caricare dati nelle tabelle Hive dall'archiviazione BLOB | Microsoft Azure" 
	description="Creare tabelle Hive e caricare dati in BLOB nelle tabelle Hive" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun" />  

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/14/2016" 
	ms.author="hangzh;bradsev" />

 
#Creare e caricare dati nelle tabelle Hive dall'archiviazione BLOB di Azure

Questo argomento presenta le query Hive generiche che consentono di creare tabelle Hive e di caricare i dati dall'archiviazione BLOB di Azure. Vengono inoltre fornite alcune linee guida sul partizionamento delle tabelle Hive e sull'uso della formattazione ORC per migliorare le prestazioni delle query.

Questo **menu** si collega ad argomenti che descrivono come inserire dati in ambienti di destinazione in cui i dati possono essere archiviati ed elaborati durante il Processo di analisi scientifica dei dati per i team (TDSP).

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


## Prerequisiti
Questo articolo presuppone che l'utente abbia:
 
* Creato un account di archiviazione di Azure. Per istruzioni, vedere [Creare un account di archiviazione di Azure](../hdinsight-get-started.md#storage).
* Eseguito il provisioning di un cluster Hadoop personalizzato con il servizio HDInsight. Per istruzioni, vedere [Personalizzazione di cluster Hadoop di Azure HDInsight per l'analisi scientifica dei dati](machine-learning-data-science-customize-hadoop-cluster.md).
* Abilitato l'accesso remoto al cluster, eseguito l'accesso e aperto la console della riga di comando di Hadoop. Per istruzioni, vedere [Accesso al nodo head del cluster Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#headnode).

## Caricare dati nell'archivio BLOB di Azure
Se si crea una macchina virtuale di Azure seguendo le istruzioni fornite nell'articolo [Configurare una macchina virtuale di Azure per l'analisi avanzata](machine-learning-data-science-setup-virtual-machine.md), questo file di script deve essere scaricato nella directory *C:\\Users<nome utente>\\Documents\\Data Science Scripts* della macchina virtuale. Affinché le query Hive siano pronte per l'invio, è necessario solo collegare il proprio schema dei dati e la configurazione dell'archiviazione BLOB di Azure ai campi appropriati.

Si supponga che i dati delle tabelle Hive siano in un formato tabulare **non compresso** e che i dati siano stati caricati nel contenitore predefinito (o in uno aggiuntivo) dell'account di archiviazione usato dal cluster Hadoop.

Se gli utenti vogliono fare pratica con i _dati di viaggio dei taxi di NYC_, devono prima scaricare i 24 file relativi ai <a href="http://www.andresmh.com/nyctaxitrips/" target="_blank">dati di viaggio dei taxi di NYC</a> (12 file sui viaggi e 12 file sulle tariffe), **decomprimere** tutti i file in file con estensione csv, quindi caricarli nel contenitore predefinito o appropriato dell'account di archiviazione di Azure usato dalla procedura descritta nell'argomento [Personalizzare i cluster Hadoop di Azure HDInsight per l'analisi scientifica dei dati](machine-learning-data-science-customize-hadoop-cluster.md). Il processo per caricare i file con estensione csv per il contenitore predefinito nell'account di archiviazione sono disponibili in questa [pagina](machine-learning-data-science-process-hive-walkthrough.md#upload).


## <a name="submit"></a>Come inviare query Hive

È possibile inviare query Hive mediante:

1. [Inviare le query Hive attraverso la riga di comando di Hadoop nel nodo head del cluster Hadoop](#headnode)
2. [Inviare le query Hive con l'editor Hive](#hive-editor)
3. [Inviare le query Hive con i comandi di Azure PowerShell](#ps)
 
Le query Hive sono simili a SQL. Gli utenti che conoscono SQL possono trovare utile il [foglio informativo relativo all'uso di Hive per gli utenti di SQL](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf).

Quando si invia una query Hive, è possibile controllare la destinazione dell'output, sia questo visualizzato sullo schermo o incluso in un file locale nel nodo head o in un BLOB di Azure.


###<a name="headnode"></a> 1. Inviare le query Hive attraverso la riga di comando di Hadoop nel nodo head del cluster Hadoop

Se la query Hive è complessa e la si invia direttamente al nodo head del cluster Hadoop, il completamento dell'operazione sarà più rapido rispetto a quando l'invio viene effettuato con l'editor Hive o gli script di Azure PowerShell.

Accedere al nodo head del cluster Hadoop, aprire la riga di comando di Hadoop sul desktop del nodo head e immettere il comando `cd %hive_home%\bin`.

Gli utenti dispongono di tre modi per inviare query Hive nella riga di comando di Hadoop:

* Direttamente
* Mediante i file con estensione hql
* Con la console dei comandi di Hive

#### Inviare query Hive direttamente nella riga di comando di Hadoop 

Gli utenti possono eseguire comandi come `hive -e "<your hive query>;` per inviare query Hive semplici direttamente nella riga di comando di Hadoop. In questo esempio, la casella rossa evidenzia il comando che consente di inviare la query Hive, mentre quella verde evidenzia l'output della query.

![Creare un'area di lavoro](./media/machine-learning-data-science-move-hive-tables/run-hive-queries-1.png)

#### Inviare query nei file con estensione hql

Quando la query Hive è più complicata e presenta più righe, modificare le query nella riga di comando o nella console dei comandi di Hive non è pratico. Un'alternativa consiste nell'usare un editor di testo nel nodo head del cluster Hadoop per salvare le query Hive in un file con estensione hql in una directory locale del nodo head. Quindi la query Hive nel file con estensione hql può essere inviata usando l'argomento `-f` nel modo seguente:
	
	hive -f "<path to the .hql file>"

![Creare un'area di lavoro](./media/machine-learning-data-science-move-hive-tables/run-hive-queries-3.png)


**Eliminare la visualizzazione relativa allo stato di avanzamento delle query Hive sullo schermo**

Per impostazione predefinita, dopo l'invio della query Hive nella riga di comando di Hadoop, lo stato di avanzamento del processo di mapping e riduzione verrà stampato sullo schermo. Per eliminare la stampa della schermata di avanzamento del processo di mapping e riduzione, è possibile usare un argomento `-S` ("S" in lettere maiuscole) nella riga di comando nel modo seguente:

	hive -S -f "<path to the .hql file>"
	hive -S -e "<Hive queries>"

#### Inviare query Hive nella console dei comandi di Hive

Gli utenti possono innanzitutto immettere la console dei comandi di Hive eseguendo il comando `hive` nella riga di comando di Hadoop, quindi inviare query Hive nella console dei comandi di Hive. Di seguito è fornito un esempio. In questo esempio, le due caselle rosse evidenziano i comandi usati per inserire la console dei comandi di Hive e la query Hive inviata nella console dei comandi. La casella verde evidenzia l'output dalla query Hive.

![Creare un'area di lavoro](./media/machine-learning-data-science-move-hive-tables/run-hive-queries-2.png)

Negli esempi precedenti, i risultati della query Hive vengono visualizzati direttamente sullo schermo. Gli utenti possono anche salvare l'output su un file locale nel nodo head o in un BLOB di Azure. Quindi, gli utenti possono usare altri strumenti per analizzare ulteriormente l'output delle query Hive.

**Restituire i risultati delle query Hive in un file locale.**

Per restituire i risultati delle query Hive in una directory locale nel nodo head, gli utenti devono inviare la query Hive nella riga di comando di Hadoop come indicato di seguito:

	hive -e "<hive query>" > <local path in the head node>

Nell'esempio seguente, l'output della query Hive viene scritto in un file `hivequeryoutput.txt` all'interno della directory `C:\apps\temp`.

![Creare un'area di lavoro](./media/machine-learning-data-science-move-hive-tables/output-hive-results-1.png)

**Restituire i risultati delle query Hive in un BLOB di Azure**

Gli utenti possono anche restituire i risultati della query Hive in un BLOB di Azure, nel contenitore predefinito del cluster Hadoop. La query Hive deve avere l'aspetto seguente:

	insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

Nell'esempio seguente, l'output della query Hive viene scritto in una directory del BLOB `queryoutputdir` nel contenitore predefinito del cluster Hadoop. In questo caso, è sufficiente fornire il nome della directory, senza il nome del BLOB. Verrà generato un errore se si specificano i nomi della directory e del BLOB, ad esempio `wasb:///queryoutputdir/queryoutput.txt`.

![Creare un'area di lavoro](./media/machine-learning-data-science-move-hive-tables/output-hive-results-2.png)

Se si apre il contenitore predefinito del cluster Hadoop usando strumenti come Esplora archivi di Azure, verrà visualizzato l'output della query Hive come indicato di seguito. È possibile applicare il filtro (evidenziato da una casella rossa) per recuperare solo il BLOB con le lettere specificate nei nomi.

![Creare un'area di lavoro](./media/machine-learning-data-science-move-hive-tables/output-hive-results-3.png)

###<a name="hive-editor"></a> 2. Inviare le query Hive con l'editor Hive

Gli utenti possono usare la Console di query (editor Hive) inserendo l'URL del modulo

nome cluster *https://&#60;Hadoop>.azurehdinsight.net/Home/HiveEditor*

in un Web browser. Si noti che verrà richiesto di immettere le credenziali del cluster Hadoop per l'accesso.

###<a name="ps"></a> 3. Inviare le query Hive con i comandi di Azure PowerShell

Gli utenti possono usare anche PowerShell per inviare le query Hive. Per istruzioni, vedere [Invio di processi Hive tramite PowerShell](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell).


## <a name="create-tables"></a>Creare il database e le tabelle Hive

Le query Hive vengono condivise nell'[archivio GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) e possono essere scaricate da questa posizione.

Di seguito è presentata la query Hive che crea una tabella Hive.

    create database if not exists <database name>;
	CREATE EXTERNAL TABLE if not exists <database name>.<table name>
	(
		field1 string, 
		field2 int, 
		field3 float, 
		field4 double, 
		...,
		fieldN string
	) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>' 
	STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Di seguito sono presentate le descrizioni dei campi che gli utenti devono collegare e altre configurazioni:

- **&#60;database name>**: nome del database che gli utenti desiderano creare. Se gli utenti desiderano usare solo il database predefinito, la query di *creazione del database* può essere omessa.
- **&#60;table name>**: nome della tabella che gli utenti desiderano creare nel database specificato. Se gli utenti desiderano usare il database predefinito, è possibile fare direttamente riferimento alla tabella da *&#60;table name>* senza &#60;database name>.
- **&#60;field separator>**: separatore che delimita i campi nel file di dati da caricare nella tabella Hive.
- **&#60;line separator>**: separatore che delimita le righe nel file di dati.
- **&#60;storage location>**: percorso di archiviazione di Azure in cui salvare i dati delle tabelle Hive. Se gli utenti non specificano un *PERCORSO &#60;storage location>*, il database e le tabelle vengono archiviate per impostazione predefinita nella directory *hive/warehouse/* nel contenitore predefinito del cluster Hive. Se un utente vuole specificare il percorso di archiviazione, questo deve trovarsi nel contenitore predefinito per database e tabelle. Questo percorso deve essere definito come percorso relativo per il contenitore predefinito del cluster nel formato *'wasb:///&#60;directory 1>/'* o *'wasb:///&#60;directory 1>/&#60;directory 2>/'* e così via. Dopo l'esecuzione della query, verranno create le relative directory nel contenitore predefinito.
- **TBLPROPERTIES("skip.header.line.count"="1")**: se il file di dati presenta una riga di intestazione, gli utenti devono aggiungere questa proprietà **alla fine** della query di *creazione della tabella*. In caso contrario, la riga di intestazione verrà caricata come un record nella tabella. Se il file di dati non presenta una riga di intestazione, questa configurazione può essere omessa nella query.

## <a name="load-data"></a>Caricare dati nelle tabelle Hive
Di seguito è presentata la query Hive che carica i dati in una tabella Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- **&#60;path to blob data>**: se il file BLOB da caricare nella tabella Hive si trova nel contenitore predefinito del cluster Hadoop di HDInsight, *&#60;path to blob data>* deve essere nel formato *'wasb:///&#60;directory in this container>/&#60;blob file name>'*. Il file BLOB può trovarsi inoltre in un contenitore aggiuntivo del cluster Hadoop di HDInsight. In questo caso, *&#60;path to blob data>* deve essere nel formato *'wasb://&#60;container name>@&#60;storage account name>.blob.core.windows.net/&#60;blob file name>'*.

	>[AZURE.NOTE] I dati BLOB da caricare nella tabella Hive devono trovarsi nel contenitore predefinito o aggiuntivo dell'account di archiviazione del cluster Hadoop. In caso contrario, la query di *CARICAMENTO DEI DATI* avrà esito negativo perché non può accedere ai dati.


## <a name="partition-orc"></a>Argomenti avanzati: Tabella partizionata e archiviazione dei dati Hive in formato ORC

Se i dati sono di grandi dimensioni, il partizionamento della tabella è utile per le query che devono solo eseguire l'analisi di alcune partizioni della tabella. Ad esempio, è ragionevole partizionare i dati di log di un sito Web per date.

Oltre al partizionamento delle tabelle Hive, è inoltre utile archiviare i dati Hive in formato ORC. Per altre informazioni sulla formattazione ORC, vedere l’<a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">uso dei file ORC per migliorare le prestazioni quando Hive legge, scrive ed elabora dati</a>.

### Tabella partizionata
Di seguito è presentata la query Hive che crea una tabella partizionata e vi carica i dati.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
	(field1 string,
	...
	fieldN string
	)
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
		 lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
	LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name> 
		PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Quando si interrogano tabelle partizionate, è opportuno aggiungere la condizione di partizione all'**inizio** della clausola `where` in modo da migliorare sensibilmente l'efficacia della ricerca.

    select 
		field1, field2, ..., fieldN
	from <database name>.<partitioned table name> 
	where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Archiviare i dati Hive in formato ORC

Gli utenti non possono caricare direttamente i dati del BLOB nelle tabelle Hive nel formato di archiviazione ORC. Di seguito sono presentati i passaggi che gli utenti devono eseguire per caricare dati dai BLOB Azure nelle tabelle Hive archiviate in formato ORC.

1. Creare una tabella esterna **ARCHIVIATA COME TEXTFILE** e caricare i dati dall'archiviazione BLOB nella tabella.

		CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
		(
			field1 string,
			field2 int,
			...
			fieldN date
		)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' 
			lines terminated by '<line separator>' STORED AS TEXTFILE 
			LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

		LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

2. Creare una tabella interna con lo stesso schema della tabella esterna nel passaggio 1, lo stesso delimitatore dei campi e archiviare i dati Hive in formato ORC.

		CREATE TABLE IF NOT EXISTS <database name>.<ORC table name> 
		(
			field1 string,
			field2 int,
			...
			fieldN date
		) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

3. Selezionare i dati della tabella esterna nel passaggio 1 e inserirli nella tabella ORC.

		INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

	>[AZURE.NOTE] Se la tabella TEXTFILE *&#60;database name>.&#60;external textfile table name>* presenta partizioni, al PASSAGGIO 3, il comando `SELECT * FROM <database name>.<external textfile table name>` selezionerà la variabile della partizione come un campo nel set di dati restituito. L'inserimento di tale tabella in *&#60;database name>.&#60;ORC table name>* avrà esito negativo poiché *&#60;database name>.&#60;ORC table name>* non dispone della variabile della partizione come un campo nello schema della tabella. In questo caso, gli utenti devono selezionare i campi da inserire in *&#60;database name>.&#60;ORC table name>* come indicato di seguito:

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		   SELECT field1, field2, ..., fieldN
		   FROM <database name>.<external textfile table name> 
		   WHERE <partition variable>=<partition value>;

4. È opportuno eliminare *&#60;external textfile table name>* quando si usa la query seguente dopo che tutti i dati sono stati inseriti in *&#60;database name>.&#60;ORC table name>*:

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Al termine della procedura, si disporrà di una tabella con i dati nel formato ORC pronta per l'uso.

<!---HONumber=AcomDC_0914_2016-->