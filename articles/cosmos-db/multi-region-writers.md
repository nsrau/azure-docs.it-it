---
title: Architetture di database multimaster con Azure Cosmos DB | Microsoft Docs
description: "Informazioni su come progettare architetture di applicazioni con operazioni di lettura e scrittura locali in più aree geografiche con Azure Cosmos DB."
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 706ced74-ea67-45dd-a7de-666c3c893687
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cf1482ae7b1070023703f5dbe861d151f5d64fd8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="multi-master-globally-replicated-database-architectures-with-azure-cosmos-db"></a>Architetture di database multimaster replicate a livello globale con Cosmos DB
Cosmos DB di Azure supporta la [replica globale](distribute-data-globally.md) chiavi in mano, che consente di distribuire i dati in più aree con accesso a bassa latenza in qualsiasi punto del carico di lavoro. Questo modello viene usato in genere per carichi di lavoro di pubblicazione/consumer in cui è presente un writer in una singola area geografica e con lettori distribuiti a livello globale in altre aree (lettura). 

È anche possibile usare il supporto per la replica globale di Azure Cosmos DB per creare applicazioni in cui i writer e i lettori sono distribuiti in modo globale. Questo documento illustra un modello che consente di ottenere l'accesso in scrittura e lettura locale per i writer distribuiti mediante Azure Cosmos DB.

## <a id="ExampleScenario"></a>Pubblicazione del contenuto: scenario di esempio
È possibile esaminare uno scenario concreto per illustrare il modo in cui possono essere usati i modelli di lettura e scrittura in più aree e multimaster distribuiti a livello globale con Cosmos DB. Esaminare una piattaforma di pubblicazione di contenuti creata in Cosmos DB. Ecco alcuni requisiti che devono essere rispettati da questa piattaforma per offrire un'esperienza utente ottimale per server di pubblicazione e consumer.

* Gli autori e i sottoscrittori sono distribuiti in tutto il mondo. 
* Gli autori devono pubblicare (scrivere) articoli nella rispettiva area locale (più vicina).
* Gli autori hanno lettori/sottoscrittori degli articoli distribuiti in tutto il mondo. 
* I sottoscrittori ricevono una notifica quando vengono pubblicati nuovi articoli.
* I sottoscrittori devono essere in grado di leggere articoli dalla rispettiva area locale. Devono anche potere aggiungere revisioni per questi articoli. 
* Qualunque utente, incluso l'autore degli articoli, deve potere visualizzare tutte le revisioni allegate agli articoli da un'area locale. 

Supponendo che siano presenti milioni di consumer e server di pubblicazione con miliardi di articoli, sarà presto necessario affrontare i problemi relativi alla scalabilità, oltre a garantire l'accesso locale. Analogamente alla maggior parte dei problemi di scalabilità, la soluzione è basata su una strategia di partizionamento ottimale. Ecco come modellare gli articoli, le revisioni e le notifiche come documenti, configurare gli account Cosmos DB e implementare un livello di accesso ai dati. 

Per altre informazioni sul partizionamento e sulle chiavi di partizione, vedere [Partizionamento e scalabilità in Azure Cosmos DB](partition-data.md).

## <a id="ModelingNotifications"></a>Modellazione delle notifiche
Le notifiche sono feed di dati specifici per un utente. I modelli di accesso per i documenti di notifica sono quindi sempre nel contesto di un singolo utente. È ad esempio possibile "inserire una notifica per un utente" o "recuperare tutte le notifiche per un utente specifico". La scelta ottimale di chiave di partizionamento per questo tipo sarebbe quindi `UserId`.

    class Notification 
    { 
        // Unique ID for Notification. 
        public string Id { get; set; }

        // The user Id for which notification is addressed to. 
        public string UserId { get; set; }

        // The partition Key for the resource. 
        public string PartitionKey 
        { 
            get 
            { 
                return this.UserId; 
            }
        }

        // Subscription for which this notification is raised. 
        public string SubscriptionFilter { get; set; }

        // Subject of the notification. 
        public string ArticleId { get; set; } 
    }

## <a id="ModelingSubscriptions"></a>Modellazione delle sottoscrizioni
Le sottoscrizioni possono essere create per diversi criteri, ad esempio per una categoria di articoli rilevanti o un server di pubblicazione specifico. `SubscriptionFilter` è quindi una scelta appropriata per la chiave di partizione.

    class Subscriptions 
    { 
        // Unique ID for Subscription 
        public string Id { get; set; }

        // Subscription source. Could be Author | Category etc. 
        public string SubscriptionFilter { get; set; }

        // subscribing User. 
        public string UserId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.SubscriptionFilter; 
            } 
        } 
    }

## <a id="ModelingArticles"></a>Modellazione degli articoli
Dopo l'identificazione di un articolo tramite le notifiche, le query successive sono in genere basate su `Article.Id`. Scegliendo `Article.Id` come chiave di partizione si ottiene quindi la distribuzione ottimale per l'archiviazione di articoli all'interno di una raccolta di Cosmos DB. 

    class Article 
    { 
        // Unique ID for Article 
        public string Id { get; set; }
        
        public string PartitionKey 
        { 
            get 
            { 
                return this.Id; 
            } 
        }
        
        // Author of the article
        public string Author { get; set; }

        // Category/genre of the article
        public string Category { get; set; }

        // Tags associated with the article
        public string[] Tags { get; set; }

        // Title of the article
        public string Title { get; set; }
        
        //... 
    }

## <a id="ModelingReviews"></a>Modellazione delle revisioni
Analogamente agli articoli, le revisioni vengono in genere scritte e lette nel contesto dell'articolo. Scegliendo `ArticleId` come chiave di partizione si ottiene quindi la distribuzione ottimale e l'accesso più efficiente per le revisioni associate all'articolo. 

    class Review 
    { 
        // Unique ID for Review 
        public string Id { get; set; }

        // Article Id of the review 
        public string ArticleId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.ArticleId; 
            } 
        }
        
        //Reviewer Id 
        public string UserId { get; set; }
        public string ReviewText { get; set; }
        
        public int Rating { get; set; } }
    }

## <a id="DataAccessMethods"></a>Metodi per i livelli di accesso ai dati
È possibile esaminare i metodi di accesso ai dati principali da implementare. Ecco l'elenco di metodi necessari per `ContentPublishDatabase`:

    class ContentPublishDatabase 
    { 
        public async Task CreateSubscriptionAsync(string userId, string category);
    
        public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId);
    
        public async Task<Article> ReadArticleAsync(string articleId);
    
        public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating);
    
        public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId); 
    }

## <a id="Architecture"></a>Configurazione dell'account Azure Cosmos DB
Per assicurare operazioni di lettura e scrittura locali, è necessario partizionare i dati non solo nella chiave di partizione, ma anche in base al modello di accesso geografico nelle aree. Il modello si basa sulla presenza di un account di database di Azure Cosmos DB con replica geografica per ogni area. Ad esempio, con due aree si può ottenere uno scenario come il seguente per le operazioni di scrittura in più aree:

| Nome account | Area di scrittura | Area di lettura |
| --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |

Il diagramma seguente mostra il modo in cui le operazioni di lettura e scrittura vengono eseguite in un'applicazione tipica con questa configurazione:

![Architettura multimaster di Azure Cosmos DB](./media/multi-region-writers/multi-master.png)

Ecco un frammento di codice che illustra come inizializzare i client in un livello di accesso ai dati in esecuzione nell'area `West US`.
    
    ConnectionPolicy writeClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    writeClientPolicy.PreferredLocations.Add(LocationNames.WestUS);
    writeClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

    DocumentClient writeClient = new DocumentClient(
        new Uri("https://contentpubdatabase-usa.documents.azure.com"), 
        writeRegionAuthKey,
        writeClientPolicy);

    ConnectionPolicy readClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    readClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);
    readClientPolicy.PreferredLocations.Add(LocationNames.WestUS);

    DocumentClient readClient = new DocumentClient(
        new Uri("https://contentpubdatabase-europe.documents.azure.com"),
        readRegionAuthKey,
        readClientPolicy);

Con la configurazione precedente, il livello di accesso ai dati può inoltrare tutte le operazioni di scrittura all'account locale in base alla posizione in cui è distribuito. Le operazioni di lettura vengono eseguite leggendo da entrambi gli account per ottenere la visualizzazione globale dei dati. Questo approccio può essere esteso a tutte le aree necessarie. Ad esempio, ecco una configurazione con tre aree geografiche:

| Nome account | Area di scrittura | Area di lettura 1 | Area di lettura 2 |
| --- | --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |`Southeast Asia` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |`Southeast Asia` |
| `contentpubdatabase-asia.documents.azure.com` | `Southeast Asia` |`North Europe` |`West US` |

## <a id="DataAccessImplementation"></a>Implementazione del livello di accesso ai dati
È ora possibile esaminare l'implementazione del livello di accesso ai dati per un'applicazione con due aree scrivibili. Il livello di accesso ai dati deve implementare i passaggi seguenti:

* Creare più istanze di `DocumentClient` per ogni account. Con due aree, ogni istanza del livello di accesso ai dati ha un valore `writeClient` e un valore `readClient`. 
* In base all'area di distribuzione dell'applicazione, configurare gli endpoint per `writeclient` e `readClient`. Ad esempio, il livello di accesso ai dati distribuito in `West US` usa `contentpubdatabase-usa.documents.azure.com` per l'esecuzione di operazioni di scrittura. Il livello di accesso ai dati distribuito in `NorthEurope` usa `contentpubdatabase-europ.documents.azure.com` per l'esecuzione di operazioni di scrittura.

Con la configurazione precedente, i metodi di accesso ai dati possono essere implementati. Le operazioni di scrittura inoltrano la scrittura al `writeClient` corrispondente.

    public async Task CreateSubscriptionAsync(string userId, string category)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Subscriptions
        {
            UserId = userId,
            SubscriptionFilter = category
        });
    }

    public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Review
        {
            UserId = userId,
            ArticleId = articleId,
            ReviewText = reviewText,
            Rating = rating
        });
    }

Per le notifiche e le revisioni di lettura, è necessario leggere da entrambe le aree e unire i risultati, come illustrato nel frammento di codice seguente:

    public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId)
    {
        IDocumentQuery<Notification> writeAccountNotification = (
            from notification in this.writeClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();
        
        IDocumentQuery<Notification> readAccountNotification = (
            from notification in this.readClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();

        List<Notification> notifications = new List<Notification>();

        while (writeAccountNotification.HasMoreResults || readAccountNotification.HasMoreResults)
        {
            IList<Task<FeedResponse<Notification>>> results = new List<Task<FeedResponse<Notification>>>();

            if (writeAccountNotification.HasMoreResults)
            {
                results.Add(writeAccountNotification.ExecuteNextAsync<Notification>());
            }

            if (readAccountNotification.HasMoreResults)
            {
                results.Add(readAccountNotification.ExecuteNextAsync<Notification>());
            }

            IList<FeedResponse<Notification>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Notification> feed in notificationFeedResult)
            {
                notifications.AddRange(feed);
            }
        }
        return notifications;
    }

    public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId)
    {
        IDocumentQuery<Review> writeAccountReviews = (
            from review in this.writeClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();
        
        IDocumentQuery<Review> readAccountReviews = (
            from review in this.readClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();

        List<Review> reviews = new List<Review>();
        
        while (writeAccountReviews.HasMoreResults || readAccountReviews.HasMoreResults)
        {
            IList<Task<FeedResponse<Review>>> results = new List<Task<FeedResponse<Review>>>();

            if (writeAccountReviews.HasMoreResults)
            {
                results.Add(writeAccountReviews.ExecuteNextAsync<Review>());
            }

            if (readAccountReviews.HasMoreResults)
            {
                results.Add(readAccountReviews.ExecuteNextAsync<Review>());
            }

            IList<FeedResponse<Review>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Review> feed in notificationFeedResult)
            {
                reviews.AddRange(feed);
            }
        }

        return reviews;
    }

Se si sceglie una chiave di partizione ottimale e un partizionamento basato su account statici, è possibile ottenere operazioni di scrittura e lettura locali in più aree usando Azure Cosmos DB.

## <a id="NextSteps"></a>Passaggi successivi
In questo articolo è stato illustrato come è possibile usare modelli di lettura e scrittura in più aree con distribuzione globale con Cosmos DB usando la pubblicazione di contenuti come scenario di esempio.

* Altre informazioni sul modo in cui Cosmos DB supporta la [distribuzione globale](distribute-data-globally.md)
* Altre informazioni sui [failover automatici o manuali in Azure Cosmos DB](regional-failover.md)
* Altre informazioni sulla [coerenza globale con Cosmos DB di Azure](consistency-levels.md)
* Sviluppare in più aree usando [Azure Cosmos DB: API di DocumentDB](tutorial-global-distribution-documentdb.md)
* Sviluppare in più aree usando [Azure Cosmos DB: API di MongoDB](tutorial-global-distribution-MongoDB.md)
* Sviluppare in più aree usando [Azure Cosmos DB: API della tabella](tutorial-global-distribution-table.md)
