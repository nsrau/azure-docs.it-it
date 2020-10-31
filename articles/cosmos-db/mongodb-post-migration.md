---
title: Passaggi di ottimizzazione post-migrazione con l'API di Azure Cosmos DB per MongoDB
description: Questo documento fornisce le tecniche di ottimizzazione post-migrazione da MongoDB all'APi di Azure Cosmos DB per il database Mongo.
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/20/2020
ms.author: jasonh
ms.openlocfilehash: 3b0c291f942cd970f7996f0b9fa599ee8cabfdab
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096445"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Passaggi di ottimizzazione post-migrazione quando si usa l'API di Azure Cosmos DB per MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Dopo aver eseguito la migrazione dei dati archiviati nel database MongoDB all'API per MongoDB di Azure Cosmos DB, è possibile connettersi ad Azure Cosmos DB e gestire i dati. Questa guida contiene la procedura da seguire dopo la migrazione. Per la procedura di migrazione, vedere l' [esercitazione eseguire la migrazione di MongoDB all'API Azure Cosmos DB per MongoDB](../dms/tutorial-mongodb-cosmos-db.md) .

In questa guida si apprenderà come:

- [Connettere l'applicazione](#connect-your-application)
- [Ottimizzare i criteri di indicizzazione](#optimize-the-indexing-policy)
- [Configurare la distribuzione globale per l'API Azure Cosmos DB per MongoDB](#globally-distribute-your-data)
- [Imposta livello di coerenza](#set-consistency-level)

> [!NOTE]
> L'unico passaggio di post-migrazione obbligatorio a livello di applicazione sta cambiando la stringa di connessione nell'applicazione in modo che punti al nuovo account Azure Cosmos DB. Tutti gli altri passaggi della migrazione sono le ottimizzazioni consigliate.
>

## <a name="connect-your-application"></a>Connettere l'applicazione

1. In una nuova finestra accedere al [portale di Azure](https://www.portal.azure.com/)
2. Dal [portale di Azure](https://www.portal.azure.com/), nel riquadro sinistro aprire il menu **tutte le risorse** e individuare l'account Azure Cosmos DB a cui è stata eseguita la migrazione dei dati.
3. Aprire il pannello della **stringa di connessione** . Il riquadro destro contiene tutte le informazioni necessarie per connettersi correttamente all'account.
4. Usare le informazioni di connessione nella configurazione dell'applicazione (o in altri punti rilevanti) per riflettere la connessione dell'API Azure Cosmos DB per MongoDB nell'app.
:::image type="content" source="./media/mongodb-post-migration/connection-string.png" alt-text="Screenshot mostra le impostazioni per una stringa di connessione.":::

Per altri dettagli, vedere la pagina [connettere un'applicazione MongoDB a Azure Cosmos DB](connect-mongodb-account.md) .

## <a name="optimize-the-indexing-policy"></a>Ottimizzare i criteri di indicizzazione

Per impostazione predefinita, tutti i campi dati vengono indicizzati automaticamente durante la migrazione dei dati a Azure Cosmos DB. In molti casi, questi criteri di indicizzazione predefiniti sono accettabili. In generale, la rimozione di indici ottimizza le richieste di scrittura e i criteri di indicizzazione predefiniti (ovvero l'indicizzazione automatica) ottimizza le richieste di lettura.

Per altre informazioni sull'indicizzazione, vedere indicizzazione [dei dati nell'API Azure Cosmos DB per MongoDB](mongodb-indexing.md) e l' [indicizzazione negli](index-overview.md) articoli di Azure Cosmos DB.

## <a name="globally-distribute-your-data"></a>Distribuire i dati a livello globale

Azure Cosmos DB è disponibile in tutte le [aree di Azure](https://azure.microsoft.com/regions/#services) a livello mondiale. Dopo aver selezionato il livello di coerenza predefinito per l'account Azure Cosmos DB, è possibile associare una o più aree di Azure (a seconda delle esigenze di distribuzione globale). Per la disponibilità elevata e la continuità aziendale, è sempre consigliabile eseguire in almeno 2 aree. È possibile esaminare i suggerimenti per l' [ottimizzazione dei costi delle distribuzioni in più aree in Azure Cosmos DB](optimize-cost-regions.md).

Per distribuire i dati a livello globale, vedere [distribuire i dati a livello globale nell'API Azure Cosmos DB per MongoDB](tutorial-global-distribution-mongodb.md).

## <a name="set-consistency-level"></a>Imposta livello di coerenza

Azure Cosmos DB offre cinque [livelli di coerenza](consistency-levels.md)ben definiti. Per informazioni sul mapping tra MongoDB e Azure Cosmos DB livelli di coerenza, vedere [livelli di coerenza di lettura e api Azure Cosmos DB](./consistency-levels.md). Il livello di coerenza predefinito è il livello di coerenza della sessione. Modificare il livello di coerenza è facoltativo ed è possibile ottimizzarlo per l'app. Per modificare il livello di coerenza utilizzando portale di Azure:

1. Passare al pannello **coerenza predefinito** in impostazioni.
2. Selezionare il [livello di coerenza](consistency-levels.md)

La maggior parte degli utenti lascia il proprio livello di coerenza in base all'impostazione predefinita della coerenza di sessione. Esistono tuttavia [compromessi in merito a disponibilità e prestazioni per diversi livelli di coerenza](./consistency-levels.md).

## <a name="next-steps"></a>Passaggi successivi

* [Connettere un'applicazione MongoDB ad Azure Cosmos DB](connect-mongodb-account.md)
* [Connettersi a un account Azure Cosmos DB usando Studio 3T](mongodb-mongochef.md)
* [Come distribuire a livello globale le letture tramite l'API di Azure Cosmos DB per MongoDB](mongodb-readpreference.md)
* [Impostare la scadenza dei dati con l'API di Azure Cosmos DB per MongoDB](mongodb-time-to-live.md)
* [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md)
* [Indicizzazione in Azure Cosmos DB](index-overview.md)
* [Unità richiesta in Azure Cosmos DB](request-units.md)