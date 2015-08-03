<properties 
   pageTitle="Prova Azure App Service con Ricerca di Azure" 
   description="Prova la Ricerca di Azure gratuitamente, per un'ora intera utilizzando il modello TryAzureAppService." 
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="mblythe" 
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="search" 
   ms.date="07/13/2015"
   ms.author="heidist"/>

# Prova Azure App Service con Ricerca di Azure

[Prova Azure App Service](https://tryappservice.azure.com/)è un modo nuovo e interamente gratuito per provare i servizi di Azure selezionati, tra cui la ricerca di Azure, fino a un'ora senza alcuna che sia richiesta l’abbonamento a Azure.

Il sito offre diversi modelli disponibili. Quando si seleziona il modello ASP.NET che include la Ricerca di Azure, si ottiene un'ora di accesso a un sito web completamente funzionale, supportato dai servizi selezionati. Non sarà possibile aggiornare o eliminare i dati gestiti dalla Ricerca di Azure, ma è possibile eseguire query e apportare un numero di modifiche al codice che rimodella l'esperienza dell'utente. Se la sessione scade prima di aver terminato di esplorare, è possibile ricominciare con un'altra sessione o passare al periodo di prova o effettuare l’iscrizione completa se l'obiettivo consiste nel creare o caricare direttamente un indice.

Nel sito [Prova Azure App Service](https://tryappservice.azure.com/), la Ricerca di Azure è parte del modello di applicazione Web, fornendo un'esperienza di ricerca full-text avanzata con una quantità di funzionalità basate su ricerca disponibile solo in questo servizio nella piattaforma Azure.

Anche se altri servizi di Azure, quali il Database SQL offrono la ricerca full-text, un servizio come la Ricerca di Azure fornisce l’ottimizzazione il controllo, l'impaginazione e i conteggi, poter evidenziare, usare completamento automatico nei suggerimenti delle query, l’assistenza sul linguaggio naturale, esplorazione in base a facet, filtro e così via. Come molti dei nostri[esempi](https://github.com/AzureSearch)dimostrano, è possibile sviluppare un'applicazione basata su ricerca completa utilizzando solo ricerca di Azure e ASP.NET.

Come parte dell’offerta [Prova App Azure Service](https://tryappservice.azure.com/), il servizio di Ricerca di Azure che verrà utilizzato è di sola lettura, pertanto sarà necessario utilizzare il corpo di ricerca disponibile nella sessione. Non è possibile caricare o utilizzare il proprio indice o i dati. I dati che verranno utilizzati sono del[United States Geological Survey (USG)]()costituiti da righe di circa 3 milioni di punti di riferimento, siti cronologici, edifici e altre funzionalità di punti di riferimento negli Stati Uniti.

Per sfruttare al meglio la sessione di un'ora, le istruzioni seguenti fungeranno da guida sulle query e sui codici.

Prima di proseguire, è possibile dedicare alcuni minuti per esaminare alcuni punti chiave relativi al codice, al servizio e ai dati sui cui è possibile effettuare la ricerca. Avere una breve introduzione può rivelarsi utile se non si possiede già familiarità con la Ricerca di Azure.

##Informazioni sul codice e la Ricerca di Azure

La Ricerca di Azure è una offerta un service-e-data [PaaS](https://en.wikipedia.org/wiki/Platform_as_a_service), costituita da un servizio di ricerca completamente gestito, oltre a poter eseguire ricerche su dati caricati quando si utilizza un'istanza senza vincolata di Ricerca di Azure (ad esempio, quando non si utilizza l’opzione Prova Azure App Service).

I Dati utilizzati nelle operazioni di ricerca vengono archiviati nel servizio di Ricerca in Azure, dove la prossimità dei dati alle operazioni permette una bassa latenza e una ricerca coerente. Attualmente, non esiste alcun supporto per l'archiviazione non offline o remota dei dati ricercabili. per approfondire il discorso:

- I dati ricercati vengono archiviati in un indice gestito dalla Ricerca di Azure, popolati da documenti, con un documento per ogni elemento ricercato. 
- La maggior parte degli indici vengono caricati da un singolo set di dati preparati in anticipo da parte dell'utente che includere solo i campi utili nel contesto delle operazioni di ricerca. 
- Lo schema che definisce l'indice è definito dall'utente e specifica i campi di ricerca, i campi che non è possibile ricercare che però possono essere utili in un'espressione di filtro e costruisce profili di punteggio per l'ottimizzazione dei risultati.
- I Dati possono essere caricati automaticamente da un indicizzatore (compatibile solo con Database SQL di Azure o Azure DocumentDB) o inseriti in un indice di ricerca tramite una delle API di Ricerca di Azure. Quando si utilizza l'API, è possibile inserire i dati da qualsiasi sorgente dati, basta che siano in formato JSON.

Nella opzione [Prova Azure App Service](https://tryappservice.azure.com/) l’ ASP.NET + il modello del sito della Ricerca di Azure fornisce i codice sorgente per l'applicazione Web modificabile in Visual Studio Online (disponibile come parte della sessione di un'ora). Nessuno strumento di sviluppo distinto è necessari per visualizzare o modificare il codice.

Il Codice è scritto in c#, utilizzando la [libreria client .NET della Ricerca di Azure](https://msdn.microsoft.com/library/dn951165.aspx)per eseguire query sull'indice, consentire l’esplorazione in base a facet e visualizzare i conteggi e risultati della ricerca in una pagina web.

Un’altro codice, non incluso nel modello, è stato utilizzato per creare e caricare l'indice di ricerca di gruppi di protezione utente. Poiché il servizio è di sola lettura, tutte le operazioni che richiedono l'utilizzo della scrittura sono state completate in anticipo. È possibile visualizzare una[copia dello schema](#schema)utilizzato per compilare lo schema alla fine di questo articolo.

##Introduzione

Se è stato avviata la sessione di 1 ora, attenersi alla seguente procedura per iniziare.

1. Andare su[https://tryappservice.azure.com](https://tryappservice.azure.com/)e scorrere verso il basso per selezionare**Web App**. 
2. Fare clic su **Avanti**.
3. Scegliere il modello**ASP.NET + sito di Ricerca di Azure**.

    ![][1]

4. Fare clic su **Crea**.
5. Scegliere un metodo di accesso e fornire il nome utente e password.

    ![][2]

6. Attendere che il sito venga caricato. Quando è pronto, verrà visualizzata una pagina simile alla seguente. Su ogni pagina viene visualizzato un orologio in modo da sapere sempre quanto tempo è ancora a disposizione.

    ![][3]

7. Scegliere**Modifica con Visual Studio Online**per visualizzare la soluzione ed esplorare il sito.
9. In Visual Studio Online, espandere le opzioni della sessione nella parte superiore della pagina e quindi fare clic su**Esplora sito Web**.

    ![][4]

10. Verrà visualizzata che la pagina per iniziare del sito web della Ricerca di Azure. Fare clic sul tasto**Per iniziare**per aprire il sito.

    ![][5]

11. Un sito web ASP.NET verrà visualizzata nel browser, che fornisce una casella di ricerca. Immettere un termine da cercare, ad esempio*Yellowstone*o una montagna nota come*Il Monte Rainer*. Partire da un punto di riferimento noto rende più semplice valutare i risultati.

    ![][6]


##Prime operazioni da eseguire
Poiché l'indice di ricerca è completamente operativo, un buon primo passo consiste nel provare alcune query. La Ricerca di Azure è compatibile con tutti gli operatori di ricerca standard (+, -, |), virgolette per corrispondenze letterali, il carattere jolly (*) e operatori di priorità. È possibile esaminare il riferimento della sintassi della query per l'elenco completo degli operatori.

- Iniziare con una ricerca che utilizza il carattere jolly, aggiungendo un asterisco (`*`). Questo indica il numero di documenti presenti nell'indice: 2,262,578.
- Successivamente, immettere "Yellowstone", quindi aggiungere "+ centro", "+ compilazione" e "-ND" per restringere progressivamente i risultati della ricerca solo su Centro visitatori di Yellowstone, escludendo quelli del Nord Dakota:`Yellowstone +center +building -ND`.  
- Provare una frase di ricerca che combina gli operatori degli precedenza e la stringa corrispondente:`statue+(park+MT)`. Si otterrà un risultato simile alla schermata riportata di seguito. Si noti che le categorie di facet, compaiono sotto un’istanza di classe, offrendo un filtro autodidattico che filtra la navigazione a facet, una funzionalità comunemente presente nella maggior parte delle applicazioni di ricerca.

    ![][7]

Pronto a Procedere? Modificare alcune righe di codice per controllare l'impatto sulle operazioni di ricerca full-text.

##Modificare searchMode.All

Ricerca di Azure ha una proprietà **searchMode**configurabile che è possibile utilizzare per controllare il comportamento degli operatori di ricerca. I valori validi per questa proprietà sono`Any`(impostazione predefinita) o`All`. Vedere[Sintassi Semplice delle Query ](https://msdn.microsoft.com/library/dn798920.aspx)per ulteriori informazioni sull'impostazione di queste opzioni.

- **searchMode.Any**stabilisce che qualsiasi corrispondenza di un termine di ricerca è sufficiente per includere un elemento nei risultati della ricerca. Se la frase di ricerca è`Yellowstone visitor center`allora qualsiasi documento contenente uno di questi termini è inclusa nei risultati della ricerca. Questa modalità è alterata dai*richiami*.
- Il **searchModel.All**utilizzato in questo esempio, richiede che tutti i termini specificati siano presenti nel documento. Questa modalità è più rigorose di**searchMode.Any**ma se si predilige la*precisione*rispetto al richiamo, è probabilmente la scelta giusta per l'applicazione. 

> [AZURE.NOTE]**searchMode.Any**è ottimale quando le query sono composte principalmente da frasi, con un utilizzo minimo degli operatori. Una regola empirica generale è che la ricerca di applicazioni per il consumatore, come siti di e-commerce, tendono a utilizzare solo termini, mentre le persone che eseguono ricerche sul contenuto o su dati sono più probabile che includano operatori nella frase di ricerca. Se si ritiene che le ricerche molto probabilmente includano operatori, in particolare l’`NOT (-)`operatore, iniziare con**searchModel.All**. Al contrario, l'altra scelta**searchMode.Any**sarà`OR`l’`NOT`operatore con altre condizioni di ricerca che possono espandere notevolmente i risultati anziché diminuirli. l’esempio seguente consente di comprendere la differenza.

In questa attività si modificherà il**searchMode**e si confronteranno i risultati di ricerca in base alla modalità.

1. Aprire la finestra del browser contenente l'applicazione di esempio, scegliere**Connetti a Visual Studio Online**.

    ![][8]

2. Aprire**Search.cshtml**trovare`searchMode.All`nella riga 39 e cambiarlo con`searchMode.Any`.

    ![][9]

3. Nella barra di spostamento a destra, fare clic su**Esegui**.

    ![][10]
 
Nella finestra dell'applicazione ricostruito, immettere un termine di ricerca utilizzato in precedenza, ad esempio`Yellowstone +center +building -ND`e confrontare i risultati prima e dopo delle modifiche apportate a**searchMode**.

È una differenza vera e propria. Invece di sette risultati di ricerca, è possibile ottenere più di due milioni.

   ![][11]
 
Il comportamento che si osserva è dovuto all’inclusione dell’`NOT`operatore (in questo caso, "-ND"), ovvero*o *anziché*e*quando**searchMode**è impostato su`Any`.

Data questa configurazione, i risultati della ricerca includono riscontri per i termini di ricerca`Yellowstone`,`center`, e`building`ma anche ogni documento che è`NOT North Dakota`. Poiché esistono solo 13,081 documenti che contengono la frase`North Dakota`, quasi tutti i set di dati vengono restituiti.

Si tratta probabilmente di uno scenario improbabile, ma illustra gli effetti di**searchMode**su frasi di ricerca che includono l’`NOT`operatore, pertanto è utile comprendere il motivo per cui si verifica il comportamento e come è possibile cambiarlo se questo non è ciò che si desidera.

Per continuare con questa esercitazione, ripristinare**searchMode**al valore originale (impostato sulla`All`riga 39), eseguire il programma e utilizzare l'app ristrutturata per le attività rimanenti.
 
##Aggiungere un filtro globale per lo stato di Washington

In genere, se si desidera eseguire la ricerca su un sottogruppo di dati disponibili, è possibile impostare il filtro nell'origine dati quando si importano dati. Per scopi di apprendimento, utilizzando dati di sola lettura, impostare il filtro dell’applicazione per ottenere solo documenti che includono lo stato di Washington.

1. Aprire Search.cshtml, trovare il**SearchParameters**(a partire da riga di 36) codice di blocco e aggiungere una riga di commento e il filtro.

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


I filtri vengono specificati utilizzando la sintassi di OData e vengono spesso utilizzati con navigazione a facet o inclusi nella stringa di query per limitare la query. Vedere[Sintassi del filtro OData](https://msdn.microsoft.com/library/azure/dn798921.aspx)per ulteriori informazioni.

2. Fare clic su **Run**.

3. Aprire l'applicazione.

4. Digitare il carattere jolly (*) per restituire un numero. Si noti che i risultati sono ora limitati a 42,411 elementi, che sono tutti i documenti per tutte le funzionalità geografiche nello stato di Washington.

   ![][12]

##Aggiungere evidenziazione dei riscontri

Ora che sono state apportate una serie di cambi alle singole righe di codice, è possibile provare le modifiche più approfondite che richiedono modifiche al codice in più posizioni. La seguente versione di**Search.cshtml**può essere incollata direttamente sul file Search.cshtml nella sessione corrente.

Il codice seguente aggiunge l'evidenziazione dei riscontri. Si notino le nuove proprietà dichiarate nel[SearchParameters](https://msdn.microsoft.com/library/microsoft.azure.search.models.searchparameters_properties.aspx). Esiste anche una nuova funzione che scorre l'insieme di risultati per ottenere documenti che richiedono l'evidenziazione, oltre a HTML che esegue il rendering del risultato.

Quando si esegue questo esempio di codice, I termini imputati che hanno una corrispondenza con i campi specificati sono evidenziate in grassetto.

   ![][14]

È possibile salvare una copia dell'originale**Search.cshtml**file per confrontare le versioni.

> [AZURE.NOTE]Per ridurre le dimensioni del file sono stati eliminati i commenti.
 
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


##Passaggi successivi

Utilizzando del servizio di sola lettura disponibile sul sito[Prova Azure App Service](https://tryappservice.azure.com/)sito, è stato possibile vedere la sintassi della query con la ricerca full-text in azione, apprendere nozioni su searchMode e i filtri e aggiungere l’evidenziazione all'applicazione di ricerca. Come passaggio successivo, prendere in considerazione la possibilità di creare e aggiornare gli indici Questo aggiunge la possibilità di:

- [Definire i profili dei punteggi](https://msdn.microsoft.com/library/dn798928.aspx)utilizzati per ottimizzare i punteggi di ricerca in modo che gli elementi di maggior rilevanza siano visualizzati per primi.
- [Definire le componenti di suggerimento](https://msdn.microsoft.com/library/mt131377.aspx)che consentono di aggiungere suggerimenti alle query di digitate o il completamento automatico in risposta all'input dell'utente.
- [Definire gli indicizzatori](https://msdn.microsoft.com/library/dn946891.aspx)che aggiornano l'indice automaticamente ogni volta che l'origine dati è il Database SQL Azure o Azure DocumentDB.

Per eseguire tutte queste attività, è necessario iscriversi ad Azure in modo che sia possibile creare e popolare gli indici in un servizio. Per ulteriori informazioni su come registrarsi per un periodo di prova gratuita, visitare[https://azure.microsoft.com/pricing/free-trial](https://azure.microsoft.com/pricing/free-trial/).

Per ulteriori informazioni sulla ricerca di Azure, visitare la [Pagina della documentazione](http://azure.microsoft.com/documentation/services/search/)su[http://azure.microsoft.com](http://azure.microsoft.com)o verificare qualsiasi numero di[esempi e video](https://msdn.microsoft.com/library/dn818681.aspx)che esplorano le tutte le funzionalità della Ricerca di Azure.

<a name="Schema"></a>
##Informazioni sullo schema

Nella istantanea video che seguente viene illustrato lo schema utilizzato per creare l'indice utilizzato in questo modello.
 
   ![][13]

###Schema.JSON file

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

<!---HONumber=July15_HO4-->