---
title: Esempi di script di Azure PowerShell per database SQL | Microsoft Docs
description: Esempi di script di Azure PowerShell per creare e gestire server di database SQL di Azure, pool elastici, database e firewall.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/29/2018
ms.openlocfilehash: 98495c35270ea3d6d500151c8e5dfb35751d5cc5
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232138"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Esempi di Azure PowerShell per database SQL di Azure

La tabella seguente include collegamenti a esempi di script di Azure PowerShell per database SQL di Azure.

| |  |
|---|---|
|**Creare un database singolo e un pool elastico**||
| [Creare un database singolo e configurare una regola del firewall](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell crea un database SQL di Azure singolo e configura una regola del firewall a livello di server. |
| [Creare pool elastici e spostare i database in pool](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell crea pool elastici di database SQL di Azure, sposta i database in pool e modifica le dimensioni di calcolo.|
| [Create and manage a Managed Instance](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) (Creare e gestire un'Istanza gestita) | Questo script di PowerShell mostra come creare e gestire un'istanza gestita tramite Azure PowerShell |
|**Configurare la replica geografica ed eseguire il failover**||
| [Configurare un database singolo ed eseguirne il failover usando la replica geografica attiva](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell configura la replica geografica attiva per un database SQL di Azure singolo e ne effettua il failover nella replica secondaria. |
| [Configurare un database in pool ed eseguirne il failover usando la replica geografica attiva](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell configura la replica geografica attiva per un database SQL di Azure in un pool elastico SQL e ne effettua il failover nella replica secondaria. |
| [Eseguire la configurazione e il failover di un gruppo di failover per un database singolo](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell configura un gruppo di failover per un'istanza del server di database SQL di Azure, aggiunge un database al gruppo di failover e ne effettua il failover nel server secondario. |
|**Ridimensionare un database singolo e un pool elastico**||
| [Ridimensionare un database singolo](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell effettua il monitoraggio delle metriche delle prestazioni di un database SQL di Azure, lo ridimensiona con una dimensione di calcolo superiore e crea una regola di avviso per una delle metriche delle prestazioni. |
| [Ridimensionare un pool elastico](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell effettua il monitoraggio delle metriche delle prestazioni di un pool elastico di database SQL di Azure, lo ridimensiona con una dimensione di calcolo superiore e crea una regola di avviso per una delle metriche delle prestazioni.  |
| **Controllo e rilevamento delle minacce** |
| [Configurare il controllo e il rilevamento delle minacce](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell configura i criteri di controllo e rilevamento delle minacce per un database SQL di Azure. |
| **Ripristinare, copiare e importare un database**||
| [Ripristinare un database](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell ripristina un database SQL di Azure da un backup con ridondanza geografica e ripristina un database SQL di Azure eliminato in base al backup più recente. |
| [Copiare un database in un nuovo server](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell crea una copia di un database SQL di Azure esistente in un nuovo server SQL di Azure. |
| [Importare un database da un file BACPAC](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell importa un database in un server SQL di Azure da un file BACPAC. |
| **Sincronizzare i dati tra database**||
| [Sincronizzare i dati tra database SQL](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell permette di configurare la sincronizzazione dei dati per sincronizzare più database SQL di Azure. |
| [Sincronizzare i dati tra database SQL e istanze di SQL Server locali](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell permette di configurare la sincronizzazione dei dati per sincronizzare un database SQL di Azure e un database locale di SQL Server. |
| [Update the SQL Data Sync sync schema](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) (Aggiornare lo schema di sincronizzazione dati SQL) | Questo script di PowerShell consente di aggiungere o rimuovere elementi dallo schema di sincronizzazione dati. |
|||
