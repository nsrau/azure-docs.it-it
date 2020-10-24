---
title: Vantaggio Azure Hybrid
titleSuffix: Azure SQL Database & SQL Managed Instance
description: USA licenze di SQL Server esistenti per il database SQL di Azure e gli sconti di SQL Istanza gestita.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=4
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 11/13/2019
ms.openlocfilehash: 186721fbb52ae153bd51076a9fe3a7413f29d1b4
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92479120"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Vantaggio Azure Hybrid: database SQL di Azure & SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Nel livello di calcolo di cui è stato effettuato il provisioning del modello di acquisto basato su vCore è possibile scambiare le licenze esistenti con tariffe scontate nel database SQL di Azure e in Azure SQL Istanza gestita usando [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/). Questo vantaggio di Azure ti permette di risparmiare fino al 30% o addirittura superiore nel database SQL & SQL Istanza gestita usando le tue licenze di SQL Server con Software Assurance. La pagina [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) dispone di una calcolatrice che consente di determinare il risparmio.  Si noti che il Vantaggio Azure Hybrid non si applica al database SQL di Azure senza server.

> [!NOTE]
> Il passaggio a Vantaggio Azure Hybrid non richiede alcun tempo di inattività.

![struttura dei prezzi di vcore](./media/azure-hybrid-benefit/pricing.png)

## <a name="choose-a-license-model"></a>Scegliere un modello di licenza

Con Vantaggio Azure Hybrid, è possibile scegliere di pagare solo per l'infrastruttura di Azure sottostante usando la licenza di SQL Server esistente per il motore di database SQL Server stesso (prezzi di calcolo di base) oppure è possibile pagare sia per l'infrastruttura sottostante che per la licenza di SQL Server (prezzi inclusi in licenza).

È possibile scegliere o modificare il modello di licenza nella portale di Azure: 
- Per i nuovi database, durante la creazione selezionare **Configura database** nella scheda **nozioni di base** e selezionare l'opzione per risparmiare denaro.
- Per i database esistenti, selezionare **Configura** nel menu **Impostazioni** e selezionare l'opzione per risparmiare denaro.

È anche possibile configurare un database nuovo o esistente usando una delle API seguenti:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per impostare o aggiornare il tipo di licenza usando PowerShell:

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per impostare o aggiornare il tipo di licenza usando l'interfaccia della riga di comando di Azure:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi create](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[REST API](#tab/rest)

Per impostare o aggiornare il tipo di licenza usando l'API REST:

- [Database-creazione o aggiornamento](/rest/api/sql/databases/createorupdate)
- [Databases - Update](/rest/api/sql/databases/update)
- [Managed Instances - Create Or Update](/rest/api/sql/managedinstances/createorupdate)
- [Managed Instances - Update](/rest/api/sql/managedinstances/update)

* * *


### <a name="azure-hybrid-benefit-questions"></a>Domande relative a Vantaggio Azure Hybrid

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Il Vantaggio Azure Hybrid per SQL Server include diritti di doppio uso della licenza?

Si può disporre dei diritti di doppio uso della licenza per 180 giorni, per garantire che le migrazioni vengano eseguite senza problemi. Dopo tale periodo di 180 giorni, è possibile usare solo la licenza SQL Server nel cloud nel database SQL. Non sono più disponibili due diritti di utilizzo in locale e nel cloud.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Qual è la differenza tra il Vantaggio Azure Hybrid per SQL Server e la mobilità delle licenze?

Offriamo vantaggi di mobilità delle licenze per SQL Server clienti con Software Assurance. Questo consente la riassegnazione delle licenze ai server condivisi di un partner. È possibile usare questo vantaggio in Azure IaaS e AWS EC2.

Il Vantaggio Azure Hybrid per SQL Server differisce dalla mobilità delle licenze per due aspetti principali:

- Offre vantaggi economici per lo spostamento di carichi di lavoro altamente virtualizzati in Azure. I clienti di SQL Server Enterprise Edition possono ottenere quattro core in Azure nello SKU per utilizzo generico per ogni core di cui sono proprietari in locale per applicazioni altamente virtualizzate. La mobilità delle licenze non consente vantaggi speciali in termini di costi per lo spostamento di carichi di lavoro virtualizzati nel cloud.
- Fornisce una destinazione PaaS in Azure (SQL Istanza gestita) altamente compatibile con SQL Server.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Quali sono i diritti specifici inclusi nel Vantaggio Azure Hybrid per SQL Server?

I clienti del database SQL dispongono dei diritti seguenti associati a Vantaggio Azure Hybrid per SQL Server:

|Impronta della licenza|Che cosa Vantaggio Azure Hybrid per SQL Server ottenere?|
|---|---|
|Clienti di SQL Server Enterprise Edition con Software Assurance|<li>Può pagare la tariffa di base in per utilizzo generico o business critical SKU</li><br><li>1 core in locale = 4 core nello SKU del livello Utilizzo generico</li><br><li>1 core in locale = 1 core nello SKU del livello Business Critical</li>|
|Clienti di SQL Server Standard Edition con Software Assurance|<li>Può pagare la tariffa di base solo per per utilizzo generico SKU</li><br><li>1 core in locale = 1 core nello SKU del livello Utilizzo generico</li>|
|||


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla scelta di un'opzione di distribuzione SQL di Azure, vedere [scegliere l'opzione di distribuzione corretta in SQL di Azure](azure-sql-iaas-vs-paas-what-is-overview.md).
- Per un confronto tra le funzionalità del database SQL e di SQL Istanza gestita, vedere SQL [database & sql istanza gestita features](database/features-comparison.md).
