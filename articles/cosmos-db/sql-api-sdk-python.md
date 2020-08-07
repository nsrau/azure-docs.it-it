---
title: 'Azure Cosmos DB: API Python, risorse e SDK per SQL'
description: Informazioni complete sull'SDK e sull'API Python per SQL, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB Python SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 08/05/2020
ms.author: anfeldma
ms.custom: devx-track-python
ms.openlocfilehash: 44d9521e9d02195cb1d4ff61fd519f31ce9c0018
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876258"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Python SDK di Azure Cosmos DB per l'API SQL: note sulla versione e risorse

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [SDK di feed di modifiche .NET v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data](sql-api-sdk-java-spring.md)
> * [Connettore Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Provider di risorse REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor in blocco-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Esecuzione bulk - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Download dell'SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**Documentazione sull'API**|[Documentazione di riferimento delle API di Python](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**Istruzioni per l'installazione dell'SDK**|[Istruzioni per l'installazione dell'SDK di Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)|
|**Operazioni preliminari**|[Introduzione all'SDK di Python](create-sql-api-python.md)|
|**Piattaforma attualmente supportata**|[Python 2.7](https://www.python.org/downloads/) e [Python 3.5.3 e versioni successive](https://www.python.org/downloads/)|

## <a name="release-history"></a>Cronologia delle versioni

### <a name="400"></a>4.0.0

* Versione stabile.
* Aggiunta di HttpLoggingPolicy alla pipeline per consentire il passaggio di un logger personalizzato per le intestazioni di richiesta e risposta.

### <a name="400b6"></a>4.0.0b6

* Correzione del bug in synchronized_request per le API di file multimediali.
* Rimozione di MediaReadMode e MediaRequestTimeout da ConnectionPolicy perché le richieste di file multimediali non sono supportate.

### <a name="400b5"></a>4.0.0b5

* Deprecazione del modulo azure.cosmos.errors, sostituito con azure.cosmos.exceptions
* I parametri della condizione di accesso (`access_condition`, `if_match`, `if_none_match`) sono stati deprecati a favore di parametri `match_condition` e `etag` distinti.
* Correzione del bug nel provider della mappa di routing.
* Aggiunta del supporto per query Distinct, Offset e Limit.
* Contesto di esecuzione di query del documento predefinito ora usato per

  * Query ChangeFeed
  * Query a partizione singola (partitionkey, partitionKeyRangeId è presente nelle opzioni)
  * Query non di tipo documento

* Errori per le aggregazioni in più partizioni, con l'abilitazione della query tra partizioni impostata su true, ma nessuna parola chiave "value" presente
* Endpoint del piano di query dei riscontri per altri scenari per recuperare il piano di query
* Aggiunta del supporto di `__repr__` per gli oggetti entità di Cosmos.
* Aggiornamento della documentazione.

### <a name="400b4"></a>4.0.0b4

* Aggiunta del supporto per un argomento di parola chiave `timeout` in tutte le operazioni per specificare un timeout assoluto in secondi entro il quale l'operazione deve essere completata. Se il valore di timeout viene superato, verrà generato un errore `azure.cosmos.errors.CosmosClientTimeoutError`.

* Aggiunta di un nuovo criterio `ConnectionRetryPolicy` per gestire il comportamento dei tentativi durante gli errori di connessione HTTP.

* Aggiunta di nuovi argomenti di parola chiave di configurazione per costruttore e per singola operazione:

  * `retry_total`: numero massimo di tentativi.
  * `retry_backoff_max`: tempo massimo di attesa tra tentativi in secondi.
  * `retry_fixed_interval`: intervallo fisso tra tentativi in millisecondi.
  * `retry_read`: numero massimo di tentativi di lettura socket.
  * `retry_connect`: numero massimo di tentativi in caso di errori di connessione.
  * `retry_status`: numero massimo di tentativi in seguito a codici di stato di errore.
  * `retry_on_status_codes`: elenco di codici di stato specifici da usare per nuovi tentativi.
  * `retry_backoff_factor`: fattore per calcolare il tempo di attesa tra tentativi.

### <a name="400b3"></a>4.0.0b3

* Aggiunta delle funzionalità `create_database_if_not_exists()` e `create_container_if_not_exists` rispettivamente a CosmosClient e a Database.

### <a name="400b2"></a>4.0.0b2

* La versione 4.0.0b2 rappresenta il secondo tentativo di creare una libreria client adatta alle procedure consigliate del linguaggio Python.

**Modifiche di rilievo**

* La connessione client è stata adattata in modo da utilizzare la pipeline HTTP definita in `azure.core.pipeline`.

* Gli oggetti interattivi sono stati rinominati come proxy. Ad esempio:

  * `Database` -> `DatabaseProxy`
  * `User` -> `UserProxy`
  * `Container` -> `ContainerProxy`
  * `Scripts` -> `ScriptsProxy`

* Il costruttore di `CosmosClient` è stato aggiornato:

  * Il parametro `auth` è stato rinominato in `credential` e ora accetta direttamente un tipo di autenticazione. Questo significa che è possibile passare il valore della chiave master, un dizionario di token di risorsa o un elenco di autorizzazioni. Il formato del dizionario precedente è però ancora supportato.

  * Il parametro `connection_policy` è stato convertito in parametro costituito solo da parole chiave e, anche se è ancora supportato, è ora possibile passare ognuno dei singoli attributi del criterio come argomento di parola chiave esplicito:

    * `request_timeout`
    * `media_request_timeout`
    * `connection_mode`
    * `media_read_mode`
    * `proxy_config`
    * `enable_endpoint_discovery`
    * `preferred_locations`
    * `multiple_write_locations`

* È stato aggiunto un nuovo costruttore a `CosmosClient` per consentire la creazione tramite una stringa di connessione recuperata dal portale di Azure.

* Alcune operazioni di `read_all` sono state rinominate in `list`:

  * `CosmosClient.read_all_databases` -> `CosmosClient.list_databases`
  * `Container.read_all_conflicts` -> `ContainerProxy.list_conflicts`
  * `Database.read_all_containers` -> `DatabaseProxy.list_containers`
  * `Database.read_all_users` -> `DatabaseProxy.list_users`
  * `User.read_all_permissions` -> `UserProxy.list_permissions`

* Tutte le operazioni che accettano parametri `request_options` o `feed_options` sono state convertite in parametri costituiti solo da parole chiave. Anche se questi dizionari di opzioni sono ancora supportati, inoltre, ognuna delle singole opzioni all'interno del dizionario è ora supportata come argomento di parola chiave esplicito.

* La gerarchia degli errori viene ora ereditata da `azure.core.AzureError` :

  * `HTTPFailure` è stata rinominato in `CosmosHttpResponseError`
  * `JSONParseFailure` è stato rimosso e sostituito da `azure.core.DecodeError`
  * Sono stati aggiunti altri errori per codici di risposta specifici:
    * `CosmosResourceNotFoundError` per lo stato 404
    * `CosmosResourceExistsError` per lo stato 409
    * `CosmosAccessConditionFailedError` per lo stato 412

* È ora possibile eseguire `CosmosClient` in un gestore di contesto per gestire la chiusura della connessione client.

* Le risposte iterabili, ad esempio le risposte a query e le risposte a elenco, sono ora di tipo `azure.core.paging.ItemPaged`. Il metodo `fetch_next_block` è stato sostituito da un iteratore secondario, accessibile dal metodo `by_page`.

### <a name="400b1"></a>4.0.0b1

La versione 4.0.0b1 rappresenta il primo tentativo di creare una libreria client intuitiva adatta alle procedure consigliate del linguaggio Python. Per altre informazioni su questa e sulle versioni di anteprima di altre librerie di Azure SDK, vedere https://aka.ms/azure-sdk-preview1-python.

**Modifiche di rilievo: struttura della nuova API**

* Le operazioni sono ora limitate a un client specifico:

  * `CosmosClient`: questo client gestisce le operazioni a livello di account, tra cui la gestione delle proprietà del servizio e la definizione dell'elenco dei database all'interno di un account.
  * `Database`: questo client gestisce le operazioni a livello di database, tra cui la creazione e l'eliminazione di contenitori, utenti e stored procedure. È possibile accedervi da un'istanza di `cosmosClient` in base al nome.
  * `Container`: questo client gestisce le operazioni per un contenitore specifico, tra cui l'esecuzione di query e l'inserimento di elementi, oltre alla gestione delle proprietà.
  * `User`: questo client gestisce le operazioni per un utente specifico, tra cui l'aggiunta e l'eliminazione di autorizzazioni e la gestione delle proprietà utente.

    Per accedere a questi client, è possibile spostarsi verso il basso nella gerarchia client usando il metodo `get_<child>_client`. Per tutti i dettagli sulla nuova API, vedere la [documentazione di riferimento](https://aka.ms/azsdk-python-cosmos-ref).

* L'accesso ai client viene eseguito tramite il nome anziché l'ID. Non è necessario concatenare le stringhe per creare collegamenti.

* Non è più necessario importare tipi e metodi dai singoli moduli. La superficie di attacco dell'API pubblica è disponibile direttamente nel pacchetto `azure.cosmos`.

* Le singole proprietà della richiesta possono essere fornite come argomenti di parola chiave invece di creare un'istanza separata di `RequestOptions`.

### <a name="302"></a>3.0.2

* Aggiunta del supporto per il tipo di dati MultiPolygon
* Correzione di bug nei criteri di ripetizione di lettura della sessione
* Correzione di bug relativo al riempimento non corretto durante la decodifica di stringhe in base 64

### <a name="301"></a>3.0.1

* Correzione di bug in LocationCache
* Correzione di bug nella logica di ripetizione degli endpoint
* Correzione della documentazione

### <a name="300"></a>3.0.0

* Aggiunta del supporto per la scrittura in più aree
* Modifiche relative all'assegnazione dei nomi
  * Da DocumentClient a CosmosClient
  * Da Collection a Container
  * Da Document a Item
  * Nome del pacchetto aggiornato in "azure-cosmos"
  * Spazio dei nomi aggiornato in "azure.cosmos"

### <a name="233"></a>2.3.3

* Aggiunta del supporto per proxy
* Aggiunta del supporto per il feed di modifiche in lettura
* Aggiunta del supporto per l'intestazione delle quote delle raccolte
* Correzione di bug relativi ai token di sessioni di grandi dimensioni
* Correzione di bug relativi all'API ReadMedia
* Correzione di bug nella cache dell'intervallo di chiavi di partizione

### <a name="232"></a>2.3.2

* Aggiunta del supporto per nuovi tentativi predefiniti in caso di problemi di connessione.

### <a name="231"></a>2.3.1

* Documentazione aggiornata con riferimento a Azure Cosmos DB anziché Azure DocumentDB.

### <a name="230"></a>2.3.0

* Questa versione dell'SDK richiede la versione più recente dell'emulatore di Azure Cosmos DB che è possibile scaricare dalla pagina https://aka.ms/cosmosdb-emulator.

### <a name="221"></a>2.2.1

* Correzione di bug per la funzione dict aggregate
* Correzione di bug per la rimozione di barre iniziali nel collegamento alla risorsa
* Test per la codifica Unicode

### <a name="220"></a>2.2.0

* Aggiunta del supporto per la funzionalità di unità richiesta al minuto (UR/min).
* Aggiunta del supporto per un nuovo livello di coerenza denominato ConsistentPrefix.

### <a name="210"></a>2.1.0

* Aggiunta del supporto per le query di aggregazione (COUNT, MIN, MAX, SUM e AVG).
* Aggiunta un'opzione che consente di disabilitare la verifica SSL durante l'esecuzione nell'emulatore DocumentDB.
* Rimossa la restrizione per cui il modulo delle richieste dipendenti deve essere esattamente 2.10.0.
* Velocità effettiva minima ridotta nelle raccolte partizionate da 10.100 UR/s a 2.500 UR/s.
* Aggiunta del supporto per l'abilitazione della registrazione degli script durante l'esecuzione di stored procedure.
* Versione API REST incrementata a "2017-01-19" con questo rilascio.

### <a name="201"></a>2.0.1

* Modifiche editoriali ai commenti alla documentazione.

### <a name="200"></a>2.0.0

* Aggiunto il supporto per Python 3.5.
* Aggiunto il supporto per il pool di connessioni con il modulo di richiesta.
* Aggiunto il supporto per la coerenza di sessione.
* Aggiunto il supporto per le query TOP/ORDERBY per le raccolte partizionate.

### <a name="190"></a>1.9.0

* Aggiunta del supporto per il criterio di ripetizione dei tentativi delle richieste limitate (le richieste limitate ricevano un'eccezione troppo grande per la frequenza delle richieste, con codice di errore 429). Per impostazione predefinita, DocumentDB esegue nove tentativi per ogni richiesta quando viene rilevato il codice di errore 429, rispettando il tempo di RetryAfter nell'intestazione della risposta.
  Adesso è possibile impostare un intervallo di tempo fisso per i tentativi come parte della proprietà RetryOptions nell'oggetto ConnectionPolicy se si desidera ignorare il tempo di retryAfter restituito dal server tra i tentativi.
  Ora, DocumentDB attende al massimo 30 secondi per ciascuna richiesta che viene limitata (indipendentemente dal numero di tentativi) e restituisce la risposta con il codice di errore 429.
  Questo tempo può essere sottoposto a override nella proprietà RetryOptions dell'oggetto ConnectionPolicy.

* DocumentDB restituisce ora i parametri x-ms-throttle-retry-count e x-ms-throttle-retry-wait-time-ms come intestazioni di risposta in ogni richiesta per indicare il conteggio dei tentativi di limitazione di ripetere conteggio e il tempo cumulativo di attesa della richiesta tra i tentativi.

* Rimozione della classe RetryPolicy e della proprietà corrispondente (retry_policy) esposta nella classe document_client e introduzione di una classe RetryOptions che espone la proprietà RetryOptions nella classe ConnectionPolicy che può essere utilizzata per eseguire l’override di alcune opzioni di ripetizione dei tentativi predefinite.

### <a name="180"></a>1.8.0

* Aggiunta del supporto per gli account di database con replica geografica.
* Correzioni di test per spostare l'host globale e masterKey nelle singole classi di test.

### <a name="170"></a>1.7.0

* Aggiunta del supporto per la funzionalità di durata (TTL) relativa ai documenti.

### <a name="161"></a>1.6.1

* Correzioni di bug relativi al partizionamento lato server per consentire caratteri speciali nel percorso partitionkey.

### <a name="160"></a>1.6.0

* Aggiunta del supporto per la funzionalità delle raccolte partizionate sul lato server.

### <a name="150"></a>1.5.0

* Aggiunta del framework di partizionamento orizzontale sul lato client all'SDK. Implementazione delle classi HashPartionResolver e RangePartitionResolver.

### <a name="142"></a>1.4.2

* Implementazione di Upsert. Nuovi metodi upsertXXX aggiunti per supportare la funzionalità Upsert.
* Implementazione del routing basato su ID. Nessuna modifica API pubblica, tutte modifiche interne.

### <a name="130"></a>1.3.0

* Versione ignorata per riportare il numero di versione in allineamento con altri SDK

### <a name="120"></a>1.2.0

* Supporta l'indice geospaziale.
* Convalida della proprietà ID per tutte le risorse. Gli ID per le risorse non possono contenere i caratteri `?, /, #, \\` o terminare con uno spazio.
* Aggiunge la nuova intestazione "stato di trasformazione dell'indice" a ResourceResponse.

### <a name="110"></a>1.1.0

* Implementa criteri di indicizzazione V2

### <a name="101"></a>1.0.1

* Supporto della connessione proxy

## <a name="release--retirement-dates"></a>Date di rilascio e di ritiro

Microsoft invia una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata. Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente. È quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima.

> [!WARNING]
> Dopo il 31 agosto 2022, Azure Cosmos DB non effettueranno più correzioni di bug, aggiungono nuove funzionalità e forniranno il supporto per le versioni 1. x o 2. x dell'API Azure Cosmos DB Python SDK per SQL. Se si preferisce non eseguire l'aggiornamento, le richieste inviate dalla versione 1. x e 2. x dell'SDK continueranno a essere gestite dal servizio Azure Cosmos DB.

| Versione | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [4.0.0](#400) |20 maggio 2020 |--- |
| [3.0.2](#302) |15 novembre 2018 |--- |
| [3.0.1](#301) |4 ottobre 2018 |--- |
| [2.3.3](#233) |8 settembre 2018 |30 agosto 2020 |
| [2.3.2](#232) |8 maggio 2018 |30 agosto 2020 |
| [2.3.1](#231) |21 dicembre 2017 |30 agosto 2020 |
| [2.3.0](#230) |10 novembre 2017 |30 agosto 2020 |
| [2.2.1](#221) |29 settembre 2017 |30 agosto 2020 |
| [2.2.0](#220) |10 maggio 2017 |30 agosto 2020 |
| [2.1.0](#210) |01 maggio 2017 |30 agosto 2020 |
| [2.0.1](#201) |30 ottobre 2016 |30 agosto 2020 |
| [2.0.0](#200) |29 settembre 2016 |30 agosto 2020 |
| [1.9.0](#190) |07 luglio 2016 |30 agosto 2020 |
| [1.8.0](#180) |14 giugno 2016 |30 agosto 2020 |
| [1.7.0](#170) |26 aprile 2016 |30 agosto 2020 |
| [1.6.1](#161) |08 aprile 2016 |30 agosto 2020 |
| [1.6.0](#160) |29 marzo 2016 |30 agosto 2020 |
| [1.5.0](#150) |03 gennaio 2016 |30 agosto 2020 |
| [1.4.2](#142) |06 ottobre 2015 |30 agosto 2020 |
| 1.4.1 |06 ottobre 2015 |30 agosto 2020 |
| [1.2.0](#120) |06 agosto 2015 |30 agosto 2020 |
| [1.1.0](#110) |09 luglio 2015 |30 agosto 2020 |
| [1.0.1](#101) |25 maggio 2015 |30 agosto 2020 |
| 1.0.0 |07 aprile 2015 |30 agosto 2020 |
| 0.9.4-prelease |14 gennaio 2015 |29 febbraio 2016 |
| 0.9.3-prelease |09 dicembre 2014 |29 febbraio 2016 |
| 0.9.2-prelease |25 novembre 2014 |29 febbraio 2016 |
| 0.9.1-prelease |23 settembre 2014 |29 febbraio 2016 |
| 0.9.0-prelease |21 agosto 2014 |29 febbraio 2016 |

## <a name="faq"></a>Domande frequenti

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 
