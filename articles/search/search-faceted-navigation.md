---
title: Come implementare l'esplorazione in base a facet in Ricerca di Azure | Documentazione Microsoft
description: Aggiungere Esplorazione in base a facet alle applicazioni che si integrano con la Ricerca di Azure, un servizio di ricerca ospitato sul cloud in Microsoft Azure.
services: search
documentationcenter: 
author: HeidiSteen
manager: mblythe
editor: 
ms.assetid: cdf98fd4-63fd-4b50-b0b0-835cb08ad4d3
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 3/10/2017
ms.author: heidist
ms.translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: 413f498eeb0bbc9a971c7a65200ed2fd8caa9aaf
ms.contentlocale: it-it
ms.lasthandoff: 03/16/2017

---
# <a name="how-to-implement-faceted-navigation-in-azure-search"></a>Come implementare l'esplorazione in base a facet in Ricerca di Azure
L'esplorazione in base a facet è un meccanismo di filtro che consente un'esplorazione drill-down mirata nelle applicazioni di ricerca. Il termine "esplorazione in base a facet" può essere poco noto, ma probabilmente è già stato usato in precedenza. Come illustrato nell'esempio seguente, l'esplorazione in base a facet non è rappresentata dalle categorie usate per filtrare i risultati.

 ![Job Portal Demo di Ricerca di Azure][1]

L'esplorazione in base a facet è un punto di partenza alternativo per la ricerca. Offre una valida alternativa all'immissione manuale di espressioni di ricerca complesse. I facet consentono di trovare ciò che si sta cercando con una percentuale di successo accertata. Per gli sviluppatori i facet consentono di esporre i criteri di ricerca più utili per lo spostamento del corpo di ricerca. Nelle applicazioni di vendita online, l'esplorazione in base a facet si basa spesso sulle marche, sui reparti (scarpe per bambini), sulla taglia, sul prezzo, sulla popolarità e sulle classificazioni. 

L'implementazione dell'esplorazione in base a facet si distingue tra le tecnologie di ricerca. Nella Ricerca di Azure, l'esplorazione in base a facet viene creata in fase di query, usando campi attribuiti precedenti nello schema.

-   Le query compilate dall'applicazione devono consentire l'invio dei *parametri di query facet* per ottenere i valori di filtro facet disponibili per il set di risultati dei documenti.

-   Per ridurre effettivamente il set di risultati dei risultati, l'applicazione deve applicare anche un'espressione `$filter`.

Nello sviluppo di applicazioni la scrittura di codice che crea query costituisce la maggior parte del lavoro. Molti dei comportamenti dell'applicazione previsti per l'esplorazione in base a facet vengono forniti dal servizio, inclusi il supporto predefinito per la definizione degli intervalli e il recupero dei conteggi relativi ai risultati di facet. Il servizio include anche valori predefiniti appropriati che consentono di evitare le strutture di spostamento ingombranti. 

## <a name="sample-code-and-demo"></a>Demo e codice di esempio
Questo articolo usa un portale di ricerca dei processi come esempio. L'esempio viene implementato come applicazione MVC ASP.NET.

-   Esaminare e testare la demo di lavoro online in [Job Portal Demo di Ricerca di Azure](http://azjobsdemo.azurewebsites.net/).

-   Scaricare il codice dal [repository di esempi di Azure su GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

## <a name="get-started"></a>Introduzione
Se non si ha familiarità con lo sviluppo di funzionalità di ricerca, per comprendere l'esplorazione in base a facet, tenere presente che mostra le diverse possibilità di ricerca autodeterminata. Si tratta di un tipo di esperienza di ricerca drill-down, basata su filtri predefiniti usati per limitare rapidamente i risultati di ricerca tramite semplici azioni in un solo clic. 

### <a name="interaction-model"></a>Modello di interazione

L'esperienza di ricerca dell'esplorazione in base a facet è iterativa. Pertanto, è opportuno iniziare con il concepirlo come una sequenza di query create in risposta ad azioni dell'utente.

Il punto di partenza è una pagina dell'applicazione che consente un'esplorazione in base a facet, in genere sul perimetro. L'esplorazione in base a facet è spesso una struttura ad albero, con caselle di controllo per ogni valore o testo selezionabile. 

1. Una query inviata a Ricerca di Azure specifica la struttura dell'esplorazione in base a facet tramite uno o più parametri di query del facet. Ad esempio, la query potrebbe includere `facet=Rating`, ad esempio con l’opzione `:values` o `:sort` per specificare ulteriormente la presentazione.
2. Il livello di presentazione consente di eseguire il rendering di una pagina di ricerca che permette l'esplorazione in base ai facet specificati nella richiesta.
3. A fronte di una struttura di esplorazione in base a facet che include la classificazione, si fa clic su "4" per indicare che devono essere visualizzati solo i prodotti con una valutazione pari a 4 stelle o superiore. 
4. In risposta, l'applicazione invia una query che include `$filter=Rating ge 4` 
5. Il livello di presentazione aggiorna la pagina con un set di risultati ridotto, contenente solo gli elementi che soddisfano i nuovi criteri (in questo caso, i prodotti valutati con 4 stelle e superiore).

Il facet è un parametro di query, da non confondere con l'input della query. Non viene mai usato come criterio di selezione in una query. Al contrario, occorre concepire i parametri di query di un facet come input per la struttura di navigazione che viene restituita nella risposta. Per ogni parametro di query del facet fornito, Ricerca di Azure restituisce quanti documenti sono presenti nei risultati parziali per ogni valore del facet.

Si noti il `$filter` nel passaggio 4. Il filtro è un aspetto importante dell'esplorazione in base a facet. Sebbene facet e filtri siano indipendenti nell'API, sono necessari entrambi per offrire l'esperienza desiderata. 

### <a name="app-design-pattern"></a>Schema progettuale di app

Nel codice dell'applicazione, il modello consiste nell'usare parametri di query di facet per restituire la struttura di navigazione in base a facet con i risultati di facet, oltre a un'espressione $filter.  L'espressione filtro gestisce l'evento clic sul valore del facet. L'espressione `$filter` deve essere considerata come il codice alla base della riduzione effettiva dei risultati della ricerca restituiti al livello di presentazione. A fronte di un facet di colori, la selezione del colore rosso viene implementata tramite un'espressione `$filter` che consente di selezionare solo gli elementi di colore rosso. 

### <a name="query-basics"></a>Nozioni di base sulle query

In Ricerca di Azure una richiesta viene specificata tramite uno o più parametri di query (vedere [Eseguire ricerche nei documenti](http://msdn.microsoft.com/library/azure/dn798927.aspx) per una descrizione di ciascuno di essi). Nessuno dei parametri di query è obbligatorio, ma è necessario disporre di almeno di uno affinché una query sia valida.

La precisione, interpretata come la possibilità di filtrare i riscontri irrilevanti, avviene attraverso una o entrambe queste espressioni:

-   **ricerca =**  
    Il valore di questo parametro rappresenta l'espressione di ricerca. Potrebbe essere una singola parte di testo o un'espressione di ricerca complessa che include più termini e operatori. Nel server un'espressione di ricerca viene usata per la ricerca full-text, per l'esecuzione di query su campi disponibili per la ricerca nell'indice di termini corrispondenti, per la restituzione dei risultati in ordine di classificazione. Se si imposta `search` su null, l'esecuzione della query va oltre l'intero indice (ovvero, `search=*`). In questo caso, gli altri elementi della query, ad esempio `$filter` o un profilo di punteggio, sono i fattori primari che determinano quali documenti vengono restituiti `($filter`) e in quale ordine (`scoringProfile` o `$orderby`).

-   **$filter=**  
    Un filtro è un meccanismo potente per limitare le dimensioni dei risultati della ricerca in base ai valori degli attributi del documento specifico. Viene prima valutato un `$filter` , seguito dalla logica di facet che genera i valori disponibili e i conteggi corrispondenti per ciascun valore.

Le espressioni di ricerca complesse comportano una riduzione delle prestazioni della query. Ove possibile, usare le espressioni di filtro ben strutturate per aumentare la precisione e migliorare le prestazioni delle query.

Per comprendere meglio in che modo un filtro aggiunge maggiore precisione, confrontare un'espressione di ricerca complessa che includa un'espressione di filtro:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Entrambe le query sono valide, ma la seconda è migliore se si stanno cercando non motel con parcheggio in Seattle.
-   La prima query si basa su tali parole specifiche non indicate nei campi stringa come nome, descrizione e qualsiasi altro campo contenente dati disponibili per la ricerca.
-   La seconda query consente di cercare le corrispondenze precise sui dati strutturati e probabilmente più accurate.

Nelle applicazioni che includono l'esplorazione in base a facet, è necessario assicurarsi che ogni azione dell'utente su una struttura di esplorazione in base a facet sia accompagnata da una restrizione dei risultati della ricerca. Per limitare i risultati, usare un'espressione filtro.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Creare un'app di esplorazione in base a facet
Si implementa l'esplorazione in base a facet con Ricerca di Azure nel codice dell'applicazione che crea la richiesta di ricerca. L'esplorazione in base a facet si basa su elementi dello schema definiti in precedenza.

Predefinito nell'indice di ricerca è l'attributo dell'indice `Facetable [true|false]` , impostato in campi selezionati per abilitare o disabilitare l'uso in una struttura di esplorazione in base a facet. Senza `"Facetable" = true`un campo non può essere usato nell'esplorazione in base a facet.

Il livello di presentazione del codice offre l'esperienza dell'utente. Dovrebbero essere indicate le parti costitutive dell'esplorazione in base a facet, ad esempio l'etichetta, i valori, le caselle di controllo e il conteggio. L'API REST di Ricerca di Azure è indipendente dalla piattaforma, quindi è possibile usare qualsiasi lingua e la piattaforma desiderata. L'aspetto importante consiste nell'includere gli elementi dell'interfaccia utente che supportano l'aggiornamento incrementale, con lo stato dell'interfaccia utente aggiornato man mano che vengono selezionate facet aggiuntive. 

In fase di query, il codice dell'applicazione consente di creare una richiesta che include `facet=[string]`, un parametro di richiesta che fornisce i facet per il campo. Una query può avere più facet, ad esempio `&facet=color&facet=category&facet=rating`, ciascuno separato da un carattere e commerciale (&).

Il codice dell'applicazione deve inoltre consentire la creazione di un'espressione `$filter` per gestire gli eventi di selezione nell'esplorazione in base a facet. `$filter` riduce i risultati della ricerca, usando il valore del facet come criteri di filtro.

Ricerca di Azure restituisce i risultati della ricerca in base a uno o più termini immessi, insieme agli aggiornamenti della struttura di esplorazione in base a facet. In Ricerca di Azure l'esplorazione in base a facet è una costruzione a livello singolo, con valori di facet, che calcola il numero di risultati trovati per ciascuno di essi.

Nelle sezioni seguenti verrà esaminata più in dettaglio la compilazione di ogni parte.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Compilare l'indice
L'esplorazione in base a facet viene abilitata per ogni campo singolarmente, tramite l'attributo di indice: `"Facetable": true`.  
Tutti i tipi di campo che possono essere usati in esplorazione in base a facet sono `Facetable` per impostazione predefinita. Tali tipi di campo includono `Edm.String`, `Edm.DateTimeOffset` e tutti i tipi di campo numerico (ossia, tutti i tipi di campo consentono un'esplorazione in base a facet tranne `Edm.GeographyPoint`, che non può essere usato nell'esplorazione in base a facet). 

Durante la creazione di un indice, è consigliabile disattivare in modo esplicito l'esplorazione in base a facet per i campi che non devono mai essere usati come un facet.  In particolare, i campi stringa per i valori singleton, ad esempio un nome di prodotto o ID, devono essere impostati su `"Facetable": false` per impedirne l'uso accidentale (e inefficace) nell'esplorazione in base a facet. La disattivazione dell'esplorazione in base a facet, laddove non necessaria, consente di mantenere ridotte le dimensioni dell'indice e in genere di migliorare le prestazioni.

Di seguito è riportata una parte dello schema per l'app di esempio Job Portal Demo, senza alcuni attributi per ridurre le dimensioni:

```json
{
  ...
  "name": "nycjobs",
  "fields": [
    { “name”: "id",                 "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { “name”: "job_id",             "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { “name”: "agency",              "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "posting_type",        "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "num_of_positions",    "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "business_title",      "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "civil_service_title", "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "title_code_no",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "level",               "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_range_from",   "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_range_to",     "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_frequency",    "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "work_location",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
…
    { “name”: "geo_location",        "type": "Edm.GeographyPoint",     "searchable": false, "filterable": true, ...  "facetable": false, ... },
    { “name”: "tags",                "type": "Collection(Edm.String)", "searchable": true,  "filterable": true, ...  "facetable": true, ...  }
  ],
…
}
```

Come risulta dallo schema di esempio, `Facetable` è disattivato per i campi stringa che non devono essere usati come facet, ad esempio i valori ID. La disattivazione dell'esplorazione in base a facet, laddove non necessaria, consente di mantenere ridotte le dimensioni dell'indice e in genere di migliorare le prestazioni.

> [!TIP]
> Come procedura consigliata, includere il set completo di attributi dell'indice per ogni campo. Benché `Facetable` sia attivato per impostazione predefinita per quasi tutti i campi, l'impostazione intenzionale di ogni attributo consentono di valutare le implicazioni di ogni decisione di schema. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Controllare i dati
La qualità dei dati ha un effetto diretto sulla possibilità che la struttura di esplorazione in base a facet si prospetti come previsto. Influisce anche sulla facilità di creazione di filtri per ridurre il set di risultati.

Se si desidera effettuare un'esplorazione in base a facet per marca o prezzo, ciascun documento deve contenere i valori relativi a *BrandName* e *ProductPrice* che sono validi, coerenti e produttivi come opzione di filtro.

Di seguito sono elencati alcuni promemoria di ciò che è necessario eliminare:

* Per ogni campo da usare per l'esplorazione in base a facet, è necessario stabilire se contiene valori adatti all'uso come filtri nella ricerca mirata. I valori devono essere brevi, descrittivi sufficientemente distintivi per offrire una scelta definita tra opzioni concorrenti.
* Errori di ortografia o valori quasi corrispondenti. Se si effettua l'esplorazione in base a facet per Colore e i valori dei campi includono Arancione e Arncione (un errore di ortografia), un'esplorazione in base a facet in base al campo colore comporterebbe la selezione di entrambi.
* Il testo con maiuscole e minuscole e può inoltre provocare danni all'esplorazione in base a facet con arancione e Arancione visualizzati come due valori diversi. 
* Le versioni singolari e plurali dello stesso valore possono comportare un facet separato.

Come si può immaginare, la scrupolosità di preparazione dei dati è un aspetto essenziale dell'esplorazione in base a facet.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>Creare l'interfaccia utente
Partire dal livello di presentazione consente di scoprire i requisiti che è possibile non vengano individuati in caso contrario e di comprendere le funzionalità essenziali per l'esperienza di ricerca.

In termini di esplorazione in base a facet, la pagina Web o un'applicazione consente di visualizzare la struttura del sito di collaborazione, rileva l'input dell'utente nella pagina e inserisce gli elementi modificati. 

Per le applicazioni Web, viene comunemente usata la tecnologia AJAX nel livello di presentazione, poiché consente di aggiornare le modifiche incrementali. È inoltre possibile usare ASP.NET MVC o qualsiasi altra piattaforma di visualizzazione che può connettersi a un servizio di Ricerca di Azure tramite HTTP. L'applicazione di esempio a cui fa riferimento in questo articolo, **Job Portal Demo di Ricerca di Azure**, è un'applicazione MVC ASP.NET.

Nell'esempio, l'esplorazione in base a facet viene creata nella pagina di risultati della ricerca. L'esempio seguente, tratto dal file `index.cshtml` dell'applicazione di esempio, mostra la struttura HTML dinamica per la visualizzazione dell'esplorazione in base a facet nella pagina dei risultati di ricerca. L'elenco di facet viene compilato o ricompilato in modo dinamico quando si invia un termine di ricerca oppure si seleziona o deseleziona un facet.

```html
<div class="widget sidebar-widget jobs-filter-widget">
  <h5 class="widget-title">Filter Results</h5>
    <p id="filterReset"></p>
    <div class="widget-content">

      <h6 id="businessTitleFacetTitle">Business Title</h6>
      <ul class="filter-list" id="business_title_facets">
      </ul>

      <h6>Location</h6>
      <ul class="filter-list" id="posting_type_facets">
      </ul>

      <h6>Posting Type</h6>
      <ul class="filter-list" id="posting_type_facets"></ul>

      <h6>Minimum Salary</h6>
      <ul class="filter-list" id="salary_range_facets">
      </ul>

  </div>
</div>
```

Il frammento di codice seguente, tratto dalla pagina `index.cshtml`, crea in modo dinamico il codice HTML per visualizzare il primo facet, Business Title. Funzioni simili compilano in modo dinamico il codice HTML per gli altri facet. Ogni facet contiene un'etichetta e un conteggio, che visualizza il numero di elementi trovati per questo risultato di facet.

```js
function UpdateBusinessTitleFacets(data) {
  var facetResultsHTML = '';
  for (var i = 0; i < data.length; i++) {
    facetResultsHTML += '<li><a href="javascript:void(0)" onclick="ChooseBusinessTitleFacet(\'' + data[i].Value + '\');">' + data[i].Value + ' (' + data[i].Count + ')</span></a></li>';
  }

  $("#business_title_facets").html(facetResultsHTML);
}
```

> [!TIP]
> Quando si progetta la pagina dei risultati di ricerca, ricordarsi di aggiungere un meccanismo per la cancellazione dei facet. Se si aggiungono caselle di controllo, gli utenti possono facilmente intuire come cancellare i filtri. Per altri layout potrebbe essere necessario un modello di navigazione o un altro approccio creativo. Ad esempio, nell'applicazione di esempio Job Search Portal è possibile fare clic su `[X]` dopo un facet selezionato per cancellare il facet.

<a name="buildquery"></a>

## <a name="build-the-query"></a>Creare la query
Il codice creato per la creazione di query deve specificare tutte le parti di una query valida, tra cui espressioni di ricerca, i facet, i filtri, l'assegnazione dei punteggi dei profili, ovvero qualsiasi elemento che consente di formulare una richiesta. In questa sezione verrà illustrata la collocazione dei facet in una query e l'uso dei filtri con i facet per fornire un set di risultati ridotto.

Si noti che i facet sono parte integrante di questa applicazione di esempio. La funzionalità di ricerca in Job Portal Demo è progettata sulla base dei filtri e dell'esplorazione in base a facet. Il posizionamento principale nella pagina dell'esplorazione in base a facet ne dimostra l'importanza. 

Un esempio è spesso un buon punto di inizio. L'esempio seguente, tratto dal file `JobsSearch.cs`, crea una richiesta che permette l'esplorazione in base a facet basata su qualifica professionale, posizione, tipo di pubblicazione e stipendio minimo. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Un parametro di query del facet è impostato su un campo e a seconda del tipo di dati usati, può essere ulteriormente dotato di parametri in un elenco delimitato da virgole che includa `count:<integer>`, `sort:<>`, `interval:<integer>` e `values:<list>`. Durante l'impostazione di intervalli è supportato un elenco di valori per i dati numerici. Per informazioni dettagliate, vedere [Eseguire ricerche nei documenti (API di Ricerca di Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx) .

Insieme ai facet, la richiesta formulata dall'applicazione deve anche permettere la creazione di filtri per limitare il set di documenti candidato in base alla selezione di un valore di facet. Per uno store di biciclette, l'esplorazione in base a facet fornisce indicazioni a domande su *colore, produttore e tipi di bicicletta disponibili*. Il filtro risponde a domande come *Quali biciclette sono mountain bike rosse in questa fascia di prezzo?*. Quando si fa clic su "Red" per indicare che devono essere visualizzati solo i prodotti rossi, la query successiva inviata dall'applicazione include `$filter=Color eq ‘Red’`.

Il frammento di codice seguente della pagina `JobsSearch.cs` aggiunge il Business Title selezionato al filtro, se si seleziona un valore dal facet Business Title.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Suggerimenti e procedure consigliate

### <a name="indexing-tips"></a>Suggerimenti per l'indicizzazione
**Migliorare l'efficienza di un indice se non si usa una casella di ricerca**

Se l'applicazione usa esclusivamente l'esplorazione in base a facet (vale a dire nessuna casella di ricerca), è possibile contrassegnare il campo come `searchable=false`, `facetable=true` per produrre un indice più compatto. Inoltre, l'indicizzazione si verifica solo su valori di facet interi con nessuna interruzione parola o indicizzazione delle parti di un valore per più parole.

**Specificare quali campi possono essere usati come facet**

Tenere presente che lo schema dell'indice determina quali campi sono disponibili da usare come facet. Presupponendo che un campo consenta un'esplorazione in base a facet, la query specifica per quali campi eseguirla. Il campo per cui si eseguire l'esplorazione in base a facet fornisce i valori visualizzati sotto l'etichetta. 

I valori visualizzati in ogni etichetta vengono recuperati dall'indice. Ad esempio, se il campo del facet è il *colore*, i valori disponibili per i filtri aggiuntivi sono i valori per il campo, come rosso, bianco e così via.

Per valori numerici e data/ora solo, è possibile impostare in modo esplicito i valori nel campo facet (ad esempio, `facet=Rating,values:1|2|3|4|5`). È consentito un elenco di valori per questi tipi di campo semplificare la separazione dei risultati di facet in intervalli contigui (entrambi gli intervalli in base a valori numerici o periodi di tempo). 

**Per impostazione predefinita, è possibile avere un solo livello di esplorazione in base a facet** 

Come accennato, non esiste alcun supporto diretto per la nidificazione facet in una gerarchia. Per impostazione predefinita, l'esplorazione in base a facet in Ricerca di Azure supporta solo un livello di filtri. Tuttavia, esistono soluzioni alternative. È possibile codificare una struttura gerarchica facet in un `Collection(Edm.String)` con un punto di ingresso per singola gerarchia. L'implementazione di questa soluzione alternativa esula dall'ambito di questo articolo. 

### <a name="querying-tips"></a>Suggerimenti per le query
**Convalidare i campi**

Se si compila l'elenco di facet dinamicamente in base all'input dell'utente non attendibile, verificare che i nomi dei campi in base ai facet siano validi. In alternativa, escluderne i nomi durante la creazione degli URL usando `Uri.EscapeDataString()` in .NET o l'equivalente nella piattaforma preferita.

### <a name="filtering-tips"></a>Suggerimenti per i filtri
**Aumentare la precisione della ricerca con i filtri**

Usare i filtri. Se si usano solo le espressioni di ricerca, lo stemming può comportare la restituzione di un documento che non dispone del valore di facet esatto in uno dei relativi campi.

**Aumentare le prestazioni della ricerca con i filtri**

I filtri riducono il set di documenti candidati per la ricerca ed escluderli dalla classificazione. Se si dispone di un ampio set di documenti, l'uso di un drill-down di facet selettivo spesso consente un miglioramento significativo delle prestazioni.
  
**Filtrare solo i campi con facet**

In un drill-down dell'esplorazione in base a facet, è possibile includere solo i documenti che contengono il valore del facet in un campo specifico (con facet), non in qualsiasi punto in tutti i campi disponibili per la ricerca. L'aggiunta di un filtro mette in evidenza il campo di destinazione indirizzando il servizio alla ricerca solo nel campo di esplorazione in base a facet per un valore corrispondente.

**Ridurre i risultati di facet con altri filtri**

I risultati facet sono documenti trovati nei risultati della ricerca che corrispondono a un termine di facet. Nell'esempio seguente, nei risultati della ricerca per il *cloud computing*, i 254 elementi dispongono inoltre di una *specifica interna* come tipo di contenuto. Gli elementi non si escludono necessariamente a vicenda. Se un elemento soddisfa i criteri di entrambi i filtri, viene conteggiato in ognuno di essi. Questa duplicazione è possibile quando si esegue l'esplorazione in base a facet su campi `Collection(Edm.String)` che vengono spesso usati per implementare l'aggiunta di tag nel documento.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

In generale, se si ritiene che i risultati dei facet siano costantemente troppo elevati, è consigliabile che aggiungere altri filtri per offrire agli utenti più opzioni per restringere la ricerca.

### <a name="tips-about-result-count"></a>Suggerimenti sul conteggio dei risultati

**Limitare il numero di elementi nell'esplorazione in base a facet**

Per ogni campo di esplorazione in base a facet nella struttura di spostamento, esiste un limite predefinito di 10 valori. Questa impostazione predefinita risulta utile per le strutture di navigazione perché mantiene l'elenco di valori a un livello gestibile. È possibile sostituire il valore predefinito assegnando un valore per il conteggio.

* `&facet=city,count:5` specifica che solo le prime 5 città incluse nei risultati migliori vengono restituite come risultato di facet. Si consideri una query di esempio con un termine di ricerca di "aeroporto" e 32 corrispondenze. Se la query specifica `&facet=city,count:5`, solo le prime cinque città univoche con il maggior numero di documenti nei risultati della ricerca vengono incluse nei risultati del facet.

Si noti la distinzione tra i risultati di facet e i risultati della ricerca. I risultati della ricerca sono tutti i documenti che corrispondono alla query. I risultati facet sono le corrispondenze per ogni valore del facet. Nell'esempio vengono restituiti nomi di città non presenti nell'elenco di classificazione dei facet, 5 nel nostro esempio. I risultati che vengono filtrati tramite l'esplorazione in base a facet diventano visibili quando si cancellano i facet o si scelgono altri facet oltre a città. 

> [!NOTE]
> La discussione relativa a `count` quando ne esiste più di un tipo potrebbe portare a confusione. La tabella seguente offre un breve riepilogo di come viene usato il termine nell'API di Ricerca di Azure, nel codice di esempio e nella documentazione. 

* `@colorFacet.count`<br/>
  Nel codice di presentazione, si noterà un parametro di conteggio del facet usato per visualizzare il numero di risultati facet. Nei risultati facet, Conteggio indica il numero di documenti che corrispondono a un intervallo o termine facet.
* `&facet=City,count:12`<br/>
  In una query di facet, è possibile impostare il conteggio su un valore.  Il valore predefinito è 10, ma è possibile impostarlo a un livello superiore o inferiore. L'impostazione `count:12` consente di recuperare le 12 corrispondenze principali nei risultati di facet per numero di documenti.
* "`@odata.count`"<br/>
  Nella risposta alla query, questo valore indica il numero di elementi corrispondenti nei risultati della ricerca. In media, è maggiore della somma di tutti i facet risultati combinati, a causa della presenza di elementi che corrispondono al termine di ricerca, ma senza corrispondenze di valore del facet.

**Ottenere i conteggi nei risultati di facet**

Quando si aggiunge un filtro a una query in base a facet, è possibile mantenere l'istruzione facet (ad esempio, `facet=Rating&$filter=Rating ge 4`). Tecnicamente, facet=Rating non è necessaria, ma mantenerla permette di restituire i conteggi dei valori di facet per le classificazioni a 4 stelle e superiori. Ad esempio, se si fa clic su "4" e la query include un filtro per maggiore o uguale a "4", i conteggi vengono restituiti per ciascuna valutazione uguale a 4 e superiore.  

**Verificare di ottenere conteggi di facet accurati**

In determinate circostanze, si noterà che i conteggi facet non corrispondono ai set di risultati (vedere [navigazione collaborazione nella Ricerca di Azure (post di forum)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

I conteggi di facet possono essere inesatti grazie all'architettura di partizionamento orizzontale. Ogni indice di ricerca include più partizioni e ciascuno di essi segnala i principali N facet per numero di documenti, combinando poi i dati in un singolo risultato. Se alcune partizioni dispongono di numerosi valori corrispondenti a differenza di altre, è probabile che alcuni valori di facet siano mancanti o non calcolati nei risultati.

Sebbene questo comportamento potrebbe cambiare in qualsiasi momento, se si verifica subito, è possibile risolverlo "gonfiando" artificialmente il conteggio:<number> per un numero elevato di documenti e applicare il report completo di ogni partizione. Se il valore del conteggio è maggiore o uguale al numero di valori univoci nel campo, vengono garantiti risultati accurati. Tuttavia, quando i conteggi di documenti sono elevati, si verifica una riduzione delle prestazioni ed è quindi consigliabile usare questa opzione con cautela.

### <a name="user-interface-tips"></a>Suggerimenti per l'interfaccia utente
**Aggiungere etichette per ogni campo nella navigazione facet**

Le etichette vengono in genere definite nel codice HTML o nel modulo (`index.cshtml` nell'applicazione di esempio). Non esiste alcuna API in Ricerca di Azure per le etichette di navigazione facet o qualsiasi altro metadato.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Filtro basato su un intervallo
L'esplorazione in base a facet su intervalli di valori è un requisito comune delle applicazioni di ricerca. Gli intervalli sono supportati per i dati numerici e i valori DateTime. Per ulteriori informazioni su ogni approccio in [Eseguire ricerche nei documenti (API di Ricerca di Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx).

Ricerca di Azure semplifica la creazione degli intervalli fornendo due approcci per l'elaborazione di un intervallo. Per entrambi gli approcci Ricerca di Azure crea gli intervalli appropriati in base agli input resi disponibili. Ad esempio, se si specificano valori di intervallo di 10|20|30, vengono creati automaticamente gli intervalli 0-10, 10-20, 20-30. L'applicazione può facoltativamente rimuovere qualsiasi intervallo vuoto. 

**Approccio 1: utilizzare il parametro intervallo**  
Per impostare i facet di prezzo in incrementi di 10 dollari, è necessario specificare: `&facet=price,interval:10`

**Approccio 2: utilizzare un elenco di valori**  
Per dati numerici, è possibile usare un elenco di valori.  Prendere in considerazione l'intervallo di facet per il campo `listPrice`, sottoposto a rendering come segue:

  ![Elenco di valori di esempio][5]

Per specificare un intervallo di facet come quello nello screenshot precedente, usare un elenco di valori:

    facet=listPrice,values:10|25|100|500|1000|2500

Ogni intervallo viene compilato usando 0 come punto di partenza e un valore dall'elenco come endpoint e quindi privato dell'intervallo precedente per creare intervalli discreti. Ricerca di Azure esegue questa operazione nell'ambito dell'esplorazione in base facet. Non è necessario scrivere codice per la struttura di ogni intervallo.

### <a name="build-a-filter-for-a-range"></a>Creare un filtro per un intervallo
Per filtrare i documenti in base a un intervallo selezionato, è possibile usare gli operatori di filtro `"ge"` e `"lt"` in un'espressione in due parti che definisce gli endpoint dell'intervallo. Ad esempio, se si sceglie l'intervallo 10-25 per un campo `listPrice`, il filtro sarà `$filter=listPrice ge 10 and listPrice lt 25`. Nel codice di esempio l'espressione filtro usa i parametri **priceFrom** e **priceTo** per impostare gli endpoint. 

  ![Query per un intervallo di valori][6]

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Filtro basato sulla distanza
È comune vedere filtri che consentono di scegliere un negozio, un ristorante o una destinazione in base alla prossimità alla posizione corrente. Questo tipo di filtro potrebbe somigliare all'esplorazione in base a facet, ma è semplicemente un filtro. Viene menzionato per coloro che cercano in particolare consigli di implementazione per tale problema particolare di progettazione.

Sono disponibili due funzioni geospaziali in Ricerca di Azure, **geo.distance** e **geo.intersects**.

* La funzione **geo** restituisce la distanza in chilometri tra due punti. Un punto è un campo e l'altro è una costante passata come parte del filtro. 
* La funzione **intersects** restituisce true se un punto specificato si trova all'interno di un poligono specificato. Il punto è un campo e il poligono è specificato come un elenco costante di coordinate passate come parte del filtro.

È possibile trovare alcuni esempi di filtri in [Sintassi dell'espressione di OData (Ricerca di Azure)](http://msdn.microsoft.com/library/azure/dn798921.aspx).

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Provare la demo
Job Portal Demo di Ricerca di Azure contiene gli esempi descritti nel presente articolo.

-   Esaminare e testare la demo di lavoro online in [Job Portal Demo di Ricerca di Azure](http://azjobsdemo.azurewebsites.net/).

-   Scaricare il codice dal [repository di esempi di Azure su GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

Quando si lavora con i risultati, controllare l'URL per le modifiche nella costruzione delle query. Questa applicazione è casualmente incaricata all'accodamento i facet all'URI a ogni selezione.

1. Per usare la funzionalità di mapping dell'app demo, ottenere una chiave Bing Mappe dal [Bing Maps Dev Center](https://www.bingmapsportal.com/). Incollare il codice sulla chiave esistente nella pagina `index.cshtml`. L'impostazione `BingApiKey` nel file `Web.config` non viene usata. 

2. Eseguire l'applicazione. Iniziare la presentazione facoltativa o chiudere la finestra di dialogo.
   
3. Immettere un termine di ricerca, ad esempio "analyst" e fare clic sull'icona di ricerca. La query viene eseguita rapidamente.
   
   Una struttura di esplorazione in base a facet viene inoltre restituita con i risultati della ricerca. Nella pagina dei risultati di ricerca la struttura di esplorazione in base a facet include conteggi per ogni risultato facet. Non sono stati selezionati facet e quindi vengono restituiti tutti i risultati corrispondenti.
   
   ![Risultati della ricerca prima della selezione di facet][11]

4. Fare clic su un valore per Business Title, Location o Minimum Salary. I facet sono null in una ricerca iniziale, ma come assumono i valori, vengono eliminati i risultati della ricerca di elementi che non corrispondono.
   
   ![Risultati della ricerca dopo la selezione di facet][12]

5. Per cancellare la query basata su facet in modo che sia possibile provare i comportamenti di query diversi, fare clic su `[X]` dopo i facet selezionati per cancellarli.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Altre informazioni
Guardare [Azure Search Deep Dive](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410) (Approfondimenti su Ricerca di Azure). Al minuto 45:25 è presente una dimostrazione su come implementare i facet.

Per altre informazioni sui principi di progettazione per l'esplorazione in base a facet, è consigliabile usare i collegamenti seguenti:

* [Progettazione per la ricerca con esplorazione in base a facet](http://www.uie.com/articles/faceted_search/)
* [Modelli di progettazione: esplorazione in base a facet](http://alistapart.com/article/design-patterns-faceted-navigation)


<!--Anchors-->
[How to build it]: #howtobuildit
[Build the presentation layer]: #presentationlayer
[Build the index]: #buildindex
[Check for data quality]: #checkdata
[Build the query]: #buildquery
[Tips on how to control faceted navigation]: #tips
[Faceted navigation based on range values]: #rangefacets
[Faceted navigation based on GeoPoints]: #geofacets
[Try it out]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/azure-search-faceting-example.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png
[11]: ./media/search-faceted-navigation/faceted-search-before-facets.png
[12]: ./media/search-faceted-navigation/faceted-search-after-facets.png

<!--Link references-->
[Designing for Faceted Search]: http://www.uie.com/articles/faceted_search/
[Design Patterns: Faceted Navigation]: http://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: http://msdn.microsoft.com/library/azure/dn798921.aspx
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/ 
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: http://msdn.microsoft.com/library/azure/dn798927.aspx


