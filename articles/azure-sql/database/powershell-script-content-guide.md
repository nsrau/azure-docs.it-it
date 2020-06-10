---
title: Esempi di script di Azure PowerShell
description: Esempi di script di Azure PowerShell per creare e gestire risorse di Database SQL di Azure e Istanza gestita di SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 93fac2e3ae8d83f0fa49f94a3ba7880a2ecc2b4c
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84193895"
---
# <a name="azure-powershell-samples-for-azure-sql-database-and-azure-sql-managed-instance"></a>Esempi di Azure PowerShell per Database SQL di Azure e Istanza gestita di SQL di Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Database SQL di Azure e Istanza gestita di SQL di Azure consentono di configurare i database, le istanze e i pool con Azure PowerShell.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario AZ PowerShell 1.4.0 o versione successiva. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="azure-sql-database"></a>[Database SQL di Azure](#tab/single-database)

La tabella seguente include collegamenti a esempi di script di Azure PowerShell per database SQL di Azure.

| |  |
|---|---|
|**Creare e configurare database singoli e pool elastici**||
| [Creare un database singolo e configurare una regola del firewall a livello di server](scripts/create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell crea un database singolo e configura una regola del firewall IP a livello di server. |
| [Creare pool elastici e spostare i database in pool](scripts/move-database-between-elastic-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell crea pool elastici, sposta i database in pool e modifica le dimensioni di calcolo.|
|**Configurare la replica geografica ed eseguire il failover**||
| [Configurare un database singolo ed eseguirne il failover usando la replica geografica attiva](scripts/setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell configura la replica geografica attiva per un database singolo e ne esegue il failover nella replica secondaria. |
| [Configurare un database in pool ed eseguirne il failover usando la replica geografica attiva](scripts/setup-geodr-and-failover-elastic-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell configura la replica geografica attiva per un database in un pool elastico e ne esegue il failover nella replica secondaria. |
|**Configurare un gruppo di failover**||
| [Configurare un gruppo di failover per un database singolo](scripts/add-database-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell crea un database, un gruppo di failover, aggiunge il database al gruppo di failover e testa il failover nel server secondario. |
| [Configurare un gruppo di failover per un pool elastico](scripts/add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell crea un database, lo aggiunge a un pool elastico, aggiunge il pool elastico al gruppo di failover e testa il failover nel server secondario. |
|**Ridimensionare un database singolo e un pool elastico**||
| [Ridimensionare un database singolo](scripts/monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell consente di monitorare le metriche delle prestazioni di un database singolo, aumentare le dimensioni di calcolo del database stesso e creare una regola di avviso per una delle metriche delle prestazioni. |
| [Ridimensionare un pool elastico](scripts/monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell consente di monitorare le metriche delle prestazioni di un pool elastico, aumentare le dimensioni di calcolo del pool stesso e creare una regola di avviso per una delle metriche delle prestazioni. |
| **Controllo e rilevamento delle minacce** |
| [Configurare il controllo e il rilevamento delle minacce](scripts/auditing-threat-detection-powershell-configure.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell configura i criteri di controllo e rilevamento delle minacce per un database. |
| **Ripristinare, copiare e importare un database**||
| [Ripristinare un database](scripts/restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell ripristina un database da un backup con ridondanza geografica e ripristina un database eliminato in base al backup più recente. |
| [Copiare un database in un nuovo server](scripts/copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell crea una copia di un database esistente in un nuovo server. |
| [Importare un database da un file BACPAC](scripts/import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell importa un database in un database SQL di Azure da un file bacpac. |
| **Sincronizzare i dati tra database**||
| [Sincronizzare i dati tra database SQL](scripts/sql-data-sync-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell permette di configurare la sincronizzazione dei dati per sincronizzare più database in Database SQL di Azure. |
| [Sincronizzare i dati tra database SQL e SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell permette di configurare la sincronizzazione dei dati per sincronizzare un database in Database SQL di Azure e un database di SQL Server. |
| [Update the SQL Data Sync sync schema](scripts/update-sync-schema-in-sync-group.md?toc=%2fpowershell%2fmodule%2ftoc.json) (Aggiornare lo schema di sincronizzazione dati SQL) | Questo script di PowerShell consente di aggiungere o rimuovere elementi dallo schema di sincronizzazione dati. |
|||

Vedere altre informazioni sull'[API Azure PowerShell per i database singoli](single-database-manage.md#powershell). 

## <a name="azure-sql-managed-instance"></a>[Istanza gestita di database SQL di Azure](#tab/managed-instance)

La tabella seguente include collegamenti a esempi di script di Azure PowerShell per Istanza gestita di SQL di Azure.

| |  |
|---|---|
|**Creare e configurare istanze gestite**||
| [Creare e gestire un'istanza gestita](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | Questo script di PowerShell mostra come creare e gestire un'istanza gestita tramite Azure PowerShell |
| [Creare e gestire un'istanza gestita con il modello di Azure Resource Manager](../managed-instance/scripts/create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell mostra come creare e gestire un'istanza gestita tramite Azure PowerShell e il modello di Azure Resource Manager.|
| [Ripristinare un database in un'istanza gestita in un'altra area geografica](../managed-instance/scripts/restore-geo-backup.md) | Questo script di PowerShell esegue un backup di un database e lo ripristina in un'altra area. Quest'operazione è nota come scenario di ripristino geografico di emergenza. |
| **Configurare Transparent Data Encryption (TDE)**||
| [Gestire Transparent Data Encryption in un'istanza gestita usando una chiave personalizzata di Azure Key Vault](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Questo script di PowerShell configura Transparent Data Encryption (TDE) in uno scenario Bring Your Own Key per un'istanza gestita di SQL di Azure usando una chiave di Azure Key Vault|
|**Configurare un gruppo di failover**||
| [Configurare un gruppo di failover per un'istanza gestita](../managed-instance/scripts/add-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Questo script di PowerShell crea due istanze gestite, le aggiunge a un gruppo di failover e quindi testa il failover dall'istanza gestita primaria all'istanza gestita secondaria. |
|||

Altre informazioni sui [Cmdlet di PowerShell per Istanza gestita di SQL di Azure](../managed-instance/api-references-create-manage-instance.md#powershell-create-and-manage-managed-instances).

---

## <a name="additional-resources"></a>Risorse aggiuntive

Gli esempi elencati in questa pagina usano i [cmdlet di PowerShell](/powershell/module/az.sql/) per la creazione e la gestione delle risorse SQL di Azure. Altri cmdlet per l'esecuzione di query e di molte attività del database si trovano nel modulo [sqlserver](/powershell/module/sqlserver/). Per altre informazioni, vedere [SQL Server PowerShell](/sql/powershell/sql-server-powershell/).
