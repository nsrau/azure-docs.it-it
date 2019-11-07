---
title: Informazioni di riferimento sull'API di gestione per Istanza gestita di database SQL di Azure
description: Informazioni sulla creazione e sulla gestione di Istanza gestita di database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: bdc17dd11269bafa271f69ec001f8dcad484b7b8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73688183"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Riferimento all'API di gestione per Istanza gestita di database SQL di Azure

È possibile creare e gestire Istanza gestita di database SQL di Azure usando il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure, l'API REST e Transact-SQL. Questo articolo contiene una panoramica di funzioni e API che è possibile usare per creare e configurare Istanza gestita.

## <a name="azure-portal-create-a-managed-instance"></a>Portale di Azure: creare un'istanza gestita

Per una guida introduttiva che illustra come creare una Istanza gestita di database SQL di Azure, vedere [Guida introduttiva: creare una istanza gestita di database SQL di Azure](sql-database-managed-instance-get-started.md).

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell: creare e gestire istanze gestite

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato dal database SQL di Azure, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Per questi cmdlet, vedere [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo AZ e nei moduli AzureRm sono sostanzialmente identici.

Per creare e gestire istanze gestite del database SQL con Azure PowerShell, usare i cmdlet di PowerShell seguenti. Se è necessario installare o aggiornare PowerShell, vedere l'articolo su come [installare il modulo Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Per gli script di esempio di PowerShell, vedere [script di avvio rapido: creare istanza gestita SQL di Azure con la libreria di PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Cmdlet | Descrizione |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Crea Istanza gestita di database SQL di Azure |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Restituisce informazioni su Istanza gestita di database SQL di Azure|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Imposta le proprietà di Istanza gestita di database SQL di Azure|
|[Remove-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Rimuove Istanza gestita di database SQL di Azure|
|[New-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Crea un database dell'istanza gestita di database SQL di Azure|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Restituisce informazioni sul database dell'istanza gestita di database SQL di Azure|
|[Remove-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Rimuove un database dell'istanza gestita di database SQL di Azure|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Ripristina un database dell'istanza gestita di database SQL di Azure|

## <a name="azure-cli-create-and-manage-managed-instances"></a>INTERFACCIA della riga di comando di Azure: creare e gestire istanze gestite

Per creare e gestire istanze gestite con l'[interfaccia della riga di comando di Azure](/cli/azure), usare i comandi dell'[Istanza gestita di database SQL dell'interfaccia della riga di comando di Azure](/cli/azure/sql/mi) seguenti. Usare [Cloud Shell](/azure/cloud-shell/overview) per eseguire l'interfaccia della riga di comando nel browser o [installarla](/cli/azure/install-azure-cli) in macOS, Linux o Windows.

> [!TIP]
> Per una guida introduttiva dell'interfaccia della riga di comando di Azure, vedere [Working with SQL Managed Instance using Azure CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) (Uso di Istanza gestita di database SQL tramite l'interfaccia della riga di comando di Azure).

| Cmdlet | Descrizione |
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

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: creare e gestire database di istanze

Per creare e gestire un database dell'istanza dopo la creazione dell'istanza gestita, usare i comandi T-SQL seguenti. È possibile eseguire questi comandi usando il portale di Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is), [Visual Studio Code](https://code.visualstudio.com/docs) o qualsiasi altro programma in grado di connettersi a un server di database SQL di Azure e di passare comandi Transact-SQL.

> [!TIP]
> Per le guide introduttive che mostrano che è necessario configurare e connettersi a un Istanza gestita usando SQL Server Management Studio in Microsoft Windows, vedere [Guida introduttiva: configurare una macchina virtuale di Azure per connettersi a una istanza gestita di database SQL di Azure e alla](sql-database-managed-instance-configure-vm.md) [Guida introduttiva: Configurare una connessione da punto a sito a una Istanza gestita di database SQL di Azure dall'ambiente locale](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> Non è possibile creare o eliminare Istanza gestita usando Transact-SQL.

| Comando | Descrizione |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Crea un nuovo database dell'istanza gestita. Per creare un nuovo database è necessario essere connessi al database master.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Modifica un database dell'istanza gestita di database SQL di Azure.|

## <a name="rest-api-create-and-manage-managed-instances"></a>API REST: creare e gestire istanze gestite

Per creare e gestire istanze gestite, usare queste richieste di API REST.

| Comando | Descrizione |
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
