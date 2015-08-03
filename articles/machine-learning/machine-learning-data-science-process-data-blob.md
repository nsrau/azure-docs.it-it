<properties 
	pageTitle="Elaborare i dati BLOB di Azure con analisi avanzate | Microsoft Azure" 
	description="Elaborare dati nell'archivio BLOB di Azure." 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="msolhab" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="sunliangms;fashah;msolhab;garye;bradsev" />

#<a name="heading"></a>Elaborare i dati BLOB di Azure con analisi avanzate

In questo documento vengono descritte l'esplorazione dei dati e la creazione di funzionalità da dati archiviati nell’archivio BLOB di Azure. A tale scopo, i dati devono essere scaricati dall'origine BLOB in un file locale che può essere quindi caricato in un frame di dati Pandas per l'esplorazione e la modifica. Ecco i passaggi da seguire:

1. Scaricare i dati da BLOB Azure con il codice Python di esempio riportato di seguito utilizzando il servizio BLOB. Sostituire la variabile nel codice riportato di seguito con i valori specifici: 

	    from azure.storage import BlobService
    	import tables
    	
		STORAGEACCOUNTNAME= <storage_account_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		LOCALFILENAME= <local_file_name>		
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

    	#download from blob
    	t1=time.time()
    	blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    	blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    	t2=time.time()
    	print(("It takes %s seconds to download "+blobname) % (t2 - t1))


2. Leggere i dati in un frame di dati Pandas dal file scaricato.

	    #LOCALFILE is the file path	
    	dataframe_blobdata = pd.read_csv(LOCALFILE)

A questo punto si è pronti per esplorare i dati e generare le funzionalità di questo set di dati.

##<a name="blob-dataexploration"></a>Esplorazione dei dati

Di seguito sono riportati alcuni esempi dei modi per esplorare i dati utilizzando Pandas:

1. Controllare il numero di righe e colonne 

		print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape

2. Controllare le prime o le ultime righe nel set di dati come indicato di seguito:

		dataframe_blobdata.head(10)
		
		dataframe_blobdata.tail(10)

3. Controllare il tipo di dati importato in ogni colonna mediante il seguente codice di esempio
 	
		for col in dataframe_blobdata.columns:
		    print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype

4. Controllare le statistiche di base per le colonne nel set di dati come segue
 
		dataframe_blobdata.describe()
	
5. Esaminare il numero di voci per ogni valore della colonna come indicato di seguito

		dataframe_blobdata['<column_name>'].value_counts()

6. Contare i valori mancanti rispetto al numero effettivo di voci in ogni colonna utilizzando il seguente codice di esempio

		miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
		print miss_num
	 
7.	Se si dispongono di valori mancanti per una colonna specifica nei dati, è possibile eliminarli come indicato di seguito:

		dataframe_blobdata_noNA = dataframe_blobdata.dropna()
		dataframe_blobdata_noNA.shape

	È possibile sostituire i valori mancanti anche con la funzione modalità:
	
		dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})		

8. Creare un grafico istogramma utilizzando un numero variabile di contenitori per tracciare la distribuzione di una variabile
	
		dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
		
		np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
	
9. Esaminare le correlazioni tra le variabili utilizzando un grafico di dispersione o la funzione di correlazione incorporata

		#relationship between column_a and column_b using scatter plot
		plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
		
		#correlation between column_a and column_b
		dataframe_blobdata[['<column_a>', '<column_b>']].corr()
	
	
##<a name="blob-featuregen"></a>Creazione di funzionalità
	
È quindi possibile generare funzionalità tramite Python come indicato di seguito:

###<a name="blob-countfeature"></a>Valore dell'indicatore basato sulla creazione di funzionalità

Le funzionalità relative alle categorie possono essere create come indicato di seguito:

1. Controllare la distribuzione della colonna relativa alla categoria:
	
		dataframe_blobdata['<categorical_column>'].value_counts()

2. Generare i valori dell'indicatore per ognuno dei valori della colonna

		#generate the indicator column
		dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')

3. Unire la colonna indicatore con il frame di dati originale
 
			#Join the dummy variables back to the original data frame
			dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)

4. Rimuovere la variabile originale:

		#Remove the original column rate_code in df1_with_dummy
		dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
	
###<a name="blob-binningfeature"></a>Creazione di contenitori per la creazione di funzionalità

Per creare funzionalità in contenitori, procedere come indicato di seguito:

1. Aggiungere una sequenza di colonne per suddividere una colonna numerica
 
		bins = [0, 1, 2, 4, 10, 40]
		dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
		
2. Convertire la creazione di contenitori in una sequenza di variabili booleane

		dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
	
3. Infine, aggiungere di nuovo le variabili fittizie al frame di dati originale

		dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)	

##<a name="sql-featuregen"></a>Scrittura dei dati nel BLOB di Azure e utilizzo in Azure Machine Learning

Dopo avere esaminato i dati e creato le funzionalità necessarie, è possibile caricare i dati (campionati o completi) in un BLOB di Azure e utilizzarli in Azure Machine Learning attenendosi alla procedura seguente. Tenere presente che le funzionalità aggiuntive possono essere create anche in Azure Machine Learning Studio. 1. Scrivere il frame di dati in file locali

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Caricare i dati nel BLOB di Azure come indicato di seguito:

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

3. Ora i dati possono essere letti dal BLOB utilizzando il modulo [Lettore][reader] di Azure Machine Learning, come illustrato nella schermata riportata di seguito:
 
![lettore BLOB][1]

[1]: ./media/machine-learning-data-science-process-data-blob/reader_blob.png


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

<!---HONumber=July15_HO4-->