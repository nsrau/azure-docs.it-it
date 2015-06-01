<properties title="" pageTitle="Operazioni sull'indicizzatore (API REST di Ricerca di Azure: 2014-10-20-Preview)" description="Operazioni sull'indicizzatore (API REST di Ricerca di Azure: 2014-10-20-Preview)" metaKeywords="" services="search" solutions="" documentationCenter="" authors="HeidiSteen" manager="mblythe" videoId="" scriptId="" />

<tags ms.service="search" ms.devlang="rest-api" ms.workload="search" ms.topic="article"  ms.tgt_pltfrm="na" ms.date="04/20/2015" ms.author="heidist" />

#Operazioni sull'indicizzatore (API REST di Ricerca di Azure: 2014-10-20-Preview)

> [AZURE.NOTE]Questo articolo descrive un prototipo di nuove funzionalità non presenti nella versione rilasciata dell'API. Altre informazioni sulle versioni e sul supporto sono disponibili in [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx) su MSDN. Per altre informazioni sulle funzionalità disponibili in questa API di anteprima, vedere [Versione dell'API REST di Ricerca di Azure: 2014-10-20-Preview](../search-api-2014-10-20-preview/).

## Panoramica

Ricerca di Azure può integrarsi direttamente con alcune origini dati comuni, eliminando la necessità di scrivere codice per l'indicizzazione dei dati. Per impostare questo servizio, è possibile chiamare l'API di Ricerca di Azure in modo da creare e gestire **indicizzatori** e **origini dati**.

Un'**origine dati** specifica i dati da indicizzare, le credenziali per accedere ai dati e gli hint che consentono a Ricerca di Azure di identificare in modo efficace le modifiche apportate ai dati, ad esempio righe modificate o eliminate in una tabella di database.

Un **indicizzatore** descrive la modalità di flusso dei dati dall'origine dati a un indice di ricerca. Un indicizzatore può eseguire le operazioni seguenti:

- Eseguire una copia occasionale dei dati
- Sincronizzare un indice di Ricerca di Azure con le modifiche nell'origine dati in base a una pianificazione
- Essere chiamato su richiesta in qualsiasi momento per eseguire la copia

Sono attualmente supportate le origini dati seguenti:

- Database SQL di Azure
- DocumentDB 

La possibilità di aggiungere il supporto per altre origini dati è in fase di valutazione. Per contribuire a questa decisione, è possibile fornire commenti e suggerimenti nell'apposito [forum di Ricerca di Azure](http://feedback.azure.com/forums/263029-azure-search).

**NOTA:** le funzionalità descritte in questo articolo sono supportate a partire dalla versione `2014-10-20-Preview` dell'API di Ricerca di Azure.

## Flusso di utilizzo tipico

Di seguito sono indicati i passaggi tipici per impostare l'indicizzazione automatica:

1. Identificare l'origine dati contenente i dati da indicizzare. Tenere presente che è possibile che Ricerca di Azure non supporti tutti i tipi di dati presenti nell'origine dati

2. Creare un indice di Ricerca di Azure con uno schema compatibile con l'origine dati
  
3. Creare un'origine dati di Ricerca di Azure, come descritto nella sezione Operazioni sull'origine dati seguente
  
4. Creare un indicizzatore di Ricerca di Azure e monitorarne l'esecuzione, come descritto nella sezione Operazioni sull'indicizzatore seguente

## Regole di denominazione

- Il nome di un'origine dati deve contenere solo lettere minuscole, cifre o trattini, non può iniziare o terminare con trattini e deve avere una lunghezza massima di 128 caratteri.
- Il nome di un indicizzatore deve contenere solo lettere minuscole, cifre o trattini, non può iniziare o terminare con trattini e deve avere una lunghezza massima di 128 caratteri.

## Limiti e vincoli

Per informazioni dettagliate, vedere la pagina [Limiti e vincoli](http://msdn.microsoft.com/library/azure/dn798934.aspx).

## Operazioni sull'origine dati
È possibile creare e gestire le origini dati in Ricerca di Azure tramite semplici richieste HTTP (POST, GET, PUT, DELETE) su una risorsa origine dati specifica.

### Creare un'origine dati

È possibile creare una nuova origine dati in Ricerca di Azure mediante una richiesta HTTP POST.
	
    POST https://[service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

In alternativa, è possibile usare una richiesta PUT e specificare il nome dell'origine dati nell'URI. Se l'origine dati non esiste, verrà creata.

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]

**Nota**: il numero massimo di origini dati consentite varia in base al livello di prezzo. Nel servizio gratuito sono consentite fino a 3 origini dati. Nel servizio standard sono consentite 50 origini dati.

**Richiesta**

Per tutte le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta di **creazione di un'origine dati** può essere costruita mediante un metodo POST o PUT. Se si usa il metodo POST, è necessario specificare nel corpo della richiesta il nome e la definizione dell'origine dati. Nel caso di PUT, il nome fa parte dell'URL. Se l'origine dati non esiste, viene creata. Se esiste già, viene aggiornata in base alla nuova definizione.

Il nome dell'origine dati deve essere scritto in caratteri minuscoli, iniziare con una lettera o un numero, non deve contenere barre o punti e deve avere una lunghezza inferiore ai 128 caratteri. Dopo l'iniziale costituita da una lettera o un numero, il resto del nome può includere qualsiasi lettera, numero e trattino, purché i trattini non siano consecutivi.

L'elemento `api-version` è obbligatorio. I valori validi includono `2014-10-20-Preview` o versioni successive.

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `Content-Type`: elemento obbligatorio. Impostare il valore su `application/json`.
- `api-key`: elemento obbligatorio. L'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta di **creazione di un'origine dati** deve includere un'intestazione `api-key` impostata sulla chiave amministratore, anziché su una chiave di query. 
 
Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio sia `api-key` dal dashboard servizi nel portale di anteprima di Azure. Per informazioni, vedere [Introduzione a Ricerca di Azure](search-get-started.md).

<a name="CreateDataSourceRequestSyntax"></a> **Sintassi del corpo della richiesta**

Il corpo della richiesta contiene una definizione dell'origine dati, che include il tipo di origine dati, le credenziali per leggere i dati, oltre a criteri facoltativi di rilevamento delle modifiche dei dati e dell'eliminazione dei dati usati per identificare in modo efficiente i dati modificati o eliminati nell'origine dati, quando vengono usati con un indicizzatore pianificato periodicamente.


La sintassi per la strutturazione del payload della richiesta è la seguente: Più avanti in questo argomento è riportata una richiesta di esempio.

    { 
		"name" : "Required for POST, optional for PUT. The name of the data source",
    	"description" : "Optional. Anything you want, or nothing at all",
    	"type" : "Required. Must be 'azuresql' or 'docdb'",
    	"credentials" : { "connectionString" : "Required. Connection string for your Azure SQL database" },
    	"container" : { "name" : "Required. The name of the table or collection you wish to index" },
    	"dataChangeDetectionPolicy" : { Optional. See below for details }, 
    	"dataDeletionDetectionPolicy" : { Optional. See below for details }
	}

La richiesta può contenere le proprietà seguenti:

- `name`: elemento obbligatorio. nome dell'origine dati. Il nome di un'origine dati deve contenere solo lettere minuscole, cifre o trattini, non può iniziare o terminare con trattini e deve avere una lunghezza massima di 128 caratteri.
- `description`: descrizione facoltativa. 
- `type`: elemento obbligatorio. Usare `azuresql` per un'origine dati di SQL Azure e `docdb` per un'origine dati di DocumentDB.
- `container`: 
	- La proprietà `name` obbligatoria specifica la tabella o la visualizzazione (per l'origine dati di SQL Azure) o la raccolta (per l'origine dati di DocumentDB) che sarà indicizzata. 
	- Le origini dati di DocumentDB supportano anche una proprietà `query` facoltativa che consente di specificare una query per rendere flat un documento JSON arbitrario in modo da ottenere uno schema flat che può essere indicizzato da Ricerca di Azure.   
- I criteri facoltativi `dataChangeDetectionPolicy` e `dataDeletionDetectionPolicy` sono descritti di seguito.

<a name="DataChangeDetectionPolicies"></a> **Criteri di rilevamento delle modifiche dei dati**

Scopo dei criteri di rilevamento delle modifiche dei dati è quello di identificare in modo efficace gli elementi di dati modificati. I criteri supportati variano in base al tipo di origine dati. Le sezioni seguenti descrivono ognuno di questi criteri.

***Criteri di rilevamento delle modifiche con limite massimo***

Usare questi criteri quando l'origine dati contiene una colonna o una proprietà che soddisfa le condizioni seguenti:
 
- Tutti gli inserimenti specificano un valore per la colonna. 
- Tutti gli aggiornamenti a un elemento modificano anche il valore della colonna. 
- Il valore di questa colonna aumenta in base alla modifica apportata.
- Le query che usano una clausola di filtro simile a `WHERE [High Water Mark Column] > [Current High Water Mark Value]` possono essere eseguite in modo efficiente.

Ad esempio, quando si usano origini dati di SQL Azure, una colonna `rowversion` indicizzata è il candidato ideale da usare per i criteri con limite massimo.

Quando si usano origini dati di DocumentDB, è necessario usare la proprietà `_ts` specificata da DocumentDB.
 
Questi criteri possono essere specificati come indicato di seguito:

	{ 
		"@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
		"highWaterMarkColumnName" : "[a row version or last_updated column name]" 
	} 

***Criteri di rilevamento delle modifiche integrati di SQL***

Se il database SQL supporta i [criteri di rilevamento delle modifiche integrati di SQL](http://technet.microsoft.com/library/cc280462(v=SQL.105).aspx), è consigliabile usarli. Questi criteri favoriscono il rilevamento delle modifiche più efficiente e consentono a Ricerca di Azure di identificare le righe eliminate senza dover includere nello schema una colonna di "eliminazione temporanea" esplicita.

I criteri di rilevamento delle modifiche integrati di SQL sono supportati a partire dalle versioni del database SQL seguenti: - SQL Server 2008 R2, se si usano macchine virtuali di IaaS per SQL - Versione 12 del database SQL di Azure, se si usa SQL Azure.

**NOTA:** quando si usano i criteri di rilevamento delle modifiche integrati di SQL, non specificare criteri di rilevamento dell'eliminazione dei dati separati, perché questi ultimi includono il supporto predefinito per l'identificazione delle righe eliminate.

**NOTA:** questi criteri possono essere usati solo con le tabelle, ma non con le visualizzazioni. Prima di poter usare questi criteri, è necessario abilitare il rilevamento delle modifiche per la tabella in uso.
 
I criteri di rilevamento delle modifiche integrati di SQL possono essere specificati come indicato di seguito:

	{ 
		"@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
	}

<a name="DataDeletionDetectionPolicies"></a> **Criteri di rilevamento dell'eliminazione dei dati**

Scopo dei criteri di rilevamento dell'eliminazione dei dati è quello di identificare in modo efficace gli elementi di dati eliminati. Attualmente, gli unici criteri supportati sono i criteri `Soft Delete`, che è possibile specificare come indicato di seguito:

	{ 
		"@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
		"softDeleteColumnName" : "the column that specifies whether a row was deleted", 
		"softDeleteMarkerValue" : "the value that identifies a row as deleted" 
	}

<a name="CreateDataSourceRequestExamples"></a> **Esempi del corpo della richiesta**

Se si intende usare l'origine dati con un indicizzatore eseguito in una pianificazione, questo esempio illustra come specificare gli hint dei criteri:

    { 
		"name" : "asqldatasource",
		"description" : "a description",
    	"type" : "azuresql",
    	"credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
    	"container" : { "name" : "sometable" },
    	"dataChangeDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy", "highWaterMarkColumnName" : "RowVersion" }, 
    	"dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : true }
	}

Se si intende usare solo l'origine dati per una copia occasionale dei dati, è possibile omettere gli hint dei criteri:

    { 
		"name" : "asqldatasource",
    	"description" : "anything you want, or nothing at all",
    	"type" : "azuresql",
    	"credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
    	"container" : { "name" : "sometable" }
	} 

**Risposta**

Se la richiesta ha esito positivo, viene restituito il codice di stato 201 - Creato.

### Aggiornare un'origine dati

È possibile aggiornare un'origine dati esistente mediante una richiesta HTTP PUT. È necessario specificare il nome dell'origine dati da aggiornare nell'URI della richiesta:

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Richiesta** La sintassi del corpo della richiesta è analoga a quella indicata nell'operazione di [creazione di un'origine dati](#CreateDataSourceRequestSyntax).

**Risposta** Se la richiesta ha esito positivo, viene restituito il codice di stato 201 - Creato se è stata creata una nuova origine dati, e il codice di stato 204 - Nessun contenuto se è stata aggiornata un'origine dati esistente.

**NOTA:** alcune proprietà non possono essere aggiornate in un'origine dati esistente. Ad esempio, non è possibile modificare il tipo di un'origine dati esistente.

### Elencare le origini dati

L'operazione per **elencare le origini dati** restituisce un elenco delle origini dati disponibili in Ricerca di Azure.

    GET https://[service name].search.windows.net/datasources?api-version=[api-version]
    api-key: [admin key]

**Risposta**

Se la richiesta ha esito positivo, viene restituito il codice di stato 200 - OK.

Di seguito è riportato il corpo di una risposta di esempio:

    {
      "value" : [
        {
          "name": "datasource1",
          "type": "azuresql",
		  ... other data source properties
        }]
    }

Si noti che è possibile filtrare la risposta fino a visualizzare solo le proprietà a cui si è interessati. Ad esempio, se si vuole solo un elenco di nomi di origini dati, usare l'opzione di query `$select` di OData:

    GET /datasources?api-version=2014-10-20-Preview&$select=name

In questo caso, la risposta dell'esempio precedente sarà simile alla seguente:

    {
      "value" : [ { "name": "datasource1" }, ... ]
    }

Questa è una tecnica utile per risparmiare larghezza di banda, se nel servizio di ricerca sono presenti numerose origini dati.

### Ottenere un'origine dati

L'operazione per **ottenere un'origine dati** ottiene la definizione dell'origine dati da Ricerca di Azure.

    GET https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 200 OK.

La risposta è simile agli esempi nell'operazione di [creazione di un'origine dati](#CreateDataSourceRequestExamples):

	{ 
		"name" : "asqldatasource",
		"description" : "a description",
    	"type" : "azuresql",
    	"credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
    	"container" : { "name" : "sometable" },
    	"dataChangeDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
			"highWaterMarkColumnName" : "RowVersion" }, 
    	"dataDeletionDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
			"softDeleteColumnName" : "IsDeleted", 
			"softDeleteMarkerValue" : true }
	}

**NOTA:** non impostare l'intestazione della richiesta `Accept` su `application/json;odata.metadata=none` quando si chiama questa API, in quanto tale operazione comporterebbe l'omissione dell'attributo `@odata.type` dalla risposta e sarebbe impossibile distinguere tra i criteri di rilevamento dell'eliminazione dei dati e i criteri di rilevamento della modifica dei dati di tipi diversi.

### Eliminare un'origine dati

L'operazione di **eliminazione di un'origine dati** rimuove un'origine dati da Ricerca di Azure.

    DELETE https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

**NOTA:** se alcuni indicizzatori fanno riferimento all'origine dati da eliminare, l'operazione di eliminazione continuerà comunque. Questi indicizzatori, tuttavia, effettueranno una transizione a uno stato di errore all'esecuzione successiva.

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 204 Nessun contenuto.

## Operazioni sull'indicizzatore

Un indicizzatore è la risorsa che connette le origini dati agli indici di ricerca di destinazione. È consigliabile pianificare la creazione di un indicizzatore per ogni combinazione di indice di destinazione e origine dati. È possibile che più indicizzatori eseguano operazioni di scrittura nello stesso indice. Tuttavia, un indicizzatore può eseguire operazioni di scrittura in un solo indice.

È possibile creare e gestire gli indicizzatori tramite semplici richieste HTTP (POST, GET, PUT, DELETE) su una risorsa indicizzatore specifica.

Dopo aver creato un indicizzatore, è possibile ottenere il relativo stato di esecuzione mediante l'operazione per [ottenere lo stato di un indicizzatore](#GetIndexerStatus). È inoltre possibile eseguire un indicizzatore in qualsiasi momento (anziché o oltre a eseguirlo periodicamente in base a una pianificazione) usando l'operazione di [esecuzione di un indicizzatore](#RunIndexer).

### Creare un indicizzatore

È possibile creare un nuovo indicizzatore in Ricerca di Azure mediante una richiesta HTTP POST.
	
    POST https://[service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

In alternativa, è possibile usare una richiesta PUT e specificare il nome dell'origine dati nell'URI. Se l'origine dati non esiste, verrà creata.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]

**Nota**: il numero massimo di indicizzatori consentiti varia in base al livello di prezzo. Nel servizio gratuito sono consentiti fino a 3 indicizzatori. Nel servizio standard sono consentiti 50 indicizzatori.

<a name="CreateIndexerRequestSyntax"></a> **Sintassi del corpo della richiesta**

Il corpo della richiesta contiene una definizione di indicizzatore, che specifica l'origine dati e l'indice di destinazione per l'indicizzazione, nonché una pianificazione e pianificazioni di indicizzazione facoltativi.


La sintassi per la strutturazione del payload della richiesta è la seguente: Più avanti in questo argomento è riportata una richiesta di esempio.

    { 
		"name" : "Required for POST, optional for PUT. The name of the indexer",
    	"description" : "Optional. Anything you want, or null",
    	"dataSourceName" : "Required. The name of an existing data source",
        "targetIndexName" : "Required. The name of an existing index",
        "schedule" : { Optional. See Indexing Schedule below. },
        "parameters" : { Optional. See Indexing Parameters below. }
	}

**Pianificazione dell'indicizzazione**

Facoltativamente, un indicizzatore può specificare una pianificazione. Se è presente una pianificazione, l'indicizzatore verrà eseguito periodicamente in base alla pianificazione. La pianificazione ha gli attributi seguenti:

- `interval`: elemento obbligatorio. Valore di durata che specifica un intervallo o un periodo per l'esecuzione dell'indicizzatore. L'intervallo minimo consentito è di 5 minuti, quello massimo di un giorno. Il valore deve essere formattato come valore XSD "dayTimeDuration" (un subset limitato di un valore di durata [ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)). Il modello è: `P(nD)(T(nH)(nM))`. Esempi: `PT15M` per indicare l'esecuzione ogni 15 minuti, `PT2H` per indicare l'esecuzione ogni 2 ore. 

- `startTime`: elemento obbligatorio. Data e ora UTC di inizio dell'esecuzione dell'indicizzatore.

**Parametri dell'indicizzazione**

Un indicizzatore può facoltativamente specificare diversi parametri che ne influenzano il comportamento. Tutti i parametri sono facoltativi.

- `maxFailedItems`: numero di elementi per cui l'indicizzazione può avere esito negativo prima che l'indicizzatore venga considerato in errore. Il valore predefinito è `0`. Le informazioni sugli elementi non riusciti vengono restituite dall'operazione per [ottenere lo stato di un indicizzatore](#GetIndexerStatus). 

- `maxFailedItemsPerBatch`: numero di elementi per cui l'indicizzazione può avere esito negativo in ogni batch prima che l'indicizzatore venga considerato in errore. Il valore predefinito è `0`.

- `base64EncodeKeys`: specifica se le chiavi del documento saranno codificate in base 64. Ricerca di Azure impone restrizioni sui caratteri che possono essere presenti in una chiave del documento. Tuttavia, i valori nei dati di origine possono contenere caratteri non validi. Se è necessario indicizzare questi valori come chiavi del documento, questo contrassegno può essere impostato su true. Il valore predefinito è `false`.

<a name="CreateIndexerRequestExamples"></a> **Esempi del corpo della richiesta**

L'esempio seguente crea un indicizzatore che copia i dati dalla tabella a cui fa riferimento l'origine dati `ordersds` all'indice `orders` in base a una pianificazione che inizia l'1 gennaio 2015 UTC e viene eseguita ogni ora. Ogni chiamata all'indicizzatore avrà esito positivo se l'indicizzazione non ha esito negativo per più di 5 elementi in ogni batch e per più di 10 elementi in totale.

	{
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
	}

**Risposta**

Se la richiesta ha esito positivo, viene restituito il codice di stato 201 - Creato.

### Aggiornare un indicizzatore

È possibile aggiornare un indicizzatore esistente mediante una richiesta HTTP PUT. Nell'URI della richiesta viene specificato il nome dell'indicizzatore da aggiornare:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Richiesta**

La sintassi del corpo della richiesta è analoga a quella dell'operazione di [creazione di un indicizzatore](#CreateIndexerRequestSyntax).

**Risposta**

Se la richiesta ha esito positivo, viene restituito il codice di stato 201 - Creato se è stato creato un nuovo indicizzatore, e il codice di stato 204 - Nessun contenuto se è stato aggiornato un indicizzatore esistente.

### Elencare gli indicizzatori

L'operazione per **elencare gli indicizzatori** restituisce l'elenco di indicizzatori in Ricerca di Azure.

    GET https://[service name].search.windows.net/indexers?api-version=[api-version]
    api-key: [admin key]

**Risposta**

Se la richiesta ha esito positivo, viene restituito il codice di stato 200 - OK.

Di seguito è riportato il corpo di una risposta di esempio:

    {
      "value" : [
      {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        ... other indexer properties
	  }]
    }

Si noti che è possibile filtrare la risposta fino a visualizzare solo le proprietà a cui si è interessati. Ad esempio, se si vuole solo un elenco di nomi di indicizzatori, usare l'opzione di query `$select` di OData:

    GET /indexers?api-version=2014-10-20-Preview&$select=name

In questo caso, la risposta dell'esempio precedente sarà simile alla seguente:

    {
      "value" : [ { "name": "myindexer" } ]
    }

Questa è una tecnica utile per risparmiare larghezza di banda, se nel servizio di ricerca sono presenti numerosi indicizzatori.

### Ottenere un indicizzatore

L'operazione per **ottenere un indicizzatore** recupera la definizione dell'indicizzatore da Ricerca di Azure.

    GET https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 200 OK.

La risposta è simile agli esempi nella richiesta di [creazione di un indicizzatore](#CreateIndexerRequestExamples):

	{
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
	}

### Eliminare un indicizzatore

L'operazione di **eliminazione di un indicizzatore** rimuove un indicizzatore da Ricerca di Azure.

    DELETE https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

Quando viene eliminato un indicizzatore, le esecuzioni dell'indicizzatore in corso verranno completate, ma non verranno pianificate altre esecuzioni. I tentativi di utilizzare un indicizzatore inesistente restituiscono un codice di stato HTTP 404 Pagina non trovata.
 
**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 204 Nessun contenuto.

<a name="RunIndexer"></a>
### Eseguire un indicizzatore

Oltre a essere eseguito periodicamente in base a una pianificazione, un indicizzatore può anche essere chiamato su richiesta tramite l'operazione di **esecuzione di un indicizzatore**:

	POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [admin key]

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 202 Accettato.

<a name="GetIndexerStatus"></a>
### Ottenere lo stato di un indicizzatore

L'operazione per **ottenere lo stato di un indicizzatore** recupera la cronologia di esecuzione e lo stato corrente di un indicizzatore:

	GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [admin key]

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 200 OK.

Il corpo della risposta contiene informazioni sullo stato di integrità globale dell'indicizzatore, l'ultima chiamata all'indicizzatore, nonché la cronologia delle chiamate recenti, se presenti.

Un corpo della risposta di esempio è simile al seguente:

	{
		"status":"running",
		"lastResult": {
			"status":"success",
			"errorMessage":null,
			"startTime":"2014-11-26T03:37:18.853Z",
			"endTime":"2014-11-26T03:37:19.012Z",
			"errors":[],
			"itemsProcessed":11,
			"itemsFailed":0,
			"initialTrackingState":null,
			"finalTrackingState":null
         },
		"executionHistory":[ {
			"status":"success",
         	"errorMessage":null,
			"startTime":"2014-11-26T03:37:18.853Z",
			"endTime":"2014-11-26T03:37:19.012Z",
			"errors":[],
			"itemsProcessed":11,
			"itemsFailed":0,
			"initialTrackingState":null,
			"finalTrackingState":null
		}]
	}

**Stato dell'indicizzatore**

Lo stato dell'indicizzatore può avere uno dei valori seguenti:

- `running` indica che l'indicizzatore viene eseguito normalmente. Si noti che è possibile che si verifichino comunque errori nelle esecuzioni dell'indicizzatore. È quindi consigliabile controllare anche la proprietà `lastResult`. 

- `error` indica che nell'indicizzatore si è verificato un errore che non può essere corretto senza un intervento da parte dell'utente. È ad esempio possibile che le credenziali dell'origine dati siano scadute o che lo schema dell'origine dati o dell'indice di destinazione sia cambiato in modo significativo.

**Risultato dell'esecuzione dell'indicizzatore**

Il risultato dell'esecuzione dell'indicizzatore contiene informazioni su una singola esecuzione dell'indicizzatore. Il risultato più recente viene esposto come proprietà `lastResult` dello stato dell'indicizzatore. Altri risultati recenti, se presenti, vengono restituiti come proprietà `executionHistory` dello stato dell'indicizzatore.

Il risultato dell'esecuzione dell'indicizzatore contiene le proprietà seguenti:

- `status`: stato di questa esecuzione. Per informazioni dettagliate, vedere [Stato di esecuzione dell'indicizzatore](#IndexerExecutionStatus) più avanti. 

- `errorMessage`: messaggio di errore per un'esecuzione non riuscita.

- `startTime`: ora UTC di avvio dell'esecuzione.

- `endTime`: ora UTC di fine dell'esecuzione. Questo valore non viene impostato se l'esecuzione è ancora in corso.

- `errors`: elenco di errori a livello di elemento, se presenti.

- `itemsProcessed`: numero di elementi di origine dati (ad esempio, righe di tabella) che l'indicizzatore ha tentato di indicizzare durante questa esecuzione.

- `itemsFailed`: numero di elementi non riusciti durante questa esecuzione.
 
- `initialTrackingState`: sempre `null` per la prima esecuzione dell'indicizzatore o se i criteri di rilevamento delle modifiche dei dati non sono abilitati sull'origine dati usata. Se i criteri sono abilitati, nelle esecuzioni successive questo valore indica il primo (minimo) valore di rilevamento delle modifiche elaborato da questa esecuzione.

- `finalTrackingState`: sempre `null` se i criteri di rilevamento delle modifiche dei dati non sono abilitati sull'origine dati usata. In caso contrario, indica l'ultimo (massimo) valore di rilevamento delle modifiche elaborato correttamente da questa esecuzione.

<a name="IndexerExecutionStatus"></a> **Stato di esecuzione dell'indicizzatore**

Lo stato di esecuzione dell'indicizzatore acquisisce lo stato di una singola esecuzione dell'indicizzatore. I valori possibili sono i seguenti:

- `success` indica che l'esecuzione dell'indicizzatore è stata completata correttamente.

- `inProgress` indica che l'esecuzione dell'indicizzatore è in corso.

- `transientFailure` indica che la chiamata all'indicizzatore non è riuscita, ma l'errore può essere temporaneo. Le chiamate all'indicizzatore continuano in base alla pianificazione, se presente.

- `persistentFailure` indica che l'indicizzatore non è riuscito ed è richiesto l'intervento dell'utente (ad esempio, a causa di incompatibilità di schemi tra l'origine dati e l'indice di destinazione). Le esecuzioni pianificate dell'indicizzatore vengono arrestate. È richiesto l'intervento dell'utente per risolvere il problema (descritto nella proprietà `errorMessage`) e riavviare l'esecuzione dell'indicizzatore.

- `reset` indica che l'indicizzatore è stato reimpostato tramite una chiamata all'API di reimpostazione di un indicizzatore (vedere la sezione seguente).

<a name="ResetIndexer"></a>
### Reimpostare un indicizzatore

L'operazione di **reimpostazione di un indicizzatore** reimposta lo stato di rilevamento delle modifiche associato all'indicizzatore. Questo consente di attivare la reindicizzazione da zero, ad esempio se è cambiato lo schema dell'origine dati, oppure di modificare i criteri di rilevamento delle modifiche per un'origine dati associata all'indicizzatore.

	POST https://[service name].search.windows.net/indexers/[indexer name]/reset?api-version=[api-version]
    api-key: [admin key]

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 204 Nessun contenuto.

## Mapping tra tipi di dati SQL e tipi di dati di Ricerca di Azure

<table style="font-size:12">
<tr>
<td>Tipo di dati SQL</td>	
<td>Tipi di campi dell'indice di destinazione consentiti</td>
<td>Note</td>
</tr>
<tr>
<td>bit</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>int, smallint, tinyint</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>bigint</td>
<td>Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>real, float</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>smallmoney, money<br>decimal<br>numeric
</td>
<td>Edm.String</td>
<td>Ricerca di Azure non supporta la conversione di tipi decimali in Edm.Double, perché in tal caso si perderebbe la precisione
</td>
</tr>
<tr>
<td>char, nchar, varchar, nvarchar</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>smalldatetime, datetime, datetime2, date, datetimeoffset</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>uniqueidentifer</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>rowversion</td>
<td>N/D</td>
<td>Le colonne di versione di riga non possono essere archiviate nell'indice di ricerca, ma possono essere usate per il rilevamento modifiche</td>
</tr>
<tr>
<td>time, timespan<br>binary, varbinary, image<br>xml<br>geometry<br> geography<br>Tipi CLR</td>
<td>N/D</td>
<td>Non supportato</td>
</tr>
</table>

## Mapping tra tipi di dati JSON e tipi di dati di Ricerca di Azure

<table style="font-size:12">
<tr>
<td>Tipo di dati JSON</td>	
<td>Tipi di campi dell'indice di destinazione consentiti</td>
<td>Note</td>
</tr>
<tr>
<td>bool</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Numeri integrali</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td>JSON non ha valori integer tipizzati, è quindi necessario presupporre il più ampio: valori integer a 64 bit</td>
</tr>
<tr>
<td>Numeri a virgola mobile</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>stringa</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>Matrici di tipi primitivi, ad esempio "a", "b", "c"</td>
<td>Collection(Edm.String)</td>
<td></td>
</tr>
<tr>
<td>Stringhe che rappresentano date</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Oggetti JSON</td>
<td>N/D</td>
<td>Non supportati. Ricerca di Azure attualmente supporta solo tipi primitivi e raccolte di stringhe</td>
</tr>
</table>
<!--HONumber=54-->