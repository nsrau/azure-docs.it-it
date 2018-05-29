---
title: Creare un indicizzatore (API REST per il servizio Ricerca di Azure - api-version=2017-11-11-Preview)
description: Nella versione di anteprima dell'API, gli indicizzatori vengono estesi per includere i parametri outputFieldMapping usati per eseguire il mapping dell'output di arricchimento a un campo in un indice di Ricerca di Azure.
services: search
manager: pablocas
author: luiscabrer
ms.author: luisca
ms.date: 05/01/2018
ms.prod: azure
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.openlocfilehash: d47b14342caf0312e052584d20f1a9c86ca29cad
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786920"
---
# <a name="create-indexer-azure-search-service-rest-api-version2017-11-11-preview"></a>Creare un indicizzatore (API REST per il servizio Ricerca di Azure - api-version=2017-11-11-Preview)

Queste informazioni di riferimento sull'API costituiscono una versione della documentazione specifica per l'anteprima e illustrano i miglioramenti apportati alla ricerca cognitiva nell'indicizzazione.

Analogamente a quanto avviene con la versione [disponibile a livello generale](https://docs.microsoft.com/rest/api/searchservice/create-indexer), è possibile creare un nuovo indicizzatore nel servizio Ricerca di Azure usando una richiesta HTTP POST. 

```http
POST https://[service name].search.windows.net/indexers?api-version=[api-version]  
    Content-Type: application/json  
    api-key: [admin key]  
```  
L'oggetto **api-key** deve essere una chiave amministratore e non una chiave di query. Per altre informazioni sulle chiavi, fare riferimento alla sezione sull'autenticazione in [Sicurezza in Ricerca di Azure](search-security-overview.md). L'articolo [Creare un servizio di Ricerca di Azure nel portale](search-create-service-portal.md) illustra come ottenere l'URL del servizio e le proprietà delle chiavi usati nella richiesta.

In alternativa, è possibile usare una richiesta PUT e specificare il nome dell'origine dati nell'URI. Se l'origine dati non esiste, verrà creata.  

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]  
```  
L'oggetto **api-version** è obbligatorio. La versione corrente è `2016-09-01`. Per informazioni dettagliate, vedere [Versioni API in Ricerca di Azure](search-api-versions.md).

Per indicazioni specifiche della piattaforma sulla creazione di indicizzatori, iniziare dall'articolo [Indicizzatori in Ricerca di Azure](search-indexer-overview.md), che include l'elenco completo degli [articoli correlati](search-indexer-overview.md#next-steps).

> [!NOTE]  
>  Il numero massimo di indicizzatori consentiti varia in base al piano tariffario. Nel servizio gratuito sono consentiti fino a 3 indicizzatori. Nel servizio standard sono consentiti 50 indicizzatori. Per informazioni dettagliate, vedere l'articolo relativo ai [limiti del servizio](search-limits-quotas-capacity.md) .    

## <a name="request"></a>Richiesta  
 Il corpo della richiesta contiene una definizione di indicizzatore, che specifica l'origine dati e l'indice di destinazione per l'indicizzazione, nonché una pianificazione e pianificazioni di indicizzazione facoltativi.  

 La sintassi per la strutturazione del payload della richiesta è la seguente: Più avanti in questo argomento è illustrata una richiesta di esempio.  

```json
{   
    "name" : "Required for POST, optional for PUT. The name of the indexer",  
    "description" : "Optional. Anything you want, or null",  
    "dataSourceName" : "Required. The name of an existing data source",  
    "targetIndexName" : "Required. The name of an existing index",  
    "schedule" : { Optional. See Indexing Schedule below. },  
    "parameters" : { Optional. See Indexing Parameters below. },  
    "fieldMappings" : { Optional. See fieldMappings below. },
    "outputFieldMappings" : { Required for enrichment pipelines. See outputFieldMappings below. },
    "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.
}  
```

### <a name="indexer-schedule"></a>Pianificazione dell'indicizzatore  
Facoltativamente, un indicizzatore può specificare una pianificazione. Se è presente una pianificazione, l'indicizzatore viene eseguito periodicamente in base alla pianificazione. L'utilità di pianificazione è integrata e non è possibile usare un'utilità di pianificazione esterna. L'elemento **schedule** ha gli attributi seguenti: 

-   **interval**: obbligatorio. Valore di durata che specifica un intervallo o un periodo per l'esecuzione dell'indicizzatore. L'intervallo minimo consentito è di 5 minuti, quello massimo di un giorno. Il valore deve essere formattato come valore XSD "dayTimeDuration" (un subset limitato di un valore [duration ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Il modello è: `"P[nD][T[nH][nM]]".` Esempi: `PT15M` per indicare ogni 15 minuti, `PT2H` per indicare ogni due ore.  

-   **startTime**: facoltativo. Data e ora UTC di inizio dell'esecuzione dell'indicizzatore.  

### <a name="indexer-parameters"></a>Parametri dell'indicizzatore  
 Un indicizzatore può facoltativamente specificare diversi parametri che ne influenzano il comportamento. Tutti i parametri elencati di seguito sono facoltativi.  

-   **maxFailedItems**: numero di elementi per cui l'indicizzazione può avere esito negativo prima che l'esecuzione dell'indicizzatore venga considerata non riuscita. Il valore predefinito è 0. Le informazioni sugli elementi con esito negativo vengono restituite dall'operazione di [recupero dello stato di un indicizzatore dell'API REST per il servizio Ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).  

-   **maxFailedItemsPerBatch**: numero di elementi per cui l'indicizzazione può avere esito negativo in ogni batch prima che l'esecuzione dell'indicizzatore venga considerata non riuscita. Il valore predefinito è 0.  

-   **batchSize**: specifica il numero di elementi che vengono letti dall'origine dati e indicizzati come singolo batch per migliorare le prestazioni. Il valore predefinito dipende dal tipo di origine dati: 1000 per Azure SQL e Azure Cosmos DB e 10 per Archiviazione BLOB di Azure.

### <a name="field-mapping-parameters"></a>Parametri di mapping dei campi

Le definizioni di indicizzatore contengono associazioni di campi per il mapping di un campo di origine a un campo di destinazione in un indice di Ricerca di Azure. Ci sono due tipi di associazioni, a seconda che il trasferimento del contenuto segua un percorso diretto o arricchito:

+ Gli elementi **fieldMappings** sono facoltativi e vengono applicati quando i nomi dei campi di origine e di destinazione non corrispondono o quando si vuole specificare una funzione.
+ Gli elementi **outputFieldMappings** sono obbligatori se si sta creando una [pipeline di arricchimento](cognitive-search-concept-intro.md). In una pipeline di arricchimento, il campo di output è un costrutto definito durante il processo di arricchimento. Ad esempio, il campo di output potrebbe essere una struttura composta creata durante il processo di arricchimento da due campi distinti nel documento di origine. 

#### <a name="fieldmappings"></a>fieldMappings

Nell'esempio seguente si consideri una tabella di origine con un campo `_id`. Ricerca di Azure non consente un nome di campo che inizia con un carattere di sottolineatura, quindi il campo deve essere rinominato. Questa operazione può essere eseguita usando la proprietà `fieldMappings` dell'indicizzatore nel modo seguente:

```json
"fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
```

È possibile specificare più mapping dei campi.

```json
"fieldMappings" : [
    { "sourceFieldName" : "_id", "targetFieldName" : "id" },
    { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" }
]
```

Per i nomi dei campi di origine e destinazione non viene fatta distinzione tra maiuscole e minuscole.

Per informazioni sugli scenari in cui i mapping dei campi sono utili, vedere [Mapping dei campi negli indicizzatori di Ricerca di Azure](https://docs.microsoft.com/azure/search/search-indexer-field-mappings).

#### <a name="outputfieldmappings"></a>outputFieldMappings

Negli scenari di ricerca cognitiva in cui un set di competenze è associato a un indicizzatore, è necessario aggiungere `outputFieldMappings` per associare l'output di un passaggio di arricchimento che fornisce il contenuto per un campo ricercabile nell'indice.

```json
  "outputFieldMappings" : [
        {
          "sourceFieldName" : "/document/organizations", 
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*", 
          "targetFieldName" : "keyphrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "language",
            "mappingFunction": null
        }      
   ],
```

<a name="FieldMappingFunctions"></a>

#### <a name="field-mapping-functions"></a>Funzioni del mapping dei campi

I mapping dei campi possono essere usati anche per trasformare i valori del campo di origine mediante le *funzioni del mapping dei campi*. Ad esempio, un valore stringa arbitrario può avere una codifica base64 e quindi può essere usato per popolare un campo chiave di un documento.

Per altre informazioni su quando e come usare le funzioni di mapping dei campi, vedere [Funzioni del mapping dei campi](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions).

### <a name="request-body-examples"></a>Esempi del corpo della richiesta  
 L'esempio seguente crea un indicizzatore che copia i dati dalla tabella a cui fa riferimento l'origine dati `ordersds` all'indice `orders` in base a una pianificazione che inizia l'1 gennaio 2015 UTC e viene eseguita ogni ora. Ogni chiamata all'indicizzatore avrà esito positivo se l'indicizzazione non ha esito negativo per più di 5 elementi in ogni batch e per più di 10 elementi in totale.  

```json
{
    "name" : "myindexer",  
    "description" : "a cool indexer",  
    "dataSourceName" : "ordersds",  
    "targetIndexName" : "orders",  
    "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },  
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }  
}
```

## <a name="response"></a>Risposta  
 Se la richiesta ha esito positivo, viene restituito il codice di stato 201 - Creato.  

## <a name="see-also"></a>Vedere anche 

+ [Cognitive search overview](cognitive-search-concept-intro.md) (Panoramica della ricerca cognitiva)
+ [Guida introduttiva: Provare la ricerca cognitiva](cognitive-search-quickstart-blob.md)
+ [Come eseguire il mapping dei campi](cognitive-search-output-field-mapping.md)
