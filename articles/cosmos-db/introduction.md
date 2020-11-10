---
title: Introduzione ad Azure Cosmos DB
description: Informazioni su Azure Cosmos DB. Questo database multimodello distribuito a livello globale è pensato per garantire bassa latenza, scalabilità elastica, disponibilità elevata e offre il supporto nativo per i dati NoSQL.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2020
ms.openlocfilehash: eb2212d618d45187779b328c7ba164bbacc980a4
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93088689"
---
# <a name="welcome-to-azure-cosmos-db"></a>Introduzione ad Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Le applicazioni moderne devono garantire velocità di risposta elevate ed essere sempre online. Per ottenere bassa latenza e disponibilità elevata, è necessario che le istanze di queste applicazioni siano distribuite in data center vicini agli utenti. Le applicazioni devono rispondere in tempo reale alle importanti modifiche nell'utilizzo negli orari di punta, archiviare volumi sempre maggiori di dati e renderli disponibili agli utenti in millisecondi.

Azure Cosmos DB è un database NoSQL completamente gestito per lo sviluppo di app moderne. I tempi di risposta di pochi millisecondi e la scalabilità automatica e istantanea garantiscono la velocità ottimale anche su larga scala. La continuità aziendale è garantita dalla disponibilità [supportata dal contratto di servizio](https://azure.microsoft.com/support/legal/sla/cosmos-db) e dalla sicurezza di livello aziendale. Lo sviluppo di app è più veloce e più produttivo grazie alla distribuzione chiavi in mano dei dati multimaster in tutto il mondo, alle API open source e agli SDK per i linguaggi di programmazione più diffusi. In qualità di servizio completamente gestito, Azure Cosmos DB solleva gli utenti dalle attività di amministrazione dei database occupandosi automaticamente della gestione, degli aggiornamenti e dell'applicazione di patch, nonché della gestione della capacità con opzioni di scalabilità automatica e serverless convenienti che rispondono alle esigenze dell'applicazione in termini di capacità per soddisfare la richiesta.

È possibile [provare gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) senza una sottoscrizione di Azure, a titolo gratuito e senza impegno, oppure usare il [livello gratuito di Azure Cosmos DB](optimize-dev-test.md#azure-cosmos-db-free-tier) per ottenere un account che include gratuitamente le prime 400 UR/s e 5 GB di spazio di archiviazione.

> [!div class="nextstepaction"]
> [Prova gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)

:::image type="content" source="./media/introduction/azure-cosmos-db.png" alt-text="Azure Cosmos DB è un database NoSQL completamente gestito per lo sviluppo di app moderne." border="false":::

## <a name="key-benefits"></a>Vantaggi principali

### <a name="guaranteed-speed-at-any-scale"></a>Velocità garantita su qualsiasi scala

Velocità effettiva [supportata da contratto di servizio](https://azure.microsoft.com/support/legal/sla/cosmos-db) senza precedenti, accesso rapido globale ed elasticità istantanea.

- Accesso in tempo reale con latenze di lettura e scrittura veloci a livello globale, velocità effettiva e coerenza supportati da [contratti di servizio](https://azure.microsoft.com/support/legal/sla/cosmos-db)
- Scritture in più aree e distribuzione dei dati in qualsiasi area di Azure al clic di un pulsante.
- Scalabilità delle risorse di archiviazione e della velocità effettiva in modo indipendente ed elastico in qualsiasi area di Azure, anche in caso di picchi di traffico imprevedibili, per la scalabilità illimitata in tutto il mondo.

### <a name="simplified-application-development"></a>Sviluppo di applicazioni semplificato

Creazione rapida con API open source, più SDK, dati senza schema e analisi senza estrazione, trasformazione e caricamento sui dati operativi.

- Stretta integrazione con i servizi di Azure principali usati per lo sviluppo di app moderne (native del cloud), tra cui Funzioni di Azure, hub IoT, servizio Azure Kubernetes, Servizio app e altri ancora.
- Possibilità di scelta tra più API di database, tra cui l'API Core (SQL) nativa, l'API per MongoDB, l'API Cassandra, l'API Gremlin e l'API Tabella.
- Creazione di app nell'API Core (SQL) usando i linguaggi preferiti con gli SDK per .NET, Java, Node.js e Python oppure i driver preferiti per le altre API di database.
- Esecuzione di analisi senza estrazione, trasformazione e caricamento sui dati operativi near-real time archiviati in Azure Cosmos DB con Azure Synapse Analytics.
- Il feed di modifiche semplifica il rilevamento e la gestione delle modifiche apportate ai contenitori di database e la creazione di eventi attivati con Funzioni di Azure.
- Il servizio senza schema di Azure Cosmos DB indicizza automaticamente tutti i dati, indipendentemente dal modello di dati, fino al recapito di query velocissime.

### <a name="mission-critical-ready"></a>Supporto di carichi di lavoro cruciali

Garanzia della continuità aziendale, della disponibilità del 99,999% e della sicurezza a livello aziendale per ogni applicazione.

- Azure Cosmos DB offre una suite completa di [contratti di servizio](https://azure.microsoft.com/support/legal/sla/cosmos-db) che includono la disponibilità leader di settore in tutto il mondo.
- Distribuzione semplificata dei dati in qualsiasi area di Azure con la replica automatica dei dati. Nessun tempo di inattività grazie alle scritture in più aree o l'obiettivo del punto di ripristino pari a 0 con la coerenza assoluta.
- Crittografia dei dati inattivi di livello aziendale con le chiavi autogestite.
- Il controllo degli accessi in base al ruolo consente di mantenere i dati al sicuro e offre un controllo ottimizzato.

### <a name="fully-managed-and-cost-effective"></a>Completamente gestito e conveniente

Gestione end-to-end del database, con scalabilità automatica e serverless in base alle esigenze dell'applicazione e del costo totale di proprietà

- Servizio di database completamente gestito. Automatico, nessun intervento, manutenzione, applicazione di patch e aggiornamenti, per consentire agli sviluppatori di risparmiare tempo e denaro.
- Opzioni convenienti per carichi di lavoro imprevedibili o sporadici di tutte le dimensioni e su qualsiasi scala, per consentire agli sviluppatori di iniziare facilmente senza dover pianificare o gestire la capacità.
- Il modello serverless offre ai carichi di lavoro con picchi un servizio automatico e reattivo per gestire i picchi di traffico su richiesta.
- Scalabilità automatica della velocità effettiva di cui è stato effettuato il provisioning e dimensionamento istantaneo della capacità per carichi di lavoro imprevedibili, nel rispetto dei [contratti di servizio](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Soluzioni che traggono vantaggio da Azure Cosmos DB

Qualsiasi [applicazione Web, per dispositivi mobili, di gioco e IoT](use-cases.md) che debba gestire un numero molto elevato di dati, letture e scritture su scala [globale](distribute-data-globally.md) con risposte quasi in tempo reale per una varietà di dati può trarre beneficio dalle caratteristiche di disponibilità elevata, velocità effettiva elevata, bassa latenza e coerenza ottimizzabile [garantite](https://azure.microsoft.com/support/legal/sla/cosmos-db/) di Cosmos DB. Informazioni su come usare Azure Cosmos DB per compilare [IoT e dati telematici](use-cases.md#iot-and-telematics), [vendite e marketing](use-cases.md#retail-and-marketing), [giochi](use-cases.md#gaming) e [applicazioni Web e per dispositivi mobili](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Passaggi successivi

Per un'introduzione ad Azure Cosmos DB, fare riferimento alle guide introduttive seguenti:

- [Introduzione all'API SQL di Azure Cosmos DB](create-sql-api-dotnet.md)
- [Come iniziare a usare l'API di Azure Cosmos DB per MongoDB](create-mongodb-nodejs.md)
- [Introduzione all'API Cassandra di Azure Cosmos DB](create-cassandra-dotnet.md)
- [Introduzione all'API Gremlin di Azure Cosmos DB](create-graph-dotnet.md)
- [Introduzione all'API Tabelle di Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Prova gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)