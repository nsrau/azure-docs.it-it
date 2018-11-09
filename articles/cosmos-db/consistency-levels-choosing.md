---
title: Scelta del livello di coerenza ottimale per l'applicazione | Microsoft Docs
description: Scelta del livello di coerenza ottimale per l'applicazione in Azure Cosmos DB.
keywords: coerenza, prestazioni, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: 4b438320ac75918d10880a4bcdccadca4eebec32
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243986"
---
# <a name="choose-the-right-consistency-level-for-your-application"></a>Scegliere il livello di coerenza ottimale per la propria applicazione

I database distribuiti che di basano sulla replica per gestire la disponibilità elevata, la bassa latenza o entrambe, applicano il compromesso fondamentale tra coerenza di lettura e disponibilità, latenza e velocità effettiva. La maggior parte dei database distribuiti disponibili in commercio richiede che gli sviluppatori scelgano tra i due modelli di coerenza estremi: la coerenza assoluta e la coerenza finale. Azure Cosmos DB consente agli sviluppatori di scegliere tra cinque modelli di coerenza ben definiti: assoluta, decadimento ristretto, sessione, prefisso coerente e finale. Ognuno di questi modelli di coerenza è ben definito, intuitivo e può essere usato per scenari reali specifici. Ognuno dei cinque modelli di cinque prevede compromessi espliciti di disponibilità e prestazioni ed è supportato da un contratto di servizio completo. Le seguenti semplici osservazioni consentiranno all'utente di fare la scelta giusta in molti scenari comuni.

## <a name="sql-api-or-table-api"></a>API SQL o API Tabella

- Per molti scenari reali, la coerenza di sessione è ottimale ed è l'opzione consigliata. Per altre informazioni, vedere [Come gestire il token di sessione per l'applicazione](how-to-manage-consistency.md#utilize-session-tokens).
- Se l'applicazione richiede una coerenza assoluto, consigliamo di usare il livello di coerenza decadimento ristretto.
- Se per le operazioni di scrittura vengono richieste garanzie di coerenza più rigorose rispetto a quelle fornite dalla coerenza di sessione e dalla latenza in millisecondi a una cifra, consigliamo di usare il livello di coerenza decadimento ristretto.  
- Se l'applicazione richiede una coerenza finale, consigliamo di usare il livello di coerenza prefisso coerente.
- Se vengono richieste garanzie di coerenza meno rigorose rispetto a quelle offerte dalla coerenza di sessione, consigliamo di usare il livello di coerenza prefisso coerente.
- Se viene richiesta una disponibilità più elevata e una latenza più bassa, usare il livello di coerenza finale.

## <a name="cassandra-mongodb-or-gremlin-api"></a>API Cassandra, MongoDB o Gremlin

- Per informazioni dettagliate sul mapping tra il "Livello di coerenza di lettura" di Cosmos DB e Apache Cassandra e i livelli di coerenza, vedere [Livelli di coerenza e API di Cosmos DB](consistency-levels-across-apis.md#cassandra-mapping).
- Per informazioni dettagliate sul mapping tra il "Problema di lettura" di MongoDB e i livelli di coerenza di Azure Cosmos DB, vedere [Livelli di coerenza e API di Cosmos DB](consistency-levels-across-apis.md#mongo-mapping).

## <a name="you-may-get-stronger-consistency-guarantees-in-practice"></a>È possibile ottenere maggiori garanzie di coerenza in pratica

Le garanzie di coerenza garantiscono un'operazione di lettura corrispondente al livello di aggiornamento e ordinamento dello stato del database richiesto. La coerenza di lettura è associata all'ordinamento e alla propagazione delle operazioni di scrittura (aggiornamento).  

Quando il livello di coerenza è impostato su **decadimento ristretto**, Cosmos DB garantisce che i client leggano sempre il valore di una scrittura precedente, con un intervallo ristretto dalla finestra di decadimento.

Quando il livello di coerenza è impostato su **assoluto**, la finestra di decadimento equivale a zero e i client hanno la certezza di leggere il valore di commit più recente della scrittura.

Per i tre livelli di coerenza rimanenti, la finestra di decadimento dipende in gran parte dal carico di lavoro. Ad esempio, se non sono presenti scritture in corso sul database, un'operazione di lettura con livello di coerenza **finale**, **sessione** o **prefisso coerente** è probabile che fornisca gli stessi risultati di un'operazione di lettura con livello di coerenza assoluto.

Se l'account Cosmos DB è configurato con qualsiasi livello di coerenza diverso dalla coerenza assoluta, è possibile conoscere la probabilità dei client di ottenere letture con coerenza assoluta (linearizzabili) per i carichi di lavoro esaminando il calcolo del decadimento ristretto probabilistico (Probabilistic Bounded Staleness, PBS) esposto nel portale di Azure [Leggere qui per sapere come usare la metrica del PBS](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric). Il decadimento ristretto probabilistico mostra il livello di finalità della coerenza finale. Questa metrica offre informazioni dettagliate sulla frequenza con cui si ottiene una coerenza più elevata rispetto al livello di coerenza configurato per l'account Cosmos DB. In altre parole, puoi visualizzare la probabilità (indicata in millisecondi) di ottenere letture con coerenza assoluta per una combinazione di aree di scrittura e lettura.

## <a name="next-steps"></a>Passaggi successivi

È possibile reperire altre informazioni sui livelli di coerenza negli articoli seguenti:

* [Mapping dei livelli di coerenza nelle API Cosmos DB](consistency-levels-across-apis.md)
* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)
* [Come gestire il token di sessione per l'applicazione](how-to-manage-consistency.md#utilize-session-tokens)
* [Come monitorare la metrica del decadimento ristretto probabilistico (Probabilistic Bounded Staleness, PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)