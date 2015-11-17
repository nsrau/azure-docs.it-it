<properties
	pageTitle="Connettere Ricerca di Azure ad app Web ASP.NET MVC | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Associare un'app Web ASP.NET MVC a Ricerca di Azure, un servizio di ricerca cloud ospitato. Informazioni su come connettere, eseguire query ed eseguire il rendering di risultati mediante la libreria .NET o l'API REST."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor="v-lincan"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="11/04/2015"
	ms.author="heidist"/>

#Come integrare Ricerca di Azure con le app Web ASP.NET MVC

ASP.NET MVC è il framework per applicazioni Web più diffuso nelle soluzioni personalizzate che si integrano con Ricerca di Azure. In questo articolo verrà illustrato come connettere l'app Web ASP.NET a Ricerca di Azure, sfruttare i modelli di progettazione per le operazioni comuni ed esaminare alcune procedure di codifica che possono semplificare l'esperienza di sviluppo.

##Esempi e demo che usano ASP.NET e Ricerca di Azure

Sono già disponibili alcuni esempi di codice che illustrano l'integrazione di Ricerca con ASP.NET. È possibile passare direttamente al codice o a un'app demo selezionando uno dei collegamenti seguenti:

- [Sito demo relativo a offerte di lavoro a New York City (NYC)](http://aka.ms/azjobsdemo)
- [Provare il servizio app con Ricerca di Azure](search-tryappservice.md)
- [Elenco completo di video, esercitazioni, demo ed esempi di codice](earch-video-demo-tutorial-list.md)

##Connettersi al servizio

Per stabilire una connessione al servizio ed emettere richieste, l'applicazione Web necessita solo dei tre elementi seguenti:

- URL per il servizio Ricerca di Azure di cui è stato eseguito il provisioning, formattato come https://<service-name>.search.windows.net.
- Chiave API (stringa) che autentica la connessione a Ricerca di Azure.
- HTTPClient o SearchServiceClient per formulare la richiesta di connessione.

####URL e chiavi API

L'URL e la chiave API sono disponibili nel [portale](search-create-service-portal.md) o possono essere recuperati a livello di codice mediante l'[API REST di gestione](https://msdn.microsoft.com/library/dn832684.aspx).

In genere, l'URL e la chiave si trovano nel file web.config del programma di interazione utente:

      <appSettings>
    	<add key="SearchServiceName" value="[SEARCH SERVICE NAME]" />
    	<add key="SearchServiceApiKey" value="[API KEY]" />
    	. . .
      </appSettings>

Il nome del servizio di ricerca può essere il nome breve specificato durante il provisioning, purché si aggiunga il dominio (search.windows.net) alla connessione, oppure è possibile specificare il nome completo (<service-name>.search.windows.net) in web.config, senza il prefisso HTTPS.

La chiave API è un token di autenticazione generato durante il provisioning del servizio (solo chiavi amministratore) o generato manualmente se si creano chiavi di query nel portale. Il tipo di chiave determina le operazioni di ricerca disponibili per l'applicazione:

- Chiavi amministratore (autorizzazioni di lettura-scrittura, 2 per ogni servizio)
- Chiavi di query (sola lettura, fino a 50 per ogni servizio)

Le chiavi API sono stringhe di 32 caratteri. Non esiste alcuna distinzione visiva tra le chiavi amministratore e le chiavi di query. Se si perde il tipo di chiave specificato nel codice, è necessario controllare il portale o utilizzare l'API REST di gestione in modo che restituisca il tipo di chiave. Per ulteriori informazioni sulle chiavi, visitare [API REST per il servizio Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx).

> [AZURE.TIP]Una chiave di query offre una soluzione di sola lettura al client. Per provare a usare le operazioni di Ricerca di Azure disponibili in un servizio di sola lettura, vedere [Provare il servizio app con Ricerca di Azure](search-tryappservice.md). Si noti che il codice dell'app Web disponibile in tale articolo è completamente modificabile: è possibile cambiare qualsiasi elemento del codice C# nel progetto ASP.NET per modificare il layout della pagina Web, la struttura della query di ricerca o i risultati di ricerca. Solo l'indice e le operazioni di carico dei documenti del servizio Ricerca di Azure sono di sola lettura, a causa dell'inclusione della chiave API relativa alla query nella connessione al servizio.

####Connessione client

I due frammenti di codice seguenti stabiliscono una connessione al servizio di ricerca mediante l'URL e la chiave API. È importante ricordare che il nome del servizio e le chiavi API sono specificati nel file web.config. Per le chiamate REST è necessario che le chiavi amministratore vengano passate nell'intestazione della richiesta, mentre le chiavi di query possono essere passate nell'intestazione o direttamente nell'URL.

**[HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient.aspx) con chiamate API REST**

    public class CatalogSearch
    {
        private static readonly Uri _serviceUri;
        private static HttpClient _httpClient;
        public static string errorMessage;

        static CatalogSearch()
        {
            try
            {
                _serviceUri = new Uri("https://" + ConfigurationManager.AppSettings["SearchServiceName"] + ".search.windows.net");
                _httpClient = new HttpClient();
                _httpClient.DefaultRequestHeaders.Add("api-key", ConfigurationManager.AppSettings["SearchServiceApiKey"]);
            }
            catch (Exception e)
            {
                errorMessage = e.Message;
            }
        }

**[SearchServiceClient](https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchserviceclient.aspx) con .NET**

        static UsgsSearch()
        {
            try
            {
                string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
                string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];

                // Create an HTTP reference to the catalog index. Alternatively, include the index name in the query
                _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
            }
            catch (Exception e)
            {
                errorMessage = e.Message.ToString();
            }
        }

##Modelli di progettazione

Un'app Web integrata con Ricerca di Azure dovrà formulare query ed eseguire il rendering dei risultati. Questa sezione fornisce indicazioni su come strutturare il codice per attività eseguite in un programma che contiene codice di interazione utente. La definizione dello schema, la generazione dell'indice e l'inserimento di dati vengono esclusi intenzionalmente. Per indicazioni su come scrivere il codice per queste operazioni, vedere le procedure dettagliate e gli esempi elencati in [Video, esempi ed esercitazioni in Ricerca di Azure](search-video-demo-tutorial-list.md).

###Formulazione di query

Una ricerca full-text nell'indice viene eseguita sui campi contrassegnati come **isSearchable** nello schema che definisce l'indice. Dato un input di termine di ricerca (rappresentato di seguito dalla stringa "q"), il motore di ricerca cerca una corrispondenza in tutti i campi disponibili per la ricerca e restituisce risultati dai campi contrassegnati come **isRetrievable**.

> [AZURE.NOTE]Anche se è probabile che la maggior parte dei campi sia disponibile per la ricerca, un indice può includere campi usati solo nelle espressioni di filtro. In questo caso, tali campi devono essere contrassegnati come non disponibili per la ricerca, in modo da escluderli dalla ricerca full-text, e come non recuperabili, in modo da escluderli dai risultati della ricerca.

Una query di ricerca esegue il wrapping del termine di input specificato dall'utente in una richiesta di ricerca che specifica l'indice di destinazione, oltre ai parametri usati per filtrare o perfezionare la richiesta. Gli operatori incorporati nella stringa di ricerca, ad esempio +, - o |, vengono gestiti automaticamente, ovvero non sono previsti requisiti di codifica per l'analisi di un termine di ricerca. Eventuali analisi vengono eseguite dal motore di ricerca come operazione interna. È possibile presupporre che la stringa passata verrà analizzata dal motore.

Sono disponibili due tipi di query di ricerca, ovvero **Search** o **Suggestions**. È consigliabile definire metodi separati per ogni tipo di query. **Search** indica la ricerca full-text sui campi dell'indice. **Suggestions** indica la funzionalità di completamento automatico delle query in Ricerca di Azure, che crea un elenco di potenziali termini di ricerca in base ai primi tre caratteri dell'input dell'utente. Nella maggior parte dei casi è consigliabile vincolare **Suggestions** solo ai campi che contengono valori relativamente univoci o distintivi, ad esempio un nome di prodotto o di pubblicazione, invece che a campi contenenti dati non differenziati.

Il frammento di codice seguente acquisisce un input di termine di ricerca in un programma che usa l'API REST. Il termine di input è rappresentato dalla stringa q e i parametri rimanenti vengono usati per passare valori di filtro da una struttura del sito con facet nella stessa pagina di ricerca. Nel metodo Search vengono usati sia il termine di input che i parametri di filtro.

        public ActionResult Search(string q = "", string color = null, string category = null, double? priceFrom = null, double? priceTo = null, string sort = null)
        {
            dynamic result = null;

            // If blank search, assume they want to search everything
            if (string.IsNullOrWhiteSpace(q))
                q = "*";

            result = _catalogSearch.Search(q, sort, color, category, priceFrom, priceTo);
            ViewBag.searchString = q;
            ViewBag.color = color;
            ViewBag.category = category;
            ViewBag.priceFrom = priceFrom;
            ViewBag.priceTo = priceTo;
            ViewBag.sort = sort;

            return View("Index", result);
        }
Il metodo **Search** che accetta questa query è definito come segue. Si noti che definisce i parametri nella stringa di query, oltre alla struttura del sito con facet (supportata tramite filtri che eseguono la maggior parte delle operazioni necessarie per il perfezionamento dei risultati della ricerca) e l'ordinamento.

        public dynamic Search(string searchText, string sort, string color, string category, double? priceFrom, double? priceTo)
        {
            string search = "&search=" + Uri.EscapeDataString(searchText);
            string facets = "&facet=color&facet=categoryName&facet=listPrice,values:10|25|100|500|1000|2500";
            string paging = "&$top=10";
            string filter = BuildFilter(color, category, priceFrom, priceTo);
            string orderby = BuildSort(sort);

            Uri uri = new Uri(_serviceUri, "/indexes/catalog/docs?$count=true" + search + facets + paging + filter + orderby);
            HttpResponseMessage response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Get, uri);
            AzureSearchHelper.EnsureSuccessfulSearchResponse(response);

            return AzureSearchHelper.DeserializeJson<dynamic>(response.Content.ReadAsStringAsync().Result);
        }

Un metodo .NET che crea una stringa di ricerca può essere inserito in una visualizzazione MVC o in un controller. Questa funzione passa la stringa al controller Home. Definisce anche una struttura di dati per i risultati.

    function Search() {

        var q = $("#q").val();
        
        $.post('/home/search',
        {
            q: q
        },
        function (data) {
            var searchResultsHTML = "<tr><td>FEATURE NAME</td><td>FEATURE CLASS</td>";
            searchResultsHTML += "<td>STATE ALPHA</td><td>COUNTY_NAME</td>";
            searchResultsHTML += "<td>Elevation (m)</td><td>Elevation (ft)</td><td>MAP NAME</td>";
            searchResultsHTML += "<td>DESCRIPTION</td><td>HISTORY</td><td>DATE CREATED</td>";
            searchResultsHTML += "<td>DATE EDITED</td></tr>";
            for (var i = 0; i < data.length; i++) {
                searchResultsHTML += "<td>" + data[i].Document.FEATURE_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.FEATURE_CLASS + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.STATE_ALPHA + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.COUNTY_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_M + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_FT + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.MAP_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.DESCRIPTION + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.HISTORY + "</td>";
                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_CREATED) + "</td>";
                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_EDITED) + "</td></tr>";
            }

            $("#searchResults").html(searchResultsHTML);

        });

Un metodo .NET per richiamare **Search** avrà un aspetto analogo al seguente, incluso nel programma C# principale che fornisce la connessione e le operazioni di ricerca:

        public DocumentSearchResponse Search(string searchText)
        {
            // Execute search based on query string
            try
            {
                SearchParameters sp = new SearchParameters() { SearchMode = SearchMode.All };
                return _indexClient.Documents.Search(searchText, sp);
            }
            catch (Exception e)
            {
                errorMessage = e.Message.ToString();
            }
            return null;
        }


###Gestione dei risultati della ricerca

I risultati della ricerca vengono restituiti come set di righe composto da campi contrassegnati nello schema dell'indice come isRetrievable. Uno dei modi più semplici per il rendering di un set di risultati è costituito dall'uso dell'oggetto di sistema ViewBag in MVC. Il frammento di codice seguente è incluso nel file Index.cshtml nel [progetto AdventureWorksDemo su CodePlex](https://azuresearchadventureworksdemo.codeplex.com/).

	@model dynamic
	
	@{
	    ViewBag.Title = "Search";
	}
	
	<h2>Product search</h2>
	
	@if (@ViewBag.errorMessage != null) {
	    @ViewBag.errorMessage
	} else {
	    <div class="container">
	        <form action="/Home/Search" method="get">
	            <input type="search" name="q" id="q" value="@ViewBag.searchString" autocomplete="off" size="100" /> <button type="submit">Search</button>
	            <input type="hidden" name="color" id="color" value="@ViewBag.color" />
	            <input type="hidden" name="category" id="category" value="@ViewBag.category" />
	            <input type="hidden" name="priceFrom" id="priceFrom" value="@ViewBag.priceFrom" />
	            <input type="hidden" name="priceTo" id="priceTo" value="@ViewBag.priceTo" />
	            <input type="hidden" name="sort" id="sort" value="@ViewBag.sort" />
	        </form>
	    </div>

###Esplorazione in base a facet

Nello stesso file Index.cshmtl è disponibile il codice HTML usato per creare una struttura di sito con facet, che fornisce classificazioni per filtri autoindirizzati, perfezionando progressivamente i risultati della ricerca in base a colore, prezzo o categoria.

	    if (@Model != null)
	    {
	        <div class="container">
	            <div class="row">
	                <div class="col-md-4">
	                    Colors:
	                    <ul>
	                        @foreach (var colorFacet in Model["@search.facets"].color)
	                        {
	                            <li><a href="#" onclick="document.getElementById('color').value='@colorFacet.value'; 
	document.forms[0].submit(); 
	return false;">@colorFacet.value</a> (@colorFacet.count)</li>
	                        }
	                    </ul>
	                    Categories:
	                    <ul>
	                        @foreach (var categoryFacet in Model["@search.facets"].categoryName)
	                        {
	                            <li><a href="#" onclick="document.getElementById('category').value='@categoryFacet.value'; document.forms[0].submit(); return false;">@categoryFacet.value</a> (@categoryFacet.count)</li>
	                        }
	                    </ul>
	                    Prices:
	                    <ul>
	                        @foreach (var priceFacet in Model["@search.facets"].listPrice)
	                        {
	                            if (priceFacet.count > 0)
	                            {
	                       <li><a href="#" onclick="document.getElementById('priceFrom').value=@(priceFacet.from ?? 0); document.getElementById('priceTo').value=@(priceFacet.to ?? 0); 
	document.forms[0].submit(); return false;
	">@(priceFacet.from ?? 0) - @(priceFacet.to ?? "more")</a> (@priceFacet.count)</li>
	                            }
	                        }
	                    </ul>
	                </div>
	                <div class="col-md-8">
	                    <p>
	                        Sort -
	                        <a href="#" onclick="document.getElementById('sort').value=null; document.forms[0].submit(); return false;">by relevance</a>
	                        <a href="#" onclick="document.getElementById('sort').value='listPrice'; document.forms[0].submit(); return false;">by list price</a>
	                        <a href="#" onclick="document.getElementById('sort').value='color'; document.forms[0].submit(); return false;">by color</a>
	                    </p>
	                    <p>Found @Model["@odata.count"] products in the catalog</p>
	
	                    <ul>
	                        @foreach (var product in Model.value)
	                        {
	                            <li>
	                                <h3><b>@product.name</b></h3>
	                                price: @product.listPrice, color: @product.color, weight: @product.weight, size: @product.size
	                            </li>
	                        }
	                    </ul>
	                </div>
	            </div>
	        </div>
	    }
	}


###Evidenziazione dei risultati

L'applicazione di uno stile all'istanza del termine di ricerca in un risultato di ricerca è denominata evidenziazione dei risultati. In Ricerca di Azure i risultati evidenziati sono specificati nella query tramite il parametro per l'evidenziazione della ricerca, a cui si assegna un elenco di campi separati da virgole da analizzare alla ricerca di termini corrispondenti. È possibile scegliere lo stile effettivo da applicare. I tre frammenti di codice seguenti sono tratti dall'esercitazione [Provare il servizio app con Ricerca di Azure](search-tryappservice.md).

È prima di tutto necessario specificare le evidenziazioni dei risultati come parametro di ricerca e quindi elencare i campi in cui cercare i termini corrispondenti. Specificare lo stile HTML da usare per i risultati evidenziati.

	// Set the Search parameters used when executing the search request
	     var sp = new SearchParameters
	{
	// Include a count of results in the query result
	     IncludeTotalResultCount = true,
	// Limit the results to 20 documents
	     Top = 20,
	// Enable hit-highlighting
	     HighlightFields = new[] { "FEATURE_NAME", "DESCRIPTION", "FEATURE_CLASS", "COUNTY_NAME", "STATE_ALPHA" },
	     HighlightPreTag = "<b>",
	     HighlightPostTag = "</b>",
	};

Scorrere quindi i risultati della ricerca per trovare la stringa che deve essere evidenziata. private HtmlString RenderHitHighlightedString(SearchResult item, string fieldName)

	  {
	     if (item.Highlights != null && item.Highlights.ContainsKey(fieldName))
	      {
	      string highlightedResult = string.Join("...", item.Highlights[fieldName]);
	      return new HtmlString(highlightedResult);
	      }
	      return new HtmlString(item.Document[fieldName].ToString());
	   }

Fornire infine il layout dei risultati della ricerca, specificando il set di risultati valutato nel frammento di codice precedente.

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


##Procedure di codifica comuni

Se non si ha esperienza di MVC, programmazione .NET o API REST, queste sezioni offrono alcune procedure di codifica utili per iniziare.

###Modello MVC

La tabella seguente riepiloga il modo in cui i componenti del modello MVC vengono usati nelle applicazioni che includono Ricerca di Azure. Se si usa MVC 4 o MVC 5, il codice che integra Ricerca di Azure verrà in genere aggiunto a questi moduli.

File|Descrizione
----|-----------
Web.config|Fornire l'URL del servizio e la chiave API. Aggiungere un riferimento a System.Configuration nel modulo del programma principale per leggere i valori.
Program.cs|Nel programma principale configurare un HttpClient o SearchServiceClient per stabilire una connessione al servizio. Aggiungere il metodo Search a questo programma.
DataModel|Non usato. Se si presuppone che le operazioni di creazione dell'indice e di caricamento dei dati si trovano in programmi diversi, non sarà necessario alcun modello di dati per Ricerca di Azure nell'applicazione Web.
Visualizzazioni|Una visualizzazione include il codice HTML per la pagina Web dell'applicazione, dall'input nella casella di ricerca all'HTML dinamico per la gestione dei risultati della ricerca.
Controller|La creazione della query e la gestione degli errori sono in genere disponibili in HomeContoller.cs. Il controller deve includere almeno un metodo di ricerca che recupera i risultati da Ricerca di Azure e inoltra il set di risultati alla visualizzazione. 

Facoltativamente, se si usano i suggerimenti per le query con completamento automatico, è possibile includere un metodo che restituisce le query suggerite, in base alla presenza o meno nell'indice di un valore corrispondente all'input del termine ricerca fornito dall'utente.

###Quando usare la libreria client .NET invece dell' API REST

Per le applicazioni ASP.NET la libreria client .NET è considerata un'opzione migliore, perché stabilisce una connessione HTTP e gestisce automaticamente la serializzazione e la deserializzazione JSON, semplificando il codice.

In alcuni casi è possibile che la scelta dell'API sia basata sulla parità di funzionalità tra i due approcci. In genere, la [libreria client .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx) e l'[API REST del servizio](https://msdn.microsoft.com/library/azure/dn798935.aspx) sono intercambiabili, purché le operazioni necessarie siano implementate in entrambe. In alcuni casi, tuttavia, nuove funzionalità risultano disponibili prima di tutto nell'API REST come parte di una versione di anteprima e vengono aggiunte alla libreria .NET solo dopo alcuni mesi. Ad esempio, gli indicizzatori, usati per automatizzare le operazioni di caricamento dei dati da tipi di origini di dati specifiche, sono stati resi disponibili nell'API REST di anteprima e sono stati inclusi nella libreria client solo alcuni mesi dopo. Eventuali limitazioni sull'implementazione delle funzionalità sono indicate nella documentazione relativa alle funzionalità.

###Includere AzureSearchHelper.cs per la serializzazione e la deserializzazione JSON nell'API REST

A differenza della libreria .NET, che esegue automaticamente questo passaggio, le API REST del servizio devono serializzare e deserializzare i documenti JSON nello scambio di richiesta-risposta con il servizio. JSON è il formato di payload per la trasmissione di dati durante il caricamento o l'aggiornamento dei documenti nell'indice.

Il codice per la serializzazione JSON è disponibile in molti esempi in un file denominato **AzureSearchHelper.cs**:

	using System;
	using System.Net.Http;
	using System.Text;
	using Newtonsoft.Json;
	using Newtonsoft.Json.Converters;
	using Newtonsoft.Json.Serialization;
	
	namespace CatalogCommon
	{
	    public class AzureSearchHelper
	    {
	        public const string ApiVersionString = "api-version=2014-07-31-Preview";
	
	        private static readonly JsonSerializerSettings _jsonSettings;
	
	        static AzureSearchHelper()
	        {
	            _jsonSettings = new JsonSerializerSettings
	            {
	                Formatting = Formatting.Indented, // for readability, change to None for compactness
	                ContractResolver = new CamelCasePropertyNamesContractResolver(),
	                DateTimeZoneHandling = DateTimeZoneHandling.Utc
	            };
	
	            _jsonSettings.Converters.Add(new StringEnumConverter());
	        }
	
	        public static string SerializeJson(object value)
	        {
	            return JsonConvert.SerializeObject(value, _jsonSettings);
	        }
	
	        public static T DeserializeJson<T>(string json)
	        {
	            return JsonConvert.DeserializeObject<T>(json, _jsonSettings);
	        }
	
	        public static HttpResponseMessage SendSearchRequest(HttpClient client, HttpMethod method, Uri uri, string json = null)
	        {
	            UriBuilder builder = new UriBuilder(uri);
	            string separator = string.IsNullOrWhiteSpace(builder.Query) ? string.Empty : "&";
	            builder.Query = builder.Query.TrimStart('?') + separator + ApiVersionString;
	
	            var request = new HttpRequestMessage(method, builder.Uri);
	
	            if (json != null)
	            {
	                request.Content = new StringContent(json, Encoding.UTF8, "application/json");
	            }
	
	            return client.SendAsync(request).Result;
	        }
	
	        public static void EnsureSuccessfulSearchResponse(HttpResponseMessage response)
	        {
	            if (!response.IsSuccessStatusCode)
	            {
	                string error = response.Content == null ? null : response.Content.ReadAsStringAsync().Result;
	                throw new Exception("Search request failed: " + error);
	            }
	        }
	    }
	}

###Organizzare il codice

La suddivisione dei carichi di lavoro in progetti autonomi nella stessa soluzione di Visual Studio offre una maggiore flessibilità nella progettazione, gestione ed esecuzione di ogni programma. È consigliabile creare tre progetti:

- Codice per la creazione dell'indice
- Codice per l'inserimento di dati
- Codice per l'interazione utente

In Ricerca di Azure le operazioni di indicizzazione e le operazioni relative ai documenti, ad esempio l'aggiunta o l'aggiornamento di documenti o l'esecuzione di query, sono completamente indipendenti tra loro. È quindi possibile separare la gestione degli indici dal codice per l'interazione utente ASP.NET che formula le richieste di ricerca ed esegue il rendering dei risultati.

Nella maggior parte degli esempi di codice l'indice viene creato e caricato in un progetto (indicato come DataIndexer, CatalogIndexer o DataCatalog in diversi esempi), mentre il codice che gestisce le richieste e le risposte di ricerca viene inserito in un progetto di applicazione MVC ASP.NET. Negli esempi di codice risulta utile aggregare la creazione dell'indice e il caricamento dei documenti in un progetto, ma è probabile che il codice di produzione isoli queste operazioni. Dopo la creazione, un indice viene raramente modificato (in caso di modifica, è necessario ricompilarlo), mentre è probabile che i documenti vengano aggiornati su base ricorrente.

La separazione dei carichi di lavoro offre altri vantaggi, ad esempio livelli diversi di autorizzazioni per Ricerca di Azure (diritti amministrativi completi rispetto a diritti per la sola esecuzione di query), uso di linguaggi di programmazione diversi, dipendenze più specifiche per ogni programma, oltre alla possibilità di rivedere indipendentemente i programmi o creare più applicazioni front-end che operano insieme sull'indice compilato e gestito da un'applicazione di indicizzazione centrale.

##Passaggi successivi

Per una migliore comprensione dell'integrazione tra Ricerca di Azure e ASP.NET, vedere i collegamenti seguenti:

- [Come usare Ricerca di Azure da un'applicazione .NET](search-howto-dotnet-sdk.md) 
- [Case study per gli sviluppatori di Ricerca di Azure](search-dev-case-study-whattopedia.md)
- [Flusso di lavoro tipico per lo sviluppo di Ricerca di Azure](search-workflow.md) 

<!---HONumber=Nov15_HO3-->