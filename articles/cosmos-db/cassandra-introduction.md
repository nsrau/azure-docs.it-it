---
title: Introduzione all'API Cassandra di Azure Cosmos DB
description: Informazioni su come usare Azure Cosmos DB per trasferire in modalità lift-and-shift le applicazioni esistenti e sviluppare nuove applicazioni che usano l'API Cassandra tramite i driver Cassandra e CQL già noti.
author: kanshiG
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9d948164177b7acd2df722078ae590f409c8e795
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036512"
---
# <a name="introduction-to-the-azure-cosmos-db-cassandra-api"></a>Introduzione all'API Cassandra di Azure Cosmos DB

È possibile usare l'API Cassandra di Azure Cosmos DB come archivio dati per le applicazioni scritte per [Cassandra Apache](https://cassandra.apache.org/). Questo significa che, usando i [driver Apache](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) esistenti conformi con CQLv4, l'applicazione per Cassandra può comunicare con l'API Cassandra di Azure Cosmos DB. In molti casi, è possibile passare da Apache Cassandra all'API Cassandra di Azure Cosmos DB, semplicemente modificando una stringa di connessione. 

L'API Cassandra consente di interagire con i dati archiviati in Azure Cosmos DB, usando Cassandra Query Language (CQL), gli strumenti basati su Cassandra (come CQLSH) e i driver client Cassandra già noti.

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>Quali sono i vantaggi dell'utilizzo dell'API Apace Cassandra per Azure Cosmos DB?

**Nessuna gestione delle operazioni**: come un servizio cloud completamente gestito, l'API Cassandra di Azure Cosmos DB elimina il sovraccarico di gestione e il monitoraggio di innumerevoli impostazioni del sistema operativo, di JVM, dei file yaml e delle loro interazioni. Azure Cosmos DB consente di monitorare velocità effettiva, latenza, archiviazione, disponibilità e avvisi configurabili.

**Gestione delle prestazioni**: Azure Cosmos DB offre lettura e scrittura a bassa latenza garantita al 99° percentile e supportata da contratti di servizio. Gli utenti non devono preoccuparsi del sovraccarico operativo per garantire prestazioni elevate, nonché scrittura e lettura a bassa latenza. Questo significa che gli utenti non devono occuparsi di eseguire manualmente la compattazione di dati, la gestione delle rimozioni definitive e l'impostazione di repliche e filtri bloom. Azure Cosmos DB elimina il sovraccarico operativo per gestire questi problemi e permette di concentrarsi sulla logica dell'applicazione.

**Possibilità di usare codice e strumenti esistenti**: Azure Cosmos DB offre compatibilità a livello di protocollo di trasmissione con gli strumenti e gli SDK Cassandra esistenti. Questa compatibilità consente di usare la base di codici esistente con l'API Cassandra di Azure Cosmos DB apportando solo semplici modifiche.

**Elasticità di velocità effettiva e archiviazione**: Azure Cosmos DB offre velocità effettiva garantita in tutte le aree geografiche e può ridimensionare la velocità effettiva fornita con il portale di Azure, PowerShell o l'interfaccia della riga di comando. È possibile scalare velocità effettiva e archiviazione in modo elastico per le tabelle in base alle esigenze con prestazioni prevedibili.

**Distribuzione globale e disponibilità**: Azure Cosmos DB offre la possibilità di distribuire i dati in tutte le aree di Azure a livello globale e servire i dati in locale, garantendo l'accesso ai dati a bassa latenza e disponibilità elevata. Azure Cosmos DB fornisce una disponibilità elevata del 99,99% all'interno di un'area e una disponibilità in lettura e scrittura al 99,999% tra più aree, senza sovraccarichi operativi. Per altre informazioni, vedere l'articolo [Distribuire i dati a livello globale](distribute-data-globally.md). 

**Livelli di coerenza:** Azure Cosmos DB consente di scegliere tra cinque livelli di coerenza ben definiti, per ottenere un compromesso ottimale tra coerenza e prestazioni. I livelli di coerenza possibili sono: Strong, Bounded Staleness, Session, Consistent Prefix ed Eventual. Si tratta di livelli di coerenza ben definiti, pratici e intuitivi, che permettono agli sviluppatori di ottenere compromessi precisi tra coerenza, disponibilità e latenza. Vedere l'articolo [livelli di coerenza](consistency-levels.md) per altre informazioni. 

**Livello enterprise**: Azure Cosmos DB offre [certificazioni di conformità](https://www.microsoft.com/trustcenter) affinché gli utenti possano usare la piattaforma in modo sicuro. Fornisce inoltre la crittografia dei dati inattivi e in transito, firewall IP e log di controllo per le attività del piano di controllo.

## <a name="next-steps"></a>Passaggi successivi

* È possibile iniziare rapidamente con la creazione delle seguenti app specifiche della lingua, per creare e gestire i dati dell'API Cassandra:
  - [App Node.js](create-cassandra-nodejs.md)
  - [App .NET](create-cassandra-dotnet.md)
  - [App Python](create-cassandra-python.md)

* Introduzione alla [creazione di un account, un database e una tabella API Cassandra](create-cassandra-api-account-java.md) usando un'applicazione Java.

* [Caricare dati di esempio nella tabella dell'API Cassandra](cassandra-api-load-data.md) usando un'applicazione Java.

* [Eseguire query sui dati dall'account dell'API Cassandra](cassandra-api-query-data.md) usando un'applicazione Java.

* Per altre informazioni sulle funzionalità di Apache Cassandra supportate dall'API Cassandra di Azure Cosmos DB, vedere l'articolo [Supporto Cassandra](cassandra-support.md).

* Leggere le [Domande Frequenti](faq.md#cassandra).
