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
ms.date: 06/13/2019
ms.custom: seodec2018
ms.openlocfilehash: 61f2094449995e26c3d321aaf35deb3d60ff250c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056598"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Come modellare tipi di dati complessi in Ricerca di Azure

Set di dati esterni utilizzati per popolare un indice di ricerca di Azure sono disponibili in molte forme. Includono talvolta sottostrutture gerarchiche o annidate. Alcuni esempi potrebbero includere più indirizzi per un singolo cliente, più colori e dimensioni per un singolo SKU, più autori di un singolo libro e così via. In termini di modellazione, è possibile visualizzare queste strutture dette *complesse*, *composta*, *composito*, o *aggregazione* i tipi di dati. Il termine di ricerca di Azure Usa per questo concetto è **tipo complesso**. In ricerca di Azure, i tipi complessi vengono modellati utilizzando **campi complessi**. Un campo complesso è un campo che contiene gli elementi figlio (campi secondari) che possono essere di qualsiasi tipo di dati, inclusi altri tipi complessi. Questo funziona in modo simile come tipi di dati strutturati in un linguaggio di programmazione.

Campi complessi rappresentano un singolo oggetto nel documento o una matrice di oggetti, a seconda del tipo di dati. I campi di tipo `Edm.ComplexType` rappresentano oggetti singoli, mentre i campi di tipo `Collection(Edm.ComplexType)` rappresentano matrici di oggetti.

Ricerca di Azure supporta in modo nativo le raccolte e tipi complessi. Questi tipi consentono di modellare quasi ogni struttura JSON in un indice di ricerca di Azure. Nelle versioni precedenti delle API di ricerca di Azure, solo bidimensionale è stato possibile importare i set di righe. Nella versione più recente, l'indice a questo punto può corrispondere più da vicino ai dati di origine. In altre parole, se i dati di origine includono i tipi complessi, l'indice può avere tipi complessi anche.

Per iniziare, è consigliabile il [set di dati Hotels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md), che è possibile caricare nel **importare i dati** guidata nel portale di Azure. La procedura guidata rileva i tipi complessi nell'origine e suggerisce uno schema di indice basato sulle strutture rilevate.

> [!Note]
> Supporto per i tipi complessi è disponibile a livello generale in `api-version=2019-05-06`. 
>
> Se la soluzione di ricerca si basa su versioni precedenti soluzioni alternative dei set di dati bidimensionale in una raccolta, è necessario modificare l'indice per includere i tipi complessi come supportato nella versione più recente dell'API. Per altre informazioni sull'aggiornamento di versioni dell'API, vedere [l'aggiornamento alla versione più recente API REST](search-api-migration.md) oppure [esegue l'aggiornamento alla versione più recente del SDK di .NET](search-dotnet-sdk-migration-version-9.md).

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

Nell'esempio seguente viene illustrato uno schema di indice JSON con semplici campi, raccolte e tipi complessi. Si noti che all'interno di un tipo complesso, ogni campo secondario dispone di un tipo e possa avere attributi, i campi solo di primo livello. Lo schema corrisponde ai dati di esempio precedenti. `Address` è un campo di tipo complesso che non è una raccolta (un hotel presenta un solo indirizzo). `Rooms` è un campo di raccolta complessa (dispone di un hotel chat molti).

<!---
For indexes used in a [push-model data import](search-what-is-data-import.md) strategy, where you are pushing a JSON data set to an Azure Search index, you can only have the basic syntax shown here: single complex types like `Address`, or a `Collection(Edm.ComplexType)` like `Rooms`. You cannot have complex types nested inside other complex types in an index used for push-model data ingestion.

Indexers are a different story. When defining an indexer, in particular one used to build a knowledge store, your index can have nested complex types. An indexer is able to hold a chain of complex data structures in-memory, and when it includes a skillset, it can support highly complex data forms. For more information and an example, see [How to get started with knowledge store](knowledge-store-howto.md).
-->

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>L'aggiornamento di campi complessa

Tutti i [la reindicizzazione delle regole](search-howto-reindex.md) che si applicano a campi comunque si applicano in genere ai campi complessi. Incrementa alcune delle regole principale in questo caso, aggiunta di un campo non richiede una ricompilazione dell'indice, ma eseguire la maggior parte delle modifiche.

### <a name="structural-updates-to-the-definition"></a>Aggiornamenti strutturali alla definizione

È possibile aggiungere nuovi campi secondari a un campo complesso in qualsiasi momento senza la necessità di una ricompilazione dell'indice. Ad esempio, aggiungendo "ZipCode" per `Address` o "Servizi" per `Rooms` è consentito, esattamente come l'aggiunta di un campo di primo livello a un indice. I documenti esistenti hanno un valore null per i nuovi campi fino a quando non esplicitamente popolare questi campi mediante l'aggiornamento dei dati.

Si noti che all'interno di un tipo complesso, ogni campo secondario ha un tipo e possa avere attributi, solo come primo livello dei campi

### <a name="data-updates"></a>Aggiornamenti dei dati

Aggiornamento di documenti esistenti in un indice con il `upload` azione funziona allo stesso modo per i campi semplici e complessi, ovvero tutti i campi vengono sostituiti. Tuttavia `merge` (o `mergeOrUpload` quando applicato a un documento esistente) non funzionano allo stesso in tutti i campi. In particolare, `merge` non supporta l'unione di elementi all'interno di una raccolta. Questa limitazione esiste per le raccolte di tipi primitivi e raccolte complesse. Per aggiornare una raccolta, si sarà necessario recuperare il valore della raccolta completa, apportare modifiche e quindi includere la nuova raccolta nella richiesta di API di indice.

## <a name="searching-complex-fields"></a>La ricerca di campi complessi

Espressioni di ricerca in formato libero funzionano come previsto con i tipi complessi. Se corrisponde a qualsiasi campo ricercabile o sottocampo in qualsiasi punto in un documento, il documento stesso è una corrispondenza.

Get di query più netti quando si dispone di più termini e operatori, e alcune condizioni hanno nomi di campo specificati, così come sono possibili con la [sintassi Lucene](query-lucene-syntax.md). Questa query, ad esempio, tenta di ottenere due termini, "Napoli" e "OR", in base a due campi secondari del campo dell'indirizzo:

    search=Address/City:Portland AND Address/State:OR

Esegue una query come questa sono *non correlate* per la ricerca full-text, a differenza dei filtri. Nei filtri, eseguire query sui campi secondari di un insieme complesso sono correlate utilizzando le variabili di intervallo nella [ `any` oppure `all` ](search-query-odata-collection-operators.md). La query di Lucene precedente restituisce documenti che contengono sia "Portland, Maine" e "Portland, Oregon", insieme a altra città in Oregon. Ciò accade perché ogni clausola si applica a tutti i valori del campo nell'intero documento, pertanto non presenta alcun concetto di un "documento secondario corrente". Per altre informazioni, vedere [filtri di raccolta di informazioni su OData in ricerca di Azure](search-query-understand-collection-filters.md).

## <a name="selecting-complex-fields"></a>Selezionare i campi complessi

Il `$select` parametro viene utilizzato per scegliere quali campi vengono restituiti nei risultati della ricerca. Per usare questo parametro per selezionare campi secondari specifici di un campo complesso, includere il campo padre e il sottocampo separati da una barra (`/`).

    $select=HotelName, Address/City, Rooms/BaseRate

Se si desidera che nei risultati della ricerca, i campi devono essere contrassegnati come recuperabile nell'indice. Solo i campi contrassegnati come recuperabile possono essere usati un `$select` istruzione.

## <a name="filter-facet-and-sort-complex-fields"></a>Filtro, facet e ordinamento complessi campi

Lo stesso [sintassi del percorso OData](query-odata-filter-orderby-syntax.md) utilizzato per il filtro e natura prese in considerazione le ricerche sono anche utilizzabile per i facet, ordinamento e selezionano campi in una richiesta di ricerca. Per i tipi complessi, si applicano regole che determinano quali campi secondari possono essere contrassegnati come ordinabili o con facet. Per altre informazioni su queste regole, vedere la [riferimento API di creazione dell'indice](https://docs.microsoft.com/rest/api/searchservice/create-index#request).

### <a name="faceting-sub-fields"></a>Uso di facet campi secondari

Qualsiasi campo secondarie può essere contrassegnato come facetable a meno che non è di tipo `Edm.GeographyPoint` o `Collection(Edm.GeographyPoint)`.

I conteggi di documenti restituiti nei risultati del facet vengono calcolati per il documento padre (albergo), non i documenti secondari in un insieme complesso (chat). Si supponga, ad esempio, che dispone di un hotel 20 stanze di tipo "gruppo". Dato questo parametro facet `facet=Rooms/Type`, il conteggio di facet sarà uno per hotel, non i 20 per la chat.

### <a name="sorting-complex-fields"></a>L'ordinamento campi complessi

Operazioni di ordinamento si applicano ai documenti (hotel) e non documenti secondari (chat). Quando si dispone di una raccolta di tipo complesso, ad esempio le chat, è importante tenere presente che non è possibile ordinare nella chat room affatto. È in realtà, non è possibile ordinare qualsiasi raccolta.

Operazioni di ordinamento funzionano quando i campi hanno un singolo valore per ogni documento, se il campo è un campo semplice, o un campo secondarie in un tipo complesso. Ad esempio, `Address/City` può essere ordinabile perché è presente solo un indirizzo per ogni albergo, pertanto `$orderby=Address/City` ordinerà gli hotel in base alla città.

### <a name="filtering-on-complex-fields"></a>Filtrando i campi complessi

È possibile fare riferimento a campi secondari di un campo in un'espressione di filtro complesso. Usare semplicemente lo stesso [sintassi del percorso OData](query-odata-filter-orderby-syntax.md) che viene usato per i facet, ordinamento e la selezione di campi. Ad esempio, il filtro seguente restituirà tutti gli alberghi in Canada:

    $filter=Address/Country eq 'Canada'

Per applicare filtri a un campo di raccolta complessa, è possibile usare una **espressione lambda** con il [ `any` e `all` operatori](search-query-odata-collection-operators.md). In tal caso, il **variabile di intervallo** dell'espressione lambda espressione è un oggetto con campi secondari. È possibile fare riferimento a tali campi secondari con la sintassi del percorso OData standard. Ad esempio, il filtro seguente restituirà tutti gli alberghi con almeno una chat room deluxe e tutti i non-fumatori chat:

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

Come con campi semplici di livello superiore, semplici campi secondari dei campi complessi possono essere inclusa solo nei filtri se hanno le **filtrabile** attributo impostato su `true` nella definizione dell'indice. Per altre informazioni, vedere la [riferimento API di creazione dell'indice](https://docs.microsoft.com/rest/api/searchservice/create-index#request).

## <a name="next-steps"></a>Passaggi successivi

Provare il [set di dati Hotels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) nel **importare i dati** guidata. È necessario le informazioni di connessione di Cosmos DB specificate nel file Leggimi per accedere ai dati.

Con queste informazioni a disposizione, il primo passaggio della procedura guidata consiste nel creare una nuova origine dati Azure Cosmos DB. Ulteriormente nella procedura guidata, quando si arriva alla pagina di indice di destinazione, si noterà un indice con tipi complessi. Creare e caricare l'indice e quindi eseguire query per comprendere la nuova struttura.

> [!div class="nextstepaction"]
> [Guida introduttiva: creazione guidata del portale per l'importazione, l'indicizzazione e query](search-get-started-portal.md)