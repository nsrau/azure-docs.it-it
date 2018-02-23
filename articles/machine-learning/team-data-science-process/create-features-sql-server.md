---
title: "Creare funzionalità per i dati in SQL Server tramite SQL e Python | Documentazione Microsoft"
description: Elaborazione dei dati di SQL Azure
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: 
ms.assetid: bf1f4a6c-7711-4456-beb7-35fdccd46a44
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: bradsev;garye
ms.openlocfilehash: df1e658d532dcb23fdf0aa14bfad59e98c8ff7a8
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Creare funzionalità per i dati in SQL Server tramite SQL e Python
Questo documento illustra come creare funzionalità per i dati archiviati in una VM di SQL Server in Azure che consentono agli algoritmi di apprendere in modo più efficiente dai dati. A tale scopo, è possibile usare SQL o un linguaggio di programmazione come Python. Entrambi gli approcci sono illustrati di seguito.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

Questo **menu** fornisce collegamenti ad argomenti che descrivono come creare funzionalità per dati in diversi ambienti. Questa attività è un passaggio del [Processo di analisi scientifica dei dati per i team (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

> [!NOTE]
> Per un esempio pratico, è possibile consultare il [set di dati dei taxi di NYC](http://www.andresmh.com/nyctaxitrips/) e fare riferimento all'IPNB intitolato [Gestione dei dati di NYC usando IPython Notebook e SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) per una procedura dettagliata end-to-end.
> 
> 

## <a name="prerequisites"></a>prerequisiti
Questo articolo presuppone che l'utente abbia:

* Creato un account di archiviazione di Azure. Per istruzioni, vedere [Creare un account di archiviazione di Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* I dati vengono archiviati in SQL Server. In caso contrario, consultare [Spostamento dei dati in un database di SQL Azure per Azure Machine Learning](move-sql-azure.md) per istruzioni su come spostare i dati disponibili.

## <a name="sql-featuregen"></a>Creazione di funzionalità con SQL
In questa sezione viene descritto come creare funzionalità tramite SQL:  

1. [Creazione di funzionalità basate sul conteggio](#sql-countfeature)
2. [Creazione di contenitori per la creazione di funzionalità](#sql-binningfeature)
3. [Implementazione delle funzionalità da una singola colonna](#sql-featurerollout)

> [!NOTE]
> Dopo aver creato le funzionalità aggiuntive, è possibile aggiungerle come colonne alla tabella esistente oppure creare una nuova tabella con le funzionalità aggiuntive e la chiave primaria, che può essere unita alla tabella originale.
> 
> 

### <a name="sql-countfeature"></a>Creazione di funzionalità basate sul conteggio
In questo documento vengono descritte due modalità per creare funzionalità di conteggio. Nel primo metodo viene utilizzata la somma condizionale, mentre nel secondo la clausola "where". Tali metodi possono essere uniti alla tabella originale (tramite le colonne della chiave primaria) al fine di visualizzare le funzionalità di conteggio insieme ai dati originali.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="sql-binningfeature"></a>Creazione di contenitori per la creazione di funzionalità
Nell'esempio seguente viene descritto come creare funzionalità categorizzate, inserendo una colonna numerico (usando 5 contenitori) che può essere utilizzata come funzionalità:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Implementazione delle funzionalità da una singola colonna
In questa sezione viene illustrato come implementare una singola colonna in una tabella per generare funzionalità aggiuntive. In questo esempio si presuppone che nella tabella dalla quale si tenta di creare la funzionalità sia presente una colonna relativa alla latitudine o alla longitudine.

Di seguito, viene riportata una breve introduzione sui dati di posizione relativi a latitudine e longitudine (risorse assegnate da stackoverflow `http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Ecco alcuni concetti utili da conoscere sui dati di posizione prima di creare funzionalità dal campo:

* Il segno indica se l'utente si trova a Nord, Sud, Ovest o Est del globo.
* Una cifra dell'ordine delle centinaia diversa da zero indica la longitudine, non la latitudine in uso.
* Una cifra nell'ordine delle decine indica una posizione a circa 1.000 km. Offre informazioni utili sul continente o sull'oceano in cui si trova l'utente.
* La cifra nell'ordine dell'unità (un grado decimale) indica posizioni fino a 111 km (60 miglia nautiche, circa 69 miglia standard). Indica approssimativamente in quale stato o regione si trova l'utente.
* La prima posizione decimale è caratterizzata da un valore massimo di 11,1 km: consente di rilevare la posizione di una grande città da una circostante.
* La seconda posizione decimale è caratterizzata da un valore massimo di 1,1 km: consente di dividere un paese dall'altro.
* La terza posizione decimale è caratterizzata da un valore massimo di 110 m: consente di identificare un campo agricolo o una sede istituzionale.
* La quarta posizione decimale è caratterizzata da un valore massimo di 11 m: consente di identificare una porzione di terreno. La sua accuratezza è paragonabile a quella di un'unità GPS non corretta e senza interferenze.
* La quinta posizione decimale è caratterizzata da un valore massimo di 1,1 m e consente di distinguere un albero da un altro. Un'accuratezza di questo tipo, con le unità GPS commerciali, può essere raggiunta soltanto con una correzione differenziale.
* La sesta posizione decimale è caratterizzata da un valore massimo di 0,11 m: è possibile usarla per visualizzare i dettagli delle strutture, per progettare panorami e costruire strade. È più che sufficiente per rilevare i movimenti dei ghiacciai e dei fiumi. È possibile ottenere questa accuratezza eseguendo misurazioni accurate con il GPS, quale quello corretto in modo differenziale.

Le informazioni sulla posizione possono essere inserite nelle funzionalità separando le informazioni su regioni, posizioni e città. Tenere presente che è possibile chiamare anche un endpoint REST come l'API di Bing Maps, disponibile in `https://msdn.microsoft.com/library/ff701710.aspx` per visualizzare informazioni sull'area/quartiere.

    select
        <location_columnname>
        ,round(<location_columnname>,0) as l1        
        ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
        ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
        ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
        ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
        ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
        ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
    from <tablename>

Queste funzionalità basate sulla posizione possono essere usate anche per generate altre funzionalità di conteggio, come descritto in precedenza.

> [!TIP]
> A livello di programmazione, è possibile inserire i record usando il linguaggio preferito. Potrebbe essere necessario inserire i dati in blocchi per migliorare l'efficienza di scrittura. [Ecco un esempio di come procedere usando pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Un'altra alternativa consiste nell'inserire i dati nel database usando l' [utilità BCP](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="sql-aml"></a>Connessione ad Azure Machine Learning
La funzionalità appena creata può essere aggiunta come una colonna a una tabella esistente oppure archiviata in una nuova tabella e unita a quella originale ai fini dell'apprendimento automatico. È possibile creare o accedere alle funzionalità già create utilizzando il modulo [Import Data](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) in Azure ML, come descritto di seguito:

![Lettori di Azure ML](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="python"></a>Utilizzo di un linguaggio di programmazione quale Python
L'uso di Python per creare funzionalità quando i dati si trovano in SQL Server funziona in modo analogo all'elaborazione dei dati nei BLOB di Azure tramite Python. Per il confronto, vedere [Elaborare i dati BLOB di Azure nell'ambiente data science](data-blob.md). Caricare i dati dal database in un frame di dati pandas per elaborarli più approfonditamente. In questa sezione, è stato descritto il processo di connessione al database e di caricamento dati all'interno di un frame di dati.

Il seguente formato della stringa di connessione può essere usato per connettersi a un database di SQL Server da Pyhton usando pyodbc (sostituire il nome del server, quello del database, il nome utente e la password con i valori personalizzati):

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La [libreria Pandas](http://pandas.pydata.org/) in Python fornisce una vasta gamma di strutture di dati e strumenti di analisi dei dati per la manipolazione dei dati nella programmazione in Python. Il codice seguente consente di leggere i risultati restituiti da un database di SQL Server all'interno di un frame di dati di Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

A questo punto è possibile usare il frame di dati di Pandas, come descritto nell'argomento relativo alla [creazione di funzionalità per i dati di archiviazione BLOB di Azure tramite Panda](create-features-blob.md).

