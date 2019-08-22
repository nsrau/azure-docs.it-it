---
title: Filtri facet per l'esplorazione di ricerca nelle app - Ricerca di Azure
description: Filtrare i criteri in base a identità di sicurezza dell'utente, località geografica o valori numerici per ridurre i risultati della ricerca nelle query in Ricerca di Azure, un servizio di ricerca cloud ospitato in Microsoft Azure.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 5/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: a2fe29cf1d7c183aa62e6b86a4b29479d1f34ff8
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649873"
---
# <a name="how-to-build-a-facet-filter-in-azure-search"></a>Come creare un filtro facet in Ricerca di Azure 

L'esplorazione in base a facet è usata per i filtri autoindirizzati nei risultati della query in un'applicazione di ricerca, in cui l'applicazione offre i controlli dell'interfaccia utente per limitare l'ambito a gruppi di documenti (ad esempio, categorie o marchi) e Ricerca di Azure offre la struttura di dati che supporta l'esperienza. Questo articolo esamina rapidamente i passaggi di base per la creazione di una struttura di esplorazione in base a facet, a sostegno dell'esperienza di ricerca che si vuole offrire. 

> [!div class="checklist"]
> * Scegliere i campi per il filtro e i facet
> * Impostare gli attributi nel campo
> * Creare l'indice e caricare i dati
> * Aggiungere i filtri facet a una query
> * Gestire i risultati

I facet sono dinamici e vengono restituiti in seguito a una query. Le risposte alla ricerca includono le categorie di facet usate per esplorare i risultati. Se non si ha familiarità con i facet, l'esempio seguente illustra una struttura di esplorazione in base a facet.

  ![](./media/search-filters-facets/facet-nav.png)

Per altre informazioni sull'esplorazione in base a facet: Vedere [Come implementare l'esplorazione in base a facet in Ricerca di Azure](search-faceted-navigation.md).

## <a name="choose-fields"></a>Selezionare i campi

È possibile calcolare i facet su campi a valore singolo nonché su raccolte. I campi che funzionano meglio nell'esplorazione in base a facet hanno una bassa cardinalità: un numero ridotto di valori distinti che si ripetono in tutti i documenti nel corpus di ricerca (ad esempio, un elenco di colori, Paesi/aree geografiche o nomi di marchio). 

Il facet viene abilitato in base al campo quando si crea l'indice impostando l' `facetable` attributo su. `true` In genere, è necessario impostare `filterable` anche l' `true` attributo su per tali campi in modo che l'applicazione di ricerca possa filtrare in base ai facet selezionati dall'utente finale. 

Quando si crea un indice usando l'API REST, qualsiasi [tipo di campo](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) che potrebbe essere usato nell'esplorazione in base a `facetable` facet è contrassegnato come per impostazione predefinita:

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ Tipi di campi numerici `Edm.Int64`: `Edm.Int32`,,`Edm.Double`
+ Raccolte dei tipi precedenti (ad esempio, `Collection(Edm.String)` o) `Collection(Edm.Double)`

Non è possibile `Edm.GeographyPoint` usare `Collection(Edm.GeographyPoint)` i campi o nell'esplorazione in base a facet. I facet funzionano meglio nei campi con cardinalità bassa. A causa della risoluzione delle coordinate geografiche, è raro che tutti i due set di coordinate siano uguali in un determinato set di dati. Di conseguenza, i facet non sono supportati nelle coordinate di area geografica. È necessario un campo città o regione per esplorare una posizione in base a facet.

## <a name="set-attributes"></a>Impostare gli attributi

Gli attributi di indice che controllano la modalità di utilizzo di un campo vengono aggiunti alle singole definizioni di campo nell'indice. Nell'esempio seguente, i campi con cardinalità bassa, utili per i facet, sono costituiti da: `category` (Hotel, Motel, Ostello), `tags`e `rating`. Questi campi hanno gli `filterable` attributi `facetable` e impostati in modo esplicito nell'esempio seguente a scopo illustrativo. 

> [!Tip]
> Disattivare i facet per i campi che non devono essere usati come facet è una procedura consigliata per l'ottimizzazione delle prestazioni e dell'archiviazione. In particolare, i campi stringa per i valori univoci, ad esempio un ID o un nome di prodotto `"facetable": false` , devono essere impostati su per impedirne l'uso accidentale (e inefficace) nell'esplorazione in base a facet.


```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "baseRate", "type": "Edm.Double" },
    { "name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "hotelName", "type": "Edm.String", "facetable": false },
    { "name": "category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false },
    { "name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false },
    { "name": "lastRenovationDate", "type": "Edm.DateTimeOffset" },
    { "name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "location", "type": "Edm.GeographyPoint" }
  ]
}
```

> [!Note]
> Questa definizione di indice è copiata da [Creare un indice di Ricerca di Azure con l'API REST](https://docs.microsoft.com/azure/search/search-create-index-rest-api). È identica, ad eccezione di alcune lievi differenze nelle definizioni di campo. Gli `filterable` attributi `facetable` e vengono aggiunti in modo esplicito `tags`nei `parkingIncluded` `category`campi `smokingAllowed`,, `rating` , e. In pratica, `filterable` e `facetable` verrebbero abilitati per impostazione predefinita in questi campi quando si usa l'API REST. Quando si usa .NET SDK, questi attributi devono essere abilitati in modo esplicito.

## <a name="build-and-load-an-index"></a>Creare e caricare un indice

Un passaggio intermedio necessario (e probabilmente ovvio) consiste nel [compilare e popolare l'indice](https://docs.microsoft.com/azure/search/search-get-started-dotnet#1---create-index) prima di formulare una query. Questo passaggio viene citato qui per motivi di completezza. Un modo per determinare se l'indice è disponibile consiste nel controllare l'elenco degli indici nel [portale](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Aggiungere i filtri facet a una query

Nel codice dell'applicazione, creare una query che specifica tutte le parti di una query valida, tra cui espressioni di ricerca, facet, filtri, assegnazione dei punteggi dei profili, ovvero qualsiasi elemento che consente di formulare una richiesta. L'esempio seguente crea una richiesta che consente l'esplorazione in base a facet per il tipo di alloggio, classificazione e altri servizi offerti.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>Restituisce i risultati filtrati facendo clic su eventi

Quando l'utente finale fa clic su un valore di facet, il gestore per l'evento Click deve usare un'espressione di filtro per realizzare lo scopo dell'utente. Dato un `category` facet, fare clic sulla categoria "Motel" viene implementato `$filter` con un'espressione che seleziona le sistemazioni di quel tipo. Quando un utente fa clic su "Motel" per indicare che devono essere visualizzati solo i Motel, la query successiva che `$filter=category eq 'motel'`l'applicazione invia include.

Il frammento di codice seguente aggiunge la categoria al filtro, se un utente seleziona un valore del facet di categoria.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

Se l'utente fa clic su un valore di facet per un `tags`campo della raccolta come, ad esempio, il valore "pool", l'applicazione deve usare la sintassi di filtro seguente:`$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>Suggerimenti e soluzioni alternative

### <a name="initialize-a-page-with-facets-in-place"></a>Inizializzare una pagina con facet applicati

È possibile inviare una query come parte dell'inizializzazione della pagina, per inizializzare la pagina con una struttura di facet iniziale.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Mantenere una struttura di esplorazione in base a facet in modo asincrono per i risultati filtrati

Uno dei problemi dell'esplorazione in base a facet in Ricerca di Azure è che i facet esistono solo per i risultati correnti. Nella pratica, si conserva in genere un set statico di facet in modo che l'utente possa spostarsi in ordine inverso, ripercorrendo i passaggi per esplorare percorsi alternativi nel contenuto della ricerca. 

Sebbene sia un caso d'uso comune, non è un servizio che la struttura di esplorazione in base a facet fornisce in modo predefinito. Gli sviluppatori che intendono usare i facet statici aggirandone i limiti eseguono due query filtrate: una limitata ai risultati, l'altra usata per creare un elenco statico di facet per l'esplorazione.

## <a name="see-also"></a>Vedere anche

+ [Filtri in Ricerca di Azure](search-filters.md)
+ [Creare un indice: API REST](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) (API REST di Ricerca di documenti)
