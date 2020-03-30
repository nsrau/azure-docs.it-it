---
title: Passaggi di ottimizzazione post-migrazione con l'API di Azure Cosmos DB per MongoDB
description: Questo documento fornisce le tecniche di ottimizzazione post-migrazione da MongoDB all'APi di Azure Cosmos DB per Mongo DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: lbosq
ms.openlocfilehash: ce33651aae64d0a90264dde6da64b4044c6ce132
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063600"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Passaggi di ottimizzazione post-migrazione quando si usa l'API di Azure Cosmos DB per MongoDB

Dopo aver eseguito la migrazione dei dati archiviati nel database MongoDB all'API di Azure Cosmos DB per MongoDB, è possibile connettersi a Azure Cosmos DB e gestire i dati. In questa guida vengono descritti i passaggi da considerare dopo la migrazione. Per la procedura di migrazione, vedere l'esercitazione Eseguire la migrazione di [MongoDB all'API di Azure Cosmos DB per MongoDB.See the Migrate MongoDB to Azure Cosmos DB's API for MongoDB tutorial](../dms/tutorial-mongodb-cosmos-db.md) for the migration steps.

In questa guida si apprenderà come:

- [Connettere l'applicazione](#connect-your-application)
- [Ottimizzare i criteri di indicizzazioneOptimize the indexing policy](#optimize-the-indexing-policy)
- [Configurare la distribuzione globale per l'API di Azure Cosmos DB per MongoDBConfigure global distribution for Azure Cosmos DB's API for MongoDB](#globally-distribute-your-data)
- [Impostare il livello di coerenza](#set-consistency-level)

> [!NOTE]
> L'unico passaggio obbligatorio dopo la migrazione a livello di applicazione è la modifica della stringa di connessione nell'applicazione in modo che punti al nuovo account di Azure Cosmos DB. Tutti gli altri passaggi di migrazione sono ottimizzazioni consigliate.
>

## <a name="connect-your-application"></a>Connettere l'applicazione

1. In una nuova finestra accedere al portale di [AzureIn](https://www.portal.azure.com/) a new window sign into the Azure portal
2. Dal [portale di Azure](https://www.portal.azure.com/), nel riquadro sinistro aprire il menu **Tutte le risorse** e individuare l'account azure Cosmos DB in cui è stata eseguita la migrazione dei dati.
3. Aprire il pannello **Stringa di connessione.** Il riquadro destro contiene tutte le informazioni necessarie per connettersi correttamente all'account.
4. Usare le informazioni di connessione nella configurazione dell'applicazione (o in altre posizioni rilevanti) per riflettere l'API di Azure Cosmos DB per la connessione MongoDB nell'app.
![Stringa di connessione](./media/mongodb-post-migration/connection-string.png)

Per altri dettagli, vedere la pagina [Connettere un'applicazione MongoDB a Azure Cosmos DB.](connect-mongodb-account.md)

## <a name="optimize-the-indexing-policy"></a>Ottimizzare i criteri di indicizzazioneOptimize the indexing policy

Tutti i campi dati vengono indicizzati automaticamente, per impostazione predefinita, durante la migrazione dei dati in Azure Cosmos DB. In molti casi, questo criterio di indicizzazione predefinito è accettabile. In generale, la rimozione degli indici ottimizza le richieste di scrittura e la presenza dei criteri di indicizzazione predefiniti, ovvero l'indicizzazione automatica, ottimizza le richieste di lettura.

Per altre informazioni sull'indicizzazione, vedere L'indicizzazione dei dati nell'API di Azure Cosmos DB per MongoDB e gli articoli [sull'indicizzazione in database Cosmos](index-overview.md) di Azure.For more information on [indexing, see Data indexing in Azure Cosmos DB's API for MongoDB](mongodb-indexing.md) as well as the Indexing in Azure Cosmos DB articles.

## <a name="globally-distribute-your-data"></a>Distribuisci globalmente i tuoi dati

Azure Cosmos DB è disponibile in tutte le [aree di Azure](https://azure.microsoft.com/regions/#services) a livello mondiale. Dopo aver selezionato il livello di coerenza predefinito per l'account di database Cosmos di Azure, è possibile associare una o più aree di Azure (a seconda delle esigenze di distribuzione globale). Per la disponibilità elevata e la continuità aziendale, è sempre consigliabile eseguire in almeno 2 aree. È possibile esaminare i suggerimenti per [l'ottimizzazione dei costi delle distribuzioni in più aree in Azure Cosmos DB](optimize-cost-regions.md).

Per distribuire globalmente i dati, vedere Distribuire dati a [livello globale nell'API di Azure Cosmos DB per MongoDB.](tutorial-global-distribution-mongodb.md)

## <a name="set-consistency-level"></a>Impostare il livello di coerenza

Azure Cosmos DB offre 5 livelli di [coerenza](consistency-levels.md)ben definiti. Per informazioni sul mapping tra i livelli di coerenza di MongoDB e Azure Cosmos DB, leggere Livelli di [coerenza e API db Cosmos](consistency-levels-across-apis.md)di Azure . Il livello di coerenza predefinito è il livello di coerenza della sessione. La modifica del livello di coerenza è facoltativa ed è possibile ottimizzarla per l'app. Per modificare il livello di coerenza tramite il portale di Azure:To change consistency level using Azure portal:

1. Passare al pannello Coerenza predefinita in Impostazioni.Go to the **Default Consistency** blade under Settings.
2. Selezionare il [livello di coerenza](consistency-levels.md)

La maggior parte degli utenti lascia il livello di coerenza all'impostazione di coerenza della sessione predefinita. Tuttavia, esistono compromessi di disponibilità e prestazioni per vari livelli di [coerenza.](consistency-levels-tradeoffs.md)

## <a name="next-steps"></a>Passaggi successivi

* [Connettere un'applicazione MongoDB ad Azure Cosmos DB](connect-mongodb-account.md)
* [Connettersi all'account di Azure Cosmos DB usando Studio 3TConnect to Azure Cosmos DB account using Studio 3T](mongodb-mongochef.md)
* [Come distribuire a livello globale le letture tramite l'API di Azure Cosmos DB per MongoDB](mongodb-readpreference.md)
* [Impostare la scadenza dei dati con l'API di Azure Cosmos DB per MongoDB](mongodb-time-to-live.md)
* [Livelli di coerenza in Azure Cosmos DBConsistency Levels in Azure Cosmos DB](consistency-levels.md)
* [Indicizzazione in Azure Cosmos DB](index-overview.md)
* [Unità di richiesta in Azure Cosmos DBRequest Units in Azure Cosmos DB](request-units.md)