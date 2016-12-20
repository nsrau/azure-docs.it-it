---
title: Esplorare i dati nell&quot;archiviazione BLOB di Azure con Pandas | Microsoft Docs
description: Come esplorare i dati archiviati nel contenitore BLOB di Azure utilizzando Pandas.
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: feaa9e54-01e0-48c8-a917-1eba0f9d9ec7
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b663a9df7e8cb692f4156f2609fb515265526868


---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Esplorare i dati nell'archiviazione BLOB di Azure con Pandas
In questo documento viene descritto come esplorare i dati archiviati nel contenitore BLOB di Azure mediante il pacchetto Python [Pandas](http://pandas.pydata.org/) .

Il **menu** seguente collega ad argomenti che descrivono come usare gli strumenti per esplorare i dati da vari ambienti di archiviazione. Questa attività è un passaggio del [Processo di analisi scientifica dei dati]().

[!INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che l'utente abbia:

* Creato un account di archiviazione di Azure. Per istruzioni, vedere [Creare un account di archiviazione di Azure](../storage/storage-create-storage-account.md#create-a-storage-account)
* I dati sono stati archiviati in un account di archiviazione BLOB di Azure. Per le istruzioni, vedere [Spostamento dei dati da e verso Archiviazione di Azure](../storage/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Caricare i dati in un intervallo di dati Pandas
Per esplorare e modificare un set di dati, i dati devono essere innanzitutto scaricati dall'origine BLOB in un file locale che può essere quindi caricato in un frame di dati Pandas. Ecco i passaggi da seguire per questa procedura:

1. Scaricare i dati da BLOB Azure con l’esempio di codice Python riportato di seguito utilizzando il servizio BLOB. Sostituire la variabile nel codice seguente con i valori specifici: 
   
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

## <a name="a-nameblob-dataexplorationaexamples-of-data-exploration-using-pandas"></a><a name="blob-dataexploration"></a>Esempi di esplorazione dei dati utilizzando Pandas
Di seguito sono riportati alcuni esempi dei modi per esplorare i dati utilizzando Pandas:

1. Controllare il **numero di righe e colonne** 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. **Controllare** le prime o le ultime **righe** nel set di dati seguente:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Controllare il **tipo di dati** importato in ogni colonna mediante il seguente codice di esempio
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Controllare le **statistiche di base** per le colonne nel set di dati come segue
   
        dataframe_blobdata.describe()
5. Esaminare il numero di voci per ogni valore della colonna come indicato di seguito
   
        dataframe_blobdata['<column_name>'].value_counts()
6. **Contare i valori mancanti** rispetto al numero effettivo di voci in ogni colonna utilizzando il seguente codice di esempio
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Se si dispongono di **valori mancanti** per una colonna specifica nei dati, è possibile eliminarli come indicato di seguito:
   
     dataframe_blobdata_noNA = dataframe_blobdata.dropna()   dataframe_blobdata_noNA.shape
   
   È possibile sostituire i valori mancanti anche con la funzione modalità:
   
     dataframe_blobdata_mode = dataframe_blobdata.fillna({'<nome_colonna>':dataframe_blobdata['<nome_colonna>'].mode()[0]})        
8. Creare un grafico **istogramma** utilizzando un numero variabile di contenitori per tracciare la distribuzione di una variabile    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Esaminare le **correlazioni** tra le variabili utilizzando un grafico di dispersione o la funzione di correlazione incorporata
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()




<!--HONumber=Nov16_HO3-->


