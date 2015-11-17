<properties
	pageTitle="Importare dati in Ricerca di Azure tramite l'API REST | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Come caricare dati in un indice di Ricerca di Azure tramite l'API REST."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Importare dati in Ricerca di Azure tramite l'API REST
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

Questo articolo illustra come popolare un indice con l'[API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). Parte del contenuto seguente è estratto dall'articolo [Aggiungere, aggiornare o eliminare documenti (API REST di Ricerca di Azure)] (https://msdn.microsoft.com/library/azure/dn798930.aspx). Per altro contesto, vedere l'articolo padre.

I prerequisiti per l'importazione includono la disponibilità di un indice esistente già configurato per ricevere i dati.

Quando si usa l'API REST, l'inserimento dei dati si basa su una richiesta HTTP PUT o POST. I frammenti di codice sono estratti dall'[esempio di profili di punteggio](search-get-started-scoring-profiles.md).

        static bool PostDocuments(string fileName)
        {
            // Add some documents to the newly created index
            Uri requestUri = new Uri(serviceUrl + indexName + "/docs/index?" + ApiVersion);

            // Load the json containing the data from an external file
            string json = File.ReadAllText(fileName);

            using (HttpClient client = new HttpClient())
            {
                // Create the index
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.PostAsync(requestUri,       // To add data use POST
                    new StringContent(json, Encoding.UTF8, "application/json")).Result;

                if (response.StatusCode == HttpStatusCode.OK)
                {
                    Console.WriteLine("Documents posted from file {0}. \r\n", fileName);
                    return true;
                }

                Console.WriteLine("Documents failed to upload: {0} {1} \r\n", (int)response.StatusCode, response.Content.ReadAsStringAsync().Result.ToString());
                return false;

            }
        }

<!---HONumber=Nov15_HO3-->