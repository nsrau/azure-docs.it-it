---
title: Mapping dei campi per l'indicizzazione automatica usando gli indicizzatori - Ricerca di Azure
description: Configurare i mapping dei campi dell'indicizzatore di Ricerca di Azure per rilevare le differenze nei nomi dei campi e nelle rappresentazioni dei dati.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 3546e342b535a122ea4ed3f844cd5e28a76d551a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147799"
---
# <a name="field-mappings-and-transformations-using-azure-search-indexers"></a>Mapping dei campi e le trasformazioni usando gli indicizzatori di ricerca di Azure

Quando si usano gli indicizzatori di ricerca di Azure, a volte si scopre che i dati di input non corrispondano allo schema dell'indice di destinazione. In questi casi, è possibile usare **mapping dei campi** per modificare la forma dei dati durante il processo di indicizzazione.

Alcune situazioni in cui i mapping dei campi sono utili:

* L'origine dati include un campo denominato `_id`, ma ricerca di Azure non consente i nomi dei campi che iniziano con un carattere di sottolineatura. Un mapping dei campi consente di rinominare in modo efficace un campo.
* Si vuole popolare diversi campi nell'indice dei dati di origine dati stessa. Ad esempio, voler applicare diversi analizzatori a questi campi.
* Si vuole popolare un campo di indice con dati provenienti da più di un'origine dati e origini dati usano nomi campo diversi.
* È necessaria la codifica o decodifica Base64 dei dati. I mapping dei campi supportano diverse **funzioni di mapping**, incluse quelle per la codifica e decodifica Base64.

> [!NOTE]
> La caratteristica di mapping campi di indicizzatori di ricerca di Azure fornisce un modo semplice per eseguire il mapping dei campi dati ai campi dell'indice, con alcune opzioni per la conversione dei dati. Dati più complessi potrebbe essere necessario pre-elaborazione per modificarne la forma in un form che è facile da indicizzare.
>
> Microsoft Azure Data Factory è una potente soluzione basata sul cloud per l'importazione e la trasformazione dei dati. È anche possibile scrivere codice per trasformare i dati di origine prima dell'indicizzazione. Per esempi di codice, vedere [modellare dati relazionali](search-example-adventureworks-modeling.md) e [modellare facet multilivello](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Configurare mapping dei campi

Un mapping dei campi è costituito da tre parti:

1. `sourceFieldName`, che rappresenta un campo nell'origine dati. Questa proprietà è obbligatoria.
2. `targetFieldName`facoltativo, che rappresenta un campo nell'indice di ricerca. Se omesso, viene usato lo stesso nome nell'origine dati.
3. `mappingFunction`facoltativo, che può trasformare i dati usando una delle varie funzioni predefinite. L'elenco completo delle funzioni è riportato [di seguito](#mappingFunctions).

Mapping dei campi vengono aggiunti al `fieldMappings` matrice della definizione dell'indicizzatore.

## <a name="map-fields-using-the-rest-api"></a>Eseguire il mapping di campi usando l'API REST

È possibile aggiungere i mapping dei campi quando si crea un nuovo indicizzatore usando il [creare un indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) richiesta dell'API. È possibile gestire i mapping dei campi di un indicizzatore esistente utilizzando il [aggiornamento dell'indicizzatore](https://docs.microsoft.com/rest/api/searchservice/update-indexer) richiesta dell'API.

Ad esempio, di seguito viene illustrato come eseguire il mapping di un campo di origine a un campo di destinazione con un nome diverso:

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

Un campo di origine può fare riferimento più mapping dei campi. Nell'esempio seguente viene illustrato come "biforcare" un campo, copiare lo stesso campo di origine in due campi di indice diverso:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Ricerca di Azure usa confronti senza distinzione tra maiuscole e minuscole per risolvere il campo e i nomi di funzione nei mapping dei campi. Questa caratteristica è utile perché non è necessario fare attenzione all'uso di maiuscole e minuscole, ma significa anche che l'origine dati o l'indice non può contenere campi differenziati solo in base all'uso di maiuscole e minuscole.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Eseguire il mapping di campi usando .NET SDK

Si definiscono i mapping dei campi in .NET SDK usando il [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) classe, che presenta le proprietà `SourceFieldName` e `TargetFieldName`ed eventualmente `MappingFunction` riferimento.

È possibile specificare i mapping dei campi quando si costruisce l'indicizzatore o in un secondo momento impostando direttamente i `Indexer.FieldMappings` proprietà.

Il seguente C# esempio imposta i mapping dei campi quando si crea un indicizzatore.

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

Una funzione di mapping di campo trasforma il contenuto di un campo prima di essere archiviato nell'indice. Attualmente sono supportate le funzioni di mapping seguente:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode (funzione)

Esegue la codifica Base64 *sicura per gli URL* della stringa di input. Si presuppone che l'input sia con codifica UTF-8.

#### <a name="example---document-key-lookup"></a>Esempio: ricerca delle chiavi di documento

Caratteri solo sicuri per gli URL possono essere visualizzati in una chiave di documento di ricerca di Azure (perché i clienti devono essere in grado di risolvere il documento usando il [API di ricerca](https://docs.microsoft.com/rest/api/searchservice/lookup-document) ). Se il campo di origine per la chiave contiene caratteri non sicuri URL, è possibile usare il `base64Encode` funzione per convertirlo in fase di indicizzazione.

Quando si recupera la chiave codificata in fase di ricerca, è quindi possibile usare il `base64Decode` funzione per ottenere il valore della chiave originale e usarlo per recuperare il documento di origine.

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

Se non si include una proprietà di parametri per la funzione di mapping, per impostazione predefinita al valore `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Ricerca di Azure supporta due diverse codifiche Base64. È consigliabile usare gli stessi parametri durante la codifica e decodifica lo stesso campo. Per altre informazioni, vedere [opzioni di codifica base64](#base64details) per decidere quali parametri da usare.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode (funzione)

Esegue la decodifica Base64 della stringa di input. L'input viene considerato come un *sicuri per gli URL* stringa con codifica Base64.

#### <a name="example---decode-blob-metadata-or-urls"></a>Esempio: decodificare i metadati del blob o URL

I dati di origine potrebbero contenere le stringhe con codifica Base64, ad esempio le stringhe dei metadati di blob o gli URL web, che si desidera eseguire la ricerca come testo normale. È possibile usare il `base64Decode` funzione per riconvertire i dati codificati in stringhe regolari durante il popolamento dell'indice di ricerca.

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

Se non si include una proprietà di parametri, per impostazione predefinita al valore `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Ricerca di Azure supporta due diverse codifiche Base64. È consigliabile usare gli stessi parametri durante la codifica e decodifica lo stesso campo. Per altre informazioni, vedere [opzioni di codifica base64](#base64details) per decidere quali parametri da usare.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>opzioni di codifica Base64

Ricerca di Azure supporta due diverse codifiche Base64: **Token URL HttpServerUtility**, e **codifica base 64 sicura per gli URL senza spaziatura interna**. Una stringa con codifica base64 durante l'indicizzazione deve essere decodificata in un secondo momento con le stesse opzioni di codifica, altrimenti il risultato non corrisponde all'originale.

Se il `useHttpServerUtilityUrlTokenEncode` oppure `useHttpServerUtilityUrlTokenDecode` parametri per la codifica e decodifica rispettivamente vengono impostati `true`, quindi `base64Encode` si comporta come [HttpServerUtility. UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) e `base64Decode` si comporta come [UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Se non si usa la versione completa di .NET Framework (vale a dire, si usa un altro framework o .NET Core) per produrre i valori delle chiavi per emulare il comportamento di ricerca di Azure, è necessario impostare `useHttpServerUtilityUrlTokenEncode` e `useHttpServerUtilityUrlTokenDecode` a `false`. A seconda della libreria usata, la codifica base64 e la decodifica funzioni potrebbero essere diversi da quelli usati da ricerca di Azure.

La tabella seguente confronta diverse codifiche Base64 della stringa `00>00?00`. Per determinare l'eventuale elaborazione aggiuntiva necessaria per le funzioni Base64, applicare la funzione di codifica della libreria nella stringa `00>00?00` e confrontare l'output con l'output previsto `MDA-MDA_MDA`.

| Codifica | Output della codifica Base64 | Elaborazione aggiuntiva dopo la codifica della libreria | Elaborazione aggiuntiva prima della codifica della libreria |
| --- | --- | --- | --- |
| Base64 con spaziatura interna | `MDA+MDA/MDA=` | Usare caratteri sicuri per gli URL e rimuovere la spaziatura interna | Usare caratteri Base64 standard e aggiungere spaziatura interna |
| Base64 senza spaziatura interna | `MDA+MDA/MDA` | Usare caratteri sicuri per gli URL | Usare caratteri Base64 standard |
| Base64 sicura per gli URL con spaziatura interna | `MDA-MDA_MDA=` | Rimuovere la spaziatura interna | Aggiungere spaziatura interna |
| Base64 sicura per gli URL senza spaziatura interna | `MDA-MDA_MDA` | Nessuna | Nessuna |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition (funzione)

Divide un campo stringa usando il delimitatore specificato e sceglie il token nella posizione specificata della divisione risultante.

Questa funzione Usa i parametri seguenti:

* `delimiter`: stringa da usare come separatore quando si divide la stringa di input.
* `position`: posizione in base zero di tipo integer del token da scegliere dopo la divisione della stringa di input.

Ad esempio, se l'input è `Jane Doe`, `delimiter` è `" "` (spazio) e `position` è 0, il risultato è `Jane`; se `position` è 1, il risultato è `Doe`. Se la posizione fa riferimento a un token che non esiste, viene restituito un errore.

#### <a name="example---extract-a-name"></a>Esempio - extract un nome

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

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringCollection function

Trasforma una stringa formattata come matrice di stringhe JSON in una matrice di stringhe che può essere usata per popolare un campo `Collection(Edm.String)` nell'indice.

Ad esempio, se la stringa di input è `["red", "white", "blue"]`, il campo di destinazione di tipo `Collection(Edm.String)` viene popolato con i tre valori `red`, `white` e `blue`. Per i valori di input che non possono essere analizzati come matrici di stringhe JSON, viene restituito un errore.

#### <a name="example---populate-collection-from-relational-data"></a>Esempio - popolare raccolta da dati relazionali

Database SQL di Azure non ha un tipo di dati incorporato che esegue normalmente il mapping a `Collection(Edm.String)` campi in ricerca di Azure. Per popolare i campi stringa della raccolta, è possibile pre-elaborare i dati di origine come matrice di stringhe JSON e quindi usare il `jsonArrayToStringCollection` funzione di mapping.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Per un esempio dettagliato che trasforma i dati relazionali in campi di raccolta dell'indice, vedere [modellare dati relazionali](search-example-adventureworks-modeling.md).
