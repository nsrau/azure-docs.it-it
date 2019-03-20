---
title: Riferimento all'API di gestione per Istanza gestita di database SQL di Azure | Microsoft Docs
description: Informazioni sulla creazione e sulla gestione di Istanza gestita di database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: c4f8144c3e941c673da3ecc9be7ac7f091583d4a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57864662"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Riferimento all'API di gestione per Istanza gestita di database SQL di Azure

È possibile creare e gestire Istanza gestita di database SQL di Azure usando il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure, l'API REST e Transact-SQL. Questo articolo contiene una panoramica di funzioni e API che è possibile usare per creare e configurare Istanza gestita.

## <a name="azure-portal-create-a-managed-instance"></a>Portale di Azure: Creare un'istanza gestita

Per una guida introduttiva che illustra come creare Istanza gestita di database SQL di Azure, vedere [Guida introduttiva: Creare Istanza gestita di database SQL di Azure](sql-database-managed-instance-get-started.md).

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell: Creare e gestire istanze gestite

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure PowerShell per Resource Manager è ancora supportato dal Database SQL di Azure, ma i progetti di sviluppo future è per il modulo Az.Sql. Per questi cmdlet, vedere [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo Az e nei moduli AzureRm sono sostanzialmente identici.

Per creare e gestire istanze gestite del database SQL con Azure PowerShell, usare i cmdlet di PowerShell seguenti. Se è necessario installare o aggiornare PowerShell, vedere [Installare il modulo Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Per gli script di esempio di PowerShell, vedere [Quick-start script: Create Azure SQL Managed Instance using PowerShell library](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/) (Guida introduttiva: creare Istanza gestita di database SQL di Azure tramite la libreria PowerShell).

| Cmdlet | DESCRIZIONE |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Crea Istanza gestita di database SQL di Azure |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Restituisce informazioni su Istanza gestita di database SQL di Azure|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Imposta le proprietà di Istanza gestita di database SQL di Azure|
|[Remove-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Rimuove Istanza gestita di database SQL di Azure|
|[New-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Crea un database di Istanza gestita di database SQL di Azure|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Restituisce informazioni sul database di Istanza gestita di database SQL di Azure|
|[Remove-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Rimuove un database di Istanza gestita di database SQL di Azure|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Ripristina un database di Istanza gestita di database SQL di Azure|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Interfaccia della riga di comando di Azure: Creare e gestire istanze gestite

Per creare e gestire istanze gestite con l'[interfaccia della riga di comando di Azure](/cli/azure), usare i comandi dell'[Istanza gestita di database SQL dell'interfaccia della riga di comando di Azure](/cli/azure/sql/mi) seguenti. Usare [Cloud Shell](/azure/cloud-shell/overview) per eseguire l'interfaccia della riga di comando nel browser o [installarla](/cli/azure/install-azure-cli) in macOS, Linux o Windows.

> [!TIP]
> Per una guida introduttiva dell'interfaccia della riga di comando di Azure, vedere [Working with SQL Managed Instance using Azure CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) (Uso di Istanza gestita di database SQL tramite l'interfaccia della riga di comando di Azure).

| Cmdlet | DESCRIZIONE |
| --- | --- |
|[az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Crea Istanza gestita|
|[az sql mi list](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Elenca le istanze gestite disponibili|
|[az sql mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Ottiene i dettagli per Istanza gestita|
|[az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Aggiorna Istanza gestita|
|[az sql mi delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Rimuove Istanza gestita|
|[az sql midb create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Crea un database gestito|
|[az sql midb list](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Elenca i database gestiti disponibili|
|[az sql midb restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Ripristina un database gestito|
|[az sql midb delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Rimuove un database gestito|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: Creare e gestire i database dell'istanza

Per creare e gestire un database dell'istanza dopo la creazione dell'istanza gestita, usare i comandi T-SQL seguenti. È possibile eseguire questi comandi usando il portale di Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is), [Visual Studio Code](https://code.visualstudio.com/docs) o qualsiasi altro programma in grado di connettersi a un server di database SQL di Azure e di passare comandi Transact-SQL.

> [!TIP]
> Per guide introduttive che descrivono la configurazione e la connessione a Istanza gestita tramite SQL Server Management Studio in Microsoft Windows, vedere [Guida introduttiva: Configurare una macchina virtuale di Azure per la connessione a Istanza gestita di database SQL di Azure](sql-database-managed-instance-configure-vm.md) e [Guida introduttiva: Configurare una connessione da punto a sito a Istanza gestita di database SQL di Azure da un computer locale](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> Non è possibile creare o eliminare Istanza gestita usando Transact-SQL.

| Comando | DESCRIZIONE |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Crea un nuovo database di Istanza gestita. Per creare un nuovo database è necessario essere connessi al database master.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Modifica un database di Istanza gestita di database SQL di Azure.|

## <a name="rest-api-create-and-manage-managed-instances"></a>API REST: Creare e gestire istanze gestite

Per creare e gestire istanze gestite, usare queste richieste di API REST.

| Comando | DESCRIZIONE |
| --- | --- |
|[Managed Instances - Create Or Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Crea o aggiorna Istanza gestita.|
|[Managed Instances - Delete](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Elimina Istanza gestita.|
|[Managed Instances - Get](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Ottiene Istanza gestita.|
|[Managed Instances - List](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Restituisce un elenco di istanze gestite in una sottoscrizione.|
|[Managed Instances - List By Resource Group](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Restituisce un elenco di istanze gestite in un gruppo di risorse.|
|[Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Aggiorna Istanza gestita.|

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla migrazione di un database SQL Server in Azure, vedere [Migrazione al database SQL di Azure](sql-database-single-database-migrate.md).
- Per informazioni sulle funzionalità supportate, vedere [Azure SQL Database features](sql-database-features.md) (Funzioni del database SQL di Azure).
