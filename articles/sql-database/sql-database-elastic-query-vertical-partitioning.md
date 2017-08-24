---
title: Eseguire query in database cloud con schemi diversi | Documentazione Microsoft
description: Informazioni su come configurare le query tra database su partizioni verticali.
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
ms.assetid: 84c261f2-9edc-42f4-988c-cf2f251f5eff
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2016
ms.author: torsteng
ms.translationtype: Human Translation
ms.sourcegitcommit: 430fed27780076738e319dabca4cc9abaed70691
ms.openlocfilehash: 078784bcdf7a3a6d4423389d2f5ca4ffdb67c89f
ms.contentlocale: it-it
ms.lasthandoff: 02/22/2017



---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Eseguire query in database cloud con schemi diversi (anteprima)
![Eseguire una query tra tabelle in vari database][1]

I database con partizionamento verticale usano set di tabelle diversi su database diversi. Lo schema risulta quindi diverso nei diversi database. Ad esempio, tutte le tabelle per l'inventario si trovano in un database, mentre le tabelle correlate alla contabilità si trovano in un altro database. 

## <a name="prerequisites"></a>Prerequisiti
* L'utente deve disporre dell'autorizzazione ALTER ANY origine dei dati esterni. Questa autorizzazione è inclusa nell'autorizzazione ALTER DATABASE.
* Per il riferimento all'origine dati sottostante sono necessarie autorizzazioni ALTER ANY EXTERNAL DATA SOURCE.

## <a name="overview"></a>Panoramica

> [!NOTE]
> A differenza del partizionamento orizzontale, queste istruzioni DDL non dipendono dalla definizione di un livello dati con una mappa partizioni tramite la libreria client del database elastico.
>

1. [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CREATE EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CREATE EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="create-database-scoped-master-key-and-credentials"></a>Creare la chiave master e le credenziali con ambito database
Le credenziali vengono usate dalla query elastica per connettersi ai database remoti.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Assicurarsi che `<username>` non includa alcun suffisso **"@servername"**. 
>

## <a name="create-external-data-sources"></a>Creare origini dati esterne
Sintassi:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

> [!IMPORTANT]
> Il parametro TYPE deve essere impostato su **RDBMS**. 
>

### <a name="example"></a>Esempio
Nell'esempio seguente viene illustrato l'utilizzo dell'istruzione CREATE per origini dati esterne. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 

Per recuperare l'elenco di origini dati esterne correnti: 

    select * from sys.external_data_sources; 

### <a name="external-tables"></a>Tabelle esterne
Sintassi:

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
    { WITH ( <rdbms_external_table_options> ) } 
    )[;] 

    <rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

### <a name="example"></a>Esempio
    CREATE EXTERNAL TABLE [dbo].[customer]( 
        [c_id] int NOT NULL, 
        [c_firstname] nvarchar(256) NULL, 
        [c_lastname] nvarchar(256) NOT NULL, 
        [street] nvarchar(256) NOT NULL, 
        [city] nvarchar(256) NOT NULL, 
        [state] nvarchar(20) NULL, 
        [country] nvarchar(50) NOT NULL, 
    ) 
    WITH 
    ( 
           DATA_SOURCE = RemoteReferenceData 
    ); 

Nell'esempio seguente viene illustrato come recuperare l'elenco di tabelle esterni dal database corrente: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Osservazioni
La query elastica estende la sintassi esistente della tabella esterna per definire le tabelle esterne che usano origini dati esterne di tipo RDBMS. Una definizione di tabella esterna per il partizionamento verticale comprende gli aspetti seguenti: 

* **Schema**: il DDL della tabella esterna definisce uno schema che può essere usato dalle query. Lo schema fornito nella definizione della tabella esterna deve corrispondere allo schema delle tabelle nel database remoto in cui sono archiviati i dati effettivi. 
* **Riferimento al database remoto**: il DDL della tabella esterna fa riferimento a un'origine dati esterna. L'origine dati esterna specifica il nome del server logico e il nome del database remoto in cui sono archiviati i dati effettivi della tabella. 

Se si usa un'origine dati esterna, come illustrato nella sezione precedente, la sintassi per la creazione di tabelle esterne è la seguente: 

La clausola DATA_SOURCE definisce l'origine dati esterna, ovvero il database remoto nel caso del partizionamento verticale, usata per la tabella esterna.  

Le clausole SCHEMA_NAME e OBJECT_NAME consentono di mappare la definizione della tabella esterna a una tabella in uno schema diverso sul database remoto o a una tabella con un nome diverso, rispettivamente. Ciò risulta utile se si vuole definire una tabella esterna per una vista del catalogo o una DMV nel database remoto o in qualsiasi altra situazione in cui il nome della tabella remota è già usato a livello locale.  

L'istruzione DDL seguente elimina una definizione di tabella esterna esistente da un catalogo locale. Non ha alcun impatto sul database remoto. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Autorizzazioni per CREATE/DROP EXTERNAL TABLE**: le autorizzazioni di tipo ALTER ANY EXTERNAL DATA SOURCE sono necessarie per il DDL di tabelle esterne, che è richiesto anche per fare riferimento all'origine dati sottostante.  

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza
Gli utenti con accesso alla tabella esterna ottengono automaticamente l'accesso alle tabelle remote sottostanti con le credenziali specificate nella definizione dell'origine dati esterna. È necessario gestire con attenzione l'accesso alla tabella esterna, in modo da evitare l'elevazione indesiderata dei privilegi tramite le credenziali dell'origine dati esterna. È possibile usare le normali autorizzazioni SQL per CONCEDERE o REVOCARE l'accesso a una tabella esterna, come se fosse una tabella normale.  

## <a name="example-querying-vertically-partitioned-databases"></a>Esempio: esecuzione di query su database partizionati verticalmente
La query seguente esegue un join a tre vie tra le due tabelle locali per gli ordini e le righe di ordine e la tabella remota per i clienti. Ecco un esempio di caso di utilizzo dei dati di riferimento per la query elastica: 

    SELECT      
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline, 
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer 
    JOIN orders 
    ON c_id = o_c_id
    JOIN  order_line 
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100


## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Stored procedure per l'esecuzione remota di T-SQL: sp\_execute_remote
La query elastica introduce anche una stored procedure che fornisce l'accesso diretto alle partizioni. La stored procedure è denominata [sp\_execute \_remote](https://msdn.microsoft.com/library/mt703714) e può essere usata per eseguire stored procedure remote o codice T-SQL sui database remoti. È necessario specificare i seguenti parametri: 

* Nome dell'origine dati (nvarchar): il nome dell'origine dati esterna di tipo RDBMS. 
* Query (nvarchar): la query T-SQL da eseguire in ogni partizione. 
* Dichiarazione del parametro (nvarchar) - Facoltativo: stringa con definizioni del tipo di dati per i parametri usati nel parametro della query, ad esempio sp_executesql. 
* Elenco di valori dei parametri (facoltativo): elenco delimitato da virgole di valori dei parametri, ad esempio sp_executesql.

La stored procedure sp\_execute\_remote usa l'origine dati esterna specificata nei parametri di chiamata per eseguire l'istruzione T-SQL inclusa nei database remoti. Usa le credenziali dell'origine dati esterna per connettersi al database di gestione shardmap e ai database remoti.  

Esempio: 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 



## <a name="connectivity-for-tools"></a>Connettività per gli strumenti
È possibile usare le normali stringhe di connessione di SQL Server per connettere gli strumenti di Business Intelligence e di integrazione dei dati ai database nel server del database SQL per cui sono abilitate le query elastiche e sono state definite tabelle esterne. Assicurarsi che SQL Server sia supportato come origine dati per lo strumento. Fare quindi riferimento al database elastico sottoposto a query e alle relative tabelle esterne come se fosse un qualsiasi altro database di SQL Server a cui ci si può connettere con lo strumento. 

## <a name="best-practices"></a>Procedure consigliate
* Assicurarsi che il database di endpoint della query elastica disponga di accesso al database remoto mediante l'abilitazione dell'accesso per i servizi di Azure nella rispettiva configurazione del firewall del database SQL. Assicurarsi anche che le credenziali fornite nella definizione dell'origine dati esterna possano accedere correttamente al database remoto e abbiano le autorizzazioni necessarie per accedere alla tabella remota.  
* La query elastica funziona in modo ottimale per le query in cui la maggior parte dei calcoli può essere eseguita sui database remoti. In genere si ottengono le prestazioni migliori per le query tramite i predicati di filtro selettivo, che possono essere valutati sui database remoti, o mediante join che possono essere eseguiti completamente nel database remoto. Altri modelli di query potrebbero richiedere il caricamento di quantità elevate di dati dal database remoto e potrebbero offrire prestazioni ridotte. 

## <a name="next-steps"></a>Passaggi successivi

* Per un approfondimento, vedere [Panoramica delle query elastiche](sql-database-elastic-query-overview.md).
* Per un'esercitazione sul partizionamento verticale, vedere [Introduzione alle query tra database (partizionamento verticale)](sql-database-elastic-query-getting-started-vertical.md).
* Per un'esercitazione sul partizionamento orizzontale, vedere la [guida introduttiva alle query elastiche per il partizionamento orizzontale](sql-database-elastic-query-getting-started.md).
* Per le query di esempio e sintassi per i dati con partizionamento orizzontale, vedere [Eseguire query su dati con partizionamento orizzontale](sql-database-elastic-query-horizontal-partitioning.md)
* Vedere [sp\_execute \_remote](https://msdn.microsoft.com/library/mt703714) per una stored procedure che esegue un'istruzione Transact-SQL su un singolo database SQL di Azure remoto o un set di database che fungono da partizioni in uno schema di partizionamento orizzontale.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->

