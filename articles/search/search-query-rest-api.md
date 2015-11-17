<properties
	pageTitle="Compilare query in Ricerca di Azure con chiamate REST | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Compilare una query di ricerca in Ricerca di Azure e usare i parametri di ricerca per filtrare, ordinare ed esplorare in base a facet i risultati della ricerca con la libreria .NET o l'SDK."
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
	ms.date="11/10/2015"
	ms.author="heidist"/>

#Compilare query in Ricerca di Azure con chiamate REST 
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [Postman](search-chrome-postman.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Questo articolo illustra come creare una query su un indice con [API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). Parte del contenuto riportato di seguito è estratto dall'articolo [Eseguire ricerche nei documenti (API REST di Ricerca di Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx). Per altro contesto, vedere l'articolo padre.

I prerequisiti per l'importazione includono la disponibilità di un indice esistente in cui sono già caricati i documenti che forniscono dati ricercabili.

Quando si usa l'API REST, le query sono basate su una richiesta HTTP GET. I frammenti di codice sono estratti dall'[esempio di profili di punteggio](search-get-started-scoring-profiles.md).

        static JObject ExecuteRequest(string action, string query = "")
        {
            // original:  string url = serviceUrl + indexName + "/" + action + "?" + ApiVersion; 
            string url = serviceUrl + indexName + "/docs?" + action ;
            if (!String.IsNullOrEmpty(query))
            {
                url += query + "&" + ApiVersion;
            }

            string response = ExecuteGetRequest(url);
            return JObject.Parse(response);

        }

        static string ExecuteGetRequest(string requestUri)
        {
            //This will execute a get request and return the response
            using (HttpClient client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.GetAsync(requestUri).Result;        // Searches are done over port 80 using Get
                return response.Content.ReadAsStringAsync().Result;
            }

        }

<!---HONumber=Nov15_HO3-->