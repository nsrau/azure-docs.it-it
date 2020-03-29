---
title: Glossario dei termini
description: Glossario dei termini relativi al database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/22/2020
ms.openlocfilehash: 733901d38703e02ab7dbe811b0f80a1dfedf03d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705786"
---
# <a name="azure-sql-database-glossary-of-terms"></a>Glossario dei termini relativi al database SQL di Azure

|Context|Termine|Ulteriori informazioni|
|:---|:---|:---|
|Servizio di Azure|Database SQL di Azure o database SQL|[Servizio database SQL di Azure](sql-database-technical-overview.md)|
|Modello di acquisto|modello di acquisto basato su DTU|[Modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md)|
||Modello di acquisto basato su vCore|[Modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md)|
|Opzione di distribuzione |Database singolo|[Database singoli](sql-database-single-database.md)|
||Pool elastico|[Piscina elastica](sql-database-elastic-pool.md)|
||Istanza gestita|[Istanza gestita](sql-database-managed-instance.md)|
|Livello di servizio|Basic, Standard, Premium, Scopo generale, Hyperscale, Business Critical|Per i livelli di servizio nel modello vCore, vedere [database singolo ed elastico pool](sql-database-service-tiers-vcore.md#service-tiers) e istanza [gestita](sql-database-managed-instance.md#managed-instance-service-tiers). Per i livelli di servizio nel modello DTU, vedere [Modello DTU](sql-database-service-tiers-dtu.md#compare-the-dtu-based-service-tiers).|
|Livello di calcolo|Calcolo serverless|[Calcolo serverless](sql-database-service-tiers-vcore.md#compute-tiers)
||Calcolo con provisioning|[Calcolo con provisioning](sql-database-service-tiers-vcore.md#compute-tiers)
|Generazione di calcolo|Gen5, serie M, Serie Fsv2|[Generazioni di hardware](sql-database-service-tiers-vcore.md#hardware-generations)
|Entità Server|Server di database SQL o server di database|[Server di database](sql-database-servers.md)|
||Server di istanza gestita di database SQL, server di istanza gestita o server di istanza|[Istanza gestita](sql-database-managed-instance.md)|
|Tipo di risorsa|vCore|Un core CPU fornito alla risorsa di calcolo per un singolo database, pool elastico o istanza gestita.|
||Dimensioni del calcolo e quantità di archiviazione|Le dimensioni di calcolo indicano la quantità massima di CPU, memoria e altre risorse non correlate all'archiviazione disponibili per un singolo database, pool elastico o istanza gestita.  La dimensione di archiviazione è la quantità massima di spazio di archiviazione disponibile per un singolo database, pool elastico o istanza gestita.  Per le opzioni di ridimensionamento nel modello vcore, vedere [database singoli vCore](sql-database-vcore-resource-limits-single-databases.md), [pool elastici vCore](sql-database-vcore-resource-limits-elastic-pools.md) e [istanze gestite](sql-database-managed-instance-resource-limits.md).  Per le opzioni di ridimensionamento nel modello DTU, vedere [Database Singoli DTU](sql-database-dtu-resource-limits-single-databases.md) e [Pool elastici DTU](sql-database-dtu-resource-limits-elastic-pools.md).
