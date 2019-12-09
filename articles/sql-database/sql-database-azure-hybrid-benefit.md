---
title: Vantaggio Azure Hybrid
description: Usare licenze di SQL Server esistenti per gli sconti del database SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: 394c674da63bbda643246c3d61fb670d6ac8d1f7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928608"
---
# <a name="azure-hybrid-benefit"></a>Vantaggio Azure Hybrid

Nel livello di calcolo di cui è stato effettuato il provisioning del modello di acquisto basato su vCore, è possibile scambiare le licenze esistenti con tariffe scontate nel database SQL usando [vantaggio Azure Hybrid per SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Questo vantaggio di Azure ti permette di risparmiare fino al 30% sul database SQL di Azure usando le tue licenze SQL Server locali con Software Assurance. 

> [!NOTE]
> Il passaggio a Vantaggio Azure Hybrid non richiede alcun tempo di inattività.

![prezzi](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Scegliere un modello di licenza

Con Vantaggio Azure Hybrid è possibile scegliere di pagare solo per l'infrastruttura di Azure sottostante usando la licenza di SQL Server esistente per il motore di database SQL stesso (prezzi di calcolo di base) oppure è possibile pagare sia per l'infrastruttura sottostante che per il SQL Server licenza (prezzo incluso in licenza).

È possibile scegliere o modificare il modello di licenza usando il portale di Azure o usando una delle API seguenti:

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Per impostare o aggiornare il tipo di licenza usando PowerShell:

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)

Per impostare o aggiornare il tipo di licenza usando l'interfaccia della riga di comando di Azure:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi create](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-apitabrest"></a>[REST API](#tab/rest)

Per impostare o aggiornare il tipo di licenza usando l'API REST:

- [Database: crea o aggiorna](/rest/api/sql/databases/createorupdate)
- [Databases - Update](/rest/api/sql/databases/update)
- [Managed Instances - Create Or Update](/rest/api/sql/managedinstances/createorupdate)
- [Managed Instances - Update](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>Passaggi successivi

- Per la scelta tra le opzioni di distribuzione del database SQL, vedere [scegliere l'opzione di distribuzione corretta in SQL di Azure](sql-database-paas-vs-sql-server-iaas.md).
- Per un confronto delle funzionalità del database SQL, vedere [funzionalità del database SQL di Azure](sql-database-features.md).
