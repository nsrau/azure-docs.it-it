---
title: Come utilizzare i risultati della ricerca
titleSuffix: Azure Cognitive Search
description: Strutturare e ordinare i risultati della ricerca, ottenere un numero di documenti e aggiungere l'esplorazione del contenuto per la ricerca nei risultati di Ricerca cognitiva di Azure.Structure and sort search results, get a document count, and add content navigation to search results in Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 451e83fa6ab547536a4cfd85304930e749a8247f
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998387"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Come usare i risultati della ricerca in Ricerca cognitiva di AzureHow to work with search results in Azure Cognitive Search

Questo articolo spiega come ottenere una risposta alla query che ritorna con un conteggio totale di documenti corrispondenti, risultati impaginati, risultati ordinati e termini evidenziati dall'hit.

La struttura di una risposta è determinata dai parametri nella query: Documento di [ricerca](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) nell'API REST o [Classe DocumentSearchResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) in .NET SDK.

## <a name="result-composition"></a>Composizione dei risultati

Mentre un documento di ricerca può essere costituito da un numero elevato di campi, in genere solo alcuni sono necessari per rappresentare ogni documento nel set di risultati. In una richiesta `$select=<field list>` di query, accodare per specificare quali campi visualizzare nella risposta. Un campo deve essere attribuito come **Recuperabile** nell'indice per essere incluso in un risultato. 

I campi che funzionano meglio includono quelli che contrastano e differenziano tra i documenti, fornendo informazioni sufficienti per invitare una risposta di click-through da parte dell'utente. In un sito di e-commerce, potrebbe essere un nome di prodotto, descrizione, marca, colore, dimensioni, prezzo e valutazione. Per l'esempio incorporato hotels-sample-index, potrebbero trattarsi di campi nell'esempio seguente:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> Se si desidera includere file di immagine in un risultato, ad esempio una foto del prodotto o un logo, archiviarli all'esterno di Ricerca cognitiva di Azure, ma includere un campo nell'indice per fare riferimento all'URL dell'immagine nel documento di ricerca. Gli indici di esempio che supportano le immagini nei risultati includono la demo **realestate-sample-us,** presente in questa [guida introduttiva,](search-create-app-portal.md)e l'app [demo di New York City Jobs](https://aka.ms/azjobsdemo).

## <a name="paging-results"></a>Risultati di paging

Per impostazione predefinita, il motore di ricerca restituisce fino alle prime 50 corrispondenze, come determinato dal punteggio di ricerca se la query è la ricerca full-text o in un ordine arbitrario per le query di corrispondenza esatta.

Per restituire un numero diverso `$top` di `$skip` documenti corrispondenti, aggiungere e i parametri alla richiesta di query. Nell'elenco seguente viene illustrata la logica.

+ Aggiungere `$count=true` per ottenere un conteggio del numero totale di documenti corrispondenti all'interno di un indice.

+ Restituisce il primo set di 15 documenti corrispondenti più un conteggio delle corrispondenze totali:`GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ Restituire il secondo set, saltando i primi 15 `$top=15&$skip=15`per ottenere il successivo 15: . Fate lo stesso per il terzo set di 15:`$top=15&$skip=30`

Non è garantito che i risultati delle query impaginate siano stabili se l'indice sottostante viene modificato. Il paging modifica `$skip` il valore di per ogni pagina, ma ogni query è indipendente e opera sulla visualizzazione corrente dei dati come esiste nell'indice in fase di query (in altre parole, non è presente la memorizzazione nella cache o lo snapshot dei risultati, ad esempio quelli trovati in un database di uso generale).
 
Di seguito è riportato un esempio di come si potrebbero ottenere duplicati. Si supponga un indice con quattro documenti:

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
    { "id": "4", "rating": 1 }
 
Si supponga ora di voler restituire i risultati due alla volta, ordinati in base alla classificazione. È necessario eseguire questa query per ottenere `$top=2&$skip=0&$orderby=rating desc`la prima pagina di risultati: , producendo i seguenti risultati:

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
 
Nel servizio, si supponga che un quinto documento venga `{ "id": "5", "rating": 4 }`aggiunto all'indice tra le chiamate di query: .  Poco dopo, si esegue una query per `$top=2&$skip=2&$orderby=rating desc`recuperare la seconda pagina: , e ottenere i seguenti risultati:

    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
 
Si noti che il documento 2 viene recuperato due volte. Questo perché il nuovo documento 5 ha un valore maggiore per la valutazione, quindi si ordina prima del documento 2 e atterra sulla prima pagina. Anche se questo comportamento potrebbe essere imprevisto, è tipico di come si comporta un motore di ricerca.

## <a name="ordering-results"></a>Ordinamento dei risultati

Per le query di ricerca full-text, i risultati vengono classificati automaticamente in base a un punteggio di ricerca, calcolato in base alla frequenza dei termini e alla prossimità in un documento, con punteggi più alti che vanno a documenti con più o più corrispondenze più forti in un termine di ricerca. 

I punteggi di ricerca mostrano un senso generale di pertinenza, che riflette l'intensità della corrispondenza rispetto ad altri documenti nello stesso set di risultati. I punteggi non sono sempre coerenti da una query all'altra, pertanto quando si lavora con le query, è possibile notare piccole discrepanze nel modo in cui i documenti di ricerca vengono ordinati. Ci sono diverse spiegazioni per cui questo potrebbe verificarsi.

| Causa | Descrizione |
|-----------|-------------|
| Volatilità dei dati | Il contenuto dell'indice varia quando si aggiungono, modificano o eliminano documenti. Le frequenze dei termini cambieranno man mano che gli aggiornamenti dell'indice vengono elaborati nel tempo, influenzando i punteggi di ricerca dei documenti corrispondenti. |
| Repliche multiple | Per i servizi che usano più repliche, le query vengono eseguite su ogni replica in parallelo. Le statistiche dell'indice utilizzate per calcolare un punteggio di ricerca vengono calcolate in base alla replica, con i risultati uniti e ordinati nella risposta alla query. Le repliche sono per lo più specchi l'uno dell'altro, ma le statistiche possono differire a causa di piccole differenze di stato. Ad esempio, una replica potrebbe aver eliminato i documenti che contribuiscono alle proprie statistiche, che sono state unite da altre repliche. In genere, le differenze nelle statistiche per replica sono più evidenti negli indici più piccoli. |
| Punteggi identici | Se più documenti hanno lo stesso punteggio, uno di essi potrebbe apparire per primo.  |

### <a name="consistent-ordering"></a>Ordinamento coerente

Data la flessibilità nell'ordinamento dei risultati, è possibile esplorare altre opzioni se la coerenza è un requisito dell'applicazione. L'approccio più semplice consiste nell'ordinamento in base a un valore di campo, ad esempio valutazione o data. Per gli scenari in cui si desidera eseguire l'ordinamento in base a un campo specifico, ad esempio una classificazione o una data, è possibile definire in modo esplicito [ `$orderby` un'espressione](query-odata-filter-orderby-syntax.md), che può essere applicata a qualsiasi campo indicizzato come **Ordinabile.**

Un'altra opzione consiste nell'utilizzare un profilo di [punteggio personalizzato.](index-add-scoring-profiles.md) I profili di punteggio ti offrono un maggiore controllo sulla classificazione degli elementi nei risultati di ricerca, con la possibilità di mettere in evidenza le corrispondenze trovate in campi specifici. La logica di assegnazione del punteggio aggiuntiva consente di ignorare le differenze minori tra le repliche perché i punteggi di ricerca per ogni documento sono più distanti. È consigliabile [l'algoritmo](index-ranking-similarity.md) di classificazione per questo approccio.

## <a name="hit-highlighting"></a>Evidenziazione dei risultati

L'evidenziazione dei risultati si riferisce alla formattazione del testo (ad esempio evidenziazioni in grassetto o gialle) applicata al termine corrispondente in un risultato, rendendo più semplice individuare la corrispondenza. Le istruzioni di evidenziazione dei risultati vengono fornite nella [richiesta di query.](https://docs.microsoft.com/rest/api/searchservice/search-documents) Il motore di ricerca racchiude il `highlightPreTag` `highlightPostTag`termine corrispondente tra tag e , e il codice gestisce la risposta, ad esempio l'applicazione di un carattere in grassetto.

La formattazione viene applicata alle query a termine completo. Nell'esempio seguente, i termini "sandy", "sabbia", "spiagge", "spiaggia" trovati all'interno del campo Descrizione sono contrassegnati per l'evidenziazione. Le query che attivano l'espansione delle query nel motore, ad esempio la ricerca fuzzy e con caratteri jolly, hanno un supporto limitato per l'evidenziazione dei risultati.

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2019-05-06 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

> [!IMPORTANT]
> I servizi creati dopo il 15 luglio 2020 offriranno un'esperienza di evidenziazione diversa. I servizi creati prima di tale data non cambieranno nel loro comportamento di evidenziazione. Con questa modifica, verranno restituite solo le frasi che corrispondono alla query completa della frase. Inoltre, sarà possibile specificare la dimensione del frammento restituito per l'evidenziazione.
>
> Quando si scrive codice client che implementa l'evidenziazione dei risultati, tenere presente questa modifica. Si noti che questo non avrà alcun impatto su di voi a meno che non si crea un servizio di ricerca completamente nuovo.

## <a name="next-steps"></a>Passaggi successivi

Per generare rapidamente una pagina di ricerca per il client, considerare le seguenti opzioni:

+ [Application Generator](search-create-app-portal.md), nel portale, crea una pagina HTML con una barra di ricerca, un'esplorazione in base a facet e un'area dei risultati che include immagini.
+ [Creare la prima app in C ,](tutorial-csharp-create-first-app.md) è un'esercitazione che crea un client funzionale. Il codice di esempio illustra le query impaginate, l'evidenziazione dei risultati e l'ordinamento.

Diversi esempi di codice includono un'interfaccia front-end Web, che è possibile trovare qui: [New York City Jobs app demo](https://aka.ms/azjobsdemo), codice di esempio [JavaScript con un sito demo dal vivo](https://github.com/liamca/azure-search-javascript-samples)e [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).