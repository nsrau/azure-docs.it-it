<properties 
	pageTitle="Creazione e caricamento di dati nelle tabelle Hive dall'archiviazione BLOB di Azure | Azure" 
	description="Creazione di tabelle Hive e caricamento dei dati in BLOB nelle tabelle Hive" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="hangzh;bradsev" />

 
#Creazione e caricamento di dati nelle tabelle Hive dall'archiviazione BLOB di Azure
 
In questo documento vengono presentate le query Hive generiche che consentono di creare tabelle Hive e di caricare i dati dall'archiviazione BLOB di Azure. Queste query Hive vengono condivise nell'archivio GitHub. [Archivio Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql). 

Se si crea una macchina virtuale di Azure seguendo le istruzioni riportate in "Configurazione di una macchina virtuale di Azure con IPython Notebook Server", questo file di script è stato scaricato nella directory  `C:\Users<user name>\Documents\Data Science Scripts` nella macchina virtuale. È necessario collegare uno schema di dati personalizzato e la configurazione dell'archiviazione BLOB nei campi corrispondenti e queste query Hive dovrebbero essere pronte per l'invio. 

Si supponga che i dati delle tabelle Hive siano in un formato tabulare **non compresso** e che i dati siano stati caricati nel contenitore predefinito o aggiuntivo dell'account di archiviazione utilizzato dal cluster Hadoop. Se gli utenti vogliono fare pratica con i _dati di viaggio dei taxi di NYC_, devono prima [scaricare tutti i 24 file](http://www.andresmh.com/nyctaxitrips/) (12 file sui viaggi e 12 file sulle tariffe), **decomprimere** tutti i file in file .csv e caricarli nel contenitore predefinito o aggiuntivo dell'account di archiviazione di Azure utilizzato quando il [cluster Hadoop di Azure HDInsight è stato personalizzato](machine-learning-data-science-customize-hadoop-cluster.html). 

Le query Hive possono essere inviate nella riga di comando di Hadoop nel nodo head del cluster Hadoop. È necessario:

1. [Abilitare l'accesso remoto al nodo head del cluster Hadoop e accedere al nodo head](machine-learning-data-science-customize-hadoop-cluster.md).
2. [Inviare le query Hive nella riga di comando di Hadoop nel nodo head](machine-learning-data-science-hive-queries.md).

Gli utenti possono inoltre utilizzare la [Console Query (Editor Hive)] immettendo l'URL in un browser Web "https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor (verrà richiesto di immettere le credenziali del cluster Hadoop per accedere) o possono [inviare processi Hive tramite PowerShell](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md). 

- [Passaggio 1: Creazione di database e tabelle Hive](#create-tables)
- [Passaggio 2: Caricamento dei dati nelle tabelle Hive](#load-data)
- [Argomenti avanzati: tabella partizionata e archiviazione dei dati Hive in formato ORC](#partition-orc)

## <a name="create-tables"></a>Creazione di database e tabelle Hive

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

Ecco le descrizioni dei campi che gli utenti devono collegare e altre configurazioni:

- `<database name>`: il nome del database che gli utenti desiderano creare. Se gli utenti desiderano utilizzare solo il database predefinito, la query di `creazione del database...` può essere omessa. 
- `<table name>`: il nome della tabella che gli utenti desiderano creare all'interno del database specificato. Se gli utenti desiderano utilizzare il database predefinito, è possibile fare direttamente riferimento alla tabella da `<table name>` senza `<database name>.`.
- `<field separator>`: il separatore che separa i campi nel file di dati da caricare nella tabella Hive. 
- `<line separator>`: il separatore che separa le righe nel file di dati. 
- `<storage location>`: il percorso di archiviazione di Azure in cui salvare i dati delle tabelle Hive. Se gli utenti non specificano `PERCORSO "<storage location>"`, per impostazione predefinita il database e le tabelle vengono archiviate nella directory `hive/warehouse/` nel contenitore predefinito del cluster Hive. Se un utente richiede di specificare il percorso di archiviazione,  il percorso di archiviazione deve rientrare nel contenitore predefinito relativo al database e alle tabelle. Questo percorso deve essere definito come percorso relativo per il contenitore predefinito del cluster nel formato `"wasb:///<directory 1>/"` o `"wasb:///<directory 1>/<directory 2>/"` e così via. Dopo l'esecuzione della query, le relative directory verranno create all'interno del contenitore predefinito. 
- `TBLPROPERTIES("skip.header.line.count"="1")`: Se il file di dati presenta una riga di intestazione gli utenti devono aggiungere questa proprietà alla **fine** della query  `create table`. In caso contrario, la riga di intestazione verrà caricata come un record nella tabella. Se il file di dati non presenta una riga di intestazione, questa configurazione può essere omessa nella query. 

## <a name="load-data"></a>Caricamento dei dati nelle tabelle Hive

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- `<path to blob data>`: Se il file BLOB da caricare nella tabella Hive si trova nel contenitore predefinito del cluster Hadoop di HDInsight, il `<path to blob data>` deve essere nel formato `"wasb:///<directory in this container>/<blob file name>"`. Il file BLOB può trovarsi inoltre nel contenitore aggiuntivo del cluster Hadoop di HDInsight. In questo caso, il `<path to blob data>` deve essere nel formato `"wasb://<container name>@<storage account name>.blob.windows.core.net/<nome file BLOB>'`.
>[AZURE.NOTE] I dati BLOB da caricare nella tabella Hive devono trovarsi nel contenitore predefinito o aggiuntivo dell'account di archiviazione del cluster Hadoop. In caso contrario, la query  `LOAD DATa` avrà esito negativo perché non può accedere ai dati. 


## <a name="partition-orc"></a>Argomenti avanzati: tabella partizionata e archiviazione dei dati Hive in formato ORC

Se i dati sono grandi, il partizionamento della tabella è utile per le query che devono solo eseguire l'analisi di alcune partizioni della tabella. Ad esempio,  è ragionevole partizionare i dati di log di un sito Web per date. 

Oltre al partizionamento della tabella, è inoltre utile archiviare i dati Hive in formato ORC. ORC è l'acronimo di "Optimized Row Columnar".  Per ulteriori dettali, fare riferimento a _"[L'utilizzo dei file ORC migliora le prestazioni quando Hive legge, scrive ed elabora dati](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles)"_.

### Tabella partizionata

Le query per la creazione di una tabella partizionata e per il caricamento dei dati sono le seguenti:

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
	(field1 string,
	...
	fieldN string
	)
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
		 lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
	LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name> 
		PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Quando si interrogano tabelle partizionate, si consiglia di aggiungere la condizione di partizione all'**inizio** della clausola  `where` per migliorare in modo significativo l'efficacia della ricerca. 

    select 
		field1, field2, ..., fieldN
	from <database name>.<partitioned table name> 
	where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Archiviazione dei dati Hive in formato ORC

Gli utenti non possono caricare direttamente i dati del BLOB nelle tabelle Hive nel formato di archiviazione ORC. Ecco i passaggi che gli utenti devono eseguire per caricare dati dai BLOB Azure nelle tabelle Hive archiviate in formato ORC. 

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

2. Creare una tabella interna con lo stesso schema della tabella esterna nel passaggio 1 e lo stesso delimitatore dei campi. Archiviare quindi i dati Hive nel formato ORC.

		CREATE TABLE IF NOT EXISTS <database name>.<ORC table name> 
		(
			field1 string,
			field2 int,
			...
			fieldN date
		) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

3. Selezionare i dati della tabella esterna nel passaggio 1 e inserirli nella tabella ORC.

		INSERT OVERWRITE TABLE <database name>.<ORC table name> SELECT * FROM <database name>.<external textfile table name>;

[AZURE.NOTE] Se la tabella TEXTFILE `<database name>.<external textfile table name>` presenta partizioni, al PASSAGGIO 3, `SELECT * FROM <database name>.<external textfile table name>` selezionerà la variabile della partizione come un campo nel set di dati restituito. Non sarà possibile completare l'inserimento in `<database name>.<ORC table name>` poiché `<database name>.<ORC table name>` non presenta la variabile della partizione come campo nello schema della tabella. In questo caso, gli utenti devono selezionare i campi da inserire in `<database name>.<ORC table nameC>` come indicato di seguito:

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		      SELECT field1, field2, ..., fieldN
		      FROM <database name>.<external textfile table name> 
		      WHERE <partition variable>=<partition value>;

4. Si consiglia di eliminare `<external textfile table name>` utilizzando la seguente query dopo che tutti i dati sono stati inseriti in `<database name>.<ORC table name>`:

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Ora abbiamo una tabella con i dati nel formato ORC pronta per l'utilizzo. 

<!--HONumber=49--> 