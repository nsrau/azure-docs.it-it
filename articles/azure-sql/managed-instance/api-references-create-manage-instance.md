---
title: Informazioni di riferimento sull'API di gestione per Istanza gestita SQL di Azure
description: Informazioni sulla creazione e la configurazione di istanze gestite di Istanza gestita SQL di Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: cf100861705bf1654b7206445c884b5fe315b06d
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792634"
---
# <a name="managed-api-reference-for-azure-sql-managed-instance"></a>Riferimento all'API di gestione per Istanza gestita di SQL di Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

È possibile creare e configurare istanze gestite di Azure SQL Istanza gestita usando il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure, l'API REST e Transact-SQL. In questo articolo è possibile trovare una panoramica delle funzioni e dell'API che è possibile usare per creare e configurare le istanze gestite.

## <a name="azure-portal-create-a-managed-instance"></a>Portale di Azure: Creare un'istanza gestita

Per una guida introduttiva che illustra come creare un'istanza gestita, vedere [Guida introduttiva: creare un'istanza gestita](instance-create-quickstart.md).

## <a name="powershell-create-and-configure-managed-instances"></a>PowerShell: creare e configurare istanze gestite

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato da Database SQL di Azure, ma tutte le attività di sviluppo future sono incentrate sul modulo Az.Sql. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo AZ e nei moduli AzureRM sono sostanzialmente identici.

Per creare e gestire istanze gestite del database SQL con Azure PowerShell, usare i cmdlet di PowerShell seguenti. Se è necessario installare o aggiornare PowerShell, vedere [installare il modulo Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Per gli script di esempio di PowerShell, vedere [script di avvio rapido: creare un'istanza gestita usando una libreria di PowerShell](/archive/blogs/sqlserverstorageengine/quick-start-script-create-azure-sql-managed-instance-using-powershell).

| Cmdlet | Descrizione |
| --- | --- |
|[New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)|Crea un'istanza gestita. |
|[Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)|Restituisce informazioni su un'istanza gestita.|
|[Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)|Imposta le proprietà per un'istanza gestita.|
|[Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance)|Rimuove un'istanza gestita.|
|[Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation)|Ottiene un elenco di operazioni di gestione eseguite sull'istanza gestita o su un'operazione specifica.|
|[Stop-AzSqlInstanceOperation](/powershell/module/az.sql/stop-azsqlinstanceoperation)|Annulla l'operazione di gestione specifica eseguita nell'istanza gestita.|
|[New-AzSqlInstanceDatabase](/powershell/module/az.sql/new-azsqlinstancedatabase)|Crea un database SQL Istanza gestita.|
|[Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase)|Restituisce informazioni su un database SQL Istanza gestita.|
|[Remove-AzSqlInstanceDatabase](/powershell/module/az.sql/remove-azsqlinstancedatabase)|Rimuove un database di SQL Istanza gestita.|
|[Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)|Ripristina un database SQL Istanza gestita.|

## <a name="azure-cli-create-and-configure-managed-instances"></a>INTERFACCIA della riga di comando di Azure: creare e configurare istanze gestite

Per creare e configurare istanze gestite con l'interfaccia della riga di comando di [Azure](/cli/azure), usare i comandi dell'interfaccia della riga [di comando di azure per SQL istanza gestita](/cli/azure/sql/mi) Usare [Azure cloud Shell](../../cloud-shell/overview.md) per eseguire l'interfaccia della riga di comando nel browser o [installarla](/cli/azure/install-azure-cli) in MacOS, Linux o Windows.

> [!TIP]
> Per una guida introduttiva dell'interfaccia della riga di comando di Azure, vedere [Working with SQL Managed Instance using Azure CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) (Uso di Istanza gestita di database SQL tramite l'interfaccia della riga di comando di Azure).

| Cmdlet | Descrizione |
| --- | --- |
|[az sql mi create](/cli/azure/sql/mi#az-sql-mi-create) |Crea un'istanza gestita.|
|[az sql mi list](/cli/azure/sql/mi#az-sql-mi-list)|Elenca le istanze gestite disponibili.|
|[az sql mi show](/cli/azure/sql/mi#az-sql-mi-show)|Ottiene i dettagli per un'istanza gestita.|
|[az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)|Aggiorna un'istanza gestita.|
|[az sql mi delete](/cli/azure/sql/mi#az-sql-mi-delete)|Rimuove un'istanza gestita.|
|[elenco AZ SQL mi op](/cli/azure/sql/mi/op#az_sql_mi_op_list)|Ottiene un elenco di operazioni di gestione eseguite nell'istanza gestita.|
|[AZ SQL mi op Show](/cli/azure/sql/mi/op#az_sql_mi_op_show)|Ottiene l'operazione di gestione specifica eseguita nell'istanza gestita.|
|[AZ SQL mi op Cancel](/cli/azure/sql/mi/op#az_sql_mi_op_cancel)|Annulla l'operazione di gestione specifica eseguita nell'istanza gestita.|
|[az sql midb create](/cli/azure/sql/midb#az-sql-midb-create) |Crea un database gestito.|
|[az sql midb list](/cli/azure/sql/midb#az-sql-midb-list)|Elenca i database gestiti disponibili.|
|[az sql midb restore](/cli/azure/sql/midb#az-sql-midb-restore)|Ripristina un database gestito.|
|[az sql midb delete](/cli/azure/sql/midb#az-sql-midb-delete)|Rimuove un database gestito.|

## <a name="transact-sql-create-and-configure-instance-databases"></a>Transact-SQL: creare e configurare i database dell'istanza

Per creare e configurare i database dell'istanza dopo la creazione dell'istanza gestita, usare i comandi T-SQL seguenti. È possibile eseguire questi comandi utilizzando la portale di Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](/sql/azure-data-studio/what-is), [Visual Studio Code](https://code.visualstudio.com/docs)o qualsiasi altro programma in grado di connettersi a un server e passare i comandi Transact-SQL.

> [!TIP]
> Per le guide introduttive che illustrano come configurare e connettersi a un'istanza gestita usando SQL Server Management Studio in Microsoft Windows, vedere [Guida introduttiva: configurare una macchina virtuale di Azure per la connessione ad Azure sql istanza gestita](connect-vm-instance-configure.md) e [Guida introduttiva: configurare una connessione da punto a sito al istanza gestita SQL di Azure dall'ambiente locale](point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Non è possibile creare o eliminare un'istanza gestita tramite Transact-SQL.

| Comando | Descrizione |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current)|Crea un nuovo database di istanza in SQL Istanza gestita. È necessario essere connessi al database master per creare un nuovo database.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current) |Modifica un database di istanza in SQL Istanza gestita.|

## <a name="rest-api-create-and-configure-managed-instances"></a>API REST: creare e configurare istanze gestite

Per creare e configurare le istanze gestite, usare queste richieste API REST.

| Comando | Descrizione |
| --- | --- |
|[Managed Instances - Create Or Update](/rest/api/sql/managedinstances/createorupdate)|Crea o aggiorna un'istanza gestita.|
|[Managed Instances - Delete](/rest/api/sql/managedinstances/delete)|Elimina un'istanza gestita.|
|[Managed Instances - Get](/rest/api/sql/managedinstances/get)|Ottiene un'istanza gestita.|
|[Managed Instances - List](/rest/api/sql/managedinstances/list)|Restituisce un elenco di istanze gestite in una sottoscrizione.|
|[Managed Instances - List By Resource Group](/rest/api/sql/managedinstances/listbyresourcegroup)|Restituisce un elenco di istanze gestite in un gruppo di risorse.|
|[Managed Instances - Update](/rest/api/sql/managedinstances/update)|Aggiorna un'istanza gestita.|
|[Operazioni Istanza gestita-list by Istanza gestita](/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|Ottiene un elenco di operazioni di gestione eseguite nell'istanza gestita.|
|[Operazioni di Istanza gestita-Get](/rest/api/sql/managedinstanceoperations/get)|Ottiene l'operazione di gestione specifica eseguita nell'istanza gestita.|
|[Operazioni di Istanza gestita-Annulla](/rest/api/sql/managedinstanceoperations/cancel)|Annulla l'operazione di gestione specifica eseguita nell'istanza gestita.|

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla migrazione di un database SQL Server in Azure, vedere [Migrazione al database SQL di Azure](../database/migrate-to-database-from-sql-server.md).
- Per informazioni sulle funzionalità supportate, vedere [Azure SQL Database features](../database/features-comparison.md) (Funzioni del database SQL di Azure).