---
title: Campionare i dati nell'archivio BLOB di Azure| Microsoft Docs
description: Dati di esempio nell'archivio BLOB di Azure
services: machine-learning,storage
documentationcenter: ''
author: bradsev
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: fashah;garye;bradsev

---
# <a name="heading"></a>Dati di esempio nell'archivio BLOB di Azure
In questo documento vengono descritti i dati di campionamento che è possibile memorizzare nell'archivio BLOB di Azure scaricandoli a livello di programmazione ed eseguendo il successivo campionamento tramite le procedure scritte in Python.

**Perché campionare i dati?** Se il set di dati da analizzare è grande, si consiglia di eseguire il downsampling dei dati per ridurli a una dimensione inferiore e più facilmente gestibile, ma comunque rappresentativa. Questa operazione facilita la comprensione e l'esplorazione dei dati, nonché la progettazione di funzionalità. Il suo ruolo nel Cortana Analytics Process consiste nell'abilitare la creazione relativa a prototipi di funzioni di elaborazione dei dati e di modelli per l'apprendimento automatico.

Il **menu** seguente collega ad argomenti che descrivono come campionare dati da vari ambienti di archiviazione.

[!INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Questo campionamento è un passaggio del [Processo di analisi scientifica dei dati per i team (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## Download e sotto-campionamento dei dati
1. Scaricare i dati dall'archivio BLOB di Azure usando il servizio BLOB del seguente codice Python di esempio:
   
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
2. Leggere i dati all'interno di un frame di dati Pandas dal file scaricato in precedenza.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)
3. Eseguire il sotto-campionamento dei dati usando `numpy`di `random.choice` nel modo seguente:
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

A questo punto, è possibile utilizzare il frame di dati precedente con il campione dell'1% per esplorare ulteriormente i dati e creare funzionalità.

## <a name="heading"></a>Caricamento e lettura dei dati in Azure Machine Learning
È possibile usare il seguente codice di esempio per campionare i dati e usarli direttamente in Azure ML:

1. Scrivere il frame di dati su un file locale
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Caricare il file locale su un BLOB di Azure usante il seguente codice di esempio:
   
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
            print ("Something went wrong with uploading to the blob:"+ BLOBNAME)
3. Leggere i dati del BLOB di Azure tramite [Import Data](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) di Azure ML, come illustrato nell’immagine successiva:

![lettore BLOB](./media/machine-learning-data-science-sample-data-blob/reader_blob.png)

<!---HONumber=AcomDC_0921_2016-->