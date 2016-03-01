<properties 
   pageTitle="Prova gratuita della ricerca di Azure con il servizio app di Azure | Microsoft Azure"
   description="Prova gratuita della ricerca di Azure, per un'ora intera, utilizzando il modello del servizio App di Azure."
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="mblythe" 
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="search" 
   ms.date="02/21/2016"
   ms.author="heidist"/>

# Prova gratuita della ricerca di Azure con il servizio app di Azure

[Prova Servizio app di Azure](https://tryappservice.azure.com/) è una sessione gratuita di un'ora basata su browser per provare i servizi di Azure, tra cui **Ricerca di Azure**, senza doversi iscrivere ad alcuna sottoscrizione. Il sito offre diversi modelli disponibili. Quando si seleziona il modello ASP.NET che include Ricerca di Azure, si ottiene accesso a un sito Web completamente funzionale, supportato dai servizi selezionati.

All'interno dell'offerta **Prova il Servizio app di Azure** il servizio di Ricerca di Azure è già configurato e pronto per ricevere query di ricerca. Non è possibile caricare o usare il proprio indice o i propri dati, ma è possibile eseguire query e apportare modifiche al codice per ridefinire l'esperienza utente.

I dati di ricerca provengono dallo [United States Geological Survey (USGS)](http://geonames.usgs.gov/domestic/download_data.htm) e sono costituiti da circa 3 milioni di righe di luoghi di interesse, siti di rilevanza storica, edifici e altri punti di riferimento negli Stati Uniti.

## Introduzione

Se è stato avviata la sessione di 1 ora, attenersi alla seguente procedura per iniziare.

1. Fare clic su [https://tryappservice.azure.com](https://tryappservice.azure.com/) per aprire il sito in una nuova finestra del browser.
2. Scorrere verso il basso e selezionare **App Web**. 
2. Fare clic su **Avanti**.
3. Scegliere il modello **ASP.NET + Azure Search Site**.

    ![][1]

4. Fare clic su **Crea**.
5. Scegliere un metodo di accesso e fornire il nome utente e password.

    ![][2]

6. Attendere che il sito venga caricato. Quando è pronto, verrà visualizzata una pagina simile alla seguente. Su ogni pagina viene visualizzato un orologio in modo da sapere sempre quanto tempo è ancora a disposizione.

    ![][3]

7. Scegliere **Edit with Visual Studio Team Services**per visualizzare la soluzione ed esplorare il sito.
9. In Visual Studio Team Services espandere le opzioni della sessione nella parte superiore della pagina e quindi fare clic su **Browse Web Site**.

    ![][4]

10. Verrà visualizzata che la pagina per iniziare del sito web della Ricerca di Azure. Fare clic sul tasto **Get Started** per aprire il sito.

    ![][5]

11. Un sito web ASP.NET verrà visualizzata nel browser, che fornisce una casella di ricerca. Immettere un termine da cercare, ad esempio*Yellowstone* o il nome di una montagna nota, ad esempio il *Mount Rainier*. Partire da un punto di riferimento noto rende più semplice valutare i risultati.

    ![][6]


## Prime operazioni da eseguire

Poiché l'indice di ricerca è completamente operativo, un buon primo passo consiste nel provare alcune query. La Ricerca di Azure è compatibile con tutti gli operatori di ricerca standard (+, -, |), virgolette per corrispondenze letterali, il carattere jolly (*) e operatori di priorità. È possibile esaminare il riferimento della sintassi della query per l'elenco completo degli operatori.

- Iniziare con una ricerca con caratteri jolly, aggiungendo un asterisco (`*`). Questo indica il numero di documenti presenti nell'indice: 2,262,578.
- Successivamente, immettere "Yellowstone", quindi aggiungere "+center", "+building" e "-ND" per restringere progressivamente i risultati della ricerca solo sul Centro visitatori di Yellowstone, escludendo quelli del Nord Dakota: `Yellowstone +center +building -ND`.  
- Provare una frase di ricerca che combina gli operatori degli precedenza e la stringa corrispondente:`statue+(park+MT)`. Si otterrà un risultato simile alla schermata riportata di seguito. Si noti che le categorie di facet, compaiono sotto un’istanza di classe, offrendo un filtro autodidattico che filtra la navigazione a facet, una funzionalità comunemente presente nella maggior parte delle applicazioni di ricerca.

    ![][7]

Pronto a Procedere? Modificare alcune righe di codice per controllare l'impatto sulle operazioni di ricerca full-text.

## Modificare searchMode.All

La ricerca di Azure dispone della proprietà **searchMode**, configurabile. Questa è utilizzabile per controllare il comportamento degli operatori di ricerca. I valori validi per questa proprietà sono `Any` (impostazione predefinita) o `All`. Vedere[Sintassi Semplice delle Query ](https://msdn.microsoft.com/library/dn798920.aspx)per ulteriori informazioni sull'impostazione di queste opzioni.

- **searchMode.Any** stabilisce che qualsiasi corrispondenza di un termine di ricerca è sufficiente per includere un elemento nei risultati della ricerca. Se la frase di ricerca è `Yellowstone visitor center`, qualsiasi documento contenente uno qualsiasi di questi termini è incluso nei risultati della ricerca. Questa modalità è orientata verso i *richiami*.
- La modalità **searchModel.All**, utilizzata in questo esempio, richiede che tutti i termini specificati siano presenti nel documento. Questa modalità è più rigorosa di **searchMode.Any** ma se si predilige la *precisione* rispetto al richiamo è probabilmente la scelta giusta per l'applicazione. 

> [AZURE.NOTE] **searchMode.Any** è ottimale quando le query sono composte principalmente da frasi, con un utilizzo minimo degli operatori. Una regola empirica generale è che la ricerca di applicazioni per il consumatore, come siti di e-commerce, tendono a utilizzare solo termini, mentre le persone che eseguono ricerche sul contenuto o su dati sono più probabile che includano operatori nella frase di ricerca. Se si ritiene molto probabile che le ricerche includano operatori, in particolare l’operatore `NOT (-)`, iniziare con **searchModel.All**. Al contrario, l'altra opzione, **searchMode.Any** contrappone con `OR` l'operatore `NOT` ad altri termini di ricerca, espandendo in modo sensibile i risultati, anziché limitarli. l’esempio seguente consente di comprendere la differenza.

In questa attività si modificherà la modalità **searchMode** e si confronteranno i risultati di ricerca in base alla modalità.

1. Aprire la finestra del browser contenente l'applicazione di esempio, scegliere **Connect to Visual Studio Team Services**.

    ![][8]

2. Aprire **Search.cshtml**, trovare`searchMode.All` alla riga 39 e modificare il codice in `searchMode.Any`.

    ![][9]

3. Nella barra di spostamento a destra, fare clic su **Run**.

    ![][10]
 
Nella finestra dell'applicazione ricompilata, immettere un termine di ricerca utilizzato in precedenza, ad esempio `Yellowstone +center +building -ND` e confrontare i risultati prima e dopo le modifiche apportate a **searchMode**.

È una differenza vera e propria. Invece di sette risultati di ricerca, è possibile ottenere più di due milioni.

   ![][11]
 
Il comportamento che si osserva è dovuto all’inclusione dell’operatore `NOT` (in questo caso, "-ND"), contrapposto con *OR* anziché con *AND* quando**searchMode** è impostata su `Any`.

Con questa configurazione, i risultati della ricerca sono costituiti dalle occorrenze dei termini di ricerca `Yellowstone`, `center` e `building` ma anche da ogni documento che soddisfa la condizione `NOT North Dakota`. Poiché esistono solo 13.081 documenti che contengono la frase `North Dakota`, vengono restituiti quasi tutti i set di dati.

Si tratta probabilmente di uno scenario improbabile, che però illustra gli effetti di **searchMode** su frasi di ricerca che includono l’operatore `NOT`. Pertanto è utile comprendere il motivo per cui questo comportamento si verifica e il modo in cui è possibile modificare tale comportamento se non è quello desiderato.

Per continuare questa esercitazione, ripristinare il valore originale di **searchMode** (impostato su `All` alla riga 39), eseguire il programma e utilizzare l'app ricompilata per le attività rimanenti.
 
## Aggiungere un filtro globale per lo stato di Washington

In genere, se si desidera eseguire la ricerca su un sottogruppo di dati disponibili, è possibile impostare il filtro nell'origine dati quando si importano dati. Per scopi di apprendimento, utilizzando dati di sola lettura, impostare il filtro dell’applicazione per ottenere solo documenti che includono lo stato di Washington.

1. Aprire Search.cshtml, trovare il blocco di codice **SearchParameters** (a partire dalla riga 36) e aggiungere una riga di commento e il filtro.

        var sp = new SearchParameters
        {
            //Add a filter for _Washington State
            Filter = "STATE_ALPHA eq 'WA'",
            // Specify whether Any or All of the search terms 
            SearchMode = SearchMode.All,
            // Include a count of results in the query result
            IncludeTotalResultCount = true,
            // Return an aggregated count of feature classes based on the specified query
            Facets = new[] { "FEATURE_CLASS" },
            // Limit the results to 20 documents
            Top = 20
        };


I filtri vengono specificati utilizzando la sintassi di OData e vengono spesso utilizzati con navigazione a facet o inclusi nella stringa di query per limitare la query. Per ulteriori informazioni, vedere [Sintassi dell'espressione OData](https://msdn.microsoft.com/library/azure/dn798921.aspx).

2. Fare clic su **Run**.

3. Aprire l'applicazione.

4. Digitare il carattere jolly (*) per restituire un numero. Si noti che i risultati sono ora limitati a 42,411 elementi, che sono tutti i documenti per tutte le funzionalità geografiche nello stato di Washington.

   ![][12]

## Aggiungere l'evidenziazione delle occorrenze

Ora che sono state apportate una serie di cambi alle singole righe di codice, è possibile provare le modifiche più approfondite che richiedono modifiche al codice in più posizioni. La seguente versione di **Search.cshtml** può essere incollata direttamente sul file Search.cshtml nella sessione corrente.

Il codice seguente aggiunge l'evidenziazione dei riscontri. Si notino le nuove proprietà dichiarate in [SearchParameters](https://msdn.microsoft.com/library/microsoft.azure.search.models.searchparameters_properties.aspx). Esiste anche una nuova funzione che scorre l'insieme di risultati per ottenere documenti che richiedono l'evidenziazione, oltre a HTML che esegue il rendering del risultato.

Quando si esegue questo esempio di codice, I termini imputati che hanno una corrispondenza con i campi specificati sono evidenziate in grassetto.

   ![][14]

È necessario salvare una copia del file **Search.cshtml** originale per confrontare le versioni.

> [AZURE.NOTE] Per ridurre le dimensioni del file sono stati eliminati i commenti.
 
    @using System.Collections.Specialized
    @using System.Configuration
    @using Microsoft.Azure.Search
    @using Microsoft.Azure.Search.Models
    
    @{
    Layout = "~/_SiteLayout.cshtml";
    }
    
    @{
    // This modified search.cshtml file adds hit-highlighting
    
    string searchText = Request.Unvalidated["q"];
    string filterExpression = Request.Unvalidated["filter"];
    
    DocumentSearchResponse response = null;
    if (!string.IsNullOrEmpty(searchText))
    {
    searchText = searchText.Trim();
    
    // Retrieve search service name and an api key from configuration
    string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
    string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];
    
    var searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
    var indexClient = searchClient.Indexes.GetClient("geonames");
    
    // Set the Search parameters used when executing the search request
    var sp = new SearchParameters
    {
    // Specify whether Any or All of the search terms must be matched in order to count the document as a match.
    SearchMode = SearchMode.All,
    // Include a count of results in the query result
    IncludeTotalResultCount = true,
    // Return an aggregated count of feature classes based on the specified query
    Facets = new[] { "FEATURE_CLASS" },
    // Limit the results to 20 documents
    Top = 20,
    // Enable hit-highlighting
    HighlightFields = new[] { "FEATURE_NAME", "DESCRIPTION", "FEATURE_CLASS", "COUNTY_NAME", "STATE_ALPHA" },
    HighlightPreTag = "<b>",
    HighlightPostTag = "</b>",
    };
    
    if (!string.IsNullOrEmpty(filterExpression))
    {
    // Add a search filter that will limit the search terms based on a specified expression.
    // This is often used with facets so that when a user selects a facet, the query is re-executed,
    // but limited based on the chosen facet value to further refine results
    sp.Filter = filterExpression;
    }
    
    // Execute the search query based on the specified search text and search parameters
    response = indexClient.Documents.Search(searchText, sp);
    }
    }
    
    @functions
    {
    private string GetFacetQueryString(string facetKey, string facetValue)
    {
    NameValueCollection queryString = HttpUtility.ParseQueryString(Request.Url.Query);
    queryString["filter"] = string.Format("{0} eq '{1}'", HttpUtility.UrlEncode(facetKey), HttpUtility.UrlEncode(facetValue));
    return queryString.ToString();
    }
    
    private HtmlString RenderHitHighlightedString(SearchResult item, string fieldName)
    {
    if (item.Highlights != null && item.Highlights.ContainsKey(fieldName))
    {
    string highlightedResult = string.Join("...", item.Highlights[fieldName]);
    return new HtmlString(highlightedResult);
    }
    return new HtmlString(item.Document[fieldName].ToString());
    }
    }
    
    <!-- Form to allow user to enter search terms -->
    <form class="form-inline" role="search">
    <div class="form-group">
    <input type="search" name="q" id="q" autocomplete="off" size="80" placeholder="Type something to search, i.e. 'park'." class="form-control" value="@searchText" />
    <button type="submit" id="search" class="btn btn-primary">Search</button>
    </div>
    </form>
    @if (response != null)
    {
    if (response.Count == 0)
    {
    <p style="padding-top:20px">No results found for "@searchText"</p>
    <h3>Suggestions:</h3>
    <ul>
    <li>Ensure words are spelled correctly.</li>
    <li>Try rephrasing keywords or using synonyms.</li>
    <li>Try less specific keywords.</li>
    </ul>
    }
    else
    {
    <div class="col-xs-3 col-md-2">
    <h3>Feature Class</h3>
    <ul class="list-unstyled">
    @if (!string.IsNullOrEmpty(filterExpression))
    {
    <li><a href="?q=@HttpUtility.UrlEncode(searchText)">All</a></li>
    }
    <!-- Cycle through the facet results and show the values and counts -->
    @foreach (var facet in response.Facets["FEATURE_CLASS"])
    {
    <li><a href="?@GetFacetQueryString("FEATURE_CLASS", (string)facet.Value)">@facet.Value (@facet.Count)</a></li>
    }
    </ul>
    </div>
    <div class="col-xs-12 col-sm-6 col-md-10">
    <p style="padding-top:20px">1 - @response.Results.Count of @response.Count results for "@searchText"</p>
    
    <ul class="list-unstyled">
    <!-- Cycle through the search results -->
    @foreach (var item in response.Results)
    {
    <li>
    <h3>@RenderHitHighlightedString(item, "FEATURE_NAME")</h3>
    <p>@RenderHitHighlightedString(item, "DESCRIPTION")</p>
    <p>@RenderHitHighlightedString(item, "FEATURE_CLASS"), elevation: @item.Document["ELEV_IN_M"] meters</p>
    <p>@RenderHitHighlightedString(item, "COUNTY_NAME") County, @RenderHitHighlightedString(item, "STATE_ALPHA")</p>
    <br />
    </li>
    }
    </ul>
    </div>
    }
    }

## Perché scegliere Ricerca di Azure?

Anche se altri servizi di Azure, quali il Database SQL offrono la ricerca full-text, un servizio come la Ricerca di Azure fornisce l’ottimizzazione il controllo, l'impaginazione e i conteggi, poter evidenziare, usare completamento automatico nei suggerimenti delle query, l’assistenza sul linguaggio naturale, esplorazione in base a facet, filtro e così via. Come molti dei nostri [esempi](https://github.com/Azure-Samples?utf8=%E2%9C%93&query=search) dimostrano, è possibile sviluppare un'applicazione completa basata sulla ricerca utilizzando solo la ricerca di Azure e ASP.NET.

## Passaggi successivi

Utilizzando il servizio di sola lettura disponibile sul sito di [prova del servizio app di Azure](https://tryappservice.azure.com/), è stato possibile vedere la sintassi della query con la ricerca full-text in azione, apprendere nozioni su searchMode e i filtri e aggiungere l’evidenziazione delle occorrenze all'applicazione di ricerca. Come passaggio successivo, prendere in considerazione la possibilità di creare e aggiornare gli indici Questo aggiunge la possibilità di:

- [Definire i profili dei punteggi](https://msdn.microsoft.com/library/dn798928.aspx) utilizzati per ottimizzare i punteggi di ricerca in modo che gli elementi di maggior rilevanza siano visualizzati per primi.
- [Definire le componenti di suggerimento](https://msdn.microsoft.com/library/mt131377.aspx)che consentono di aggiungere suggerimenti alle query di digitate o il completamento automatico in risposta all'input dell'utente.
- [Definire gli indicizzatori](https://msdn.microsoft.com/library/dn946891.aspx) che aggiornano l'indice automaticamente ogni volta che l'origine dati è un database SQL di Azure o Azure DocumentDB.

Per eseguire tutte queste attività, è necessario iscriversi ad Azure in modo che sia possibile creare e popolare gli indici in un servizio. Per ulteriori informazioni su come registrarsi per un periodo di prova gratuita, visitare il sito [https://azure.microsoft.com/pricing/free-trial](https://azure.microsoft.com/pricing/free-trial/).

Per ulteriori informazioni sulla ricerca di Azure, visitare la [Pagina della documentazione](https://azure.microsoft.com/documentation/services/search/)su[http://azure.microsoft.com](https://azure.microsoft.com/)o verificare qualsiasi numero di[esempi e video](search-video-demo-tutorial-list.md)che esplorano le tutte le funzionalità della Ricerca di Azure.

## Informazioni sul codice e la Ricerca di Azure

La ricerca di Azure è un servizio [PaaS](https://en.wikipedia.org/wiki/Platform_as_a_service) (Platform as a Service) completamente gestito che rende più semplice per gli sviluppatori l'integrazione di un'esperienza di ricerca avanzata nelle applicazioni Web e mobili. I Dati utilizzati nelle operazioni di ricerca vengono archiviati nel servizio di Ricerca in Azure, dove la prossimità dei dati alle operazioni permette una bassa latenza e una ricerca coerente. per approfondire il discorso:

- I dati disponibili per la ricerca sono archiviati in un indice gestito da Ricerca di Azure.
- Lo schema che definisce l'indice è definito dall'utente e specifica i campi di ricerca, i campi che non è possibile ricercare che però possono essere utili in un'espressione di filtro e costruisce profili di punteggio per l'ottimizzazione dei risultati.
- Un indice di ricerca contiene uno o più documenti, analoghi alle righe di una tabella, in cui è possibile eseguire ricerche e che possono essere recuperati.
- La maggior parte degli indici vengono caricati da un singolo set di dati preparati in anticipo da parte dell'utente che includere solo i campi utili nel contesto delle operazioni di ricerca. 
- I Dati possono essere caricati automaticamente da un indicizzatore (compatibile solo con Database SQL di Azure o Azure DocumentDB) o inseriti in un indice di ricerca tramite una delle API di Ricerca di Azure. Quando si utilizza l'API, è possibile inserire i dati da qualsiasi sorgente dati, basta che siano in formato JSON.

Nell'opzione relativa al [servizio app di Azure](https://tryappservice.azure.com/) il modello "ASP.NET + Azure Search Site" fornisce il codice sorgente per l'applicazione Web. Il codice è modificabile in Visual Studio Team Services (disponibile nell'ambito della sessione di un'ora). Nessuno strumento di sviluppo distinto è necessari per visualizzare o modificare il codice.

Il codice, scritto in C#, utilizza la [libreria client .NET della ricerca di Azure](https://msdn.microsoft.com/library/dn951165.aspx) per eseguire query sull'indice, consentire l’esplorazione basata su facet e visualizzare i conteggi e i risultati delle ricerche in una pagina Web.

Un altro codice, non incluso nel modello, è stato utilizzato per creare e caricare l'indice di ricerca di gruppi di protezione utente. Poiché il servizio è di sola lettura, tutte le operazioni che richiedono l'utilizzo della scrittura sono state completate in anticipo. È possibile visualizzare una[copia dello schema](#schema) utilizzato per compilare lo schema alla fine di questo articolo.

<a name="Schema"></a>
## Informazioni sullo schema

Nella istantanea video che seguente viene illustrato lo schema utilizzato per creare l'indice utilizzato in questo modello.
 
   ![][13]

### Schema.JSON file

    {
      "@odata.context": "https://tryappservice.search.windows.net/$metadata#indexes/$entity",
      "name": "geonames",
      "fields": [
    {
      "name": "FEATURE_ID",
      "type": "Edm.String",
      "searchable": false,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": true,
      "analyzer": null
    },
    {
      "name": "FEATURE_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "FEATURE_CLASS",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "STATE_ALPHA",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "STATE_NUMERIC",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "COUNTY_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "COUNTY_NUMERIC",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "ELEV_IN_M",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "ELEV_IN_FT",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "MAP_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DESCRIPTION",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "HISTORY",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DATE_CREATED",
      "type": "Edm.DateTimeOffset",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DATE_EDITED",
      "type": "Edm.DateTimeOffset",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    }
      ],
      "scoringProfiles": [
    
      ],
      "defaultScoringProfile": null,
      "corsOptions": {
    "allowedOrigins": [
      "*"
    ],
    "maxAgeInSeconds": 300
      },
      "suggesters": [
    {
      "name": "AUTO_COMPLETE",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": [
    "FEATURE_NAME",
    "FEATURE_CLASS",
    "COUNTY_NAME",
    "DESCRIPTION"
      ]
    }
      ]
    }

<!--Image references-->
[1]: ./media/search-tryappservice/AzSearch-TryAppService-TemplateTile.png
[2]: ./media/search-tryappservice/AzSearch-TryAppService-LoginAccount.png
[3]: ./media/search-tryappservice/AzSearch-TryAppService-AppCreated.png
[4]: ./media/search-tryappservice/AzSearch-TryAppService-BrowseSite.png
[5]: ./media/search-tryappservice/AzSearch-TryAppService-GetStarted.png
[6]: ./media/search-tryappservice/AzSearch-TryAppService-QueryWA.png
[7]: ./media/search-tryappservice/AzSearch-TryAppService-QueryPrecedence.png
[8]: ./media/search-tryappservice/AzSearch-TryAppService-VSOConnect.png
[9]: ./media/search-tryappservice/AzSearch-TryAppService-cSharpSample.png
[10]: ./media/search-tryappservice/AzSearch-TryAppService-RunBtn.png
[11]: ./media/search-tryappservice/AzSearch-TryAppService-searchmodeany.png
[12]: ./media/search-tryappservice/AzSearch-TryAppService-searchmodeWAState.png
[13]: ./media/search-tryappservice/AzSearch-TryAppService-Schema.png
[14]: ./media/search-tryappservice/AzSearch-TryAppService-HitHighlight.png

<!---HONumber=AcomDC_0224_2016-->