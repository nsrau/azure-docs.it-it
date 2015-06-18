<properties 
	pageTitle="Dati di esempio in SQL Server in Azure| Azure" 
	description="Dati di esempio in SQL Server in Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="fashah" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="fashah,garye" /> 

#<a name="heading"></a>Dati di esempio in SQL Server in Azure

In questo documento sono contenuti dati di esempio archiviati in SQL Server in Azure nei modi seguenti:

1. [Utilizzando SQL](#sql)
2. [Utilizzando il linguaggio di programmazione Python](#python) 

**Nota**
>Il codice SQL di esempio riportato in questo documento presuppone che i dati si trovino in un server SQL in Azure. In caso contrario, fare riferimento alla mappa relativa al processo di scienza dei dati cloud per ottenere indicazioni sul trasferimento di dati in un server SQL in Azure.

###<a name="SQL"></a>Utilizzo di SQL

In questa sezione vengono descritti alcuni metodi di utilizzo di SQL per eseguire il campionamento casuale semplice dei dati all'interno del database. Scegliere il metodo in base alla dimensione dei dati e alla loro distribuzione.

Gli elementi riportati di seguito mostrano come utilizzare il valore newId in SQL Server per effettuare il campionamento. Il metodo scelto dipende dal livello di casualità previsto per il campionamento da eseguire (il valore pk_id nel codice di esempio riportato in basso si presuppone che sia una chiave primaria generata automaticamente).

1. Campionamento casuale meno rigoroso

	    select  * from <table_name> where <primary_key> in 
    	(select top 10 percent <primary_key> from <table_name> order by newid())

2. Campionamento più casuale 

	    SELECT * FROM <table_name>
    	WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

È possibile utilizzare la clausola TABLESAMPLE per il campionamento come mostrato di seguito. Può trattarsi di un approccio più efficace se i dati sono di grandi dimensioni (presupponendo che i dati in pagine diverse non siano correlati) e per completare la query in un tempo ragionevole.

	SELECT *
	FROM <table_name> 
	TABLESAMPLE (10 PERCENT)

**Nota**
> È possibile esplorare e generare le funzionalità da questi dati campionati archiviandoli in una nuova tabella


####<a name="sql-aml"></a>Connessione ad Azure Machine Learning

È possibile utilizzare direttamente le query di esempio sopra menzionate nel modulo Reader di Azure ML per ricampionare i dati in tempo reale e inserirli in un esperimento di Azure ML. Di seguito viene riportata una schermata relativa all'uso del modulo Reader per leggere i dati campionati:
   
![reader sql][1]

###<a name="python"></a>Utilizzo del linguaggio di programmazione Python 

In questa sezione viene mostrato l'uso della libreria pyodbc per connettersi al database di un server SQL in Python. La stringa di connessione del database è la seguente (sostituire servername, dbname, username e password con la propria configurazione):

	#Set up the SQL Azure connection
	import pyodbc	
	conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La libreria [Pandas](http://pandas.pydata.org/) in Python fornisce una vasta gamma di strutture di dati e strumenti di analisi dei dati per la manipolazione dei dati nella programmazione in Python. Nel codice riportato di seguito si legge un campionamento dello 0,1% dei dati di una tabella nel database SQL di Azure in un frame di dati Pandas:

	import pandas as pd

	# Query database and load the returned results in pandas data frame
	data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

È ora possibile lavorare con i dati campionati nel frame di dati Pandas. 

####<a name="python-aml"></a>Connessione ad Azure Machine Learning

È possibile utilizzare il codice di esempio seguente per salvare i dati ricampionati in un file e caricarli in un BLOB di Azure. I dati del BLOB possono essere letti direttamente in un esperimento di Azure ML utilizzando il  *Reader Module*. Attenersi alla procedura seguente: 

1. Scrivere il frame di dati Pandas in un file locale

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Caricare il file locale nel BLOB di Azure

		from azure.storage import BlobService
    	import tables

		STORAGEACCOUNTNAME= <storage_account_name>
		LOCALFILENAME= <local_file_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

	    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
	    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
	    
	    try:
	   
	    #perform upload
	    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
	    
	    except:	        
		    print ("Something went wrong with uploading blob:"+BLOBNAME)

3. Leggere i dati nel BLOB di Azure utilizzando il  *Reader Module* di Azure ML come mostrato nella schermata riportata di seguito:
 
![reader blob][2]

### Analisi scientifica dei dati di Azure nell'esempio di azione

Per un esempio della procedura dettagliata end-to-end del processo di analisi scientifica dei dati di Azure utilizzando un set di dati pubblici, vedere [Processo di analisi scientifica dei dati di Azure in azione](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_blob.png


<!--HONumber=49--> 