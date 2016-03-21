<properties 
	pageTitle="Creare funzionalità per i dati di archiviazione BLOB di Azure mediante Panda | Microsoft Azure" 
	description="Come creare funzionalità per i dati archiviati nel contenitore BLOB di Azure mediante il pacchetto Python di Panda." 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2016" 
	ms.author="fashah;garye;bradsev" />

#Creare funzionalità per i dati di archiviazione BLOB di Azure tramite Panda


##Introduzione

In questo documento viene descritto come creare funzionalità per i dati archiviati nel contenitore BLOB di Azure mediante il pacchetto Python [Pandas](http://pandas.pydata.org/). Una volta mostrato come caricare in un frame di dati Panda, viene illustrato come generare funzionalità relative alle categorie con i valori dell'indicatore e funzionalità per la creazione di contenitori, in entrambi i casi mediante gli script di Python.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]Questo **menu** fornisce collegamenti ad argomenti che descrivono come creare funzionalità per dati in diversi ambienti. Questa attività è un passaggio in [Cortana Analytics Process (CAP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## Prerequisiti
Questo articolo presuppone che l'utente abbia:

* È stato creato un account di archiviazione BLOB di Azure e vi sono stati archiviati i dati disponibili. Per istruzioni su come configurare un account, vedere [Creare un account di archiviazione di Azure](../hdinsight-get-started.md#storage).


## Caricare i dati in un intervallo di dati Pandas
Per esplorare e modificare un set di dati, i dati devono essere scaricati dall'origine BLOB in un file locale che può essere quindi caricato in un frame di dati Pandas. Ecco i passaggi da seguire per questa procedura:

1. Scaricare i dati da BLOB Azure con il codice Python di esempio riportato di seguito utilizzando il servizio BLOB. Sostituire la variabile nel codice riportato di seguito con i valori specifici: 

	    from azure.storage.blob import BlobService
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
	
##<a name="blob-featuregen"></a>Creazione di funzionalità
	
Le due sezioni successive illustrano come generare caratteristiche relative alle categorie con i valori dell'indicatore e caratteristiche per la creazione di contenitori mediante gli script di Python.

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

Dopo avere esaminato i dati e creato le funzionalità necessarie, è possibile caricare i dati (campionati o completi) in un BLOB di Azure e utilizzarli in Azure Machine Learning attenendosi alla procedura seguente. Tenere presente che le funzionalità aggiuntive possono essere create anche in Azure Machine Learning Studio.
1. Scrivere il frame di dati in file locali

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Caricare i dati nel BLOB di Azure come indicato di seguito:

		from azure.storage.blob import BlobService
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

3. Ora i dati possono essere letti dal BLOB utilizzando il modulo [Lettore](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) di Azure Machine Learning, come illustrato nella schermata riportata di seguito:
 
![lettore BLOB](./media/machine-learning-data-science-process-data-blob/reader_blob.png)


 

<!---HONumber=AcomDC_0309_2016-->