---
title: Distribuire i dati a livello globale con Azure Cosmos DB | Microsoft Docs
description: Informazioni sulla replica geografica a livello globale, sul multimaster, sul failover e sul ripristino dei dati usando i database globali di Azure Cosmos DB, un servizio database multimodello distribuito a livello globale.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.openlocfilehash: 227c243d82665dc533e3bfa6a1fe3e9bb775a262
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408896"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Informazioni sulla distribuzione globale con Azure Cosmos DB

Azure è ovunque, offre una copertura globale in oltre 50 aree geografiche ed è in continua espansione. Ciò consente ad Azure di offrire in esclusiva un supporto multimaster agli sviluppatori e la possibilità di creare, distribuire e gestire applicazioni distribuite a livello globale con la massima semplicità.

[Azure Cosmos DB](../cosmos-db/introduction.md) è il servizio di database multimodello distribuito a livello globale di Microsoft per applicazioni cruciali. Azure Cosmos DB offre una distribuzione globale chiavi in mano, [scalabilità elastica in termini di archiviazione e velocità effettiva](../cosmos-db/partition-data.md) in qualsiasi area nel mondo, latenze pari a singole unità di millisecondi al 99° percentile, [modelli di coerenza ben definiti](consistency-levels.md) e disponibilità elevata garantita, il tutto supportato da [contratti di servizio completi leader del settore](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indicizza automaticamente tutti i dati](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) senza che sia necessario gestire manualmente indici o schemi.

## <a name="global-distribution-with-multi-master"></a>Distribuzione globale chiavi in mano con multimaster

Come servizio cloud, Azure Cosmos DB è progettato con attenzione per supportare una distribuzione globale multitenancy e multimaster per documenti, key-value, grafici e modelli di dati a colonne.

![Contenitore di Azure Cosmos DB partizionato e distribuito tra tre aree](./media/distribute-data-globally/global-apps.png)

**Un singolo contenitore di Azure Cosmos DB partizionato e distribuito tra più aree di Azure**

Durante lo sviluppo di Azure Cosmos DB, è emerso che non era possibile aggiungere la distribuzione globale a posteriori, perché non è una funzionalità che si può integrare su un sistema di database basato su "singolo sito". Le funzionalità offerte da un database distribuito a livello globale superano quelle del ripristino di emergenza con ridondanza geografica tradizionale offerte dai database basati su "singolo sito". Il database basati su singolo sito che offrono la funzionalità di ripristino di emergenza con ridondanza geografica rappresentano un sottoinsieme limitato dei database distribuiti a livello globale.

Grazie alla funzionalità di distribuzione globale chiavi in mano di Azure Cosmos DB, gli sviluppatori non devono creare i propri scaffolding di replica usando il modello lambda sul log del database oppure eseguendo "doppie scritture" tra più aree. Questi approcci *non* sono consigliati perché la correttezza non è garantita e non offrono contratti di servizio affidabili.

## <a id="Next Steps"></a>Passaggi successivi

* [Come Azure Cosmos DB abilita la distribuzione globale chiavi in mano](distribute-data-globally-turnkey.md)

* [Vantaggi chiave della distribuzione globale di Azure Cosmos DB](distribute-data-globally-benefits.md)

* [Come configurare la replica del database globale di Azure Cosmos DB](tutorial-global-distribution-sql-api.md)

* [How to enable multi-master for Azure Cosmos DB accounts (Come abilitare il multimaster per gli account di Azure Cosmos DB)](enable-multi-master.md)

* [Come funziona il failover automatico e manuale in Azure Cosmos DB](regional-failover.md)

* [Understanding conflict resolution in Azure Cosmos DB (Informazioni sulla risoluzione dei conflitti in Azure Cosmos DB)](multi-master-conflict-resolution.md)

* [Uso del multimaster di Azure Cosmos DB con database NoSQL open source](multi-master-oss-nosql.md)
