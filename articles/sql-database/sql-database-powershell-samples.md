---
title: Esempi di script di Azure PowerShell per database SQL | Microsoft Docs
description: Esempi di script di Azure PowerShell per creare e gestire server di database SQL di Azure, pool elastici, database e firewall.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 998497d3e790ba501ac5cdeb4974bf7ee9ea6a54
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059266"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Esempi di Azure PowerShell per database SQL di Azure

Il database SQL di Azure consente di configurare i database, le istanze e i pool con Azure PowerShell.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario AZ PowerShell 1.4.0 o successiva. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="single-database-and-elastic-poolstabsingle-database"></a>[Database singolo e pool elastici](#tab/single-database)

La tabella seguente include collegamenti a esempi di script di Azure PowerShell per database SQL di Azure.

| |  |
|---|---|
|**Creare e configurare database singoli e pool elastici**||
| [Creare un database singolo e configurare una regola del firewall del server di database](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell crea un database SQL di Azure singolo e configura una regola del firewall a livello di server. |
| [Creare pool elastici e spostare i database in pool](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell crea pool elastici di database SQL di Azure, sposta i database in pool e modifica le dimensioni di calcolo.|
|**Configurare la replica geografica ed eseguire il failover**||
| [Configurare un database singolo ed eseguirne il failover usando la replica geografica attiva](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell configura la replica geografica attiva per un database SQL di Azure singolo e ne effettua il failover nella replica secondaria. |
| [Configurare un database in pool ed eseguirne il failover usando la replica geografica attiva](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell configura la replica geografica attiva per un database SQL di Azure in un pool elastico SQL e ne effettua il failover nella replica secondaria. |
|**Configurare un gruppo di failover**||
| [Configurare un gruppo di failover per un database singolo](scripts/sql-database-add-single-db-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell crea un database, un gruppo di failover, aggiunge il database al gruppo di failover e testa il failover nel server secondario. | 
|**Ridimensionare un database singolo e un pool elastico**||
| [Ridimensionare un database singolo](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell effettua il monitoraggio delle metriche delle prestazioni di un database SQL di Azure, lo ridimensiona con dimensioni di calcolo superiori e crea una regola di avviso per una delle metriche delle prestazioni. |
| [Ridimensionare un pool elastico](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell effettua il monitoraggio delle metriche delle prestazioni di un pool elastico di database SQL di Azure, lo ridimensiona con dimensioni di calcolo superiori e crea una regola di avviso per una delle metriche delle prestazioni. |
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

Vedere altre informazioni sull'[API Azure PowerShell per i database singoli](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases).

## <a name="managed-instancetabmanaged-instance"></a>[Istanza gestita](#tab/managed-instance)

La tabella seguente include collegamenti a esempi di script di Azure PowerShell per database SQL di Azure - Istanza gestita.

| |  |
|---|---|
|**Creare e configurare istanze gestite**||
| [Create and manage a Managed Instance](scripts/sql-database-create-configure-managed-instance-powershell.md) (Creare e gestire un'Istanza gestita) | Questo script di PowerShell mostra come creare e gestire un'istanza gestita tramite Azure PowerShell |
| [Creare e gestire un'istanza gestita con il modello di Azure Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell mostra come creare e gestire un'istanza gestita tramite Azure PowerShell e il modello di Azure Resource Manager.|
| [Ripristinare un database in un'istanza gestita in un'altra area geografica](scripts/sql-managed-instance-restore-geo-backup.md) | Questo script di PowerShell esegue un backup di un database e lo ripristina in un'altra area. Quest'operazione è nota come scenario di ripristino geografico di emergenza. |
| **Configurare Transparent Data Encryption (TDE)**||
| [Gestire Transparent Data Encryption in un'istanza gestita usando una chiave personalizzata di Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell configura Transparent Data Encryption (TDE) in uno scenario Bring Your Own Key per un'istanza gestita di SQL di Azure usando una chiave di Azure Key Vault|
|||

Vedere altre informazioni sull'[API Azure PowerShell - Istanza gestita](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances).

---

## <a name="additional-resources"></a>Risorse aggiuntive

Gli esempi elencati in questa pagina usano i [cmdlet del database SQL di Azure](/powershell/module/az.sql/) per la creazione e la gestione delle risorse SQL di Azure. Altri cmdlet per l'esecuzione di query e di molte attività del database si trovano nel modulo [sqlserver](/powershell/module/sqlserver/). Per altre informazioni, vedere [SQL Server PowerShell](/sql/powershell/sql-server-powershell/).
