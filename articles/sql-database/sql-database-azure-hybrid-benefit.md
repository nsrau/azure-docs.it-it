---
title: Vantaggio Azure Hybrid
description: Utilizzare le licenze di SQL Server esistenti per gli sconti del database SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: d1a59e7ad86191bcc30b7d898d00f327c20fbc5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945611"
---
# <a name="azure-hybrid-benefit"></a>Vantaggio Azure Hybrid

Nel livello di calcolo di cui è stato eseguito il provisioning del modello di acquisto basato su vCore, è possibile scambiare le licenze esistenti con tariffe scontate nel database SQL usando il vantaggio [Azure Hybrid per SQL Server.](https://azure.microsoft.com/pricing/hybrid-benefit/) Questo vantaggio di Azure consente di risparmiare fino al 30% o anche superiore nel database SQL di Azure usando le licenze di SQL Server locali con Software Assurance.This Azure benefit allows you to save up to 30 percent or even higher on Azure SQL Database by using your on-premises SQL Server licenses with Software Assurance. Usare Azure Hybrid Benefit Calculator usando il collegamento indicato in precedenza per i valori corretti. 

> [!NOTE]
> La modifica del vantaggio Azure Hybrid non richiede tempi di inattività.

![prezzi](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Scegliere un modello di licenza

Con il vantaggio Azure Hybrid, è possibile scegliere di pagare solo per l'infrastruttura di Azure sottostante usando la licenza di SQL Server esistente per il motore di database SQL stesso (prezzi di calcolo di base) oppure è possibile pagare sia per l'infrastruttura sottostante che per SQL Server licenza (prezzi con licenza inclusa).

È possibile scegliere o modificare il modello di licenza usando il portale di Azure o una delle API seguenti:You can choose or change your licensing model by using the Azure portal or by using one of the following APIs:

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Per impostare o aggiornare il tipo di licenza tramite PowerShell:

- [Nuovo-AzSqlDatabaseNew-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Per impostare o aggiornare il tipo di licenza usando l'interfaccia della riga di comando di Azure:To set or update the license type by using the Azure CLI:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi create](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[REST API](#tab/rest)

Per impostare o aggiornare il tipo di licenza tramite l'API REST:

- [Database - Creazione o aggiornamento](/rest/api/sql/databases/createorupdate)
- [Databases - Update](/rest/api/sql/databases/update)
- [Managed Instances - Create Or Update](/rest/api/sql/managedinstances/createorupdate)
- [Managed Instances - Update](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>Passaggi successivi

- Per scegliere tra le opzioni di distribuzione del database SQL, vedere [Scegliere l'opzione di distribuzione corretta in SQL](sql-database-paas-vs-sql-server-iaas.md)di Azure.
- Per un confronto delle funzionalità del database SQL, vedere Funzionalità del database SQL di [Azure.](sql-database-features.md)
