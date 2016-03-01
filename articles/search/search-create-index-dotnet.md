<properties
	pageTitle="Creare un indice di Ricerca di Azure con .NET | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Creare un indice nel codice tramite la Azure Search .NET SDK o la libreria .NET."
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
	ms.date="02/18/2016"
	ms.author="heidist"/>

# Creare un indice di Ricerca di Azure con .NET
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

Questo articolo illustra come creare un indice con [Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx). Il contenuto seguente è un subset dell'articolo [Come usare Ricerca di Azure da un'applicazione .NET](search-howto-dotnet-sdk.md). Vedere l'articolo padre per la procedura end-to-end.

I prerequisiti per la creazione di un indice includono la disponibilità di una connessione stabilita in precedenza al servizio di ricerca, che in genere si esegue tramite `SearchServiceClient`. La procedura consigliata per assicurare una ridistribuzione priva di problemi consiste nell'eliminare un indice esistente con lo stesso nome, se esiste già.

Supponendo la disponibilità di un indice denominato "hotels", è possibile costruire un metodo a tale scopo come segue:

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

Questo metodo utilizza il `SearchServiceClient` fornito per verificare l'esistenza dell'indice ed eventualmente eliminarlo.

Per creare un nuovo indice denominato "hotels", costruire un metodo simile al seguente:

    private static void CreateHotelsIndex(SearchServiceClient serviceClient)
    {
        var definition = new Index()
        {
            Name = "hotels",
            Fields = new[]
            {
                new Field("hotelId", DataType.String)                       { IsKey = true },
                new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
            }
        };

        serviceClient.Indexes.Create(definition);
    }

Questo metodo crea un nuovo oggetto `Index` con un elenco di oggetti `Field` che definisce lo schema del nuovo indice. Ogni campo ha un nome, un tipo di dati e diversi attributi che definiscono il comportamento della ricerca. Oltre ai campi, è possibile aggiungere anche profili di punteggio, suggerimenti di alternative o opzioni CORS per l'indice. Per motivi di brevità nell’esempio questi elementi sono stati omessi. È possibile trovare ulteriori informazioni sull'oggetto indice e le relative parti costituenti nel riferimento a SDK su [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.index_members.aspx), e nel [riferimento all'API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx).

<!---HONumber=AcomDC_0224_2016-->