---
title: Esempi di script di Azure PowerShell per database SQL | Microsoft Docs
description: Esempi di script di Azure PowerShell per creare e gestire server di database SQL di Azure, pool elastici, database e firewall.
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: jhubbard
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: overview-samples
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 06/23/2017
ms.author: janeng
ms.openlocfilehash: 5a5f77b9adafe32e8559d0b3396febca4b191de3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Esempi di Azure PowerShell per database SQL di Azure

La tabella seguente include collegamenti a esempi di script di Azure PowerShell per database SQL di Azure.

| |  |
|---|---|
|**Creare un database singolo e un pool elastico**||
| [Creare un database singolo e configurare una regola del firewall](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell crea un database SQL di Azure singolo e configura una regola del firewall a livello di server. |
| [Creare pool elastici e spostare i database in pool](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell crea pool elastici di database SQL di Azure, sposta i database in pool e modifica i livelli di prestazioni.|
|**Configurare la replica geografica ed eseguire il failover**||
| [Configurare un database singolo ed eseguirne il failover usando la replica geografica attiva](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell configura la replica geografica attiva per un database SQL di Azure singolo e ne effettua il failover nella replica secondaria. |
| [Configurare un database in pool ed eseguirne il failover usando la replica geografica attiva](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell configura la replica geografica attiva per un database SQL di Azure in un pool elastico SQL e ne effettua il failover nella replica secondaria. |
| [Configurare un gruppo di failover per un database singolo ed effettuarne il failover (anteprima)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell configura un gruppo di failover per un'istanza del server di database SQL di Azure, aggiunge un database al gruppo di failover e ne effettua il failover nel server secondario. |
|**Ridimensionare un database singolo e un pool elastico**||
| [Ridimensionare un database singolo](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell monitora le metriche delle prestazioni di un database SQL di Azure, lo ridimensiona passando a un livello di prestazioni superiore e crea una regola di avviso per una delle metriche delle prestazioni. |
| [Ridimensionare un pool elastico](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell monitora le metriche delle prestazioni di un pool elastico di database SQL di Azure, lo ridimensiona passando a un livello di prestazioni superiore e crea una regola di avviso per una delle metriche delle prestazioni.  |
| **Controllo e rilevamento delle minacce** |
| [Configurare il controllo e il rilevamento delle minacce](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell configura i criteri di controllo e rilevamento delle minacce per un database SQL di Azure. |
| **Ripristinare, copiare e importare un database**||
| [Ripristinare un database](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell ripristina un database SQL di Azure da un backup con ridondanza geografica e ripristina un database SQL di Azure eliminato in base al backup più recente. |
| [Copiare un database in un nuovo server](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell crea una copia di un database SQL di Azure esistente in un nuovo server SQL di Azure. |
| [Importare un database da un file BACPAC](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell importa un database in un server SQL di Azure da un file BACPAC. |
| **Sincronizzare i dati tra database**||
| [Sincronizzare i dati tra database SQL](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell permette di configurare la sincronizzazione dei dati per sincronizzare più database SQL di Azure. |
| [Sincronizzare i dati tra database SQL e istanze di SQL Server locali](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell permette di configurare la sincronizzazione dei dati per sincronizzare un database SQL di Azure e un database locale di SQL Server. |
|||
|||
