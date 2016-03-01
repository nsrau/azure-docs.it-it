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
	ms.date="02/17/2016"
	ms.author="heidist"/>

# Importare dati in Ricerca di Azure tramite l'API REST
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

Questo articolo illustra come popolare un indice con l'[API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). Parte del contenuto seguente è estratto dall'articolo [Aggiungere, aggiornare o eliminare documenti (API REST di Ricerca di Azure)](https://msdn.microsoft.com/library/azure/dn798930.aspx). Per altro contesto, vedere l'articolo padre.

Per effettuare il push dei documenti nell'indice con l'API REST, vengono inviate richieste HTTP POST vengono emessi per endpoint dell'URL del servizio.

**Richiesta e intestazioni della richiesta**:

Nell'URL è necessario fornire il nome del servizio nonché la versione dell'API appropriata. Al momento della pubblicazione di questo documento la versione dell'API corrente è "2015-02-28".

Nelle intestazioni della richiesta è necessario definire il tipo di contenuto e fornire la chiave amministratore primaria del servizio.

	POST https://[servicename].search.windows.net/indexes/[indexname]/docs/index?api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key]


**Corpo della richiesta**:


	{
		"value": [
			{
				"@search.action": "upload",
				"hotelId": "1",
				"baseRate": 199.0,
				"description": "Best hotel in town",
				"description_fr": "Meilleur hôtel en ville",
				"hotelName": "Fancy Stay",
				"category": "Luxury",
				"tags": ["pool", "view", "wifi", "concierge"],
				"parkingIncluded": false,
				"smokingAllowed": false,
				"lastRenovationDate": "2010-06-27T00:00:00Z",
				"rating": 5,
				"location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
			},
			{
				"@search.action": "upload",
				"hotelId": "2",
				"baseRate": 79.99,
				"description": "Cheapest hotel in town",
				"description_fr": "Hôtel le moins cher en ville",
				"hotelName": "Roach Motel",
				"category": "Budget",
				"tags": ["motel", "budget"],
				"parkingIncluded": true,
				"smokingAllowed": true,
				"lastRenovationDate": "1982-04-28T00:00:00Z",
				"rating": 1,
				"location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
			}
		]
	}

In questo caso, si userà "upload" come azione di ricerca. Quando si aggiornano o si eliminano documenti esistenti, è possibile usare "merge", "mergeOrUpload" e "delete".

Quando si aggiorna l'indice, verrà visualizzato un codice di stato "200 OK" per una richiesta riuscita. Se almeno un elemento nella richiesta non è stato indicizzato correttamente, verrà visualizzato un codice di stato "207".

Per altre informazioni su azioni sui documenti e risposte di esito positivo/errore, vedere [questa pagina](https://msdn.microsoft.com/library/azure/dn798930.aspx).

<!---HONumber=AcomDC_0224_2016-->