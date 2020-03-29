---
title: Campionare dati in SQL Server in Azure - Processo di data science per i team
description: Campionare dati archiviati in SQL Server in Azure usando SQL o il linguaggio di programmazione Python e quindi spostarli in Azure Machine Learning.
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
ms.openlocfilehash: 71a2ec9dc4d644fb8739db3817e2cd1d09913da7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717651"
---
# <a name="sample-data-in-sql-server-on-azure"></a><a name="heading"></a>Dati di esempio in SQL Server in AzureSample data in SQL Server on Azure

Questo articolo illustra come campionare dati archiviati in SQL Server su Azure usando SQL o il linguaggio di programmazione Python. Viene inoltre illustrato come spostare i dati campionati in Azure Machine Learning salvandoli in un file, caricandoli in un BLOB di Azure e quindi leggendoli in Azure Machine Learning Studio.

Il campionamento di Python usa la libreria ODBC [pyodbc](https://code.google.com/p/pyodbc/) per connettersi al server SQL in Azure e la libreria [Pandas](https://pandas.pydata.org/) per creare il campionamento.

> [!NOTE]
> Il codice SQL di esempio riportato in questo documento presuppone che i dati si trovino in un server SQL in Azure. In caso contrario, fare riferimento all'articolo [Spostamento dei dati in SQL Server in una macchina virtuale di Azure](move-sql-server-virtual-machine.md) per istruzioni su come spostare i dati in SQL Server su Azure.
> 
> 

**Perché campionare i dati?**
Se il set di dati da analizzare è grande, è in genere opportuno sottocampionare i dati per ridurlo e ottenere dimensioni inferiori più facilmente gestibili ma comunque rappresentative. Il campionamento facilita la comprensione, l'esplorazione e la progettazione delle funzionalità dei dati. Il suo ruolo nel [Processo di analisi scientifica dei dati per i team (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) consiste nell'abilitare la creazione relativa a prototipi di funzioni di elaborazione dei dati e di modelli di Machine Learning.

Questo campionamento è un passaggio del [Processo di analisi scientifica dei dati per i team (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="using-sql"></a><a name="SQL"></a>Utilizzo di SQL
In questa sezione vengono descritti alcuni metodi di utilizzo di SQL per eseguire il campionamento casuale semplice dei dati all'interno del database. Scegliere il metodo in base alla dimensione dei dati e alla loro distribuzione.

I due elementi seguenti mostrano come usare `newid` in SQL Server per effettuare il campionamento. Il metodo scelto dipende da quanto casuale si desidera che il campione sia (pk_id nel codice di esempio seguente si presuppone che sia una chiave primaria generata automaticamente).

1. Campionamento casuale meno rigoroso
   
        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())
2. Campionamento più casuale 
   
        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

È possibile usare la clausola Tablesample anche per il campionamento dei dati. Questa opzione può essere un approccio migliore se le dimensioni dei dati sono grandi (presupponendo che i dati in pagine diverse non siano correlati) e per il completamento della query in un tempo ragionevole.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

> [!NOTE]
> È possibile esplorare e generare le funzionalità da questi dati campionati archiviandoli in una nuova tabella
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Connessione ad Azure Machine Learning
È possibile usare direttamente le query di esempio riportate sopra nel modulo [Import Data][import-data] (Importazione dati) di Azure Machine Learning per sottocampionare i dati in modo immediato e inserirli in un esperimento di Azure Machine Learning. Di seguito è riportata una schermata dell'utilizzo del modulo di lettura per leggere i dati campionati:A screenshot of using the reader module to read the sampled data is shown here:

![lettore sql][1]

## <a name="using-the-python-programming-language"></a><a name="python"></a>Utilizzo del linguaggio di programmazione Python
In questa sezione viene mostrato l'uso della [libreria pyodbc](https://code.google.com/p/pyodbc/) per stabilire un collegamento ODBC al database di un server SQL in Python. La stringa di connessione al database è la seguente: (sostituire nomeserver, nomedb, nome utente e password con la configurazione):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La libreria [Pandas](https://pandas.pydata.org/) in Python fornisce una vasta gamma di strutture di dati e strumenti di analisi dei dati per la manipolazione dei dati nella programmazione in Python. Il codice seguente legge un campione dello 0,1% dei dati da una tabella nel database SQL di Azure in un dato Pandas:The following code reads a 0.1% sample of the data from a table in Azure SQL Database into a Pandas data:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)

È ora possibile lavorare con i dati campionati nel frame di dati Pandas. 

### <a name="connecting-to-azure-machine-learning"></a><a name="python-aml"></a>Connessione ad Azure Machine Learning
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
Per illustrare un esempio del processo di analisi scientifica dei dati del team usando un set di dati pubblico, vedere Processo di analisi scientifica dei dati del [team in azione: utilizzo](sql-walkthrough.md)di SQL Server.

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
