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
ms.openlocfilehash: 023651d61f891c5a73eee234a14306add838bf98
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821833"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Esempi di interfaccia della riga di comando di Azure per un database SQL di Azure

È possibile configurare il database SQL di Azure con l'<a href="/cli/azure">interfaccia della riga di comando di Azure</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="single-database--elastic-poolstabsingle-database"></a>[Database singolo e pool elastici](#tab/single-database)

La tabella seguente include collegamenti a esempi di script dell'interfaccia della riga di comando di Azure per il database SQL di Azure.

| |  |
|---|---|
|**Creare un database singolo e un pool elastico**||
| [Creare un database singolo e configurare una regola del firewall](scripts/sql-database-create-and-configure-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Questo esempio di script dell'interfaccia della riga di comando crea un singolo database SQL di Azure e configura una regola del firewall a livello di server. |
| [Creare pool elastici e spostare i database in pool](scripts/sql-database-move-database-between-pools-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Questo esempio di script dell'interfaccia della riga di comando crea pool elastici SQL, sposta i database SQL di Azure in pool e modifica le dimensioni di calcolo.|
|**Ridimensionare un database singolo e un pool elastico**||
| [Ridimensionare un database singolo](scripts/sql-database-monitor-and-scale-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Questo esempio di script dell'interfaccia della riga di comando ridimensiona un singolo database SQL di Azure per ottenere dimensioni di calcolo diverse dopo aver recuperato informazioni sulle dimensioni del database tramite query. |
| [Ridimensionare un pool elastico](scripts/sql-database-scale-pool-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Questo esempio di script dell'interfaccia della riga di comando ridimensiona un pool elastico SQL impostando dimensioni di calcolo diverse.  |
|**Gruppi di failover**||
| [Aggiungere un database singolo al gruppo di failover](scripts/sql-database-add-single-db-to-failover-group-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Questo script dell'interfaccia della riga di comando crea un database, un gruppo di failover, aggiunge il database al gruppo di failover e testa il failover nel server secondario.|
|||

Vedere altre informazioni sull'[API dell'interfaccia della riga di comando di Azure per i database singoli](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases).

## <a name="managed-instancetabmanaged-instance"></a>[Istanza gestita](#tab/managed-instance)

La tabella seguente contiene collegamenti a esempi di script dell'interfaccia della riga di comando di Azure per Istanza gestita di database SQL di Azure.

| |  |
|---|---|
| [Creare un'istanza gestita](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/) | Questo script dell'interfaccia della riga di comando illustra come creare un'istanza gestita. |
| [Aggiornare un'istanza gestita](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/) | Questo script dell'interfaccia della riga di comando illustra come aggiornare un'istanza gestita. |
| [Spostare un database in un'altra istanza gestita](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) | Questo script dell'interfaccia della riga di comando illustra come ripristinare un backup di un database da un'istanza a un'altra. |
|||

Vedere altre informazioni sull'[API dell'interfaccia della riga di comando di Azure per le istanze gestite](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances) ed [altri esempi](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

---
