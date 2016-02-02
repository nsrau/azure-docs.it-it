<properties
	pageTitle="Compilare query in Ricerca di Azure con .NET | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Compilare una query di ricerca in Ricerca di Azure e usare i parametri di ricerca per filtrare, ordinare ed esplorare in base a facet i risultati della ricerca con la libreria .NET o l'SDK."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="dotnet"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/10/2015"
	ms.author="heidist"/>

#Compilare query in Ricerca di Azure con .NET
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Questo articolo illustra come compilare una query con [Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx). Il contenuto seguente è un subset dell'articolo [Come usare Ricerca di Azure da un'applicazione .NET](search-howto-dotnet-sdk.md). Vedere l'articolo padre per la procedura end-to-end.

I prerequisiti per la creazione di una query includono la disponibilità di una connessione stabilita in precedenza al servizio di ricerca, che in genere si esegue attraverso `SearchServiceClient`.

Il frammento di codice seguente crea un metodo che passa l'input di una stringa di ricerca a un metodo SearchDocuments.

	private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
	{
		// Execute search based on search text and optional filter
		var sp = new SearchParameters();
	
		if (!String.IsNullOrEmpty(filter))
		{
			sp.Filter = filter;
		}
	
		DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
		foreach (SearchResult<Hotel> result in response)
		{
			Console.WriteLine(result.Document);
		}
	}
	
Prima di tutto questo metodo crea un nuovo oggetto SearchParameters. Consente di specificare opzioni aggiuntive per la query di ordinamento, filtro, impaginazione e faceting. In questo esempio viene impostata solo la proprietà Filter.

Il passaggio successivo consiste nell’esecuzione effettiva della query di ricerca. Questa operazione viene eseguita con il metodo Documents.Search. In questo caso, è possibile passare il testo di ricerca da utilizzare come stringa più i parametri di ricerca creati in precedenza. Viene anche specificato Hotel come parametro di tipo per Documents.Search, che indica all'SDK di deserializzare i documenti nei risultati della ricerca in oggetti di tipo Hotel.

Infine, questo metodo scorre tutte le corrispondenze nei risultati della ricerca, stampando ogni documento nella console.

Si osservi il modo in cui viene chiamato questo metodo:

	SearchDocuments(indexClient, searchText: "fancy wifi");
	SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

Nella prima chiamata, si cercano tutti i documenti che contengono i termini di query "fancy" o "wifi". Nella seconda chiamata, il testo di ricerca è impostato su "*", che significa "trova tutti gli elementi". Per altre informazioni sulla sintassi dell'espressione di query di ricerca, vedere [Semplice sintassi di query in Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798920.aspx).

La seconda chiamata usa un'espressione OData $filter, category eq 'Luxury'. Questa chiamata vincola la ricerca alla restituzione solo dei documenti in cui il campo category corrisponde esattamente alla stringa "Luxury". Per altre informazioni sulla sintassi di OData, vedere [Sintassi delle espressioni OData per Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798921.aspx).

<!---HONumber=AcomDC_0128_2016-->