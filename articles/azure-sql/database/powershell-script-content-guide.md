---
title: Esempi di script di Azure PowerShell
description: Usare gli esempi di script di Azure PowerShell per creare e gestire le risorse di Database SQL di Azure e Istanza gestita di SQL di Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 77220fabc78dd10b4299219ef84c1f6eb32ba82e
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85987311"
---
# <a name="azure-powershell-samples-for-azure-sql-database-and-azure-sql-managed-instance"></a>Esempi di Azure PowerShell per Database SQL di Azure e Istanza gestita di SQL di Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Database SQL di Azure e Istanza gestita di SQL di Azure consentono di configurare i database, le istanze e i pool con Azure PowerShell.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario AZ PowerShell 1.4.0 o versione successiva. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="azure-sql-database"></a>[Database SQL di Azure](#tab/single-database)

La tabella seguente include collegamenti a esempi di script di Azure PowerShell per database SQL di Azure.

|Collegamento|Descrizione|
|---|---|
|**Creare e configurare database singoli e pool elastici**||
| [Creare un database singolo e configurare una regola del firewall a livello di server](scripts/create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell crea un database singolo e configura una regola del firewall IP a livello di server. |
| [Creare pool elastici e spostare i database in pool](scripts/move-database-between-elastic-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell crea pool elastici, sposta i database in pool e cambia le dimensioni di calcolo.|
|**Configurare la replica geografica ed eseguire il failover**||
| [Configurare un database singolo ed eseguirne il failover usando la replica geografica attiva](scripts/setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell configura la replica geografica attiva per un database singolo e ne esegue il failover nella replica secondaria. |
| [Configurare un database in pool ed eseguirne il failover usando la replica geografica attiva](scripts/setup-geodr-and-failover-elastic-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell configura la replica geografica attiva per un database in un pool elastico e ne esegue il failover nella replica secondaria. |
|**Configurare un gruppo di failover**||
| [Configurare un gruppo di failover per un database singolo](scripts/add-database-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell crea un database e un gruppo di failover, aggiunge il database al gruppo di failover e testa il failover nel server secondario. |
| [Configurare un gruppo di failover per un pool elastico](scripts/add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell crea un database, lo aggiunge a un pool elastico, aggiunge il pool elastico al gruppo di failover e testa il failover nel server secondario. |
|**Ridimensionare un database singolo e un pool elastico**||
| [Ridimensionare un database singolo](scripts/monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell monitora le metriche delle prestazioni di un database singolo, ne aumenta le dimensioni di calcolo e crea una regola di avviso per una delle metriche delle prestazioni. |
| [Ridimensionare un pool elastico](scripts/monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell consente di monitorare le metriche delle prestazioni di un pool elastico, aumentare le dimensioni di calcolo del pool stesso e creare una regola di avviso per una delle metriche delle prestazioni. |
| **Controllo e rilevamento delle minacce** |
| [Configurare il controllo e il rilevamento delle minacce](scripts/auditing-threat-detection-powershell-configure.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell configura i criteri di controllo e rilevamento delle minacce per un database. |
| **Ripristinare, copiare e importare un database**||
| [Ripristinare un database](scripts/restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell ripristina un database da un backup con ridondanza geografica e ripristina un database eliminato in base al backup più recente. |
| [Copiare un database in un nuovo server](scripts/copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell crea una copia di un database esistente in un nuovo server. |
| [Importare un database da un file BACPAC](scripts/import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell importa un database in un database SQL di Azure da un file bacpac. |
| **Sincronizzare i dati tra database**||
| [Sincronizzare i dati tra database](scripts/sql-data-sync-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell permette di configurare la sincronizzazione dei dati per sincronizzare più database in Database SQL di Azure. |
| [Sincronizzare i dati tra database SQL e istanze di SQL Server locali](scripts/sql-data-sync-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell configura Sincronizzazione dati per la sincronizzazione tra un database di Database SQL di Azure e un database locale di SQL Server. |
| [Update the SQL Data Sync sync schema](scripts/update-sync-schema-in-sync-group.md?toc=%2fpowershell%2fmodule%2ftoc.json) (Aggiornare lo schema di sincronizzazione dati SQL) | Questo script di PowerShell consente di aggiungere o rimuovere elementi dallo schema di sincronizzazione dati. |
|||

Vedere altre informazioni sull'[API Azure PowerShell per i database singoli](single-database-manage.md#powershell). 

## <a name="azure-sql-managed-instance"></a>[Istanza gestita di database SQL di Azure](#tab/managed-instance)

La tabella seguente include collegamenti a esempi di script di Azure PowerShell per Istanza gestita di SQL di Azure.

|Collegamento|Descrizione|
|---|---|
|**Creare e configurare istanze gestite**||
| [Creare e gestire un'istanza gestita](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | Questo script di PowerShell mostra come creare e gestire un'istanza gestita tramite Azure PowerShell. |
| [Creare e gestire un'istanza gestita con il modello di Azure Resource Manager](../managed-instance/scripts/create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell mostra come creare e gestire un'istanza gestita tramite Azure PowerShell e il modello di Azure Resource Manager.|
| [Ripristinare un database in un'istanza gestita in un'altra area geografica](../managed-instance/scripts/restore-geo-backup.md) | Questo script di PowerShell esegue un backup di un database e lo ripristina in un'altra area. Questo scenario è noto come ripristino di emergenza geografico. |
| **Configurare Transparent Data Encryption**||
| [Gestire Transparent Data Encryption in un'istanza gestita usando una chiave personale di Azure Key Vault](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell configura Transparent Data Encryption in uno scenario Bring Your Own Key per un'istanza gestita di SQL di Azure usando una chiave di Azure Key Vault.|
|**Configurare un gruppo di failover**||
| [Configurare un gruppo di failover per un'istanza gestita](../managed-instance/scripts/add-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell crea due istanze gestite, le aggiunge a un gruppo di failover e quindi testa il failover dall'istanza gestita primaria all'istanza gestita secondaria. |
|||

Altre informazioni sui [Cmdlet di PowerShell per Istanza gestita di SQL di Azure](../managed-instance/api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances).

---

## <a name="additional-resources"></a>Risorse aggiuntive

Gli esempi elencati in questa pagina usano i [cmdlet di PowerShell](/powershell/module/az.sql/) per la creazione e la gestione delle risorse SQL di Azure. Altri cmdlet per l'esecuzione di query e di molte attività del database sono disponibili nel modulo [sqlserver](/powershell/module/sqlserver/). Per altre informazioni, vedere [SQL Server PowerShell](/sql/powershell/sql-server-powershell/).
