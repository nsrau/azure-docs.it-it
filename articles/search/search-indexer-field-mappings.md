---
title: Mapping dei campi negli indicizzatori
titleSuffix: Azure Cognitive Search
description: Configurare i mapping dei campi in un indicizzatore per tenere conto delle differenze nei nomi dei campi e nelle rappresentazioni dei dati.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3e09741e841897032b8146dee67b79e0c26ea5cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275153"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Mapping e trasformazioni dei campi con gli indicizzatori di Ricerca cognitiva di AzureField mappings and transformations using Azure Cognitive Search indexers

Quando si usano gli indicizzatori di Ricerca cognitiva di Azure, talvolta i dati di input non corrispondono esattamente allo schema dell'indice di destinazione. In questi casi, è possibile utilizzare **i mapping dei campi** per rimodellare i dati durante il processo di indicizzazione.

Alcune situazioni in cui i mapping dei campi sono utili:

* L'origine dati include `_id`un campo denominato , ma Ricerca cognitiva di Azure non consente nomi di campo che iniziano con un punteggio di sottolineatura. Un mapping dei campi consente di rinominare in modo efficace un campo.
* Si desidera popolare diversi campi nell'indice dagli stessi dati dell'origine dati. Ad esempio, è possibile applicare analizzatori diversi a tali campi.
* Si desidera popolare un campo di indice con i dati di più origini dati e le origini dati utilizzano nomi di campo diversi.
* È necessaria la codifica o decodifica Base64 dei dati. I mapping dei campi supportano diverse **funzioni di mapping**, incluse quelle per la codifica e decodifica Base64.

> [!NOTE]
> La funzionalità di mapping dei campi degli indicizzatori di Ricerca cognitiva di Azure offre un modo semplice per eseguire il mapping dei campi dati ai campi dell'indice, con alcune opzioni per la conversione dei dati. Dati più complessi potrebbero richiedere la pre-elaborazione per rimodellarli in un modulo facile da indicizzare.
>
> Microsoft Azure Data Factory è una potente soluzione basata su cloud per l'importazione e la trasformazione dei dati. È anche possibile scrivere codice per trasformare i dati di origine prima dell'indicizzazione. Per esempi di codice, vedere [Model relational data](search-example-adventureworks-modeling.md) e Model [multilevel facet](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Impostare i mapping dei campi

Un mapping dei campi è costituito da tre parti:

1. `sourceFieldName`, che rappresenta un campo nell'origine dati. Questa proprietà è obbligatoria.
2. `targetFieldName`facoltativo, che rappresenta un campo nell'indice di ricerca. Se omesso, viene usato lo stesso nome nell'origine dati.
3. `mappingFunction`facoltativo, che può trasformare i dati usando una delle varie funzioni predefinite. L'elenco completo delle funzioni è riportato [di seguito](#mappingFunctions).

I mapping dei campi `fieldMappings` vengono aggiunti alla matrice della definizione dell'indicizzatore.

## <a name="map-fields-using-the-rest-api"></a>Eseguire il mapping dei campi usando l'API RESTMap fields using the REST API

È possibile aggiungere mapping di campi quando si crea un nuovo indicizzatore utilizzando la richiesta API [Crea indicizzatore.](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) È possibile gestire i mapping dei campi di un indicizzatore esistente utilizzando la richiesta dell'API [Aggiorna indicizzatore.](https://docs.microsoft.com/rest/api/searchservice/update-indexer)

Ad esempio, ecco come mappare un campo di origine a un campo di destinazione con un nome diverso:

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

È possibile fare riferimento a un campo di origine in più mapping di campi. Nell'esempio seguente viene illustrato come "fork" un campo, copiando lo stesso campo di origine in due campi indice diversi:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Ricerca cognitiva di Azure usa il confronto senza distinzione tra maiuscole e minuscole per risolvere i nomi dei campi e delle funzioni nei mapping dei campi. Questa caratteristica è utile perché non è necessario fare attenzione all'uso di maiuscole e minuscole, ma significa anche che l'origine dati o l'indice non può contenere campi differenziati solo in base all'uso di maiuscole e minuscole.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Eseguire il mapping di campi con .NET SDK

I mapping dei campi vengono definiti in .NET SDK utilizzando `SourceFieldName` `TargetFieldName`la classe `MappingFunction` [FieldMapping,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) che dispone delle proprietà e , e un riferimento facoltativo.

È possibile specificare i mapping dei campi durante la `Indexer.FieldMappings` costruzione dell'indicizzatore o in un secondo momento impostando direttamente la proprietà.

Nell'esempio in C'è riportato di seguito vengono impostati i mapping dei campi durante la costruzione di un indicizzatore.

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Funzioni del mapping dei campi

Una funzione di mapping dei campi trasforma il contenuto di un campo prima che venga archiviato nell'indice. Sono attualmente supportate le seguenti funzioni di mappatura:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [Urlencode](#urlEncodeFunction)
* [urlDecode (codice di sicurezza)](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode (funzione)

Esegue la codifica Base64 *sicura per gli URL* della stringa di input. Si presuppone che l'input sia con codifica UTF-8.

#### <a name="example---document-key-lookup"></a>Esempio - ricerca chiave documento

Solo i caratteri sicuri per gli URL possono essere visualizzati in una chiave del documento di Ricerca cognitiva di Azure (perché i clienti devono essere in grado di indirizzare il documento usando [l'API di ricerca](https://docs.microsoft.com/rest/api/searchservice/lookup-document) ). Se il campo di origine per la chiave contiene `base64Encode` caratteri non sicuri per gli URL, è possibile utilizzare la funzione per convertirlo in fase di indicizzazione. Tuttavia, una chiave del documento (prima e dopo la conversione) non può essere più lunga di 1.024 caratteri.

Quando si recupera la chiave codificata in `base64Decode` fase di ricerca, è quindi possibile utilizzare la funzione per ottenere il valore della chiave originale e utilizzarlo per recuperare il documento di origine.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

Se non si include una proprietà parameters per la funzione `{"useHttpServerUtilityUrlTokenEncode" : true}`di mapping, il valore predefinito è il valore .

Ricerca cognitiva di Azure supporta due diverse codifiche Base64.Azure Cognitive Search supports two different Base64 encodings. È necessario utilizzare gli stessi parametri durante la codifica e la decodifica dello stesso campo. Per altre informazioni, vedere Opzioni di [codifica base64](#base64details) per decidere quali parametri utilizzare.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode (funzione)

Esegue la decodifica Base64 della stringa di input. Si presuppone che l'input sia una stringa con codifica Base64 *indipendente dall'URL.*

#### <a name="example---decode-blob-metadata-or-urls"></a>Esempio: decodificare url o metadati BLOBExample - decode blob metadata or URLs

I dati di origine potrebbero contenere stringhe con codifica Base64, ad esempio stringhe di metadati BLOB o URL Web, che si desidera rendere ricercabili come testo normale. È possibile `base64Decode` utilizzare la funzione per trasformare i dati codificati in stringhe regolari durante la compilazione dell'indice di ricerca.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

Se non si include una proprietà parameters, il `{"useHttpServerUtilityUrlTokenEncode" : true}`valore predefinito è il valore .

Ricerca cognitiva di Azure supporta due diverse codifiche Base64.Azure Cognitive Search supports two different Base64 encodings. È necessario utilizzare gli stessi parametri durante la codifica e la decodifica dello stesso campo. Per ulteriori dettagli, vedere opzioni di [codifica base64](#base64details) per decidere quali parametri utilizzare.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>Opzioni di codifica base64

Ricerca cognitiva di Azure supporta la codifica base64 basata su URL e la normale codifica base64.Azure Cognitive Search supports URL-safe base64 encoding and normal base64 encoding. Una stringa codificata in base64 durante l'indicizzazione deve essere decodificata in un secondo momento con le stesse opzioni di codifica, altrimenti il risultato non corrisponderà all'originale.

Se `useHttpServerUtilityUrlTokenEncode` i `useHttpServerUtilityUrlTokenDecode` parametri or per la codifica `true`e `base64Encode` la decodifica sono impostati rispettivamente su `base64Decode` , si comporta come [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) e si comporta come [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

> [!WARNING]
> Se `base64Encode` viene utilizzato per `useHttpServerUtilityUrlTokenEncode` produrre valori di chiave, deve essere impostato su true. Solo la codifica base64 di sicurezza URL può essere utilizzata per i valori chiave. Vedere [Regole di denominazione &#40;&#41;ricerca cognitiva di Azure](https://docs.microsoft.com/rest/api/searchservice/naming-rules) per il set completo di restrizioni sui caratteri nei valori chiave.

Le librerie .NET in Ricerca cognitiva di Azure presuppongono la versione completa di .NET Framework, che fornisce la codifica incorporata. Le `useHttpServerUtilityUrlTokenEncode` `useHttpServerUtilityUrlTokenDecode` opzioni e sfruttano questa funzione incorporata. Se si utilizza .NET Core o un altro `false` framework, è consigliabile impostare tali opzioni e chiamare direttamente le funzioni di codifica e decodifica del framework.

La tabella seguente confronta diverse codifiche Base64 della stringa `00>00?00`. Per determinare l'eventuale elaborazione aggiuntiva necessaria per le funzioni Base64, applicare la funzione di codifica della libreria nella stringa `00>00?00` e confrontare l'output con l'output previsto `MDA-MDA_MDA`.

| Codifica | Output della codifica Base64 | Elaborazione aggiuntiva dopo la codifica della libreria | Elaborazione aggiuntiva prima della codifica della libreria |
| --- | --- | --- | --- |
| Base64 con spaziatura interna | `MDA+MDA/MDA=` | Usare caratteri sicuri per gli URL e rimuovere la spaziatura interna | Usare caratteri Base64 standard e aggiungere spaziatura interna |
| Base64 senza spaziatura interna | `MDA+MDA/MDA` | Usare caratteri sicuri per gli URL | Usare caratteri Base64 standard |
| Base64 sicura per gli URL con spaziatura interna | `MDA-MDA_MDA=` | Rimuovere la spaziatura interna | Aggiungere spaziatura interna |
| Base64 sicura per gli URL senza spaziatura interna | `MDA-MDA_MDA` | nessuno | nessuno |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition (funzione)

Divide un campo stringa usando il delimitatore specificato e sceglie il token nella posizione specificata della divisione risultante.

Questa funzione utilizza i seguenti parametri:

* `delimiter`: stringa da usare come separatore quando si divide la stringa di input.
* `position`: posizione in base zero di tipo integer del token da scegliere dopo la divisione della stringa di input.

Ad esempio, se l'input è `Jane Doe`, `delimiter` è `" "` (spazio) e `position` è 0, il risultato è `Jane`; se `position` è 1, il risultato è `Doe`. Se la posizione fa riferimento a un token che non esiste, viene restituito un errore.

#### <a name="example---extract-a-name"></a>Esempio - estrarre un nome

L'origine dati contiene un campo `PersonName` e si vuole indicizzarla come due campi separati, `FirstName` e `LastName`. È possibile usare questa funzione per dividere l'input usando il carattere spazio come delimitatore.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringCollection (funzione)

Trasforma una stringa formattata come matrice di stringhe JSON in una matrice di stringhe che può essere usata per popolare un campo `Collection(Edm.String)` nell'indice.

Ad esempio, se la stringa di input è `["red", "white", "blue"]`, il campo di destinazione di tipo `Collection(Edm.String)` viene popolato con i tre valori `red`, `white` e `blue`. Per i valori di input che non possono essere analizzati come matrici di stringhe JSON, viene restituito un errore.

#### <a name="example---populate-collection-from-relational-data"></a>Esempio: popolare la raccolta dai dati relazionaliExample - populate collection from relational data

Il database SQL di Azure non dispone di un `Collection(Edm.String)` tipo di dati predefinito che viene mappato naturalmente ai campi in Ricerca cognitiva di Azure.Azure SQL Database doesn't have a built-in data type that naturally maps to fields in Azure Cognitive Search. Per popolare i campi di raccolta delle stringhe, è possibile pre-elaborare i dati di origine come matrice di stringhe JSON e quindi utilizzare la `jsonArrayToStringCollection` funzione di mapping.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Per un esempio dettagliato che trasforma i dati relazionali in campi di raccolta indici, vedere [Modellare dati relazionali](search-example-adventureworks-modeling.md).

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode (funzione)

Questa funzione può essere utilizzata per codificare una stringa in modo che sia "URL safe". Se utilizzata con una stringa che contiene caratteri non consentiti in un URL, questa funzione convertirà tali caratteri "non sicuri" in equivalenti di entità carattere. Questa funzione utilizza il formato di codifica UTF-8.

#### <a name="example---document-key-lookup"></a>Esempio - ricerca chiave documento

`urlEncode`funzione può essere utilizzata come `base64Encode` alternativa alla funzione, se solo i caratteri non sicuri URL devono essere convertiti, mantenendo altri caratteri così come sono.

Ad esempio, la `<hello>` stringa di input `(Edm.String)` è - quindi il campo di destinazione di tipo verrà popolato con il valore`%3chello%3e`

Quando si recupera la chiave codificata in `urlDecode` fase di ricerca, è quindi possibile utilizzare la funzione per ottenere il valore della chiave originale e utilizzarlo per recuperare il documento di origine.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

 ### <a name="urldecode-function"></a>urlDecode (funzione)

 Questa funzione converte una stringa con codifica URL in una stringa decodificata utilizzando il formato di codifica UTF-8.

 ### <a name="example---decode-blob-metadata"></a>Esempio: decodificare i metadati BLOBExample - decode blob metadata

 Alcuni client di archiviazione di Azure urlano automaticamente i metadati BLOB di codifica se contengono caratteri non ASCII. Tuttavia, se si desidera rendere tali metadati ricercabili `urlDecode` (come testo normale), è possibile utilizzare la funzione per trasformare i dati codificati di nuovo in stringhe regolari quando popolano l'indice di ricerca.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```
 
 <a name="fixedLengthEncodeFunction"></a>
 
 ### <a name="fixedlengthencode-function"></a>fixedLengthEncode (funzione)
 
 Questa funzione converte una stringa di qualsiasi lunghezza in una stringa a lunghezza fissa.
 
 ### <a name="example---map-document-keys-that-are-too-long"></a>Esempio: mappare le chiavi del documento troppo lunghe
 
Quando si verificano errori che si lamentano che la chiave del documento è più lunga di 1024 caratteri, questa funzione può essere applicata per ridurre la lunghezza della chiave del documento.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "metadata_storage_path",
    "targetFieldName" : "your key field",
    "mappingFunction" : {
      "name" : "fixedLengthEncode"
    }
  }]
 ```
