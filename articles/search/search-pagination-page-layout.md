---
title: Come usare i risultati della ricerca - Ricerca di Azure
description: Strutturare e ordinare i risultati della ricerca, ottenere un numero di documenti e aggiungere l'esplorazione dei contenuti ai risultati della ricerca in Ricerca di Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7a4423449931b74afa15ca238c611f54b071402f
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339296"
---
# <a name="how-to-work-with-search-results-in-azure-search"></a>Come usare i risultati della ricerca in Ricerca di Azure
In questo articolo vengono fornite indicazioni su come implementare elementi standard di una pagina di risultati della ricerca, ad esempio i conteggi totali, il recupero di documenti, i criteri di ordinamento e l'esplorazione. Le opzioni relative alla pagina che forniscono dati o informazioni per i risultati della ricerca vengono specificate tramite richieste [Cerca nel documento](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) inviate al servizio Ricerca di Azure. 

Nell'API REST le richieste includono un comando GET, un percorso e parametri di query che indicano al servizio quali elementi sono richiesti e come formulare la risposta. In .NET SDK l'API equivalente è [DocumentSearchResult Class](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult?view=azure-dotnet).

Alcuni esempi di codice includono un'interfaccia front-end Web, che è possibile trovare in [New York City Jobs demo app](https://azjobsdemo.azurewebsites.net/) (App demo relativa alle offerte di lavoro della città di New York) e [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).

> [!NOTE]
> Una richiesta valida include diversi elementi, ad esempio URL e percorso del servizio, verbo HTTP, `api-version`, e così via. Per brevità, gli esempi sono stati tagliati in modo da evidenziare solo la sintassi rilevante per l'impaginazione. Per altre informazioni sulla sintassi richiesta, vedere [Azure Search Service REST](https://docs.microsoft.com/rest/api/searchservice).
>

## <a name="total-hits-and-page-counts"></a>Corrispondenze totali e conteggi delle pagine
La visualizzazione del numero totale di risultati ottenuti da una query e la restituzione di tali risultati in blocchi più piccoli è fondamentale per tutte le pagine di ricerca.

![][1]

In Ricerca di Azure è possibile utilizzare i parametri `$count`, `$top`, e `$skip` per restituire questi valori. Nell'esempio seguente viene illustrata una richiesta di esempio per i riscontri totali in un indice denominato "onlineCatalog", restituite come `@OData.count`:

        GET /indexes/onlineCatalog/docs?$count=true

Recuperare i documenti in gruppi di 15 e visualizzare le corrispondenze totali, a partire dalla prima pagina:

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

L'impaginazione dei risultati richiede sia `$top` che `$skip`, dove `$top` specifica il numero di elementi da restituire in un batch e `$skip` specifica il numero di elementi da ignorare. Nell'esempio seguente, in ogni pagina vengono visualizzati 15 elementi, indicati da salti incrementali nel parametro `$skip` .

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=15&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=30&$count=true

## <a name="layout"></a>Layout
In una pagina di risultati della ricerca, è possibile visualizzare un'immagine di anteprima, un sottoinsieme dei campi e un collegamento a una pagina di prodotto completa.

 ![][2]

In Ricerca di Azure è preferibile utilizzare `$select` e un comando di ricerca per implementare questa esperienza.

Per restituire un sottoinsieme di campi per un layout affiancato:

        GET /indexes/ onlineCatalog/docs?search=*&$select=productName,imageFile,description,price,rating 

Non è possibile eseguire ricerche direttamente in immagini e file multimediali, che devono essere archiviati in un'altra piattaforma di archiviazione, ad esempio la risorsa di archiviazione Blob di Azure, per ridurre i costi. Nell'indice e nei documenti, definire un campo che contiene l'indirizzo URL del contenuto esterno. È quindi possibile utilizzare il campo come riferimento a un'immagine. L'URL dell'immagine deve essere nel documento.

Per recuperare una pagina di descrizione del prodotto per un evento **onClick** , utilizzare [Documento ricerca](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) per passare la chiave del documento da recuperare. Il tipo di dati della chiave è `Edm.String`. In questo esempio è *246810*. 

        GET /indexes/onlineCatalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Ordinare per pertinenza, classificazione o prezzo
In genere per impostazione predefinita i risultati vengono ordinati per pertinenza, ma è comune offrire alternative in modo che i clienti possano ridisporre rapidamente i risultati in un ordine di priorità diverso.

 ![][3]

In Ricerca di Azure l'ordinamento è basato sull'espressione `$orderby` per tutti i campi indicizzati come `"Sortable": true.` Una clausola `$orderby` è un'espressione OData. Per informazioni sulla sintassi, vedere [Sintassi delle espressioni OData filter e order-by](query-odata-filter-orderby-syntax.md).

La pertinenza è fortemente associata ai profili di punteggio. È possibile utilizzare il punteggio predefinito, che si basa sull'analisi del testo e sulle statistiche, per ordinare tutti i risultati, con punteggi più alti ai documenti che contengono più corrispondenze o corrispondenze migliori con un termine di ricerca.

Gli ordinamenti alternativi sono in genere associati agli eventi **onClick** che richiamano il metodo che genera il criterio di ordinamento. Si consideri, ad esempio, questo elemento di pagina:

 ![][4]

Creare un metodo che accetta l'opzione di ordinamento selezionata come input e restituisce un elenco ordinato per i criteri associati a tale opzione.

 ![][5]

> [!NOTE]
> Mentre l'assegnazione del punteggio predefinita è sufficiente per molti scenari, è consigliabile invece basare la pertinenza su un profilo di punteggio personalizzato. Un profilo di punteggio personalizzato offre un modo per favorire gli elementi più vantaggiosi per l'azienda. Per altre informazioni, vedere [Aggiungere profili di punteggio a un indice di ricerca](index-add-scoring-profiles.md). 
> 
> 

## <a name="faceted-navigation"></a>Esplorazione in base a facet
L’esplorazione di ricerca è comune in una pagina di risultati, che spesso si trova all'inizio di una pagina o sul lato. In Ricerca di Azure, l’esplorazione basata su facet fornisce una ricerca autoindirizzata in base a filtri predefiniti. Per maggiori informazioni vendere [Esplorazione basata su facet in Ricerca di Azure](search-faceted-navigation.md) .

## <a name="filters-at-the-page-level"></a>Filtri a livello di pagina
Se la progettazione della soluzione include pagine di ricerca dedicate per specifici tipi di contenuto (ad esempio un'applicazione di vendita in linea con servizi elencati nella parte superiore della pagina), è possibile inserire un'[espressione di filtro](search-filters.md) insieme a un evento **onClick** per aprire una pagina in uno stato prefiltrato. 

È possibile inviare un filtro con o senza espressione di ricerca. Ad esempio, la seguente richiesta filtrerà la marca, restituendo solo i documenti ad essa corrispondenti.

        GET /indexes/onlineCatalog/docs?$filter=brandname eq ‘Microsoft’ and category eq ‘Games’

Vedere [Ricerca nei documenti (API di Ricerca di Azure)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) per altre informazioni sulle espressioni `$filter`.

## <a name="see-also"></a>Vedere anche
* [API REST per il servizio Ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice)
* [Operazioni sugli indici](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
* [Operazioni sui documenti](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
* [Esplorazione in base a facet in Ricerca di Azure](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png 
