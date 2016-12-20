---
title: Mapping dei campi negli indicizzatori di Ricerca di Azure
description: Configurare i mapping dei campi dell&quot;indicizzatore di Ricerca di Azure per rilevare le differenze nei nomi dei campi e nelle rappresentazioni dei dati
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 0325a4de-0190-4dd5-a64d-4e56601d973b
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: fc2f30569acc49dd383ba230271989eca8a14423
ms.openlocfilehash: 57e91f070d9a42882a56e708f12b1ce238ed9191

---

# <a name="field-mappings-in-azure-search-indexers"></a>Mapping dei campi negli indicizzatori di Ricerca di Azure
Quando si usano gli indicizzatori di Ricerca di Azure, a volte è possibile che i dati di input non corrispondano allo schema dell'indice di destinazione. In questi casi è possibile usare i **mapping dei campi** per trasformare i dati nella forma desiderata.

Alcune situazioni in cui i mapping dei campi sono utili:

* L'origine dati include un campo `_id`, ma Ricerca di Azure non consente nomi di campo che iniziano con un carattere di sottolineatura. Un mapping dei campi consente di "rinominare" un campo.
* Si vuole popolare diversi campi dell'indice con dati della stessa origine dati, ad esempio per applicare diversi analizzatori a questi campi. I mapping dei campi consentono di "biforcare" un campo dell'origine dati.
* È necessaria la codifica o decodifica Base64 dei dati. I mapping dei campi supportano diverse **funzioni di mapping**, incluse quelle per la codifica e decodifica Base64.   

## <a name="setting-up-field-mappings"></a>Configurazione del mapping dei campi
È possibile aggiungere i mapping dei campi quando si crea un nuovo indicizzatore con l'API di [creazione dell'indicizzatore](https://msdn.microsoft.com/library/azure/dn946899.aspx) . È possibile gestire i mapping dei campi in un indicizzatore con l'API di [aggiornamento dell'indicizzatore](https://msdn.microsoft.com/library/azure/dn946892.aspx) .

Un mapping dei campi è costituito da tre parti:

1. `sourceFieldName`, che rappresenta un campo nell'origine dati. Questa proprietà è obbligatoria.
2. `targetFieldName`facoltativo, che rappresenta un campo nell'indice di ricerca. Se omesso, viene usato lo stesso nome nell'origine dati.
3. `mappingFunction`facoltativo, che può trasformare i dati usando una delle varie funzioni predefinite. L'elenco completo delle funzioni è riportato [di seguito](#mappingFunctions).

I mapping dei campi vengono aggiunti alla matrice `fieldMappings` nella definizione dell'indicizzatore.

L'esempio seguente mostra come gestire le differenze nei nomi di campo:

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

Un indicizzatore può avere più mapping dei campi. L'esempio seguente mostra come "biforcare" un campo:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" },
]
```

> [!NOTE]
> Ricerca di Azure usa confronti senza distinzione tra maiuscole e minuscole per risolvere il campo e i nomi di funzione nei mapping dei campi. Questa caratteristica è utile perché non è necessario fare attenzione all'uso di maiuscole e minuscole, ma significa anche che l'origine dati o l'indice non può contenere campi differenziati solo in base all'uso di maiuscole e minuscole.  
>
>

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Funzioni del mapping dei campi
Queste funzioni sono attualmente supportate:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode"></a>base64Encode
Esegue la codifica Base64 *sicura per gli URL* della stringa di input. Si presuppone che l'input sia con codifica UTF-8.

#### <a name="sample-use-case"></a>Caso d'uso di esempio
Nella chiave del documento di Ricerca di Azure possono essere visualizzati solo caratteri sicuri per gli URL, ad esempio perché i clienti devono poter fare riferimento al documento usando l'API di ricerca. Se i dati contengono caratteri non sicuri per gli URL e si vuole usarli per popolare un campo chiave nell'indice di ricerca, usare questa funzione.   

#### <a name="example"></a>Esempio
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Path",
    "targetFieldName" : "UrlSafePath",
    "mappingFunction" : { "name" : "base64Encode" }
  }]
```

<a name="base64DecodeFunction"></a>

### <a name="base64decode"></a>base64Decode
Esegue la decodifica Base64 della stringa di input. Si presuppone che l'input sia una stringa con codifica Base64 *sicura per gli URL* .

#### <a name="sample-use-case"></a>Caso d'uso di esempio
I valori di metadati personalizzati BLOB devono essere codificati in ASCII. È possibile usare la codifica Base64 per rappresentare le stringhe Unicode arbitrarie nei metadati personalizzati BLOB. Tuttavia, per rendere significativa la ricerca, è possibile usare questa funzione per riconvertire i dati codificati in stringhe "normali" durante il popolamento dell'indice di ricerca.  

#### <a name="example"></a>Esempio
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode" }
  }]
```

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition"></a>extractTokenAtPosition
Divide un campo stringa usando il delimitatore specificato e sceglie il token nella posizione specificata della divisione risultante.

Ad esempio, se l'input è `Jane Doe`, `delimiter` è `" "` (spazio) e `position` è 0, il risultato è `Jane`; se `position` è 1, il risultato è `Doe`. Se la posizione fa riferimento a un token che non esiste, viene restituito un errore.

#### <a name="sample-use-case"></a>Caso d'uso di esempio
L'origine dati contiene un campo `PersonName` e si vuole indicizzarla come due campi separati, `FirstName` e `LastName`. È possibile usare questa funzione per dividere l'input usando il carattere spazio come delimitatore.

#### <a name="parameters"></a>Parametri
* `delimiter`: stringa da usare come separatore quando si divide la stringa di input.
* `position`: posizione in base zero di tipo integer del token da scegliere dopo la divisione della stringa di input.    

#### <a name="example"></a>Esempio
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

### <a name="jsonarraytostringcollection"></a>jsonArrayToStringCollection
Trasforma una stringa formattata come matrice di stringhe JSON in una matrice di stringhe che può essere usata per popolare un campo `Collection(Edm.String)` nell'indice.

Ad esempio, se la stringa di input è `["red", "white", "blue"]`, il campo di destinazione di tipo `Collection(Edm.String)` viene popolato con i tre valori `red`, `white` e `blue`. Per i valori di input che non possono essere analizzati come matrici di stringhe JSON, viene restituito un errore.

#### <a name="sample-use-case"></a>Caso d'uso di esempio
Il database SQL di Azure non ha un tipo di dati predefinito che esegue normalmente il mapping ai campi `Collection(Edm.String)` in Ricerca di Azure. Per popolare i campi della raccolta di stringhe, formattare i dati di origine come matrice di stringhe JSON e usare questa funzione.

#### <a name="example"></a>Esempio
```JSON

"fieldMappings" : [
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
]
```

## <a name="help-us-make-azure-search-better"></a>Come contribuire al miglioramento di Ricerca di Azure
Se si hanno domande sulle funzionalità o idee per apportare miglioramenti, contattare Microsoft sul [sito UserVoice](https://feedback.azure.com/forums/263029-azure-search/).



<!--HONumber=Nov16_HO3-->


