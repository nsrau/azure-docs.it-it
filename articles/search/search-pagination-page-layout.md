---
title: Come usare i risultati della ricerca
titleSuffix: Azure Cognitive Search
description: Strutturare e ordinare i risultati della ricerca, ottenere un numero di documenti e aggiungere l'esplorazione del contenuto ai risultati della ricerca in Azure ricerca cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: e583cedc04113615c50cc9906cbd11a99ff48683
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421720"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Come usare i risultati della ricerca in Azure ricerca cognitiva

Questo articolo illustra come ottenere una risposta alla query che restituisca un numero totale di documenti corrispondenti, risultati impaginati, risultati ordinati e termini evidenziati.

La struttura di una risposta è determinata dai parametri nella query: il [documento di ricerca](/rest/api/searchservice/Search-Documents) nell'API REST o la [classe SEARCHRESULTS](/dotnet/api/azure.search.documents.models.searchresults-1) in .NET SDK.

## <a name="result-composition"></a>Composizione risultato

Mentre un documento di ricerca può essere costituito da un numero elevato di campi, in genere sono necessari solo pochi per rappresentare ogni documento nel set di risultati. In una richiesta di query aggiungere `$select=<field list>` per specificare i campi da visualizzare nella risposta. Un campo deve essere attribuito come **recuperabile** nell'indice da includere in un risultato. 

I campi che funzionano meglio includono quelli che si differenziano e distinguono tra i documenti, fornendo informazioni sufficienti per invitare una risposta click-through sulla parte dell'utente. In un sito di e-commerce può essere un nome di prodotto, una descrizione, un marchio, un colore, una dimensione, un prezzo e una classificazione. Per l'esempio di Hotel-sample-index incorporato, potrebbe trattarsi di campi nell'esempio seguente:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> Se si vuole includere i file di immagine in un risultato, ad esempio un logo o una foto del prodotto, archiviarli all'esterno di Azure ricerca cognitiva, ma includere un campo nell'indice per fare riferimento all'URL dell'immagine nel documento di ricerca. Gli indici di esempio che supportano immagini nei risultati includono la demo **immobiliare-Sample-US** , disponibile in questa [Guida introduttiva](search-create-app-portal.md)e l' [app demo di New York City Jobs](https://aka.ms/azjobsdemo).

## <a name="paging-results"></a>Risultati di paging

Per impostazione predefinita, il motore di ricerca restituisce fino alle prime 50 corrispondenze, come determinato dal punteggio di ricerca se la query è una ricerca full-text o in un ordine arbitrario per le query di corrispondenza esatte.

Per restituire un numero diverso di documenti corrispondenti, aggiungere `$top` `$skip` i parametri e alla richiesta di query. Nell'elenco seguente viene illustrata la logica.

+ Aggiungere `$count=true` per ottenere un conteggio del numero totale di documenti corrispondenti all'interno di un indice.

+ Restituisce il primo set di 15 documenti corrispondenti, oltre a un conteggio delle corrispondenze totali: `GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ Restituisce il secondo set, ignorando le prime 15 per ottenere i successivi 15: `$top=15&$skip=15` . Eseguire la stessa operazione per il terzo set di 15: `$top=15&$skip=30`

I risultati delle query impaginate non sono necessariamente stabili se l'indice sottostante viene modificato. Il paging modifica il valore di `$skip` per ogni pagina, ma ogni query è indipendente e opera sulla visualizzazione corrente dei dati presenti nell'indice in fase di query (in altre parole, non è presente alcuna memorizzazione nella cache o snapshot dei risultati, ad esempio quelli presenti in un database per utilizzo generico).
 
Di seguito è riportato un esempio di come si potrebbero ottenere i duplicati. Si supponga che un indice con quattro documenti:

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
{ "id": "4", "rating": 1 }
```
 
Si supponga ora di voler restituire i risultati due alla volta, ordinati in base alla classificazione. Eseguire questa query per ottenere la prima pagina di risultati: `$top=2&$skip=0&$orderby=rating desc` , producendo i risultati seguenti:

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
```
 
Nel servizio, si supponga che un quinto documento venga aggiunto all'indice in tra le chiamate di query: `{ "id": "5", "rating": 4 }` .  Successivamente, si esegue una query per recuperare la seconda pagina: `$top=2&$skip=2&$orderby=rating desc` e ottenere i risultati seguenti:

```text
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
```
 
Si noti che il documento 2 viene recuperato due volte. Questo perché il nuovo documento 5 ha un valore maggiore per la classificazione, quindi Ordina prima del documento 2 e atterra nella prima pagina. Sebbene questo comportamento possa essere imprevisto, è tipico del comportamento di un motore di ricerca.

## <a name="ordering-results"></a>Ordinamento dei risultati

Per le query di ricerca full-text, i risultati vengono classificati automaticamente in base a un punteggio di ricerca, calcolato in base alla frequenza dei termini e alla prossimità in un documento, con punteggi più elevati che passano ai documenti con corrispondenze più o più sicure in un termine di ricerca. 

I punteggi di ricerca comportano un senso generale di rilevanza, riflettendo la forza della corrispondenza rispetto ad altri documenti nello stesso set di risultati. I punteggi non sono sempre coerenti da una query alla successiva, quindi, quando si lavora con le query, è possibile notare piccole discrepanze nel modo in cui i documenti di ricerca sono ordinati. Esistono diverse spiegazioni per il motivo per cui questo potrebbe verificarsi.

| Causa | Descrizione |
|-----------|-------------|
| Volatilità dei dati | Il contenuto dell'indice varia quando si aggiungono, modificano o eliminano documenti. Le frequenze dei termini cambiano quando gli aggiornamenti degli indici vengono elaborati nel tempo, influenzando i punteggi di ricerca dei documenti corrispondenti. |
| Più repliche | Per i servizi che usano più repliche, le query vengono eseguite su ogni replica in parallelo. Le statistiche di indice utilizzate per calcolare un punteggio di ricerca vengono calcolate in base alle singole repliche, con risultati Uniti e ordinati nella risposta alla query. Le repliche sono per lo più mirror, ma le statistiche possono variare a causa di piccole differenze nello stato. Ad esempio, una replica potrebbe avere eliminato i documenti che contribuiscono alle statistiche, che sono Stati Uniti da altre repliche. In genere, le differenze nelle statistiche per replica sono più evidenti negli indici più piccoli. |
| Punteggi identici | Se più documenti hanno lo stesso punteggio, è possibile che uno di essi venga visualizzato per primo.  |

### <a name="consistent-ordering"></a>Ordinamento coerente

Dato il Flex nell'ordinamento dei risultati, potrebbe essere necessario esplorare altre opzioni se la coerenza è un requisito dell'applicazione. L'approccio più semplice consiste nell'ordinamento in base a un valore di campo, ad esempio classificazione o data. Per gli scenari in cui si desidera eseguire l'ordinamento in base a un campo specifico, ad esempio una classificazione o una data, è possibile definire in modo esplicito un' [ `$orderby` espressione](query-odata-filter-orderby-syntax.md)che può essere applicata a qualsiasi campo indicizzato come **ordinabile**.

Un'altra opzione prevede l'uso di un [profilo di Punteggio personalizzato](index-add-scoring-profiles.md). I profili di Punteggio consentono un maggiore controllo sulla classificazione degli elementi nei risultati della ricerca, con la possibilità di incrementare le corrispondenze trovate in campi specifici. La logica di assegnazione dei punteggi aggiuntiva consente di eseguire l'override delle differenze minime tra le repliche perché i punteggi di ricerca per ogni documento sono più lontani. Per questo approccio è consigliabile usare l' [algoritmo di classificazione](index-ranking-similarity.md) .

## <a name="hit-highlighting"></a>Evidenziazione dei risultati

L'evidenziazione dei riscontri si riferisce alla formattazione del testo (ad esempio evidenziazioni grassetto o gialle) applicata ai termini corrispondenti in un risultato, semplificando l'individuazione della corrispondenza. Nella [richiesta di query](/rest/api/searchservice/search-documents)sono disponibili istruzioni per l'evidenziazione dei risultati. 

Per abilitare l'evidenziazione dei riscontri, aggiungere `highlight=[comma-delimited list of string fields]` per specificare i campi che utilizzeranno l'evidenziazione. L'evidenziazione è utile per i campi di contenuto più lunghi, ad esempio un campo di descrizione, in cui la corrispondenza non è immediatamente ovvia. Solo le definizioni di campo attribuite come **ricercabili** sono idonee per l'evidenziazione dei risultati.

Per impostazione predefinita, Azure ricerca cognitiva restituisce fino a cinque evidenziazioni per ogni campo. È possibile modificare questo numero accodando il campo a un trattino seguito da un numero intero. Ad esempio, `highlight=Description-10` restituisce fino a 10 evidenziazioni sul contenuto corrispondente nel campo Descrizione.

La formattazione viene applicata alle query a termini interi. Il tipo di formattazione è determinato dai tag, `highlightPreTag` e e `highlightPostTag` il codice gestisce la risposta, ad esempio applicando un tipo di carattere in grassetto o uno sfondo giallo.

Nell'esempio seguente, i termini "Sandy", "Sand", "beaches", "Beach" trovati all'interno del campo Description sono contrassegnati per l'evidenziazione. Le query che attivano l'espansione di query nel motore, ad esempio fuzzy e la ricerca con caratteri jolly, hanno un supporto limitato per l'evidenziazione dei risultati.

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2020-06-30 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

### <a name="new-behavior-starting-july-15"></a>Nuovo comportamento (a partire dal 15 luglio)

I servizi creati dopo il 15 luglio 2020 offriranno un'esperienza di evidenziazione diversa. I servizi creati prima di tale data non cambiano nel comportamento di evidenziazione. 

Con il nuovo comportamento:

* Verranno restituite solo le frasi che corrispondono alla query di frase completa. La query "Super Bowl" restituirà evidenziazioni come la seguente:

    ```html
    '<em>super bowl</em> is super awesome with a bowl of chips'
    ```
  Si noti che il termine *ciotola di chip* non presenta alcuna evidenziazione perché non corrisponde alla frase completa.

Quando si scrive codice client che implementa l'evidenziazione dei riscontri, tenere presente questa modifica. Si noti che questa operazione non avrà alcun effetto a meno che non si crei un servizio di ricerca completamente nuovo.

## <a name="next-steps"></a>Passaggi successivi

Per generare rapidamente una pagina di ricerca per il client, prendere in considerazione le opzioni seguenti:

+ Il [Generatore di applicazioni](search-create-app-portal.md), nel portale, crea una pagina HTML con una barra di ricerca, l'esplorazione in base a facet e l'area dei risultati che include le immagini.
+ [Creare la prima app in C#](tutorial-csharp-create-first-app.md) è un'esercitazione che crea un client funzionale. Il codice di esempio illustra le query impaginate, l'evidenziazione e l'ordinamento.

Alcuni esempi di codice includono un'interfaccia front-end Web, disponibile qui: [app demo di New York City Jobs](https://aka.ms/azjobsdemo), [codice di esempio JavaScript con un sito demo live](https://github.com/liamca/azure-search-javascript-samples)e [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).