---
title: Passaggi di post-migrazione ottimizzazione quando si usa l'API di Azure Cosmos DB per MongoDB
description: Questo documento fornisce le tecniche di ottimizzazione di post-migrazione da MongoDB all'APi di Azure Cosmos DB per Mongodb.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: roaror
ms.openlocfilehash: c0c761fef481a1fdaa027f1329e9a4e72d62985a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331205"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Passaggi di post-migrazione ottimizzazione quando si usa l'API di Azure Cosmos DB per MongoDB 

Dopo aver eseguito la migrazione dei dati archiviati nel database MongoDB nell'API per MongoDB di Azure Cosmos DB, è possibile connettersi ad Azure Cosmos DB e gestire i dati. Questa guida illustra la procedura che è necessario considerare dopo la migrazione. Vedere le [eseguire la migrazione di MongoDB all'API di Azure Cosmos DB per MongoDB esercitazione](../dms/tutorial-mongodb-cosmos-db.md) per i passaggi della migrazione.

In questa guida si apprenderà come:
- [Connettere l'applicazione](#connect-account)
- [Ottimizzare i criteri di indicizzazione](#indexing)
- [Configurare la distribuzione globale per l'API di Azure Cosmos DB per MongoDB](#distribute-data)
- [Impostare il livello di coerenza](#consistency)

> [!NOTE]
> Il passaggio di post-migrazione obbligatorio solo il livello di applicazione sta cambiando la stringa di connessione nell'applicazione in modo che punti al nuovo account Azure Cosmos DB. Tutti gli altri passaggi di migrazione sono consigliati le ottimizzazioni.
>

## <a id="connect-account"></a>Connettere l'applicazione 

1. In una nuova finestra accedere il [portale di Azure](https://www.portal.azure.com/)
2. Dal [portale di Azure](https://www.portal.azure.com/), nel riquadro di sinistra aprire le **tutte le risorse** menu e trovare l'account Azure Cosmos DB a cui è stata eseguita la migrazione dei dati.
3. Aprire il **stringa di connessione** pannello. Il riquadro destro contiene tutte le informazioni necessarie per connettersi correttamente all'account.
4. Usare le informazioni di connessione nella configurazione dell'applicazione (o altre posizioni rilevanti) in modo da riflettere l'API di Azure Cosmos DB per la connessione di MongoDB in app. 
![Connection-String](./media/mongodb-post-migration/connection-string.png)

Per altre informazioni, vedere la [connettere un'applicazione MongoDB ad Azure Cosmos DB](connect-mongodb-account.md) pagina.

## <a id="indexing"></a>Ottimizzare i criteri di indicizzazione

Tutti i campi dati vengono indicizzati automaticamente, per impostazione predefinita, durante la migrazione dei dati in Azure Cosmos DB. In molti casi, questa impostazione predefinita dei criteri di indicizzazione è accettabile. In generale, la rimozione di indici consente di ottimizzare le richieste di scrittura e con il valore predefinito (ad esempio, l'indicizzazione automatica) del criterio di indicizzazione ottimizza le richieste di lettura.

Per altre informazioni sull'indicizzazione, vedere [Data indicizzazione nell'API di Azure Cosmos DB per MongoDB](mongodb-indexing.md) , nonché [indicizzazione in Azure Cosmos DB](index-overview.md) articoli.

## <a id="distribute-data"></a>Distribuzione a livello globale dei dati

Azure Cosmos DB è disponibile in tutte le [aree di Azure](https://azure.microsoft.com/regions/#services) in tutto il mondo. Dopo aver selezionato il livello di coerenza predefinito per l'account Azure Cosmos DB, è possibile associare uno o più aree di Azure (a seconda delle esigenze di distribuzione globale). Per la disponibilità elevata e continuità aziendale, è sempre consigliabile eseguire almeno 2 aree. È possibile esaminare i suggerimenti per la [ottimizzazione dei costi delle distribuzioni in più aree in Azure Cosmos DB](optimize-cost-regions.md).

Per distribuire a livello globale dei dati, vedere [distribuire dati a livello globale in Azure Cosmos DB API per MongoDB](tutorial-global-distribution-mongodb.md). 

## <a id="consistency"></a>Impostare il livello di coerenza
Azure Cosmos DB offre 5 ben definiti [livelli di coerenza](consistency-levels.md). Per informazioni sul mapping tra i livelli di coerenza di MongoDB e Azure Cosmos DB, leggere [livelli di coerenza e le API di Azure Cosmos DB](consistency-levels-across-apis.md). Il livello di coerenza predefinito è il livello di coerenza di sessione. Modifica del livello di coerenza è facoltativa ed è possibile ottimizzare per l'app. Per modificare il livello di coerenza tramite il portale di Azure:

1. Andare alla **coerenza predefinita** pannello in impostazioni.
2. Selezionare il [a livello di coerenza](consistency-levels.md)

La maggior parte degli utenti lasciano il livello di coerenza al livello di coerenza di sessione predefinito. Tuttavia, esistono [compromessi tra prestazioni e disponibilità per i diversi livelli di coerenza](consistency-levels-tradeoffs.md). 

## <a name="next-steps"></a>Passaggi successivi

* [Connettere un'applicazione MongoDB ad Azure Cosmos DB](connect-mongodb-account.md)
* [Connettersi all'account Azure Cosmos DB mediante Studio 3T](mongodb-mongochef.md)
* [Come distribuire a livello globale legge tramite API di Azure Cosmos DB per MongoDB](mongodb-readpreference.md)
* [Scadenza dei dati con l'API di Azure Cosmos DB per MongoDB](mongodb-time-to-live.md)
* [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md)
* [Indicizzazione in Azure Cosmos DB](index-overview.md)
* [Unità richiesta in Azure Cosmos DB](request-units.md)





