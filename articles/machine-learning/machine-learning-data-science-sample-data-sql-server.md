---
title: Dati di esempio di SQL Server in Azure| Microsoft Docs
description: Dati di esempio in SQL Server in Azure
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 33c030d4-5cca-4cc9-99d7-2bd13a3926af
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: fashah;garye;bradsev
translationtype: Human Translation
ms.sourcegitcommit: a6bc79b2cb5b73109cddd6cf57caeba754b52e2e
ms.openlocfilehash: 44ad1c9fb54231a3942889fc24bfc92554ead6fa
ms.lasthandoff: 02/11/2017


---
# <a name="heading"></a>Dati di esempio in SQL Server in Azure
Questo documento illustra come campionare dati archiviati in SQL Server su Azure usando SQL o il linguaggio di programmazione Python. Viene inoltre illustrato come spostare i dati campionati in Azure Machine Learning salvandoli in un file, caricandoli in un BLOB di Azure e quindi leggendoli in Azure Machine Learning Studio.

Il campionamento di Python usa la libreria ODBC [pyodbc](https://code.google.com/p/pyodbc/) per connettersi al server SQL in Azure e la libreria [Pandas](http://pandas.pydata.org/) per creare il campionamento.

> [!NOTE]
> Il codice SQL di esempio riportato in questo documento presuppone che i dati si trovino in un server SQL in Azure. In caso contrario, fare riferimento all'argomento sullo [Spostamento dei dati in SQL Server in una macchina virtuale di Azure](machine-learning-data-science-move-sql-server-virtual-machine.md) per istruzioni su come spostare i dati in SQL Server su Azure.
> 
> 

Il **menu** seguente contiene collegamenti ad argomenti che descrivono come campionare dati di vari ambienti di archiviazione. 

[!INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

**Perché campionare i dati?**
Se il set di dati da analizzare è grande, è in genere opportuno sottocampionare i dati per ridurlo e ottenere dimensioni inferiori più facilmente gestibili ma comunque rappresentative. Questa operazione facilita la comprensione e l'esplorazione dei dati, nonché la progettazione di funzionalità. Il suo ruolo nel [Processo di analisi scientifica dei dati per i team (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) consiste nell'abilitare la creazione relativa a prototipi di funzioni di elaborazione dei dati e di modelli di Machine Learning.

Questo campionamento è un passaggio del [Processo di analisi scientifica dei dati per i team (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="SQL"></a>Utilizzo di SQL
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

> [!NOTE]
> È possibile esplorare e generare le funzionalità da questi dati campionati archiviandoli in una nuova tabella
> 
> 

### <a name="sql-aml"></a>Connessione ad Azure Machine Learning
È possibile usare direttamente le query di esempio riportate sopra nel modulo [Import Data][import-data] (Importazione dati) di Azure Machine Learning per sottocampionare i dati in modo immediato e inserirli in un esperimento di Azure Machine Learning. Di seguito viene riportata una schermata relativa all'uso del modulo Reader per leggere i dati campionati:

![lettore sql][1]

## <a name="python"></a>Utilizzo del linguaggio di programmazione Python
In questa sezione viene mostrato l'uso della [libreria pyodbc](https://code.google.com/p/pyodbc/) per stabilire un collegamento ODBC al database di un server SQL in Python. La stringa di connessione del database è la seguente: (sostituire servername, dbname, username e password con la propria configurazione):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La libreria [Pandas](http://pandas.pydata.org/) in Python fornisce una vasta gamma di strutture di dati e strumenti di analisi dei dati per la manipolazione dei dati nella programmazione in Python. Nel codice riportato di seguito si legge un campionamento dello 0,1% dei dati di una tabella nel database SQL di Azure in un frame di dati Pandas:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

È ora possibile lavorare con i dati campionati nel frame di dati Pandas. 

### <a name="python-aml"></a>Connessione ad Azure Machine Learning
È possibile utilizzare il codice di esempio seguente per salvare i dati ricampionati in un file e caricarli in un BLOB di Azure. I dati del BLOB possono essere letti direttamente in un esperimento di Azure Machine Learning con il modulo [Import Data][import-data] (Importazione dati). Attenersi alla procedura seguente: 

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
3. Leggere i dati nel BLOB di Azure usando il modulo [Import Data][import-data] (Importazione dati) di Azure Machine Learning come illustrato nella schermata seguente:

![lettore BLOB][2]

## <a name="the-team-data-science-process-in-action-example"></a>Esempio del Processo di analisi scientifica dei dati per i team
Per un esempio della procedura dettagliata end-to-end del Processo di analisi scientifica dei dati per i team usando un set di dati pubblici, vedere [Processo di analisi scientifica dei dati per i team in azione: uso di SQL Sever](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

