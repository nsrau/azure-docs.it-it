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
ms.openlocfilehash: 17a96479b80410cbfcb2a6061904491f95c45f10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82116265"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Mapping dei campi e trasformazioni usando gli indicizzatori di Azure ricerca cognitiva

Quando si usano gli indicizzatori di Azure ricerca cognitiva, a volte si scopre che i dati di input non corrispondono esattamente allo schema dell'indice di destinazione. In questi casi, è possibile usare i **mapping dei campi** per modificare la forma dei dati durante il processo di indicizzazione.

Alcune situazioni in cui i mapping dei campi sono utili:

* L'origine dati include un campo denominato `_id`, ma Azure ricerca cognitiva non consente i nomi di campo che iniziano con un carattere di sottolineatura. Un mapping dei campi consente di rinominare in modo efficace un campo.
* Si desidera popolare più campi nell'indice dagli stessi dati dell'origine dati. Ad esempio, potrebbe essere necessario applicare diversi analizzatori a tali campi.
* Si desidera popolare un campo di indice con dati da più di un'origine dati e le origini dati utilizzano nomi di campo diversi.
* È necessaria la codifica o decodifica Base64 dei dati. I mapping dei campi supportano diverse **funzioni di mapping**, incluse quelle per la codifica e decodifica Base64.

> [!NOTE]
> I mapping dei campi negli indicizzatori sono un modo semplice per eseguire il mapping dei campi dati ai campi dell'indice, con una certa capacità di conversione dei dati leggeri. I dati più complessi potrebbero richiedere la pre-elaborazione per riformarli in un modulo che favorisce l'indicizzazione. Una delle opzioni che è possibile prendere in considerazione è [Azure Data Factory](https://docs.microsoft.com/zure/data-factory/).

## <a name="set-up-field-mappings"></a>Configurare i mapping dei campi

Un mapping dei campi è costituito da tre parti:

1. `sourceFieldName`, che rappresenta un campo nell'origine dati. Questa proprietà è obbligatoria.
2. `targetFieldName`facoltativo, che rappresenta un campo nell'indice di ricerca. Se omesso, viene usato lo stesso nome nell'origine dati.
3. `mappingFunction`facoltativo, che può trasformare i dati usando una delle varie funzioni predefinite. L'elenco completo delle funzioni è riportato [di seguito](#mappingFunctions).

I mapping dei campi vengono aggiunti alla `fieldMappings` matrice della definizione dell'indicizzatore.

## <a name="map-fields-using-the-rest-api"></a>Eseguire il mapping dei campi con l'API REST

È possibile aggiungere i mapping dei campi quando si crea un nuovo indicizzatore usando la richiesta dell'API di [creazione dell'indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) . È possibile gestire i mapping dei campi di un indicizzatore esistente usando la richiesta dell'API di [aggiornamento dell'indicizzatore](https://docs.microsoft.com/rest/api/searchservice/update-indexer) .

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

È possibile fare riferimento a un campo di origine in più mapping dei campi. Nell'esempio seguente viene illustrato come "biforcare" un campo, copiando lo stesso campo di origine in due campi di indice diversi:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure ricerca cognitiva usa il confronto senza distinzione tra maiuscole e minuscole per risolvere i nomi di campo e funzione nei mapping dei campi. Questa caratteristica è utile perché non è necessario fare attenzione all'uso di maiuscole e minuscole, ma significa anche che l'origine dati o l'indice non può contenere campi differenziati solo in base all'uso di maiuscole e minuscole.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Eseguire il mapping di campi con .NET SDK

I mapping dei campi vengono definiti in .NET SDK usando la classe [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) , che include le proprietà `SourceFieldName` e `TargetFieldName`e un riferimento facoltativo `MappingFunction` .

È possibile specificare i mapping dei campi quando si costruisce l'indicizzatore o in un secondo momento `Indexer.FieldMappings` impostando direttamente la proprietà.

Nell'esempio C# seguente vengono impostati i mapping dei campi quando si costruisce un indicizzatore.

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

Una funzione di mapping dei campi trasforma il contenuto di un campo prima di archiviarlo nell'indice. Attualmente sono supportate le funzioni di mapping seguenti:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode (funzione)

Esegue la codifica Base64 *sicura per gli URL* della stringa di input. Si presuppone che l'input sia con codifica UTF-8.

#### <a name="example---document-key-lookup"></a>Esempio-ricerca chiave documento

In una chiave del documento ricerca cognitiva di Azure è possibile visualizzare solo i caratteri con URL sicuri, perché i clienti devono essere in grado di indirizzare il documento tramite l' [API di ricerca](https://docs.microsoft.com/rest/api/searchservice/lookup-document) . Se il campo di origine della chiave contiene caratteri non sicuri per gli URL, è possibile usare `base64Encode` la funzione per convertirlo in fase di indicizzazione. Tuttavia, una chiave del documento (sia prima che dopo la conversione) non può contenere più di 1.024 caratteri.

Quando si recupera la chiave codificata in fase di ricerca, è possibile usare la `base64Decode` funzione per ottenere il valore di chiave originale e usarlo per recuperare il documento di origine.

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

Se non si include una proprietà Parameters per la funzione di mapping, per impostazione predefinita viene impostato `{"useHttpServerUtilityUrlTokenEncode" : true}`il valore.

Azure ricerca cognitiva supporta due codifiche Base64 diverse. Quando si codifica e decodifica lo stesso campo, è necessario utilizzare gli stessi parametri. Per ulteriori informazioni, vedere [Opzioni di codifica Base64](#base64details) per decidere quali parametri utilizzare.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode (funzione)

Esegue la decodifica Base64 della stringa di input. Si presuppone che l'input sia una stringa con codifica Base64 sicura per gli *URL* .

#### <a name="example---decode-blob-metadata-or-urls"></a>Esempio: decodificare i metadati o gli URL del BLOB

I dati di origine potrebbero contenere stringhe con codifica Base64, ad esempio stringhe di metadati BLOB o URL Web, che si desidera rendere disponibili per la ricerca come testo normale. È possibile utilizzare la `base64Decode` funzione per trasformare i dati codificati nuovamente in stringhe regolari durante il popolamento dell'indice di ricerca.

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

Se non si include una proprietà Parameters, per impostazione predefinita viene impostato il `{"useHttpServerUtilityUrlTokenEncode" : true}`valore.

Azure ricerca cognitiva supporta due codifiche Base64 diverse. Quando si codifica e decodifica lo stesso campo, è necessario utilizzare gli stessi parametri. Per informazioni dettagliate, vedere [Opzioni di codifica Base64](#base64details) per decidere quali parametri usare.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>opzioni di codifica Base64

Azure ricerca cognitiva supporta la codifica Base64 sicura per gli URL e la codifica Base64 normale. Una stringa con codifica Base64 durante l'indicizzazione deve essere decodificata in un secondo momento con le stesse opzioni di codifica; in caso contrario, il risultato non corrisponderà a quello originale.

Se rispettivamente `useHttpServerUtilityUrlTokenEncode` i `useHttpServerUtilityUrlTokenDecode` parametri o per la codifica e la decodifica sono impostati `true`su, `base64Encode` si comporta come [HttpServerUtility. UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) e `base64Decode` si comporta come [HttpServerUtility. UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

> [!WARNING]
> Se `base64Encode` viene utilizzato per generare valori di chiave `useHttpServerUtilityUrlTokenEncode` , deve essere impostato su true. Per i valori delle chiavi è possibile usare solo la codifica Base64 con URL sicuro. Vedere [regole di denominazione &#40;&#41;ricerca cognitiva di Azure](https://docs.microsoft.com/rest/api/searchservice/naming-rules) per il set completo di restrizioni sui caratteri nei valori chiave.

Le librerie .NET in Azure ricerca cognitiva presuppongono la .NET Framework completa, che fornisce la codifica incorporata. Le `useHttpServerUtilityUrlTokenEncode` opzioni `useHttpServerUtilityUrlTokenDecode` e sfruttano questa funzionalità incorporata. Se si usa .NET Core o un altro Framework, è consigliabile impostare tali opzioni su `false` e chiamare direttamente le funzioni di codifica e decodifica del Framework.

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

Questa funzione usa i parametri seguenti:

* `delimiter`: stringa da usare come separatore quando si divide la stringa di input.
* `position`: posizione in base zero di tipo integer del token da scegliere dopo la divisione della stringa di input.

Ad esempio, se l'input è `Jane Doe`, `delimiter` è `" "` (spazio) e `position` è 0, il risultato è `Jane`; se `position` è 1, il risultato è `Doe`. Se la posizione fa riferimento a un token che non esiste, viene restituito un errore.

#### <a name="example---extract-a-name"></a>Esempio: estrarre un nome

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

#### <a name="example---populate-collection-from-relational-data"></a>Esempio: popolare la raccolta da dati relazionali

Il database SQL di Azure non dispone di un tipo di dati incorporato che esegue `Collection(Edm.String)` naturalmente il mapping ai campi in ricerca cognitiva di Azure. Per popolare i campi della raccolta di stringhe, è possibile pre-elaborare i dati di origine come una matrice di stringhe `jsonArrayToStringCollection` JSON e quindi usare la funzione di mapping.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>funzione urlEncode

Questa funzione può essere usata per codificare una stringa in modo che sia "URL sicuro". Quando viene usato con una stringa che contiene caratteri non consentiti in un URL, questa funzione convertirà tali caratteri "unsafe" in equivalenti di entità carattere. Questa funzione usa il formato di codifica UTF-8.

#### <a name="example---document-key-lookup"></a>Esempio-ricerca chiave documento

`urlEncode`la funzione può essere usata come alternativa alla funzione `base64Encode` , se devono essere convertiti solo i caratteri unsafe URL, mantenendo gli altri caratteri così come sono.

Supponiamo che la stringa di `<hello>` input sia, quindi il campo di `(Edm.String)` destinazione di tipo verrà popolato con il valore`%3chello%3e`

Quando si recupera la chiave codificata in fase di ricerca, è possibile usare la `urlDecode` funzione per ottenere il valore di chiave originale e usarlo per recuperare il documento di origine.

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

 Questa funzione converte una stringa codificata in URL in una stringa decodificata usando il formato di codifica UTF-8.

 ### <a name="example---decode-blob-metadata"></a>Esempio: decodifica dei metadati del BLOB

 Alcuni client di archiviazione di Azure URL codificano automaticamente i metadati del BLOB se contengono caratteri non ASCII. Tuttavia, se si desidera rendere tali metadati ricercabili (come testo normale), è possibile utilizzare la `urlDecode` funzione per trasformare i dati codificati in stringhe regolari durante il popolamento dell'indice di ricerca.

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
 
Quando si verificano errori relativi alla chiave del documento che supera i 1024 caratteri, è possibile applicare questa funzione per ridurre la lunghezza della chiave del documento.

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
