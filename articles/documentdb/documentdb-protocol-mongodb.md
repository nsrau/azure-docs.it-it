---
title: 'Introduzione ad Azure Cosmos DB: API per MongoDB | Microsoft Docs'
description: Informazioni su come usare Azure Cosmos DB per archiviare volumi elevati di documenti JSON ed eseguire query su di essi con bassa latenza usando le diffuse API MongoDB OSS.
keywords: "che cos&quot;è MongoDB"
services: cosmosdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/05/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7dc474f9f940132dd363740e8288a70ee2f2b970
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-azure-cosmos-db-api-for-mongodb"></a>Introduzione ad Azure Cosmos DB: API per MongoDB

[Azure Cosmos DB](../cosmos-db/introduction.md) è il servizio di database multimodello distribuito a livello globale di Microsoft per applicazioni cruciali. Azure Cosmos DB offre [distribuzione globale predefinita](../documentdb/documentdb-distribute-data-globally.md), [scalabilità elastica in termini di archiviazione e velocità effettiva](../documentdb/documentdb-partition-data.md) ovunque nel mondo, latenze pari a singole unità di millisecondi al 99° percentile, [cinque livelli di coerenza ben definiti](../documentdb/documentdb-consistency-levels.md) e disponibilità elevata garantita, il tutto supportato da [contratti di servizio leader del settore](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/). Azure Cosmos DB [indicizza automaticamente i dati](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) senza che sia necessario gestire manualmente indici e schemi. Si tratta di un database multimodello che supporta modelli di dati di documenti, coppie chiave-valore, grafi e colonne. 

![API di Azure per MongoDB](./media/documentdb-protocol-mongodb/cosmosdb-mongodb.png) 

È possibile usare i database DocumentDB come archivio dati per le app scritte per [MongoDB](https://docs.mongodb.com/manual/introduction/). Di conseguenza, usando i [driver](https://docs.mongodb.org/ecosystem/drivers/) esistenti l'applicazione scritta per MongoDB ora può comunicare con Cosmos DB e usare i database DocumentDB invece dei database MongoDB. In molti casi, per passare da MongoDB a DocumentDB e viceversa, è sufficiente modificare una stringa di connessione. Questa funzionalità permette di sviluppare ed eseguire con facilità applicazioni di database MongoDB nel cloud di Azure sfruttando la distribuzione globale e i [contratti di servizio completi leader del settore](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) di Azure Cosmos DB, continuando al tempo stesso a usare competenze e strumenti familiari per MongoDB.


## <a name="what-is-the-benefit-of-using-azure-cosmos-db-for-mongodb-applications"></a>Vantaggi legati all'uso di Azure Cosmos DB per applicazioni MongoDB

* **Velocità effettiva e archiviazione con scalabilità elastica:** è possibile aumentare o ridurre le prestazioni del database MongoDB in base alle esigenze dell'applicazione. I dati sono archiviati in unità SSD (Solid State Drive) per garantire livelli di latenza bassi e prevedibili. Cosmos DB supporta raccolte di MongoDB che possono raggiungere dimensioni di archiviazione e velocità effettiva con provisioning quasi illimitate. Cosmos DB offre una semplice scalabilità elastica e prestazioni prevedibili in base alla crescita dell'applicazione. 

* **Replica multiarea:** Cosmos DB replica in modo trasparente i dati in tutte le aree associate al proprio account MongoDB, consentendo lo sviluppo di applicazioni che richiedono l'accesso globale ai dati, con il necessario compromesso tra coerenza, disponibilità e prestazioni, tutto con le garanzie corrispondenti. Cosmos DB fornisce il failover trasparente a livello di area con le API multihosting e la possibilità di aumentare o ridurre la velocità effettiva e le risorse di archiviazione in tutto il mondo. Per altre informazioni, vedere [Distribuire i dati a livello globale](documentdb-distribute-data-globally.md).

**Compatibilità con MongoDB**: è possibile sfruttare le competenze, il codice dell'applicazione e degli strumenti MongoDB esistenti. È possibile sviluppare applicazioni con MongoDB e distribuirle in produzione tramite il servizio Cosmos DB distribuito a livello globale e completamente gestito.

**Nessuna gestione server**: non è necessario gestire e ridimensionare i database MongoDB. Cosmos DB è un servizio completamente gestito che elimina la necessità di gestire manualmente infrastrutture o macchine virtuali. Cosmos DB è disponibile in più di 30 [aree di Azure](https://azure.microsoft.com/regions/services/).

* **Livelli di coerenza perfezionabili:** è possibile scegliere tra cinque livelli di coerenza ben definiti per ottenere un compromesso ottimale tra coerenza e prestazioni. Per query e operazioni di lettura, Cosmos DB offre cinque livelli di coerenza distinti, ovvero avanzata, con decadimento ristretto, sessione, prefisso coerente e futura. Questi livelli di coerenza granulari e ben definiti permettono di ottenere compromessi efficaci tra coerenza, disponibilità e latenza. Per altre informazioni, vedere [Uso dei livelli di coerenza per ottimizzare la disponibilità e le prestazioni](documentdb-consistency-levels.md).

* **Indicizzazione automatica:** per impostazione predefinita, Cosmos DB indicizza automaticamente tutte le proprietà all'interno dei documenti nel database MongoDB e non prevede né richiede schemi o la creazione di indici secondari.

**Classe enterprise**: Azure Cosmos DB supporta più repliche locali per garantire il 99,99% della disponibilità e la protezione dati nonostante gli errori locali e a livello di area. Azure Cosmos DB ha [certificazioni di conformità](https://www.microsoft.com/trustcenter) e funzionalità di sicurezza di classe enterprise. 

Per altre informazioni, guardare questo video di Azure Friday con Scott Hanselman e Kirill Gavrylyuk, responsabile principale della progettazione per Azure Cosmos DB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/DocumentDB-Database-as-a-Service-for-MongoDB-Developers/player]
> 

## <a name="how-to-get-started"></a>Attività iniziali

Creare un account Azure Cosmos DB nel [portale di Azure](https://portal.azure.com) e sostituire la stringa di connessione di MongoDB con il nuovo account. 

*Non sono necessarie altre operazioni.*

Per istruzioni più dettagliate, vedere [Creare un account](documentdb-create-mongodb-account.md) e [Connessione all'account](documentdb-connect-mongodb-account.md).

## <a name="next-steps"></a>Passaggi successivi

Le informazioni sull'API di MongoDB per Azure Cosmos DB sono integrate nella documentazione generale di Azure Cosmos DB. Di seguito sono disponibili alcune informazioni utili per iniziare:

* Per istruzioni su come ottenere le informazioni della stringa di connessione dell'account, seguire l'esercitazione [Connettersi a un account MongoDB](documentdb-connect-mongodb-account.md).
* Per informazioni su come creare una connessione tra il database Azure Cosmos DB e l'app MongoDB in MongoChef, vedere l'esercitazione [Usare MongoChef con Azure Cosmos DB](documentdb-mongodb-mongochef.md).
* Per importare i dati in un'API per il database MongoDB, vedere l'esercitazione [Eseguire la migrazione di dati in Azure Cosmos DB con il supporto del protocollo per MongoDB](documentdb-mongodb-migrate.md).
* Creare la prima app dell'API per MongoDB usando [Node.js](documentdb-mongodb-samples.md).
* Creare la prima app Web dell'API per MongoDB usando [.NET](documentdb-mongodb-application.md).
* Connettersi a un account dell'API per MongoDB usando [Robomongo](documentdb-mongodb-robomongo.md).
* Informazioni sul numero di unità richiesta usate dalle operazioni con il [comando GetLastRequestStatistics e le metriche del portale di Azure](documentdb-request-units.md#GetLastRequestStatistics).
* Informazioni su come [configurare le preferenze di lettura per le app distribuite globalmente](../cosmos-db/tutorial-global-distribution-mongodb.md).

