---
title: Come modellare tipi di dati complessi - Ricerca di Azure
description: È possibile modellare strutture di dati annidate o gerarchiche in un indice di ricerca di Azure con ComplexType e raccolte di tipi di dati.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: seodec2018
ms.openlocfilehash: 397b3ea7fee67e25cd160f6b529a660e18c44046
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024751"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Come modellare tipi di dati complessi in Ricerca di Azure

Set di dati esterni utilizzati per popolare un indice di ricerca di Azure talvolta includono sottostrutture gerarchiche o annidate. Alcuni esempi potrebbero includere più ubicazioni e numeri di telefono per un singolo cliente, più colori e dimensioni per un singolo SKU, più autori di un singolo libro e così via. In termini di modellazione, è possibile visualizzare queste strutture dette *tipi di dati complessi*, *tipi di dati composti*, *tipi di dati compositi*, o *aggregazione tipi di dati*. Nella terminologia di ricerca di Azure, un tipo complesso è un campo che contiene gli elementi figlio (campi secondari) che a loro volta possono essere semplici o complessi. È simile a un tipo di dati strutturati in un linguaggio di programmazione. I campi complessi possono essere singoli campi, che rappresentano un singolo oggetto nel documento o una raccolta, che rappresenta una matrice di oggetti

Ricerca di Azure supporta in modo nativo le raccolte e tipi complessi. Insieme, questi tipi consentono di modellare quasi ogni struttura JSON nidificata in un indice di ricerca di Azure. Nelle versioni precedenti delle API di ricerca di Azure, solo bidimensionale è stato possibile importare i set di righe. Nella versione più recente, l'indice a questo punto può corrispondere più da vicino ai dati di origine. In altre parole, se i dati di origine includono i tipi complessi, l'indice può avere tipi complessi anche.

Per iniziare, è consigliabile il [set di dati Hotels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md), che è possibile caricare nel **importare i dati** guidata nel portale di Azure. La procedura guidata rileva i tipi complessi nell'origine e suggerisce uno schema di indice basato sulle strutture rilevate.

> [!Note]
> Supporto per i tipi complessi è disponibile a livello generale in `api-version=2019-05-06`. 
>
> Se la soluzione di ricerca si basa su versioni precedenti soluzioni alternative dei set di dati bidimensionale in una raccolta, è necessario modificare l'indice per includere i tipi complessi come supportato nella versione più recente dell'API. Per altre informazioni sull'aggiornamento di versioni dell'API, vedere [l'aggiornamento alla versione più recente API REST](search-api-migration.md) oppure [esegue l'aggiornamento alla versione più recente del SDK di .NET](search-dotnet-sdk-migration.md).

## <a name="example-of-a-complex-structure"></a>Esempio di una struttura complessa

Il documento JSON seguente è costituito da campi semplici e complessi. Campi complesso, ad esempio `Address` e `Rooms`, avere campi secondari. `Address` ha un singolo set di valori per tali campi secondari, poiché si tratta di un singolo oggetto nel documento. Al contrario, `Rooms` con più set di valori per i relativi campi secondari, uno per ogni oggetto nella raccolta.

```json
{
    "HotelId": "1",
    "HotelName": "Secret Point Motel",
    "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
    "Address": {
        "StreetAddress": "677 5th Ave",
        "City": "New York",
        "StateProvince": "NY"
    },
    "Rooms": [
        {
            "Description": "Budget Room, 1 Queen Bed (Cityside)",
            "Type": "Budget Room",
            "BaseRate": 96.99,
        },
        {
            "Description": "Deluxe Room, 2 Double Beds (City View)",
            "Type": "Deluxe Room",
            "BaseRate": 150.99,
        },
    ]
}
```

## <a name="creating-complex-fields"></a>Creazione di campi complessi

Come con una definizione dell'indice, è possibile usare il portale [API REST](https://docs.microsoft.com/rest/api/searchservice/create-index), o [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) per creare uno schema che include i tipi complessi. 

Nell'esempio seguente viene illustrato uno schema di indice JSON con semplici campi, raccolte e tipi complessi. Si noti che all'interno di un tipo complesso, ogni campo secondario dispone di un tipo e possa avere attributi, i campi solo di primo livello. Lo schema corrisponde ai dati di esempio precedenti. `Address` è un campo di tipo complesso che non è una raccolta (un hotel ha un indirizzo). `Rooms` è un campo di raccolta complessa (dispone di un hotel chat molti).

<!---
For indexes used in a [push-model data import](search-what-is-data-import.md) strategy, where you are pushing a JSON data set to an Azure Search index, you can only have the basic syntax shown here: single complex types like `Address`, or a `Collection(Edm.ComplexType)` like `Rooms`. You cannot have complex types nested inside other complex types in an index used for push-model data ingestion.

Indexers are a different story. When defining an indexer, in particular one used to build a knowledge store, your index can have nested complex types. An indexer is able to hold a chain of complex data structures in-memory, and when it includes a skillset, it can support highly complex data forms. For more information and an example, see [How to get started with Knowledge Store](knowledge-store-howto.md).
-->

```json
{
    "name": "hotels",
    "fields": [
        {   "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true    },
        {   "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        {   "name": "Address", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        },
        {
            "name": "Rooms",
            "type": "Collection(Edm.ComplexType)",
            "fields": [{
                    "name": "Description",
                    "type": "Edm.String",
                    "searchable": true,
                    "analyzer": "en.lucene"
                },
                {
                    "name": "Type",
                    "type": "Edm.String",
                    "searchable": true
                },
                {
                    "name": "BaseRate",
                    "type": "Edm.Double",
                    "filterable": true,
                    "facetable": true
                },
            ]
        }
    ]
}
```
## <a name="updating-complex-fields"></a>L'aggiornamento di campi complessa

Tutti i [la reindicizzazione delle regole](search-howto-reindex.md) che si applicano a campi comunque si applicano in genere ai campi complessi. Incrementa di alcune delle regole principale in questo caso, aggiunta di un campo non richiede una ricompilazione dell'indice, ma eseguire la maggior parte delle modifiche.

### <a name="structural-updates-to-the-definition"></a>Aggiornamenti strutturali alla definizione

È possibile aggiungere nuovi campi secondari a un campo complesso in qualsiasi momento senza la necessità di una ricompilazione dell'indice. Ad esempio, aggiungendo "ZipCode" per `Address` o "Servizi" per `Rooms` è consentito, esattamente come l'aggiunta di un campo di primo livello a un indice. I documenti esistenti hanno un valore null per i nuovi campi fino a quando non esplicitamente popolare questi campi mediante l'aggiornamento dei dati.

Si noti che all'interno di un tipo complesso, ogni campo secondario ha un tipo e possa avere attributi, solo come primo livello dei campi

### <a name="data-updates"></a>Aggiornamenti dei dati

L'aggiornamento di documenti esistenti in un indice con l'opzione Carica funziona allo stesso modo per i campi semplici e complessi, ovvero tutti i campi vengono sostituiti. Tuttavia, unione (o mergeOrUpload quando applicato a un documento esistente) non funzionano in tutti i campi. In particolare, merge non è la possibilità di unire elementi all'interno di una raccolta. Questo vale per le raccolte di tipi primitivi, nonché le raccolte complesse. Per aggiornare una raccolta, si sarà necessario per recuperare il valore della raccolta completa, apportare le modifiche e quindi includere la nuova raccolta nella richiesta di API di indice.


## <a name="searching-complex-fields"></a>La ricerca di campi complessi

Espressioni di ricerca in formato libero funzionano come previsto con i tipi complessi. Se corrisponde a qualsiasi campo ricercabile o sottocampo in qualsiasi punto in un documento, il documento stesso è una corrispondenza. 

Get di query più netti quando si dispone di più termini e operatori, e alcune condizioni hanno nomi di campo specificati, così come sono possibili con la [sintassi Lucene](query-lucene-syntax.md). Questa query, ad esempio, tenta di ottenere due termini, "Napoli" e "OR", in base a due campi secondari del campo dell'indirizzo:

```json
search=Address/City:Portland AND Address/State:OR
```

Le query simile alla seguente non sono correlate per la ricerca full-text (a differenza dei filtri, in cui eseguire query sui campi secondari di un insieme complesso, può essere correlata tramite uno o tutti, ad esempio una sottoquery correlata in SQL). Ciò significa che la query di Lucene precedente restituirà i documenti che contengono "Portland, Maine" nonché "Portland, Oregon" e altri città in Oregon. Questo avviene perché ogni clausola viene valutato rispetto a tutti i valori del campo specificato nell'intero documento, pertanto non presenta alcuna nozione di un "documento secondario corrente". 

 

## <a name="selecting-complex-fields"></a>Selezionare i campi complessi

Il `$select` parametro viene utilizzato per scegliere quali campi vengono restituiti nei risultati della ricerca. Per usare questo parametro per selezionare campi secondari specifici di un campo complesso, includere il campo padre e il sottocampo separati da una barra (`/`).

```json
$select=HotelName, Address/City, Rooms/BaseRate
```

Se si desidera che nei risultati della ricerca, i campi devono essere contrassegnati come recuperabile nell'indice. Solo i campi contrassegnati come recuperabile possono essere usati un `$select` istruzione. 


## <a name="filter-facet-and-sort-complex-fields"></a>Filtro, facet e ordinamento complessi campi

Lo stesso [sintassi del percorso OData](query-odata-filter-orderby-syntax.md) utilizzato per il filtro e natura prese in considerazione le ricerche sono anche utilizzabile per i facet, ordinamento e selezionano campi in una richiesta di ricerca. Per i tipi complessi, si applicano regole che determinano quali campi secondari possono essere contrassegnati come ordinabili o con facet. 

### <a name="faceting-sub-fields"></a>Uso di facet campi secondari 

Qualsiasi campo secondarie può essere contrassegnato come facetable a meno che non è di tipo `Edm.GeographyPoint` o `Collection(Edm.GeographyPoint)`. 

Quando vengono restituiti i conteggi dei documenti per la struttura di esplorazione in base a facet, i conteggi sono rispetto al documento padre (albergo), non ai documenti annidati all'interno di un insieme complesso (chat). Si supponga, ad esempio, che dispone di un hotel 20 stanze di tipo "gruppo". Dato questo parametro facet `facet=Rooms/Type`, il conteggio di facet verrà uno per il documento padre (hotel) e non a livello intermedio documenti secondari (chat). 

### <a name="sorting-complex-fields"></a>L'ordinamento campi complessi

Operazioni di ordinamento si applicano ai documenti (hotel) e non documenti secondari (chat). Quando si dispone di una raccolta di tipo complesso, ad esempio le chat, è importante tenere presente che non è possibile ordinare nella chat room affatto. È in realtà, non è possibile ordinare qualsiasi raccolta. 

Operazioni di ordinamento quando i campi sono a valore singolo, se un campo semplice, o come campo secondario in un tipo complesso. Ad esempio, `$orderby=Address/ZipCode` tipo complesso è ordinabile perché non esiste un solo codice postale per ogni albergo. 

Incrementa le regole relative all'ordinamento, all'interno di un campo di indice devono essere contrassegnati come filtrabile e ordinabile da usare un `$orderby` istruzione. 

## <a name="next-steps"></a>Passaggi successivi

 Provare il [set di dati Hotels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) nel **importare i dati** guidata. È necessario le informazioni di connessione di Cosmos DB specificate nel file Leggimi per accedere ai dati. 
 
 Con queste informazioni a disposizione, il primo passaggio della procedura guidata consiste nel creare una nuova origine dati Azure Cosmos DB. Ulteriormente nella procedura guidata, quando si arriva alla pagina di indice di destinazione, si aprirà un indice con i tipi complessi. Creare e caricare l'indice e quindi eseguire query per comprendere la nuova struttura.

> [!div class="nextstepaction"]
> [Guida introduttiva: creazione guidata del portale per l'importazione, l'indicizzazione e query](search-get-started-portal.md)