---
title: Come gestire le scritture simultanee nelle risorse in Ricerca di Azure
description: Usare la concorrenza ottimistica per evitare conflitti a livello di aggiornamento o di eliminazione in indici, indicizzatori e origini dati di Ricerca di Azure.
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/21/2017
ms.author: heidist
ms.openlocfilehash: aee1b7376d4829e3e2f5a232525e3c3cb4df9d8e
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2017
---
# <a name="how-to-manage-concurrency-in-azure-search"></a>Come gestire la concorrenza in Ricerca di Azure

Quando si gestiscono le risorse di Ricerca di Azure, ad esempio indici e origini dati, è importante aggiornare le risorse in modo sicuro, soprattutto se componenti diversi dell'applicazione accedono simultaneamente alle risorse. Quando due client aggiornano simultaneamente un risorsa senza coordinazione, si possono verificare race condition. Per evitare questo problema, Ricerca di Azure offre un *modello di concorrenza ottimistica*. Non sono presenti blocchi su una risorsa, ma per ogni risorsa esiste un ETag che identifica la versione della risorsa in modo che sia possibile creare richieste che evitano sovrascritture accidentali.

> [!Tip]
> Il codice concettuale in una [soluzione C# di esempio](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) illustra come funziona il controllo della concorrenza in Ricerca di Azure. Il codice crea le condizioni che richiamano il controllo della concorrenza. La lettura del [frammento di codice riportato più avanti](#samplecode) è probabilmente sufficiente per la maggior parte degli sviluppatori, ma, se lo si vuole eseguire, modificare appsettings.json per aggiungere il nome del servizio e una chiave API di amministrazione. Dato un URL del servizio `http://myservice.search.windows.net`, il nome del service è `myservice`.

## <a name="how-it-works"></a>Funzionamento

La concorrenza ottimistica viene implementata tramite i controlli delle condizioni di accesso nelle chiamate API che scrivono in indici, indicizzatori, origini dati e risorse synonymMap. 

Tutte le risorse hanno un [*tag di entità (ETag)*](https://en.wikipedia.org/wiki/HTTP_ETag) che fornisce informazioni sulla versione dell'oggetto. Controllando prima l'ETag, è possibile evitare aggiornamenti simultanei in un flusso di lavoro tipico (acquisizione, modifica locale, aggiornamento) assicurandosi che l'ETag della risorsa corrisponda alla copia locale. 

+ L'API REST usa un [ETag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) nell'intestazione della richiesta.
+ .NET SDK imposta l'ETag tramite un oggetto accessCondition, impostando l'[intestazione If-Match | If-Match-None](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) per la risorsa. Tutti gli oggetti che ereditano da [IResourceWithETag (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) hanno un oggetto accessCondition.

Ogni volta che si aggiorna una risorsa, l'ETag cambia automaticamente. Quando si implementa la gestione della concorrenza, si inserisce semplicemente una precondizione nella richiesta di aggiornamento, che obbliga la risorsa remota ad avere lo stesso ETag della copia della risorsa modificata nel client. Se un processo simultaneo ha già modificato la risorsa remota, l'ETag non soddisferà la precondizione e la richiesta genererà l'errore HTTP 412. Se si usa .NET SDK, viene generata una `CloudException` in cui il metodo di estensione `IsAccessConditionFailed()` restituisce true.

> [!Note]
> Esiste un solo meccanismo per la concorrenza, che viene sempre usato indipendentemente dall'API usata per gli aggiornamenti delle risorse. 

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Casi d'uso e codici di esempio

Il codice seguente illustra i controlli accessCondition per le principali operazioni di aggiornamento:

+ L'aggiornamento non riesce se la risorsa non esiste più
+ L'aggiornamento non riesce se la versione della risorsa viene modificata

### <a name="sample-code-from-dotnetetagsexplainer-programhttpsgithubcomazure-samplessearch-dotnet-getting-startedtreemasterdotnetetagsexplainer"></a>Codice di esempio dal [programma DotNetETagsExplainer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```
    class Program
    {
        // This sample shows how ETags work by performing conditional updates and deletes
        // on an Azure Search index.
        static void Main(string[] args)
        {
            IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            IConfigurationRoot configuration = builder.Build();

            SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

            Console.WriteLine("Deleting index...\n");
            DeleteTestIndexIfExists(serviceClient);

            // Every top-level resource in Azure Search has an associated ETag that keeps track of which version
            // of the resource you're working on. When you first create a resource such as an index, its ETag is
            // empty.
            Index index = DefineTestIndex();
            Console.WriteLine(
                $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

            // Once the resource exists in Azure Search, its ETag will be populated. Make sure to use the object
            // returned by the SearchServiceClient! Otherwise, you will still have the old object with the
            // blank ETag.
            Console.WriteLine("Creating index...\n");
            index = serviceClient.Indexes.Create(index);
            
            Console.WriteLine($"Test index created; Its ETag should be populated. ETag: '{index.ETag}'");

            // ETags let you do some useful things you couldn't do otherwise. For example, by using an If-Match
            // condition, we can update an index using CreateOrUpdate and be guaranteed that the update will only
            // succeed if the index already exists.
            index.Fields.Add(new Field("name", AnalyzerName.EnMicrosoft));
            index =
                serviceClient.Indexes.CreateOrUpdate(
                    index,
                    accessCondition: AccessCondition.GenerateIfExistsCondition());

            Console.WriteLine(
                $"Test index updated; Its ETag should have changed since it was created. ETag: '{index.ETag}'");

            // More importantly, ETags protect you from concurrent updates to the same resource. If another
            // client tries to update the resource, it will fail as long as all clients are using the right
            // access conditions.
            Index indexForClient1 = index;
            Index indexForClient2 = serviceClient.Indexes.Get("test");

            Console.WriteLine("Simulating concurrent update. To start, both clients see the same ETag.");
            Console.WriteLine($"Client 1 ETag: '{indexForClient1.ETag}' Client 2 ETag: '{indexForClient2.ETag}'");

            // Client 1 successfully updates the index.
            indexForClient1.Fields.Add(new Field("a", DataType.Int32));
            indexForClient1 =
                serviceClient.Indexes.CreateOrUpdate(
                    indexForClient1,
                    accessCondition: AccessCondition.IfNotChanged(indexForClient1));

            Console.WriteLine($"Test index updated by client 1; ETag: '{indexForClient1.ETag}'");

            // Client 2 tries to update the index, but fails, thanks to the ETag check.
            try
            {
                indexForClient2.Fields.Add(new Field("b", DataType.Boolean));
                serviceClient.Indexes.CreateOrUpdate(
                    indexForClient2, 
                    accessCondition: AccessCondition.IfNotChanged(indexForClient2));

                Console.WriteLine("Whoops; This shouldn't happen");
                Environment.Exit(1);
            }
            catch (CloudException e) when (e.IsAccessConditionFailed())
            {
                Console.WriteLine("Client 2 failed to update the index, as expected.");
            }

            // You can also use access conditions with Delete operations. For example, you can implement an
            // atomic version of the DeleteTestIndexIfExists method from this sample like this:
            Console.WriteLine("Deleting index...\n");
            serviceClient.Indexes.Delete("test", accessCondition: AccessCondition.GenerateIfExistsCondition());

            // This is slightly better than using the Exists method since it makes only one round trip to
            // Azure Search instead of potentially two. It also avoids an extra Delete request in cases where
            // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
            // Azure Search is idempotent.

            // And we're done! Bye!
            Console.WriteLine("Complete.  Press any key to end application...\n");
            Console.ReadKey();
        }

        private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
        {
            string searchServiceName = configuration["SearchServiceName"];
            string adminApiKey = configuration["SearchServiceAdminApiKey"];

            SearchServiceClient serviceClient =
                new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
            return serviceClient;
        }

        private static void DeleteTestIndexIfExists(SearchServiceClient serviceClient)
        {
            if (serviceClient.Indexes.Exists("test"))
            {
                serviceClient.Indexes.Delete("test");
            }
        }

        private static Index DefineTestIndex() =>
            new Index()
            {
                Name = "test",
                Fields = new[] { new Field("id", DataType.String) { IsKey = true } }
            };
    }
}
```

## <a name="design-pattern"></a>Schema progettuale

Uno schema progettuale per l'implementazione della concorrenza ottimistica deve includere un ciclo che recupera il controllo della condizione di accesso, un test per la condizione di accesso e, facoltativamente, una risorsa aggiornata prima di provare ad applicare di nuovo le modifiche. 

Questo frammento di codice illustra l'aggiunta di synonymMap a un indice già esistente. Questo codice è tratto da [Esercitazione sui sinonimi (anteprima) in C# per Ricerca di Azure](https://docs.microsoft.com/azure/search/search-synonyms-tutorial-sdk). 

Il frammento ottiene l'indice "hotels", controlla la versione dell'oggetto in un'operazione di aggiornamento, genera un'eccezione se la condizione ha esito negativo e quindi esegue un nuovo tentativo di operazione (fino a tre volte), iniziando con il recupero dell'indice dal server per ottenere la versione più recente.

        private static void EnableSynonymsInHotelsIndexSafely(SearchServiceClient serviceClient)
        {
            int MaxNumTries = 3;

            for (int i = 0; i < MaxNumTries; ++i)
            {
                try
                {
                    Index index = serviceClient.Indexes.Get("hotels");
                    index = AddSynonymMapsToFields(index);

                    // The IfNotChanged condition ensures that the index is updated only if the ETags match.
                    serviceClient.Indexes.CreateOrUpdate(index, accessCondition: AccessCondition.IfNotChanged(index));

                    Console.WriteLine("Updated the index successfully.\n");
                    break;
                }
                catch (CloudException e) when (e.IsAccessConditionFailed())
                {
                    Console.WriteLine($"Index update failed : {e.Message}. Attempt({i}/{MaxNumTries}).\n");
                }
            }
        }
        
        private static Index AddSynonymMapsToFields(Index index)
        {
            index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
            index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };
            return index;
        }


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come aggiornare in modo sicuro un indice esistente, vedere l'[esempio in C# per i sinonimi](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

Provare a modificare uno dei due esempi seguenti per includere ETag o oggetti AccessCondition.

+ [Esempio di API REST in GitHub](https://github.com/Azure-Samples/search-rest-api-getting-started) 
+ [Esempio di .NET SDK in GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). Questa soluzione include il progetto "DotNetEtagsExplainer" contenente il codice presentato in questo articolo.

## <a name="see-also"></a>Vedere anche

  [Common HTTP request and response headers](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)   (Intestazioni della risposta e della richiesta HTTP comuni)  
  [HTTP status codes](https://docs.microsoft.com/rest/api/searchservice/http-status-codes) (Codici di stato HTTP) [Index operations (REST API)](https://docs.microsoft.com/\rest/api/searchservice/index-operations) (Operazioni sugli indici - API REST)