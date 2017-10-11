---
title: Connessione del database SQL di Azure a Ricerca di Azure tramite gli indicizzatori | Documentazione Microsoft
description: Informazioni su come estrarre i dati dal database SQL di Azure a un indice di Ricerca di Azure tramite gli indicizzatori.
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: e9bbf352-dfff-4872-9b17-b1351aae519f
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/13/2017
ms.author: eugenesh
ms.openlocfilehash: 49f614fdf3ba84de238139387ea97ee62077b072
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>Connessione del database SQL di Azure a Ricerca di Azure tramite gli indicizzatori

Prima di poter eseguire una query nell'[indice di Ricerca di Azure](search-what-is-an-index.md), è necessario inserirvi i propri dati. Se i dati si trovano in un database SQL di Azure, l'**Indicizzatore di Ricerca di Azure per il database SQL di Azure**, o in breve **Indicizzatore SQL di Azure**, è in grado di automatizzare il processo di indicizzazione. Questo implica che la quantità di codice da scrivere è inferiore, così come l'infrastruttura di cui occuparsi.

In questo articolo vengono illustrati i meccanismi di uso degli [indicizzatori](search-indexer-overview.md), ma vengono anche descritte le funzionalità disponibili solo con i database SQL, ad esempio, il rilevamento delle modifiche integrato. 

Oltre ai database SQL di Azure, Ricerca di Azure offre indicizzatori per [Azure Cosmos DB](search-howto-index-documentdb.md), [Archivio BLOB di Azure](search-howto-indexing-azure-blob-storage.md) e [Archiviazione tabelle di Azure](search-howto-indexing-azure-tables.md). Per richiedere il supporto per altre origini dati, inviare commenti e suggerimenti nel [forum relativo a commenti e suggerimenti di Ricerca di Azure](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indicizzatori e origini dati

Un'**origine dati** specifica i dati da indicizzare, le credenziali necessarie per accedere ai dati e i criteri che consentono di identificare in modo efficace le modifiche apportate ai dati, ovvero righe nuove, modificate o eliminate. È definita come risorsa indipendente affinché possa essere usata da più indicizzatori.

Un **indicizzatore** è una risorsa che connette una singola origine dati agli indici di ricerca di destinazione. Un indicizzatore viene usato nei modi seguenti:

* Eseguire una copia occasionale dei dati per popolare un indice.
* Aggiornare un indice con le modifiche nell'origine dati in base a una pianificazione.
* Eseguire aggiornamenti su richiesta in un indice in base alle esigenze.

Un singolo indicizzatore può usare solo una tabella o una vista, ma è possibile creare più indicizzatori se si desidera compilare indici di ricerca multipli. Per altre informazioni sui concetti, vedere [Operazioni degli indicizzatori: flusso di lavoro tipico](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow).

È possibile impostare e configurare un indicizzatore SQL di Azure usando:

* Importazione guidata dati nel [portale di Azure](https://portal.azure.com)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet) Ricerca di Azure
* [API REST](https://docs.microsoft.com/en-us/rest/api/searchservice/indexer-operations) Ricerca di Azure

In questo articolo si userà l'API REST per creare gli **indicizzatori** e le **origini dati**.

## <a name="when-to-use-azure-sql-indexer"></a>Quando usare l’indicizzatore SQL di Azure
In base a diversi fattori relativi ai dati, l'utilizzo dell'indicizzatore di SQL di Azure potrebbe non essere appropriato. Se i dati soddisfano i requisiti seguenti, è possibile usare l’indicizzatore SQL di Azure.

| Criteri | Dettagli |
|----------|---------|
| I dati provengono da una singola tabella o vista | Se i dati sono sparsi tra più tabelle, è possibile creare un'unica vista dei dati. Tuttavia, se si usa una vista, non sarà possibile usare il rilevamento delle modifiche integrato di SQL Server per aggiornare un indice con le modifiche incrementali. Per altre informazioni, vedere [Acquisizione delle righe modificate ed eliminate](#CaptureChangedRows) di seguito. |
| Tipi di dati compatibili | Nell'indice di Ricerca di Azure è supportata la maggior parte dei tipi SQL, ma non tutti. Per un elenco, vedere [Elenco dei tipi di dati](#TypeMapping). |
| La sincronizzazione dei dati in tempo reale non è necessaria | Un indicizzatore può reindicizzare la tabella al massimo ogni 5 minuti. Se i dati vengono modificati di frequente ed è necessario riflettere le modifiche nell’indice entro pochi secondi o pochi minuti, è consigliabile usare l'[API REST](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) o [l'SDK .NET](search-import-data-dotnet.md) per eseguire direttamente il push delle righe aggiornate. |
| L'indicizzazione incrementale è possibile | Se si dispone di un set di dati di grandi dimensioni e si prevede di eseguire l'indicizzatore in una pianificazione, Ricerca di Azure deve essere in grado di identificare in modo efficiente le righe modificate, nuove ed eliminate. L'indicizzazione incrementale è consentito solo se si esegue l'indicizzazione su richiesta, non programmata, o se la si esegue per meno di 100.000 righe. Per altre informazioni, vedere [Acquisizione delle righe modificate ed eliminate](#CaptureChangedRows) di seguito. |

## <a name="create-an-azure-sql-indexer"></a>Creare un indicizzatore SQL di Azure

1. Creare l'origine dati:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   È possibile ottenere la stringa di connessione dal [portale di Azure](https://portal.azure.com). Usare l'opzione `ADO.NET connection string`.

2. Creare un indice di Ricerca di Azure di destinazione, se non ne è già disponibile uno. È possibile creare un indice usando il [portale](https://portal.azure.com) o [l'API Crea indice](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Assicurarsi che lo schema dell'indice di destinazione sia compatibile con lo schema della tabella di origine. Per informazioni dettagliate, vedere [Mapping tra tipi di dati SQL e tipi di dati di Ricerca di Azure](#TypeMapping).

3. Creare l'indicizzatore assegnandogli un nome e il riferimento all’origine dati e all'indice di destinazione:

    ```
    POST https://myservice.search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
    ```

Un indicizzatore creato in questo modo non dispone di una pianificazione. Viene eseguito non appena viene creato. È possibile rieseguirlo in qualsiasi momento mediante una richiesta di **esecuzione indicizzatore** :

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2016-09-01
    api-key: admin-key

È possibile personalizzare alcuni aspetti del comportamento dell'indicizzatore, ad esempio le dimensioni del batch e il numero di documenti che è possibile ignorare prima che un'esecuzione dell'indicizzatore abbia esito negativo. Per altre informazioni, vedere [Create Indexer API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)(Creare un'API di indicizzatore).

Potrebbe essere necessario consentire ai servizi di Azure di connettersi al database. Vedere [Connessione da Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) per istruzioni su come eseguire questa operazione.

Per monitorare lo stato dell'indicizzatore e la cronologia di esecuzione (numero di elementi indicizzati, errori e così via), utilizzare una richiesta di **stato indicizzatore** :

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2016-09-01
    api-key: admin-key

La risposta sarà simile alla seguente:

    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }

La cronologia di esecuzione contiene fino a 50 esecuzioni completate più recenti, in ordine cronologico inverso (in modo che l'esecuzione più recente venga visualizzata per prima nella risposta).
Sono disponibili informazioni aggiuntive relative alla risposta [Ottenere lo stato dell'indicizzatore](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Eseguire gli indicizzatori in base a una pianificazione
È inoltre possibile fare in modo che l'indicizzatore si esegua periodicamente in base a una pianificazione. A tale scopo, aggiungere la proprietà **schedule** al momento della creazione o dell'aggiornamento dell'indicizzatore. Nell'esempio seguente viene illustrata una richiesta PUT di aggiornamento dell'indicizzatore:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

È richiesto il parametro **interval** . L'intervallo fa riferimento al tempo tra l'inizio di due esecuzioni consecutive dell'indicizzatore. L'intervallo minimo consentito è di 5 minuti, quello massimo di un giorno. Il valore deve essere formattato come valore XSD "dayTimeDuration" (un subset limitato di un valore [duration ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Il modello è: `P(nD)(T(nH)(nM))`. Esempi: `PT15M` ogni 15 minuti, `PT2H` ogni due ore.

Il valore facoltativo **startTime** indica quando devono essere avviate le esecuzioni pianificate. Se viene omesso, verrà usata l'ora UTC corrente. Può trattarsi di un'ora del passato, caso in cui la prima esecuzione viene pianificata come se l'indicizzatore fosse stato continuamente in funzione sin dall'ora di inizio.  

È possibile effettuare solo l'esecuzione di un indicizzatore specificato per volta. Se un indicizzatore è in uso quando viene pianificata l'esecuzione, l'esecuzione viene rimandata fino al successivo orario pianificato.

Ecco un esempio per rendere il discorso più concreto. Si supponga che sia configurata la seguente pianificazione oraria:

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Di seguito è illustrato ciò che accade:

1. L’esecuzione del primo indicizzatore comincia il 1 marzo 2015 alle 12:00 UTC più o meno.
2. Si supponga che l'esecuzione richieda 20 minuti (o un tempo qualsiasi inferiore a 1 ora).
3. La seconda esecuzione inizia il 1 marzo 2015 alle 13.00, più o meno
4. Si supponga ora che l'esecuzione richieda più di un'ora, ad esempio 70 minuti, e che venga completata alle 02:10 circa.
5. Ora sono le 02:00, l’ora dell’inizio della terza esecuzione. Poiché la seconda esecuzione della 01.00 è ancora in esecuzione, la terza esecuzione viene saltata. La terza esecuzione inizia alle 03:00.

È possibile aggiungere, modificare o eliminare una pianificazione per un indicizzatore esistente utilizzando una richiesta di **indicizzatore PUT** .

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Acquisire righe nuove, modificate ed eliminate

Ricerca di Azure usa l'**indicizzazione incrementale** per evitare di reindicizzare l'intera tabella o vista ogni volta che viene eseguito un indicizzatore. Ricerca di Azure offre che due criteri per il rilevamento delle modifiche per supportare l'indicizzazione incrementale. 

### <a name="sql-integrated-change-tracking-policy"></a>Criteri di rilevamento delle modifiche integrati di SQL
Se il database SQL supporta il [rilevamento delle modifiche](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server), è consigliabile usare i **criteri di rilevamento delle modifiche integrati di SQL**. Questo è il criterio più efficiente. Inoltre consente a Ricerca di Azure di identificare le righe eliminate senza dover aggiungere allo schema una colonna di "eliminazione temporanea" esplicita.

#### <a name="requirements"></a>Requisiti 

+ Requisiti sulla versione del database:
  * SQL Server 2012 SP3 e versioni successive, se si usa SQL Server nelle macchine virtuali di Azure.
  * Database SQL di Azure V12, se si utilizza il database SQL di Azure SQL.
+ Solo tabelle, nessuna vista. 
+ Nel database [abilitare il rilevamento della modifica](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) per la tabella. 
+ Nessuna chiave primaria composta, ovvero una chiave primaria che contiene più di una colonna, nella tabella.  

#### <a name="usage"></a>Utilizzo

Per utilizzare questo criterio, creare o aggiornare l'origine dati nel modo indicato di seguito:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

Quando si usano i criteri di rilevamento delle modifiche integrati di SQL, non specificare criteri di rilevamento dell'eliminazione dei dati separati, perché questi ultimi includono il supporto predefinito per l'identificazione delle righe eliminate. Tuttavia, affinché le operazioni di eliminazione vengano rilevate "auto-magicamente", la chiave del documento nell'indice di ricerca deve essere la stessa della chiave primaria nella tabella SQL. 

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Criteri di rilevamento delle modifiche con limite massimo

Questi criteri di rilevamento delle modifiche si basano su una colonna di "livello più alto" che acquisisce la versione o l'ora dell'ultimo aggiornamento di una riga. Se si usa una vista, è consigliabile usare i criteri di livello più alto. La colonna di livello più alto deve soddisfare i requisiti seguenti.

#### <a name="requirements"></a>Requisiti 

* Tutti gli inserimenti specificano un valore per la colonna.
* Tutti gli aggiornamenti a un elemento modificano anche il valore della colonna.
* Il valore di questa colonna aumenta in base a ogni modifica o aggiornamento.
* Le query con le clausole QUERY e ORDER BY seguenti possono essere eseguite in modo efficiente: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> È consigliabile usare il tipo di dati [rowversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) per la colonna di livello più alto. Se viene usato un qualsiasi altro tipo di dati, il rilevamento delle modifiche potrebbe non garantire l'acquisizione di tutte le modifiche in presenza di transazioni in esecuzione contemporaneamente a una query dell'indicizzatore. Quando si usa **rowversion** in una configurazione con le repliche di sola lettura, è necessario puntare l'indicizzatore alla replica primaria. Per scenari di sincronizzazione dei dati, è possibile usare solo una replica primaria.

#### <a name="usage"></a>Utilizzo

Per usare questo criterio di limite massimo, creare o aggiornare l'origine dati nel modo seguente:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }

> [!WARNING]
> Se la tabella di origine non dispone di un indice nella colonna del limite massimo, le query usate dall'indicizzatore SQL possono scadere. In particolare, la clausola `ORDER BY [High Water Mark Column]` richiede l'esecuzione efficiente di un indice nel caso in cui la tabella contenga numerose righe.
>
>

Se si verificano errori di timeout, è possibile usare l'impostazione di configurazione dell'indicizzatore `queryTimeout` per impostare il timeout delle query su un valore superiore rispetto a quello predefinito di 5 minuti. Ad esempio, per impostare il timeout su 10 minuti, creare o aggiornare l'indicizzatore con la seguente configurazione:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

La clausola `ORDER BY [High Water Mark Column]` può anche essere disabilitata. Tuttavia, questa operazione è sconsigliata perché, se l'esecuzione dell'indicizzatore è stata interrotta da un errore, l'indicizzatore deve elaborare nuovamente tutte le righe in caso di esecuzione in un secondo momento, anche se l'indicizzatore ha già elaborato quasi tutte le righe nel momento in cui è stata interrotta. Per disabilitare la clausola `ORDER BY`, usare l'impostazione `disableOrderByHighWaterMarkColumn` nella definizione dell'indicizzatore:  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Criteri di rilevamento eliminazione colonna di eliminazione temporanea
Quando le righe vengono eliminate dalla tabella di origine, è probabile che si desideri eliminarle anche dall’indice di ricerca. Se si utilizzano i criteri di rilevamento delle modifiche integrati di SQL, questa operazione è automatica. Tuttavia, i criteri di rilevamento delle modifiche limite massimo non sono di supporto all’utente con le righe eliminate. Cosa fare?

Se le righe vengono rimosse fisicamente dalla tabella, la Ricerca di Azure non può dedurre in alcun modo la presenza di record che non esistono più.  Tuttavia, è possibile usare la tecnica di "eliminazione temporanea" per eliminare in modo logico le righe senza rimuoverle dalla tabella. Aggiungere una colonna alla tabella o alla vista e contrassegnare le righe come eliminate tramite la colonna.

Quando si utilizza la tecnica dell’eliminazione temporanea, è possibile specificare la modalità di eliminazione temporanea come segue se si crea o si aggiorna l’origine dati:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

**softDeleteMarkerValue** deve essere una stringa. Usare la rappresentazione stringa del valore effettivo. Ad esempio, se si dispone di una colonna di valori integer in cui le righe eliminate sono contrassegnate con il valore 1, usare `"1"`. Se si dispone di una colonna BIT in cui le righe eliminate sono contrassegnate con il valore booleano true, usare `"True"`.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-search-data-types"></a>Mapping tra tipi di dati SQL e tipi di dati di Ricerca di Azure
| Tipo di dati SQL | Tipi di campi dell'indice di destinazione consentiti | Note |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| real, float |Edm.Double, Edm.String | |
| smallmoney, money decimal numeric |Edm.String |Ricerca di Azure non supporta la conversione di tipi decimali in Edm.Double, perché in tal caso si perderebbe la precisione |
| char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |Una stringa SQL può essere usata per popolare un campo Collection(Edm.String) se la stringa rappresenta una matrice JSON di stringhe: `["red", "white", "blue"]` |
| smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| geography |Edm.GeographyPoint |Sono supportate solo le istanze geografiche di tipo POINT con SRID 4326 (ossia l'impostazione predefinita) |
| rowversion |N/D |Le colonne di versione di riga non possono essere archiviate nell'indice di ricerca, ma possono essere usate per il rilevamento modifiche |
| time, timespan, binary, varbinary, image, xml, geometry, CLR types |N/D |Non supportate |

## <a name="configuration-settings"></a>Impostazioni di configurazione
L'indicizzatore SQL espone diverse impostazioni di configurazione:

| Impostazione | Tipo di dati | Scopo | Valore predefinito |
| --- | --- | --- | --- |
| queryTimeout |string |Imposta il timeout per l'esecuzione di una query SQL |5 minuti ("00:05:00") |
| disableOrderByHighWaterMarkColumn |bool |Fa in modo che la query SQL usata dai criteri di limite massimo ometta la clausola ORDER BY. Vedere [Criteri di limite massimo](#HighWaterMarkPolicy) |false |

Queste impostazioni vengono usate nell'oggetto `parameters.configuration` nella definizione dell'indicizzatore. Ad esempio, per impostare il timeout della query su 10 minuti, creare o aggiornare l'indicizzatore con la seguente configurazione:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>Domande frequenti

**D: Posso usare l'indicizzatore di Azure SQL con i database SQL in esecuzione sulle macchine virtuali IaaS in Azure?**

Sì. Tuttavia, è necessario consentire al servizio di ricerca di connettersi al database. Per altre informazioni, vedere l'articolo [Configurare una connessione da un indicizzatore di Ricerca di Azure a SQL Server in una VM Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) .

**D: Posso usare l'indicizzatore di Azure SQL con i database SQL in esecuzione locale?**

Non direttamente. La connessione diretta non è consigliata né supportata, in quanto richiederebbe l’apertura dei database al traffico Internet. I clienti hanno avuto esito positivo in questo scenario grazie all'uso delle tecnologie bridge come Azure Data Factory. Per altre informazioni vedere [Push dei dati in un indice di Ricerca di Azure con Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector).

**D: Posso usare l'indicizzatore di Azure SQL con database diversi da SQL Server in esecuzione in IaaS in Azure?**

No. Questo scenario non è supportato, in quanto non è stato eseguito il test dell'indicizzatore con database diversi da SQL Server.  

**D: Posso creare più indicizzatori in esecuzione in una pianificazione?**

Sì. Tuttavia, è possibile eseguire un solo indicizzatore per volta in un nodo. Se è necessario eseguire più indicizzatori contemporaneamente, considerare il ridimensionamento del servizio di ricerca a più unità di ricerca.

**D: L’esecuzione di un indicizzatore influisce sul carico di lavoro della query?**

Sì. L'indicizzatore viene eseguito in uno dei nodi del servizio di ricerca e le risorse di tale nodo vengono condivise tra l'indicizzazione e la gestione del traffico di query e altre richieste API. Se si eseguono un'indicizzazione e carichi di lavoro di query intensivi e si verifica una frequenza elevata di errori 503 o un aumento dei tempi di risposta, considerare il [ridimensionamento del servizio di ricerca](search-capacity-planning.md).

**D: Posso usare una replica secondaria in un [cluster di failover](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) come origine dati?**

Dipende. Per l'indicizzazione completa di una tabella o vista, è possibile usare una replica secondaria. 

Per l'indicizzazione incrementale, Ricerca di Azure supporta due criteri di rilevamento delle modifiche: il rilevamento delle modifiche integrato di SQL e il livello più alto.

Nelle repliche di sola lettura il database SQL non supporta il rilevamento delle modifiche integrato. Pertanto, è necessario usare il criterio del livello più alto. 

È consigliabile sempre usare il tipo di dati rowversion per la colonna di livello più alto. Tuttavia, l'uso di rowversion si basa sulla funzione `MIN_ACTIVE_ROWVERSION` del Database SQL, che non è supportata nelle repliche di sola lettura. Pertanto, se si usa rowversion è necessario puntare l'indicizzatore a una replica primaria.

Se si tenta di usare rowversion su una replica di sola lettura, si visualizzerà l'errore seguente: 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**D: Posso usare una colonna diversa, non rowversion, per il rilevamento delle modifiche del livello più alto?**

Non è consigliabile. Solo **rowversion** consente una sincronizzazione dei dati affidabile. Tuttavia, a seconda della logica dell'applicazione, potrebbe essere sicuro:

+ Accertarsi che, durante l'esecuzione dell'indicizzatore, non siano presenti transazioni in sospeso nella tabella che si sta indicizzando. Ad esempio, tutti gli aggiornamenti della tabella vengono eseguiti come batch in una pianificazione e la pianificazione dell'indicizzatore di Ricerca di Azure è impostato per evitare la sovrapposizione con la pianificazione dell'aggiornamento della tabella.  

+ Eseguire periodicamente una reindicizzazione completa per prelevare le righe mancanti. 
