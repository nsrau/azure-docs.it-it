<properties
	pageTitle="Importare dati in Ricerca di Azure con .NET | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Come caricare dati in un indice di Ricerca di Azure tramite .NET SDK o la libreria .NET"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="dotnet"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/09/2016"
	ms.author="heidist"/>

# Importare dati in Ricerca di Azure tramite .NET
> [AZURE.SELECTOR]
- [Panoramica](search-what-is-data-import.md)
- [Portale](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indicizzatori](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

Questo articolo illustra come popolare un indice con [Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx). Il contenuto seguente è un subset dell'articolo [Come usare Ricerca di Azure da un'applicazione .NET](search-howto-dotnet-sdk.md). Vedere l'articolo padre per la procedura end-to-end.

I prerequisiti per l'importazione di dati in un indice includono la disponibilità di un indice creato in precedenza.

Supponendo la disponibilità di un indice denominato "hotels", è possibile costruire un metodo per l'importazione dei dati come segue.

Il passaggio successivo in `Main` è il popolamento dell'indice appena creato. Questa operazione viene eseguita nel metodo seguente:

    private static void UploadDocuments(SearchIndexClient indexClient)
    {
        var documents =
            new Hotel[]
            {
                new Hotel()
                { 
                    HotelId = "1058-441", 
                    HotelName = "Fancy Stay", 
                    BaseRate = 199.0, 
                    Category = "Luxury", 
                    Tags = new[] { "pool", "view", "concierge" }, 
                    ParkingIncluded = false, 
                    LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
                    Rating = 5, 
                    Location = GeographyPoint.Create(47.678581, -122.131577)
                },
                new Hotel()
                { 
                    HotelId = "666-437", 
                    HotelName = "Roach Motel",
                    BaseRate = 79.99,
                    Category = "Budget",
                    Tags = new[] { "motel", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                    Rating = 1,
                    Location = GeographyPoint.Create(49.678581, -122.131577)
                },
                new Hotel() 
                { 
                    HotelId = "970-501", 
                    HotelName = "Econo-Stay",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "pool", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(46.678581, -122.131577)
                },
                new Hotel()
                { 
                    HotelId = "956-532", 
                    HotelName = "Express Rooms",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "wifi", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(48.678581, -122.131577)
                },
                new Hotel() 
                { 
                    HotelId = "566-518", 
                    HotelName = "Surprisingly Expensive Suites",
                    BaseRate = 279.99,
                    Category = "Luxury",
                    ParkingIncluded = false
                }
            };

        try
        {
            var batch = IndexBatch.Upload(documents);
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // Sometimes when your Search service is under load, indexing will fail for some of the documents in
            // the batch. Depending on your application, you can take compensating actions like delaying and
            // retrying. For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait a while for indexing to complete.
        Thread.Sleep(2000);
    }

Questo metodo è costituito da quattro parti. La prima crea una matrice di oggetti `Hotel` che verranno utilizzati come dati di input per caricare l'indice. Questi dati sono hardcoded per motivi di semplicità. Nell'applicazione, i dati probabilmente proverranno da un'origine dati esterna, ad esempio un database SQL.

La seconda parte crea un `IndexAction` per ogni `Hotel`, quindi li raggruppa insieme in un nuovo `IndexBatch`. Il batch viene quindi caricato nell'indice di Ricerca di Azure dal metodo `Documents.Index`.

> [AZURE.NOTE] In questo esempio, verranno semplicemente caricati i documenti. Per unire le modifiche in un documento esistente o eliminare un documento, è possibile usare il metodo `Merge`, `MergeOrUpload` o `Delete` corrispondente.

La terza parte di questo metodo è un blocco catch che gestisce un caso di errore importante per l'indicizzazione. Se il servizio Ricerca di Azure non riesce a indicizzare alcuni dei documenti nel batch, viene generato un `IndexBatchException` da `Documents.Index`. Questa situazione può verificarsi se l'indicizzazione dei documenti avviene mentre il servizio è sovraccarico. **Si consiglia di gestire in modo esplicito questo caso nel codice.** È possibile ritardare e quindi ritentare l'indicizzazione di documenti, accedere e continuare come nell'esempio, oppure eseguire altre attività a seconda dei requisiti di coerenza di dati dell'applicazione.

Infine, il metodo ritarda per due secondi. L'indicizzazione avviene in modo asincrono nel servizio Ricerca di Azure, pertanto l'applicazione di esempio deve attendere un breve periodo per garantire che i documenti siano disponibili per la ricerca. Ritardi come questi in genere sono necessari solo in applicazioni di esempio, test e demo.

<!---HONumber=AcomDC_0302_2016-->