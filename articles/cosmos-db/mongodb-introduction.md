---
title: 'Introduzione ad Azure Cosmos DB: API per MongoDB | Microsoft Docs'
description: Informazioni su come usare Azure Cosmos DB per archiviare volumi elevati di documenti JSON ed eseguire query su di essi con bassa latenza usando le diffuse API MongoDB OSS.
keywords: "che cos'è MongoDB"
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: anhoh
ms.openlocfilehash: ffca8f4518361e8c5447d7bb7ed6022eb0e96a4a
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="introduction-to-azure-cosmos-db-api-for-mongodb"></a>Introduzione ad Azure Cosmos DB: API per MongoDB

[Azure Cosmos DB](../cosmos-db/introduction.md) è il servizio di database multimodello distribuito a livello globale di Microsoft per applicazioni cruciali. Azure Cosmos DB offre [distribuzione globale predefinita](distribute-data-globally.md), [scalabilità elastica in termini di archiviazione e velocità effettiva](partition-data.md) ovunque nel mondo, latenze pari a singole unità di millisecondi al 99° percentile e disponibilità elevata garantita, il tutto supportato da [contratti di servizio leader del settore](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indicizza automaticamente i dati](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) senza che sia necessario gestire manualmente indici e schemi. Si tratta di un database multimodello che supporta modelli di dati di documenti, coppie chiave/valore, grafi e colonne. 

![Azure Cosmos DB: API MongoDB](./media/mongodb-introduction/cosmosdb-mongodb.png) 

È possibile usare i database Azure Cosmos DB come archivio dati per le app scritte per [MongoDB](https://docs.mongodb.com/manual/introduction/). Di conseguenza, usando i [driver](https://docs.mongodb.org/ecosystem/drivers/) esistenti l'applicazione scritta per MongoDB ora può comunicare con Azure Cosmos DB e usare i database Azure Cosmos DB invece dei database MongoDB. In molti casi, per passare da MongoDB a Azure Cosmos DB e viceversa, è sufficiente modificare una stringa di connessione. Questa funzionalità permette di sviluppare ed eseguire con facilità applicazioni di database MongoDB nel cloud di Azure sfruttando la distribuzione globale e i [contratti di servizio completi leader del settore](https://azure.microsoft.com/support/legal/sla/cosmos-db) di Azure Cosmos DB, continuando al tempo stesso a usare competenze e strumenti familiari per MongoDB.

**Compatibilità con MongoDB**: è possibile sfruttare le competenze, il codice dell'applicazione e degli strumenti MongoDB esistenti poiché Azure Cosmos DB implementa il protocollo di trasmissione MongoDB 3.4 (versione 5) e supporta la [pipeline di aggregazione MongoDB](mongodb-feature-support.md#aggregation-pipeline). È possibile sviluppare applicazioni con MongoDB e distribuirle in produzione tramite il servizio Azure Cosmos DB distribuito a livello globale e completamente gestito.

## <a name="what-is-the-benefit-of-using-azure-cosmos-db-for-mongodb-applications"></a>Vantaggi legati all'uso di Azure Cosmos DB per applicazioni MongoDB

**Velocità effettiva e archiviazione con scalabilità elastica:** è possibile aumentare o ridurre le prestazioni del database MongoDB in base alle esigenze dell'applicazione. I dati sono archiviati in unità SSD (Solid State Drive) per garantire livelli di latenza bassi e prevedibili. Azure Cosmos DB supporta raccolte di MongoDB che possono raggiungere dimensioni di archiviazione e velocità effettiva con provisioning quasi illimitate. Azure Cosmos DB offre una semplice scalabilità elastica e prestazioni prevedibili in base alla crescita dell'applicazione. 

**Replica multiarea:** Azure Cosmos DB replica in modo trasparente i dati in tutte le aree associate al proprio account MongoDB, consentendo lo sviluppo di applicazioni che richiedono l'accesso globale ai dati, con il necessario compromesso tra coerenza, disponibilità e prestazioni, tutto con le garanzie corrispondenti. Azure Cosmos DB fornisce il failover trasparente a livello di area con le API multihosting e la possibilità di aumentare o ridurre la velocità effettiva e le risorse di archiviazione in tutto il mondo. Per altre informazioni, vedere [Distribuire i dati a livello globale](distribute-data-globally.md).

**Nessuna gestione server**: non è necessario gestire e ridimensionare i database MongoDB. Azure Cosmos DB è un servizio completamente gestito che elimina la necessità di gestire manualmente infrastrutture o macchine virtuali. Cosmos DB è disponibile in più di 30 [aree di Azure](https://azure.microsoft.com/regions/services/).

**Livelli di coerenza regolabili:** Azure Cosmos DB implementa attualmente la versione 3.4 di MongoDB, che include due impostazioni di coerenza: assoluta e finale. Azure Cosmos DB è un'applicazione multi-API e, pertanto, le impostazioni di coerenza sono applicabili a livello di account e l'applicazione della coerenza è controllata da ogni API. Fino a MongoDB 3.6 non esisteva il concetto di coerenza della sessione e, se si imposta un account di API MongoDB per l'uso della coerenza di sessione, quando si usano le API di MongoDB viene eseguito il downgrade della coerenza a "finale". Se è necessaria una garanzia di tipo Read Your Own Write (RYOW) per un account di API MongoDB, il livello di coerenza predefinito per l'account deve essere impostato su "assoluta" o "decadimento ristretto". Per altre informazioni, vedere [Uso dei livelli di coerenza per ottimizzare la disponibilità e le prestazioni](consistency-levels.md).

| Livello di coerenza predefinito di Azure Cosmos DB |   API di Mongo (3.4) |
|---|---|
|Finale| Finale |
|Prefisso coerente| Finale con ordine coerente |
|sessione| Finale con ordine coerente |
|Decadimento ristretto| Assoluta |
| Assoluta | Assoluta |

**Indicizzazione automatica:** per impostazione predefinita, Azure Cosmos DB indicizza automaticamente tutte le proprietà all'interno dei documenti nel database MongoDB e non prevede né richiede schemi o la creazione di indici secondari. Inoltre, la funzionalità dell'indice univoco abilita un vincolo di univocità nei campi dei documenti che vengono già indicizzati automaticamente in Azure Cosmos DB.

**Classe enterprise**: Azure Cosmos DB supporta più repliche locali per garantire il 99,99% della disponibilità e la protezione dati nonostante gli errori locali e a livello di area. Azure Cosmos DB ha [certificazioni di conformità](https://www.microsoft.com/trustcenter) e funzionalità di sicurezza di classe enterprise. 

Per altre informazioni, vedere questo video con il Senior Program Manager di Azure Cosmos DB, Aleksey Savateyev.

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T136/player]
> 

## <a name="how-to-get-started"></a>Attività iniziali

Seguire le guide rapide di MongoDB per creare un account Azure Cosmos DB ed eseguire la migrazione dell'applicazione Mongo DB esistente per usare Azure Cosmos DB o compilarne uno nuovo:

* [Eseguire la migrazione di un'app Web MongoDB Node.js esistente](create-mongodb-nodejs.md).
* [Creare un'app Web per le API MongoDB con .NET e il portale di Azure](create-mongodb-dotnet.md)
* [Creare un'app console API MongoDB con Java e il portale di Azure](create-mongodb-java.md)

## <a name="next-steps"></a>Passaggi successivi

Le informazioni sull'API di MongoDB per Azure Cosmos DB sono integrate nella documentazione generale di Azure Cosmos DB. Di seguito sono disponibili alcune informazioni utili per iniziare:

* Per istruzioni su come ottenere le informazioni della stringa di connessione dell'account, seguire l'esercitazione [Connettersi a un account MongoDB](connect-mongodb-account.md).
* Per informazioni su come creare una connessione tra il database Azure Cosmos DB e l'app MongoDB in Studio 3T, vedere l'esercitazione [Usare Studio 3T (MongoChef) con Azure Cosmos DB](mongodb-mongochef.md).
* Per importare i dati in un'API per il database MongoDB, vedere l'esercitazione [Eseguire la migrazione di dati in Azure Cosmos DB con il supporto del protocollo per MongoDB](mongodb-migrate.md).
* Connettersi a un account dell'API per MongoDB usando [Robomongo](mongodb-robomongo.md).
* Informazioni sul numero di unità richiesta usate dalle operazioni con il [comando GetLastRequestStatistics e le metriche del portale di Azure](request-units.md#GetLastRequestStatistics).
* Informazioni su come [configurare le preferenze di lettura per le app distribuite globalmente](../cosmos-db/tutorial-global-distribution-mongodb.md).
