---
title: Glossario dei termini
titleSuffix: Azure SQL
description: Glossario dei termini per l'uso del database SQL di Azure, di Azure SQL Istanza gestita e di SQL in una macchina virtuale di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/22/2020
ms.openlocfilehash: 18ff2c9690de1708cd8382d83a0c01662a8e6fb0
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91619781"
---
# <a name="azure-sql-database-glossary-of-terms"></a>Glossario dei termini relativi al database SQL di Azure
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

## <a name="azure-sql-database"></a>database SQL di Azure

|Context|Termine|Ulteriori informazioni|
|:---|:---|:---|
|Servizio di Azure|Database SQL di Azure o database SQL|[Database SQL di Azure](database/sql-database-paas-overview.md)|
|Modello di acquisto|modello di acquisto basato su DTU|[Modello di acquisto basato su DTU](database/service-tiers-dtu.md)|
||Modello di acquisto basato su vCore|[modello di acquisto basato su vCore](database/service-tiers-vcore.md)|
|Opzione di distribuzione |Database singolo|[Database singoli](database/single-database-overview.md)|
||Pool elastico|[Pool elastico](database/elastic-pool-overview.md)|
|Livello di servizio|Basic, standard, Premium, per utilizzo generico, iperscalabile, business critical|Per i livelli di servizio nel modello vCore, vedere [livelli di servizio del database SQL](database/service-tiers-vcore.md#service-tiers). Per i livelli di servizio nel modello DTU, vedere [modello DTU](database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers).|
|Livello di calcolo|Calcolo serverless|[Calcolo serverless](database/service-tiers-vcore.md#compute-tiers)
||Calcolo con provisioning|[Calcolo con provisioning](database/service-tiers-vcore.md#compute-tiers)
|Generazione di calcolo|Quinta generazione, serie M, serie Fsv2|[Generazioni hardware](database/service-tiers-vcore.md#hardware-generations)
|Entità server| Server |[Server SQL logici](database/logical-servers.md)|
|Tipo di risorsa|vCore|Un core CPU fornito alla risorsa di calcolo per un singolo database, un pool elastico. |
||Dimensioni di calcolo e quantità di risorse di archiviazione|Dimensioni di calcolo è la quantità massima di CPU, memoria e altre risorse non correlate all'archiviazione disponibili per un singolo database o un pool elastico.  Dimensioni di archiviazione è la quantità massima di spazio di archiviazione disponibile per un singolo database o un pool elastico. Per le opzioni di ridimensionamento nel modello Vcore, vedere [Vcore Single databases](database/resource-limits-vcore-single-databases.md)e [Vcore Elastic pools](database/resource-limits-vcore-elastic-pools.md).  (.. /managed-instance/resource-limits.md).  Per le opzioni di ridimensionamento nel modello DTU, vedere [database singoli DTU](database/resource-limits-dtu-single-databases.md) e [pool elastici DTU](database/resource-limits-dtu-elastic-pools.md).

## <a name="azure-sql-managed-instance"></a>Istanza gestita di SQL di Azure

|Context|Termine|Ulteriori informazioni|
|:---|:---|:---|
|Servizio di Azure|Istanza gestita di SQL di Azure|[Istanza gestita di SQL](managed-instance/sql-managed-instance-paas-overview.md)|
|Modello di acquisto|Modello di acquisto basato su vCore|[modello di acquisto basato su vCore](database/service-tiers-vcore.md)|
|Opzione di distribuzione |Istanza singola|[Istanza singola](managed-instance/sql-managed-instance-paas-overview.md)|
||Pool di istanze (anteprima)|[Pool di istanze](managed-instance/instance-pools-overview.md)|
|Livello di servizio|Per utilizzo generico, business critical|[Livelli di servizio di SQL Istanza gestita](managed-instance/sql-managed-instance-paas-overview.md#service-tiers)|
|Livello di calcolo|Calcolo con provisioning|[Calcolo con provisioning](database/service-tiers-vcore.md#compute-tiers)|
|Generazione di calcolo|Quinta generazione|[Generazioni hardware](database/service-tiers-vcore.md#hardware-generations)
|Entità server|Istanza o istanza gestita| N/A perché il Istanza gestita SQL si trova nel server |
|Tipo di risorsa|vCore|Un core CPU fornito alla risorsa di calcolo per SQL Istanza gestita.|
||Dimensioni di calcolo e quantità di risorse di archiviazione|Dimensioni di calcolo è la quantità massima di CPU, memoria e altre risorse non correlate all'archiviazione per SQL Istanza gestita.  Dimensioni di archiviazione è la quantità massima di spazio di archiviazione disponibile per un Istanza gestita SQL.  Per le opzioni di ridimensionamento, [istanze gestite di SQL](managed-instance/resource-limits.md). |

## <a name="sql-on-azure-vm"></a>SQL in macchine virtuali di Azure

sono necessarie altre informazioni
