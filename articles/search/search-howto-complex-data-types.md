---
title: Come modellare tipi di dati complessiHow to model complex data types
titleSuffix: Azure Cognitive Search
description: Le strutture di dati annidate o gerarchiche possono essere modellate in un indice di Ricerca cognitiva di Azure usando i tipi di dati ComplexType e Collections.Nested or hierarchical data structures can be modeled in an Azure Cognitive Search index using ComplexType and Collections data types.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2edd62825de08becf22f2f953a63a7f89f55e0a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283056"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>Come modellare tipi di dati complessi in Ricerca cognitiva di AzureHow to model complex data types in Azure Cognitive Search

I set di dati esterni usati per popolare un indice di Ricerca cognitiva di Azure possono avere molte forme. A volte includono sottostrutture gerarchiche o nidificate. Gli esempi possono includere più indirizzi per un singolo cliente, più colori e dimensioni per un singolo SKU, più autori di un singolo libro e così via. In termini di modellazione, è possibile visualizzare queste strutture denominate tipi di dati *complex*, *compound*, *composite*o *aggregati.* Il termine Ricerca cognitiva di Azure usa per questo concetto è **di tipo complesso.** In Ricerca cognitiva di Azure i tipi complessi vengono modellati usando **campi complessi.** Un campo complesso è un campo che contiene elementi figlio (sottocampi) che possono essere di qualsiasi tipo di dati, inclusi altri tipi complessi. Questo funziona in modo simile ai tipi di dati strutturati in un linguaggio di programmazione.

I campi complessi rappresentano un singolo oggetto nel documento o una matrice di oggetti, a seconda del tipo di dati. I campi `Edm.ComplexType` di tipo rappresentano singoli `Collection(Edm.ComplexType)` oggetti, mentre i campi di tipo rappresentano matrici di oggetti.

Ricerca cognitiva di Azure supporta in modo nativo raccolte e tipi complessi. Questi tipi consentono di modellare quasi qualsiasi struttura JSON in un indice di Ricerca cognitiva di Azure.These types allow you to model almost any JSON structure in an Azure Cognitive Search index. Nelle versioni precedenti delle API di Ricerca cognitiva di Azure era possibile importare solo set di righe appiattiti. Nella versione più recente, l'indice può ora corrispondere più strettamente ai dati di origine. In altre parole, se i dati di origine hanno tipi complessi, l'indice può avere anche tipi complessi.

Per iniziare, è consigliabile il set di dati Hotels , che è possibile caricare nella procedura guidata Importa dati nel portale di Azure.To get started, we recommend the [Hotels data set](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md), which you can load in the Import **data** wizard in the Azure portal. La procedura guidata rileva i tipi complessi nell'origine e suggerisce uno schema di indice in base alle strutture rilevate.

> [!Note]
> Il supporto per i `api-version=2019-05-06`tipi complessi è generalmente disponibile in . 
>
> Se la soluzione di ricerca si basa su soluzioni precedenti di set di dati bipiatti in una raccolta, è necessario modificare l'indice per includere tipi complessi come supportati nella versione API più recente. Per ulteriori informazioni sull'aggiornamento delle versioni delle API, vedere [Eseguire l'aggiornamento alla versione dell'API REST più recente](search-api-migration.md) o Aggiornamento alla versione più recente di [.NET SDK.](search-dotnet-sdk-migration-version-9.md)

## <a name="example-of-a-complex-structure"></a>Esempio di struttura complessa

Il documento JSON seguente è composto da campi semplici e campi complessi. I campi complessi, ad `Address` esempio e `Rooms`, hanno campi secondari. `Address`dispone di un singolo insieme di valori per tali sottocampi, poiché si tratta di un singolo oggetto nel documento. Al contrario, `Rooms` dispone di più set di valori per i relativi sottocampi, uno per ogni oggetto nella raccolta.

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
      "BaseRate": 96.99
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99
    },
  ]
}
```

## <a name="creating-complex-fields"></a>Creazione di campi complessi

Come con qualsiasi definizione di indice, è possibile usare il portale, [l'API REST](https://docs.microsoft.com/rest/api/searchservice/create-index)o [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) per creare uno schema che include tipi complessi. 

L'esempio seguente mostra uno schema di indice JSON con campi semplici, raccolte e tipi complessi. Si noti che all'interno di un tipo complesso, ogni sottocampo ha un tipo e può avere attributi, proprio come i campi di primo livello. Lo schema corrisponde ai dati di esempio precedenti. `Address`è un campo complesso che non è una raccolta (un hotel ha un indirizzo). `Rooms`è un campo di raccolta complesso (un hotel dispone di molte camere).

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

## <a name="updating-complex-fields"></a>Aggiornamento di campi complessi

Tutte le regole di [reindicizzazione](search-howto-reindex.md) che si applicano ai campi in generale si applicano ancora ai campi complessi. Ribadendo alcune delle regole principali qui, l'aggiunta di un campo non richiede una ricostruzione dell'indice, ma la maggior parte delle modifiche.

### <a name="structural-updates-to-the-definition"></a>Aggiornamenti strutturali alla definizione

È possibile aggiungere nuovi sottocampi a un campo complesso in qualsiasi momento senza la necessità di ricostruire un indice. Ad esempio, è consentito `Address` l'aggiunta di `Rooms` "Cap postale" a o "Servizi" a, proprio come l'aggiunta di un campo di primo livello a un indice. I documenti esistenti hanno un valore null per i nuovi campi fino a quando non si compilano esplicitamente tali campi aggiornando i dati.

Si noti che all'interno di un tipo complesso, ogni sottocampo ha un tipo e può avere attributi, proprio come i campi di primo livello

### <a name="data-updates"></a>Aggiornamenti dei dati

L'aggiornamento dei documenti `upload` esistenti in un indice con l'azione funziona allo stesso modo per i campi complessi e semplici: tutti i campi vengono sostituiti. Tuttavia, `merge` `mergeOrUpload` (o quando applicato a un documento esistente) non funziona allo stesso modo in tutti i campi. In `merge` particolare, non supporta l'unione di elementi all'interno di una raccolta. Questa limitazione esiste per le raccolte di tipi primitivi e raccolte complesse. Per aggiornare una raccolta, è necessario recuperare il valore completo della raccolta, apportare modifiche e quindi includere la nuova raccolta nella richiesta dell'API Index.

## <a name="searching-complex-fields"></a>Ricerca di campi complessi

Le espressioni di ricerca in formato libero funzionano come previsto con i tipi complessi. Se un campo o un sottocampo ricercabile in qualsiasi punto di un documento corrisponde, il documento stesso corrisponde.

Le query vengono più sfumate quando si dispone di più termini e operatori e alcuni termini hanno nomi di campo specificati, come è possibile con la [sintassi Lucene](query-lucene-syntax.md). Ad esempio, questa query tenta di far corrispondere due termini, "Portland" e "OR", rispetto a due sottocampi del campo Indirizzo:

    search=Address/City:Portland AND Address/State:OR

Query come questa non sono *correlate* per la ricerca full-text, a differenza dei filtri. Nei filtri, le query sui sottocampi di una raccolta [ `any` `all` ](search-query-odata-collection-operators.md)complessa vengono correlate utilizzando variabili di intervallo in o . La query Lucene precedente restituisce i documenti contenenti sia "Portland, Maine" e "Portland, Oregon", insieme ad altre città dell'Oregon. Ciò si applica perché ogni clausola si applica a tutti i valori del relativo campo nell'intero documento, quindi non esiste alcun concetto di "documento secondario corrente". Per altre informazioni, vedere [Informazioni sui filtri di raccolta OData in Ricerca cognitiva](search-query-understand-collection-filters.md)di Azure.For more information on this, see Understanding OData collection filters in Azure Cognitive Search .

## <a name="selecting-complex-fields"></a>Selezione di campi complessi

Il `$select` parametro viene utilizzato per scegliere i campi da restituire nei risultati della ricerca. Per utilizzare questo parametro per selezionare sottocampi specifici di un campo complesso,`/`includere il campo padre e il sottocampo separati da una barra ( ).

    $select=HotelName, Address/City, Rooms/BaseRate

I campi devono essere contrassegnati come recuperabili nell'indice se si desidera nei risultati della ricerca. In un `$select` rendiconto è possibile utilizzare solo i campi contrassegnati come Recuperabili.

## <a name="filter-facet-and-sort-complex-fields"></a>Filtrare, sfaccettare e ordinare campi complessi

La stessa sintassi del [percorso OData](query-odata-filter-orderby-syntax.md) utilizzata per il filtro e le ricerche in campo può essere utilizzata anche per il sfcomeface, ordinare e selezionare i campi in una richiesta di ricerca. Per i tipi complessi, si applicano regole che determinano quali sottocampi possono essere contrassegnati come ordinabili o facetable. Per ulteriori informazioni su queste regole, vedere le informazioni di [riferimento sull'API Create Index](/rest/api/searchservice/create-index).

### <a name="faceting-sub-fields"></a>Sottocampi di sfondata

Qualsiasi sottocampo può essere contrassegnato come facetable a meno che non sia di tipo `Edm.GeographyPoint` o `Collection(Edm.GeographyPoint)`.

I conteggi dei documenti restituiti nei risultati del facet vengono calcolati per il documento padre (un hotel), non per i documenti secondari in una raccolta complessa (stanze). Si supponga, ad esempio, che un hotel disponga di 20 camere di tipo "suite". Dato questo `facet=Rooms/Type`parametro facet, il conteggio delle sfaccettature sarà uno per l'hotel, non 20 per le camere.

### <a name="sorting-complex-fields"></a>Ordinamento di campi complessi

Le operazioni di ordinamento si applicano ai documenti (Hotel) e non ai documenti secondari (Camere). Quando si dispone di una raccolta di tipi complessi, ad esempio Rooms, è importante rendersi conto che non è possibile ordinare in camere a tutti. Inrealtà, non è possibile ordinare in base a qualsiasi raccolta.

Le operazioni di ordinamento funzionano quando i campi hanno un singolo valore per documento, se il campo è un campo semplice o un sottocampo in un tipo complesso. Ad esempio, `Address/City` è consentito essere ordinabile perché c'è `$orderby=Address/City` un solo indirizzo per hotel, quindi ordinerà gli hotel per città.

### <a name="filtering-on-complex-fields"></a>Filtraggio in base a campi complessi

È possibile fare riferimento ai sottocampi di un campo complesso in un'espressione di filtro. È sufficiente usare la stessa sintassi del [percorso OData](query-odata-filter-orderby-syntax.md) utilizzata per il deformato, l'ordinamento e la selezione dei campi. Ad esempio, il filtro seguente restituirà tutti gli hotel in Canada:

    $filter=Address/Country eq 'Canada'

Per filtrare in base a un campo di raccolta complesso, è possibile utilizzare **un'espressione lambda** con gli [ `any` operatori e `all` ](search-query-odata-collection-operators.md). In tal caso, la variabile di **intervallo** dell'espressione lambda è un oggetto con sottocampi. È possibile fare riferimento a tali sottocampi con la sintassi del percorso OData standard. Ad esempio, il seguente filtro restituirà tutti gli hotel con almeno una camera deluxe e tutte le camere non fumatori:

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

Come per i campi semplici di primo livello, i sottocampi semplici dei campi complessi `true` possono essere inclusi nei filtri solo se hanno l'attributo **filtrabile** impostato su nella definizione dell'indice. Per ulteriori informazioni, vedere le informazioni di [riferimento sull'API Create Index](/rest/api/searchservice/create-index).

## <a name="next-steps"></a>Passaggi successivi

Provare il set di [dati Hotels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) nell'Importazione guidata **dati.** Per accedere ai dati sono necessarie le informazioni di connessione Cosmos DB fornite nel file Leggimi.

Con queste informazioni in mano, il primo passaggio della procedura guidata consiste nel creare una nuova origine dati Cosmos DB di Azure.With that information in hand, your first step in the wizard is to create a new Azure Cosmos DB data source. Più avanti nella procedura guidata, quando si arriva alla pagina dell'indice di destinazione, verrà visualizzato un indice con tipi complessi. Creare e caricare questo indice, quindi eseguire query per comprendere la nuova struttura.

> [!div class="nextstepaction"]
> [Guida introduttiva: Creazione guidata portale per l'importazione, l'indicizzazione e le query](search-get-started-portal.md)
