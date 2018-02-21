---
title: "Creare report in database cloud con scalabilità orizzontale (partizionamento orizzontale) | Documentazione Microsoft"
description: "Usare query di database tra database per creare report tra più database."
services: sql-database
documentationcenter: 
manager: jhubbard
author: MladjoA
ms.assetid: c81ef5e3-41e9-4fd2-8631-868f2e168147
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: mlandzic
ms.openlocfilehash: 996ad1d47ece592dcf03a6eb8ed1c1916ceba374
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Creare report in database cloud con numero maggiore di istanze (anteprima)
È possibile creare report da più database SQL di Azure da un unico punto di connessione usando una [query elastica](sql-database-elastic-query-overview.md). Il database deve essere con partizionamento orizzontale.

Se si ha un database esistente, vedere [Migrazione dei database esistenti in database con un numero maggiore di istanze](sql-database-elastic-convert-to-use-elastic-tools.md).

Per comprendere quali sono gli oggetti SQL necessari per eseguire una query, vedere [Eseguire query in database con partizionamento orizzontale](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>prerequisiti
Scaricare ed eseguire [Introduzione allo strumento di esempio del Database elastico](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Creare un gestore mappe partizione utilizzando l'applicazione di esempio
Di seguito si creerà un gestore mappe partizione con diverse partizioni, seguita dall'inserimento di dati nelle partizioni. Se si dispone già di programma di installazione di partizioni con dati partizionati in essi, è possibile ignorare i passaggi seguenti e passare alla sezione successiva.

1. Compilare ed eseguire l’applicazione di esempio **Introduzione agli strumenti del Database elastico** . Seguire la procedura fino al passaggio 7 nella sezione [Scaricare ed eseguire l'app di esempio](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). Alla fine del passaggio 7, verrà visualizzato il seguente prompt dei comandi:

    ![Aprire il prompt dei comandi.][1]
2. Nella finestra di comando, digitare "1" e premere **Invio**. Viene creato il gestore delle mappe partizioni e aggiunge due partizioni al server. Digitare "3" e premere **Invio**; ripetere l'azione quattro volte. Consente di inserire righe di dati di esempio nelle partizioni.
3. Il [portale di Azure](https://portal.azure.com) dovrebbe mostrare tre nuovi database nel server:

   ![Conferma di Visual Studio][2]

   A questo punto, le query tra database sono supportate tramite le risorse del client del Database elastico. Nella finestra di comando, ad esempio, utilizzare l'opzione 4. I risultati di una query con più partizioni sono sempre un **UNION ALL** dei risultati di tutte le partizioni.

   Nella sezione successiva, verrà creato un endpoint del database di esempio che supporta l'esecuzione più completa delle query dei dati tra partizioni.

## <a name="create-an-elastic-query-database"></a>Creare un database di query elastico
1. Aprire il [portale di Azure](https://portal.azure.com) ed eseguire l'accesso.
2. Creare un nuovo database SQL Azure nello stesso server del programma di installazione del partizionamento. Denominare il database "ElasticDBQuery".

    ![Portale di Azure e il livello di prezzo][3]

    > [!NOTE]
    > È possibile usare un database esistente. Se è possibile farlo, non deve essere una delle partizioni su cui si desidera eseguire la query. Questo database verrà utilizzato per la creazione di oggetti di metadati per una query di database elastico.
    >

## <a name="create-database-objects"></a>Creare oggetti di database
### <a name="database-scoped-master-key-and-credentials"></a>Chiave master con ambito database e credenziali
Questi vengono utilizzati per la connessione per la gestione di gestore di mappe di partizioni e partizioni:

1. Apri SQL Server Management Studio e SQL Server Data Tools in Visual Studio
2. Connettersi al database ElasticDBQuery ed eseguire i comandi T-SQL seguenti:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    "nome utente" e "password" devono essere uguali alle informazioni di accesso usate nel passaggio 6 della sezione [Scaricare ed eseguire l'app di esempio](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app) in [Iniziare a utilizzare gli strumenti di database elastici](sql-database-elastic-scale-get-started.md).

### <a name="external-data-sources"></a>DROP EXTERNAL DATA SOURCE
Per creare un'origine dati esterna, eseguire il comando seguente sul database ElasticDBQuery:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 "CustomerIDShardMap" è il nome della mappa del partizionamento, se è stato creato il mapping della partizione e la gestione di mapping di partizione utilizzando l'esempio di strumenti di database flessibile. Tuttavia, se si utilizza il programma di installazione personalizzato per questo esempio, deve essere il nome di mappa partizionamento che scelto nell'applicazione.

### <a name="external-tables"></a>Tabelle esterne
Creare una tabella esterna corrispondente della tabella Customers nelle partizioni eseguendo il comando seguente sul database ElasticDBQuery:

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Eseguire una query di esempio elastica database T-SQL
Dopo aver definito l'origine dati esterna e le tabelle esterne è ora possibile utilizzare T-SQL completa tramite le tabelle esterne.

Eseguire questa query sul database ElasticDBQuery:

    select count(CustomerId) from [dbo].[Customers]

Si noterà che la query di aggregare i risultati di tutte le partizioni e produce il seguente output:

![Dettagli dell'output][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Importare i risultati della query database elastica in Excel
 È possibile importare i risultati da di una query a un file di Excel.

1. Avviare Excel 2013.
2. Individuare il **dati** della barra multifunzione.
3. Fare clic su **Da altre origini** e quindi su **Da SQL Server**.

   ![Importazione di Excel da altre origini][5]
4. In **Connessione guidata dati** digitare le credenziali di accesso e il nome del server. Quindi fare clic su **Next**.
5. Nella finestra di dialogo **Selezionare il database contenente i dati desiderati** selezionare il database **ElasticDBQuery**.
6. Selezionare la tabella **Customers** nella visualizzazione elenco e fare clic su **Avanti**. Fare clic su **Fine**.
7. Nel modulo **Importa dati** in **Specificare come visualizzare i dati nella cartella di lavoro** selezionare **Tabella** e fare clic su **OK**.

Tutte le righe dalla tabella **Clienti** , archiviate in diverse partizioni sono riportate nel foglio Excel.

È ora possibile utilizzare funzioni di visualizzazione avanzata dei dati di Excel. È possibile utilizzare la stringa di connessione con il nome del server, nome del database e credenziali per gli strumenti di integrazione di Business Intelligence e i dati di connettersi al database query elastica. Assicurarsi che SQL Server sia supportato come origine dati per lo strumento. È possibile fare riferimento al database elastica query e tabelle esterne come qualsiasi altro database di SQL Server e tabelle di SQL Server è necessario connettersi allo strumento.

### <a name="cost"></a>Costi
Non esiste senza alcun costo aggiuntivo per utilizzare la funzione elastica Query del Database.

Per informazioni sui prezzi, vedere [Dettagli prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Passaggi successivi

* Per un approfondimento, vedere [Panoramica delle query elastiche](sql-database-elastic-query-overview.md).
* Per un'esercitazione sul partizionamento verticale, vedere [Introduzione alle query tra database (partizionamento verticale)](sql-database-elastic-query-getting-started-vertical.md).
* Per le query di esempio e sintassi per i dati con partizionamento verticale, vedere [Eseguire query su dati con partizionamento verticale](sql-database-elastic-query-vertical-partitioning.md)
* Per le query di esempio e sintassi per i dati con partizionamento orizzontale, vedere [Eseguire query su dati con partizionamento orizzontale](sql-database-elastic-query-horizontal-partitioning.md)
* Vedere [sp\_execute \_remote](https://msdn.microsoft.com/library/mt703714) per una stored procedure che esegue un'istruzione Transact-SQL su un singolo database SQL di Azure remoto o un set di database che fungono da partizioni in uno schema di partizionamento orizzontale.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
