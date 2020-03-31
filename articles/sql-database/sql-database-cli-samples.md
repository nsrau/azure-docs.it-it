---
title: Esempi di script dell'interfaccia della riga di comando di Azure
description: Esempi di script dell'interfaccia della riga di comando di Azure per creare e gestire server di database SQL di Azure, pool elastici, database e firewall.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 459a5ea69e11a8614c572f68fce039b6cabbb1ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061706"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Esempi di interfaccia della riga di comando di Azure per un database SQL di Azure

È possibile configurare il database SQL di Azure con l'<a href="/cli/azure">interfaccia della riga di comando di Azure</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

# <a name="single-database--elastic-pools"></a>[Database singolo e pool elastici](#tab/single-database)

| | |
|---|---|
|**Creare un database singolo e un pool elastico**||
| [Creare un database singolo e configurare una regola del firewall](scripts/sql-database-create-and-configure-database-cli.md) | Crea un database SQL di Azure e configura una regola del firewall a livello di server. |
| [Creare pool elastici e spostare i database in pool](scripts/sql-database-move-database-between-pools-cli.md) | Crea pool elastici SQL, sposta i database SQL di Azure in pool e modifica le dimensioni di calcolo. |
|**Ridimensionare un database singolo e un pool elastico**||
| [Ridimensionare un database singolo](scripts/sql-database-monitor-and-scale-database-cli.md) | Dimensiona un database SQL di Azure per ottenere dimensioni di calcolo diverse dopo aver recuperato informazioni sulle dimensioni del database tramite query. |
| [Ridimensionare un pool elastico](scripts/sql-database-scale-pool-cli.md) | Dimensiona un pool elastico SQL impostando dimensioni di calcolo diverse. |
|**Configurare la replica geografica ed eseguire il failover**||
| [Aggiungere un database singolo al gruppo di failover](scripts/sql-database-add-single-db-to-failover-group-cli.md)| Crea un database, un gruppo di failover, aggiunge il database al gruppo di failover e testa il failover nel server secondario. |
| [Configurare un gruppo di failover per un pool elastico](scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Crea un database, lo aggiunge a un pool elastico, aggiunge il pool elastico al gruppo di failover e testa il failover nel server secondario. |
| [Configurare un database singolo ed eseguirne il failover usando la replica geografica attiva](scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Configura la replica geografica attiva per un database SQL di Azure e ne esegue il failover nella replica secondaria. |
| [Configurare un database in pool ed eseguirne il failover usando la replica geografica attiva](scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Configura la replica geografica attiva per un database SQL di Azure in un pool elastico SQL e ne esegue il failover nella replica secondaria. |
| **Controllo e rilevamento delle minacce** |
| [Configurare il controllo e il rilevamento delle minacce](scripts/sql-database-auditing-and-threat-detection-cli.md)| Configura i criteri di controllo e rilevamento delle minacce per un database SQL di Azure. |
| **Ripristinare, copiare e importare un database**||
| [Eseguire il backup di un database](scripts/sql-database-backup-database-cli.md)| Esegue il backup di un database SQL di Azure in un backup di archiviazione di Azure. |
| [Ripristinare un database](scripts/sql-database-restore-database-cli.md)| Ripristina un database SQL di Azure da un backup con ridondanza geografica e ripristina un database SQL di Azure eliminato in base al backup più recente. |
| [Copiare un database in un nuovo server](scripts/sql-database-copy-database-to-new-server-cli.md) | Crea una copia di un database SQL di Azure esistente in un nuovo server SQL di Azure. |
| [Importare un database da un file BACPAC](scripts/sql-database-import-from-bacpac-cli.md)| Importa un database in un server SQL di Azure da un file *BACPAC*. |
|||

Altre informazioni sull'[API dell'interfaccia della riga di comando di Azure per i database singoli](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases).

# <a name="managed-instance"></a>[Istanza gestita](#tab/managed-instance)

La tabella seguente contiene collegamenti a esempi di script dell'interfaccia della riga di comando di Azure per Istanza gestita di database SQL di Azure.

| | |
|---|---|
| **Creare un'istanza gestita**||
| [Creare un'istanza gestita](scripts/sql-database-create-configure-managed-instance-cli.md)| Crea un'istanza gestita. |
| **Configurare Transparent Data Encryption (TDE)**||
| [Gestire Transparent Data Encryption in un'istanza gestita usando Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Configura Transparent Data Encryption in un'istanza gestita di database SQL di Azure usando Azure Key Vault con vari scenari principali. |
|**Configurare un gruppo di failover**||
| [Configurare un gruppo di failover per un'istanza gestita](scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Crea due istanze gestite, le aggiunge a un gruppo di failover e quindi testa il failover dall'istanza gestita primaria nell'istanza gestita secondaria. |
|||

Per altri esempi relativi alle istanze gestite, vedere gli esempi di script per [creazione](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/), [aggiornamento](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/), [spostamento di un database](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) e [utilizzo](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

Altre informazioni sull'[API dell'interfaccia della riga di comando di Azure - Istanza gestita](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances).

---
