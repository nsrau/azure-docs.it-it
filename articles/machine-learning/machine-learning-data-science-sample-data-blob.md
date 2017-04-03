---
title: Campionare i dati nell&quot;archivio BLOB di Azure| Documentazione Microsoft
description: Dati di esempio nell&quot;archivio BLOB di Azure
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: e8d9ad2c-86c5-43d6-80b8-d355b5c0dccf
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: fashah;garye;bradsev
translationtype: Human Translation
ms.sourcegitcommit: a6bc79b2cb5b73109cddd6cf57caeba754b52e2e
ms.openlocfilehash: 7873ad084c9d77eea556ae3fec846bebd4c99ebd
ms.lasthandoff: 02/11/2017


---
# <a name="heading"></a>Dati di esempio nell'archivio BLOB di Azure
In questo documento vengono descritti i dati di campionamento che è possibile memorizzare nell'archivio BLOB di Azure scaricandoli a livello di programmazione ed eseguendo il successivo campionamento tramite le procedure scritte in Python.

Il **menu** seguente contiene collegamenti ad argomenti che descrivono come campionare dati di vari ambienti di archiviazione. 

[!INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

**Perché campionare i dati?**
Se il set di dati da analizzare è grande, è in genere opportuno sottocampionare i dati per ridurlo e ottenere dimensioni inferiori più facilmente gestibili ma comunque rappresentative. Questa operazione facilita la comprensione e l'esplorazione dei dati, nonché la progettazione di funzionalità. Il suo ruolo nel Cortana Analytics Process consiste nell'abilitare la creazione relativa a prototipi di funzioni di elaborazione dei dati e di modelli per l'apprendimento automatico.

Questo campionamento è un passaggio del [Processo di analisi scientifica dei dati per i team (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="download-and-down-sample-data"></a>Download e sotto-campionamento dei dati
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
Per sottocampionare i dati e usarli direttamente in Azure Machine Learning, è possibile usare il codice di esempio seguente:

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

3. Leggere i dati del BLOB di Azure con [Import Data](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) (Importazione dati) di Azure Machine Learning, come illustrato nell'immagine seguente:

![lettore BLOB](./media/machine-learning-data-science-sample-data-blob/reader_blob.png)


