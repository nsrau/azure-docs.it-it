---
title: Uso di account Azure Cosmos DB
description: Questo articolo descrive come creare e usare account Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: c0e97eed0a4028fdd9d2254fd9472dc3141d607a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239841"
---
# <a name="work-with-azure-cosmos-account"></a>Usare l'account Azure Cosmos

Azure Cosmos DB è una piattaforma completamente gestita distribuita come servizio (PaaS). Per iniziare a usare Azure Cosmos DB, è innanzitutto necessario creare un account Azure Cosmos nella sottoscrizione di Azure. L'account Azure Cosmos contiene un nome DNS univoco e può essere gestito tramite il portale di Azure, l'interfaccia della riga di comando di Azure o diversi SDK specifici del linguaggio. Per altre informazioni, vedere [Come gestire l'account Azure Cosmos](how-to-manage-database-account.md).

L'account Azure Cosmos è l'unità fondamentale della distribuzione globale e della disponibilità elevata. Per la distribuzione a livello globale dei dati e della velocità effettiva tra più aree di Azure, è possibile aggiungere e rimuovere aree di Azure all'account Azure Cosmos in qualsiasi momento. È possibile configurare l'account Azure Cosmos per una o più aree di scrittura. Per altre informazioni, vedere [Come aggiungere e rimuovere aree di Azure per l'account Azure Cosmos](how-to-manage-database-account.md). È possibile configurare il [livello di coerenza predefinito](consistency-levels.md) nell'account Azure Cosmos. Azure Cosmos DB offre contratti di servizio completi che includono velocità effettiva, latenza al 99° percentile, coerenza e disponibilità elevata. Per altre informazioni, vedere [Contratti di servizio per Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Per gestire in modo sicuro l'accesso a tutti i dati all'interno dell'account Azure Cosmos, è possibile usare la [chiavi master](secure-access-to-data.md) associate all'account. Per proteggere ulteriormente l'accesso ai dati, è possibile configurare un [endpoint del servizio rete virtuale](vnet-service-endpoint.md) e [firewall IP](firewall-support.md) nell'account Azure Cosmos. 

## <a name="elements-in-an-azure-cosmos-account"></a>Elementi nell'account Azure Cosmos

Il contenitore di Azure Cosmos DB è l'unità fondamentale di scalabilità. È possibile ottenere archiviazione e unità elaborate supportate per il provisioning (UR/s) praticamente illimitate in un contenitore. Azure Cosmos DB partiziona in modo trasparente il contenitore usando la chiave di partizione logica specificata per ridimensionare in modo elastico l'archiviazione e le unità elaborate supportate per il provisioning. Per altre informazioni, vedere [Working with Azure Cosmos containers and items](databases-containers-items.md) (Uso di contenitori ed elementi di Azure Cosmos).

Attualmente è possibile creare un massimo di 100 account Azure Cosmos in una sottoscrizione di Azure. Un solo account Azure Cosmos può gestire praticamente una quantità illimitata di dati e di unità elaborate supportate per il provisioning. Per gestire i dati e le unità elaborate supportate per il provisioning, è possibile creare uno o più database Cosmos Azure nel proprio account e all'interno del database è possibile creare uno o più contenitori. L'immagine seguente mostra la gerarchia degli elementi in un account Azure Cosmos:

![Gerarchia di un account Azure Cosmos](./media/account-overview/hierarchy.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come gestire il proprio account Azure Cosmos e altri concetti:

* [Come gestire l'account Azure Cosmos](how-to-manage-database-account.md)
* [Distribuzione globale](distribute-data-globally.md)
* [Livelli di coerenza](consistency-levels.md)
* [Working with Azure Cosmos containers and items](databases-containers-items.md) (Uso di contenitori ed elementi di Azure Cosmos)
* [Endpoint di servizio di rete virtuale per l'account Azure Cosmos](vnet-service-endpoint.md)
* [Firewall per gli indirizzi IP per l'account Azure Cosmos](firewall-support.md)
* [Come aggiungere e rimuovere aree di Azure per l'account Azure Cosmos](how-to-manage-database-account.md)
* [Contratti di servizio per Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
