---
title: Elaborare i dati BLOB di Azure con analisi avanzate - Processo di data science per i team (TDSP)
description: Esplorare i dati e creare funzionalità da dati archiviati nell'archivio BLOB di Azure con analisi avanzate.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 5a088d5918a957036b905db9136f9b16e5f0527e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93307165"
---
# <a name="process-azure-blob-data-with-advanced-analytics"></a><a name="heading"></a>Elaborare i dati BLOB di Azure con l'analisi avanzata
In questo documento vengono descritte l'esplorazione dei dati e la creazione di funzionalità da dati archiviati nell’archivio BLOB di Azure. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Caricare i dati in un intervallo di dati Pandas
Per esplorare e modificare un set di dati, è necessario scaricarlo dall'origine BLOB in un file locale che può essere quindi caricato in un frame di dati Pandas. Ecco i passaggi da seguire per questa procedura:

1. Scaricare i dati dal BLOB di Azure con il codice Python di esempio seguente usando il servizio BLOB. Sostituire la variabile nel codice riportato di seguito con i valori specifici: 
   
    ```python
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
    ```
2. Leggere i dati in un frame di dati Pandas dal file scaricato.
   
    ```python
    #LOCALFILE is the file path    
    dataframe_blobdata = pd.read_csv(LOCALFILE)
    ```

A questo punto si è pronti per esplorare i dati e generare le funzionalità di questo set di dati.

## <a name="data-exploration"></a><a name="blob-dataexploration"></a>Esplorazione dei dati
Di seguito sono riportati alcuni esempi dei modi per esplorare i dati utilizzando Pandas:

1. Controllare il numero di righe e colonne 
   
    ```python
    print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
    ```
2. Controllare le prime o le ultime righe nel set di dati come indicato di seguito:
   
    ```python
    dataframe_blobdata.head(10)
   
    dataframe_blobdata.tail(10)
    ```
3. Controllare il tipo di dati importato in ogni colonna mediante il seguente codice di esempio
   
    ```python
    for col in dataframe_blobdata.columns:
        print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
    ```
4. Controllare le statistiche di base per le colonne nel set di dati come segue
   
    ```python
    dataframe_blobdata.describe()
    ```
5. Esaminare il numero di voci per ogni valore della colonna come indicato di seguito
   
    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```
6. Contare i valori mancanti rispetto al numero effettivo di voci in ogni colonna utilizzando il seguente codice di esempio
   
    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print miss_num
    ```
7. Se si dispongono di valori mancanti per una colonna specifica nei dati, è possibile eliminarli come indicato di seguito:
   
    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```
   
   È possibile sostituire i valori mancanti anche con la funzione modalità:
   
    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})  
    ```      
8. Creare un grafico istogramma utilizzando un numero variabile di contenitori per tracciare la distribuzione di una variabile    
   
    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```
9. Esaminare le correlazioni tra le variabili utilizzando un grafico di dispersione o la funzione di correlazione incorporata
   
    ```python
    #relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
    #correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```

## <a name="feature-generation"></a><a name="blob-featuregen"></a>Generazione di funzionalità
È quindi possibile generare funzionalità tramite Python come indicato di seguito:

### <a name="indicator-value-based-feature-generation"></a><a name="blob-countfeature"></a>Creazione di funzionalità basata sul valore dell'indicatore
Le funzionalità relative alle categorie possono essere create come indicato di seguito:

1. Controllare la distribuzione della colonna relativa alla categoria:
   
    ```python
    dataframe_blobdata['<categorical_column>'].value_counts()
    ```
2. Generare i valori dell'indicatore per ognuno dei valori della colonna
   
    ```python
    #generate the indicator column
    dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
    ```
3. Unire la colonna indicatore con il frame di dati originale 
   
    ```python
    #Join the dummy variables back to the original data frame
    dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
    ```
4. Rimuovere la variabile originale:
   
    ```python
    #Remove the original column rate_code in df1_with_dummy
    dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
    ```

### <a name="binning-feature-generation"></a><a name="blob-binningfeature"></a>Creazione di contenitori per la creazione di funzionalità
Per creare funzionalità in contenitori, procedere come indicato di seguito:

1. Aggiungere una sequenza di colonne per suddividere una colonna numerica
   
    ```python
    bins = [0, 1, 2, 4, 10, 40]
    dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
    ```
2. Convertire la creazione di contenitori in una sequenza di variabili booleane
   
    ```python
    dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
    ```
3. Infine, aggiungere di nuovo le variabili fittizie al frame di dati originale
   
    ```python
    dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)  
    ```  

## <a name="writing-data-back-to-azure-blob-and-consuming-in-azure-machine-learning"></a><a name="sql-featuregen"></a>Scrittura dei dati nel BLOB di Azure e utilizzo in Azure Machine Learning
Dopo aver esplorato i dati e creato le funzionalità necessarie, è possibile caricare i dati (sampled o trasformato) in un BLOB di Azure e usarli in Azure Machine Learning usando la procedura seguente: è possibile creare funzionalità aggiuntive anche nell'Azure Machine Learning Studio (classica). 

1. Scrivere il frame di dati in file locali
   
    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```
2. Caricare i dati nel BLOB di Azure come indicato di seguito:
   
    ```python
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
    ```
3. Ora i dati possono essere letti dal BLOB utilizzando il modulo [Import Data][import-data] di Azure Machine Learning, come illustrato nella schermata riportata di seguito:

![lettore BLOB][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data