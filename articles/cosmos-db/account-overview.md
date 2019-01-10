---
title: Uso di account Azure Cosmos DB
description: Questo articolo descrive come creare e usare account Azure Cosmos DB
author: dharmas-cosmos
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 7c4c1a5991445448f015dc0912383baf53f4e38c
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034931"
---
# <a name="working-with-azure-cosmos-account"></a>Uso dell'account Azure Cosmos

Azure Cosmos DB è una piattaforma completamente gestita distribuita come servizio (PaaS). Per iniziare a usare Azure Cosmos DB, è innanzitutto necessario creare un account Azure Cosmos nella sottoscrizione di Azure. L'account Azure Cosmos contiene un nome DNS univoco e può essere gestito tramite il portale di Azure, l'interfaccia della riga di comando di Azure o diversi SDK specifici del linguaggio. Per altre informazioni, vedere [Come gestire l'account Azure Cosmos](how-to-manage-database-account.md).

L'account Azure Cosmos è l'unità fondamentale della distribuzione globale e della disponibilità elevata. Per la distribuzione a livello globale dei dati e della velocità effettiva tra più aree di Azure, è possibile aggiungere e rimuovere aree di Azure all'account Azure Cosmos in qualsiasi momento. È possibile configurare l'account Azure Cosmos per una o più aree di scrittura. Per altre informazioni, vedere [Come aggiungere e rimuovere aree di Azure per l'account Azure Cosmos](how-to-manage-database-account.md). È possibile configurare il [livello di coerenza predefinito](consistency-levels.md) nell'account Azure Cosmos. Azure Cosmos DB offre contratti di servizio completi che includono velocità effettiva, latenza al 99° percentile, coerenza e disponibilità elevata. Per altre informazioni, vedere [Contratti di servizio per Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Per gestire in modo sicuro l'accesso a tutti i dati all'interno dell'account Azure Cosmos, è possibile usare le chiavi master associate all'account. Per proteggere ulteriormente l'accesso ai dati, è possibile configurare un endpoint di servizio di rete virtuale e il firewall per gli indirizzi IP nell'account Azure Cosmos. 

## <a name="elements-in-an-azure-cosmos-account"></a>Elementi nell'account Azure Cosmos

Il contenitore di Azure Cosmos DB è l'unità fondamentale di scalabilità. È possibile ottenere archiviazione e unità elaborate supportate per il provisioning (UR/s) praticamente illimitate in un contenitore. Azure Cosmos DB partiziona in modo trasparente il contenitore usando la chiave di partizione logica specificata per ridimensionare in modo elastico l'archiviazione e le unità elaborate supportate per il provisioning. Per altre informazioni, vedere [Working with Azure Cosmos containers and items](databases-containers-items.md) (Uso di contenitori ed elementi di Azure Cosmos).

Attualmente è possibile creare un massimo di 100 account Azure Cosmos in una sottoscrizione di Azure. Un solo account Azure Cosmos può gestire praticamente una quantità illimitata di dati e di unità elaborate supportate per il provisioning. Per gestire i dati e le unità elaborate supportate per il provisioning, è possibile creare uno o più database Cosmos Azure nel proprio account e all'interno del database è possibile creare uno o più contenitori. L'immagine seguente mostra la gerarchia degli elementi in un account Azure Cosmos:

![Gerarchia di un account Azure Cosmos](./media/account-overview/hierarchy.png)

## <a name="next-steps"></a>Passaggi successivi

È ora possibile passare ad altre risorse che descrivono come gestire l'account Azure Cosmos o altri concetti associati ad Azure Cosmos DB:

* [Come gestire l'account Azure Cosmos](how-to-manage-database-account.md)
* [Distribuzione globale](distribute-data-globally.md)
* [Livelli di coerenza](consistency-levels.md)
* [Working with Azure Cosmos containers and items](databases-containers-items.md) (Uso di contenitori ed elementi di Azure Cosmos)
* [Endpoint di servizio di rete virtuale per l'account Azure Cosmos](vnet-service-endpoint.md)
* [Firewall per gli indirizzi IP per l'account Azure Cosmos](firewall-support.md)
* [Come aggiungere e rimuovere aree di Azure per l'account Azure Cosmos](how-to-manage-database-account.md)
* [Contratti di servizio per Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
