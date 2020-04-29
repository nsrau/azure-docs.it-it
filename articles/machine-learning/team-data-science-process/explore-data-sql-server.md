---
title: Esplorare i dati in una macchina virtuale di SQL Server - Processo di data science per i team
description: Come esplorare i dati archiviati in una macchina virtuale di SQL Server in Azure usando SQL o un linguaggio di programmazione come Python.
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
ms.openlocfilehash: ae8c7c43ecbf9bc625e1e46be3e2c71c8d57b6f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76720096"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Esplorazione dei dati nella macchina virtuale di SQL Server in Azure

Questo articolo illustra come esplorare i dati archiviati in una macchina virtuale SQL Server in Azure. Usare SQL o Python per esaminare i dati.

Questa attività è un passaggio del [processo di data science per i team](overview.md).

> [!NOTE]
> Le istruzioni SQL di esempio fornite nel documento presuppongono che i dati si trovino in SQL Server. In caso contrario, fare riferimento al processo di analisi scientifica dei dati cloud per visualizzare informazioni su come spostare i dati in SQL Server.
> 
> 

## <a name="explore-sql-data-with-sql-scripts"></a><a name="sql-dataexploration"></a>Esplorare i dati SQL mediante gli script SQL
Di seguito, sono riportati alcuni script SQL di esempio da utilizzare per esplorare gli archivi dati in SQL Server.

1. Visualizzare il numero di osservazioni per giorno
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Visualizzare i livelli in una colonna di categoria 
   
    `select  distinct <column_name> from <databasename>`
3. Visualizzare il numero di livelli in una combinazione di due colonne di categoria 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Visualizzare la distribuzione per colonne numeriche
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Per un esempio pratico, è possibile usare il [set di dati dei taxi di New York City](https://www.andresmh.com/nyctaxitrips/) e fare riferimento all'IPNB sulla [gestione dei dati di New York City tramite IPython Notebook e SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) per una procedura dettagliata end-to-end.
> 
> 

## <a name="explore-sql-data-with-python"></a><a name="python"></a>Esplorare i dati SQL mediante Python
L'uso di Python per l'esplorazione dei dati e la generazione di funzionalità quando i dati sono in SQL Server è analogo all'elaborazione dei dati nel BLOB di Azure con Python, come descritto in [elaborare i dati BLOB di Azure nell'ambiente Data Science](data-blob.md). Caricare i dati dal database in un frame di dati Pandas, quindi è possibile elaborarli ulteriormente. In questa sezione, è stato descritto il processo di connessione al database per caricare dati all'interno di un frame di dati.

Il seguente formato della stringa di connessione può essere usato per connettersi a un database di SQL Server da Pyhton usando pyodbc (sostituire il nome del server, quello del database, il nome utente e la password con i valori personalizzati):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La [libreria Pandas](https://pandas.pydata.org/) in Python fornisce una vasta gamma di strutture di dati e strumenti di analisi dei dati per la manipolazione dei dati per la programmazione Python. Il codice seguente consente di leggere i risultati restituiti da un database di SQL Server all'interno di un frame di dati di Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

A questo punto, è possibile usare il frame di dati di Pandas, come descritto nell'argomento [Elaborazione dei dati BLOB di Azure nell'ambiente di analisi scientifica dei dati](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>Esempio del processo di data science per i team in azione
Per un esempio della procedura dettagliata end-to-end del Cortana Analytics Process tramite un set di dati pubblico, vedere [Processo di analisi scientifica dei dati per i team in azione: uso di SQL Sever](sql-walkthrough.md).

