---
title: Esempi di script dell'interfaccia della riga di comando di Azure
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Esempi di script dell'interfaccia della riga di comando di Azure per creare e gestire Database SQL di Azure e Istanza gestita di SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc, sqldbrb=2
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 1006b8719a1141bd38792bdbbe116d0c477071dd
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84190050"
---
# <a name="azure-cli-samples-for-azure-sql-database-and-sql-managed-instance"></a>Esempi dell'interfaccia della riga di comando di Azure per Database SQL di Azure e Istanza gestita di SQL 
 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

È possibile configurare il database SQL di Azure e l'istanza gestita di SQL usando l'<a href="/cli/azure">interfaccia della riga di comando di Azure</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

# <a name="azure-sql-database"></a>[Database SQL di Azure](#tab/single-database)

La tabella seguente contiene collegamenti a esempi di script dell'interfaccia della riga di comando di Azure per gestire database singoli e in pool nel database SQL di Azure. 

| | |
|---|---|
|**Creare database nel database SQL di Azure**||
| [Creare un database singolo e configurare una regola del firewall](scripts/create-and-configure-database-cli.md) | Crea un database SQL e configura una regola del firewall a livello di server. |
| [Creare pool elastici e spostare i database in pool](scripts/move-database-between-elastic-pools-cli.md) | Crea pool elastici, sposta i database SQL in pool e modifica le dimensioni di calcolo. |
|**Dimensionare i database nel database SQL di Azure**||
| [Ridimensionare un database singolo](scripts/monitor-and-scale-database-cli.md) | Dimensiona un database nel database SQL di Azure impostandolo su dimensioni di calcolo diverse dopo aver recuperato le informazioni sulle dimensioni del database tramite query. |
| [Ridimensionare un pool elastico](scripts/scale-pool-cli.md) | Dimensiona un pool elastico SQL impostando dimensioni di calcolo diverse. |
|**Configurare la replica geografica ed eseguire il failover**||
| [Aggiungere un database singolo a un gruppo di failover](scripts/add-database-to-failover-group-cli.md)| Crea un database, un gruppo di failover, aggiunge il database al gruppo di failover e testa il failover nel server secondario. |
| [Configurare un gruppo di failover per un pool elastico](../../sql-database/scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Crea un database, lo aggiunge a un pool elastico, aggiunge il pool elastico al gruppo di failover e testa il failover nel server secondario. |
| [Configurare un database singolo ed eseguirne il failover usando la replica geografica attiva](../../sql-database/scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Configura la replica geografica attiva per un database SQL di Azure e ne esegue il failover nella replica secondaria. |
| [Configurare un database in pool ed eseguirne il failover usando la replica geografica attiva](../../sql-database/scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Configura la replica geografica attiva per un database SQL di Azure in un pool elastico SQL e ne esegue il failover nella replica secondaria. |
| **Controllo e rilevamento delle minacce** |
| [Configurare il controllo e il rilevamento delle minacce](../../sql-database/scripts/sql-database-auditing-and-threat-detection-cli.md)| Configura i criteri di controllo e rilevamento delle minacce per un database nel database SQL di Azure. |
| **Eseguire il backup, il ripristino, la copia e l'importazione di un database**||
| [Eseguire il backup di un database](../../sql-database/scripts/sql-database-backup-database-cli.md)| Esegue il backup di un database nel database SQL in un backup di archiviazione di Azure. |
| [Ripristinare un database](../../sql-database/scripts/sql-database-restore-database-cli.md)| Ripristina un database nel database SQL da un backup con ridondanza geografica e ripristina un database eliminato in base al backup più recente. |
| [Copiare un database in un nuovo server](../../sql-database/scripts/sql-database-copy-database-to-new-server-cli.md) | Crea una copia di un database esistente nel database SQL in un nuovo server. |
| [Importare un database da un file BACPAC](../../sql-database/scripts/sql-database-import-from-bacpac-cli.md)| Importa un database nel database SQL da un file BACPAC. |
|||

Altre informazioni sull'[API dell'interfaccia della riga di comando di Azure per i database singoli](single-database-manage.md#azure-cli).

# <a name="azure-sql-managed-instance"></a>[Istanza gestita di database SQL di Azure](#tab/managed-instance)

La tabella seguente contiene collegamenti a esempi di script dell'interfaccia della riga di comando di Azure per Istanza gestita di SQL di Azure.

| | |
|---|---|
| **Creare un'istanza gestita di SQL**||
| [Creare un'istanza gestita di SQL](../../sql-database/scripts/sql-database-create-configure-managed-instance-cli.md)| Crea un'istanza gestita di SQL. |
| **Configurare Transparent Data Encryption (TDE)**||
| [Gestire Transparent Data Encryption in un'istanza gestita di SQL usando Azure Key Vault](../../sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Configura Transparent Data Encryption in un'istanza gestita di SQL usando Azure Key Vault con vari scenari principali. |
|**Configurare un gruppo di failover**||
| [Configurare un gruppo di failover per l'istanza gestita di SQL](../../sql-database/scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Crea due istanze gestite di SQL, le aggiunge a un gruppo di failover e quindi testa il failover dall'istanza gestita di SQL primaria all'istanza gestita di SQL secondaria. |
|||

Per altri esempi relativi alle istanze gestite di SQL, vedere gli esempi di script per [creazione](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/), [aggiornamento](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/), [spostamento di un database](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) e [utilizzo](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

Altre informazioni sull'[API dell'interfaccia della riga di comando di Azure per l'istanza gestita di SQL](../managed-instance/api-references-create-manage-instance.md#azure-cli-create-and-manage-managed-instances).

---
