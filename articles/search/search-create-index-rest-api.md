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
	ms.date="02/18/2016"
	ms.author="heidist"/>

# Creare un indice di Ricerca di Azure con un'API REST
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

Questo articolo illustra come creare un indice con l'[API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). Parte del contenuto riportato di seguito è estratto dall'articolo [Creare un indice (API REST di Ricerca di Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx). Per altro contesto, vedere l'articolo padre.

I prerequisiti per la creazione di un indice includono la disponibilità di una connessione stabilita in precedenza al servizio di ricerca, che in genere si esegue tramite "httpClient".

## Definire lo schema dell'indice

Per creare l'indice con l'API REST, inviare una richiesta POST all'endpoint dell'URL di Ricerca di Azure. La struttura dell'indice sarà definita usando JSON nel corpo della richiesta.

**Richiesta e intestazioni della richiesta**:

Nell'esempio seguente è necessario usare l'endpoint dell'URL del servizio, notando in particolare il nome del servizio e la versione dell'API appropriata. Al momento della pubblicazione di questo documento la versione dell'API corrente è "2015-02-28". Nelle intestazioni della richiesta si dovrà fornire anche la chiave amministratore primaria del servizio ricevuta al momento della [creazione di un servizio](https://msdn.microsoft.com/library/azure/dn798941.aspx/).

	POST https://[servicename].search.windows.net/indexes?api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key]


**Corpo della richiesta**:

In questa sezione si identifica il nome dell'indice, "hotels" in questo caso, nonché [nomi, tipi e gli attributi dei campi](https://msdn.microsoft.com/library/azure/dn798941.aspx).

	{
		"name": "hotels",  
		"fields": [
			{"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
			{"name": "baseRate", "type": "Edm.Double"},
			{"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
			{"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, analyzer:"fr.lucene"},
			{"name": "hotelName", "type": "Edm.String"},
			{"name": "category", "type": "Edm.String"},
			{"name": "tags", "type": "Collection(Edm.String)"},
			{"name": "parkingIncluded", "type": "Edm.Boolean"},
			{"name": "smokingAllowed", "type": "Edm.Boolean"},
			{"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
			{"name": "rating", "type": "Edm.Int32"},
			{"name": "location", "type": "Edm.GeographyPoint"}
		]
	}

Per una richiesta riuscita, verrà visualizzato il codice di stato "201 Creato". Per altre informazioni sulla creazione di un indice tramite l'API REST, vedere [questa pagina](https://msdn.microsoft.com/library/azure/dn798941.aspx).

<!---HONumber=AcomDC_0224_2016-->