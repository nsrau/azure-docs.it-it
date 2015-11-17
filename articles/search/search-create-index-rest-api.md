<properties
	pageTitle="Creare un indice di Ricerca di Azure con un'API REST | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Creare un indice nel codice tramite Ricerca di Azure e un'API REST HTTP."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Creare un indice di Ricerca di Azure con un'API REST
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

Questo articolo illustra come creare un indice con l'[API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). Parte del contenuto riportato di seguito è estratto dall'articolo [Creare un indice (API REST di Ricerca di Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx). Per altro contesto, vedere l'articolo padre.

I prerequisiti per la creazione di un indice includono la disponibilità di una connessione stabilita in precedenza al servizio di ricerca, che in genere si esegue tramite "httpClient".

Un approccio in 3 parti per la creazione di un indice tramite l'API REST include la configurazione di una connessione, l'indicazione di uno schema dell'indice e quindi l'esecuzione di un comando che compila l'indice. I frammenti di codice sono estratti dall'[esempio di profili di punteggio](search-get-started-scoring-profiles.md).

##Definire lo schema dell'indice

L'API REST accetta le richieste in formato JSON. Per creare lo schema è possibile includere un file di schema autonomo come documento JSON. Di seguito è illustrato un esempio.

Nell'esempio da cui deriva questo frammento il codice JSON seguente vien salvato in un file denominato schema.json.

	{
	    "name": "musicstoreindex",
	    "fields": [
	        { "name": "key", "type": "Edm.String", "key": true },
	        { "name": "albumTitle", "type": "Edm.String"},
	        { "name": "albumUrl", "type": "Edm.String", "filterable": false },
	        { "name": "genre", "type": "Edm.String" },
	        { "name": "genreDescription", "type": "Edm.String", "filterable": false },
	        { "name": "artistName", "type": "Edm.String"},
	        { "name": "orderableOnline", "type": "Edm.Boolean" },
	        { "name": "rating", "type": "Edm.Int32" },
	        { "name": "tags", "type": "Collection(Edm.String)" },
	        { "name": "price", "type": "Edm.Double", "filterable": false },
	        { "name": "margin", "type": "Edm.Int32", "retrievable": false },
	        { "name": "inventory", "type": "Edm.Int32" },
	        { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }
	    ],
	    "scoringProfiles": [
	        {
	            "name": "boostGenre",
	            "text": {
	                "weights": {
	                    "albumTitle": 5,
	                    "genre": 50,
	                    "artistName": 5
	                }
	            }
	        },
	        {
	            "name": "newAndHighlyRated",
	            "functions": [
	                {
	                    "type": "freshness",
	                    "fieldName": "lastUpdated",
	                    "boost": 10,
	                    "interpolation": "quadratic",
	                    "freshness": {
	                        "boostingDuration": "P365D"
	                    }
	                },
	                {
	                    "type": "magnitude",
	                    "fieldName": "rating",
	                    "boost": 10,
	                    "interpolation": "linear",
	                    "magnitude": {
	                        "boostingRangeStart": 1,
	                        "boostingRangeEnd": 5,
	                        "constantBoostBeyondRange": false
	                    }
	                }
	            ]
	        },
	        {
	            "name": "boostMargin",
	            "functions": [
	
	                {
	                    "type": "magnitude",
	                    "fieldName": "margin",
	                    "boost": 50,
	                    "interpolation": "linear",
	                    "magnitude": {
	                        "boostingRangeStart": 50,
	                        "boostingRangeEnd": 100,
	                        "constantBoostBeyondRange": false
	                    }
	                }
	            ]
	        }
	    ]
	}

##Configurare una richiesta HTTP per la connessione e la compilazione dell'indice

Le API REST formulano la connessione HTTP a ogni richiesta. Ogni richiesta stabilisce sempre l'URL del servizio, la versione dell'API in uso e una chiave amministratore che concede l'autorizzazione per le operazioni di lettura e scrittura nel servizio, definita chiave primaria di seguito perché così è chiamata nel portale.

Il frammento di codice usa valori statici come input, ad esempio un nome per l'indice, come specificato nel file app.config (non illustrato). L'esempio è stato creato in questo modo per semplificare il codice.

        private const string ApiVersion = "api-version=2015-02-28";
        private static string serviceUrl = ConfigurationManager.AppSettings["serviceUrl"];
        private static string indexName = ConfigurationManager.AppSettings["indexName"];
        private static string primaryKey = ConfigurationManager.AppSettings["primaryKey"];

L'esempio seguente include una richiesta HTTP PUT all'URL del servizio, insieme ad `api-version` e al nome dell'indice da creare.

        static bool CreateIndex()
        {
            // Create an index using an index name
            Uri requestUri = new Uri(serviceUrl + indexName + "?" + ApiVersion);

            // Load the json containing the schema from an external file
            string json = File.ReadAllText("schema.json");

            using (HttpClient client = new HttpClient())
            {
                // Create the index
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.PutAsync(requestUri,        // To create index use PUT
                    new StringContent(json, Encoding.UTF8, "application/json")).Result;

                if (response.StatusCode == HttpStatusCode.Created)   // For Posts we want to know response had no content
                {
                    Console.WriteLine("Index created. \r\n");
                    return true;
                }

                Console.WriteLine("Index creation failed: {0} {1} \r\n", (int)response.StatusCode, response.Content.ReadAsStringAsync().Result.ToString());
                return false;

            }
        }

<!---HONumber=Nov15_HO3-->