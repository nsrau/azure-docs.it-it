---
title: 'Esempio di PowerShell: aggiornare lo schema di sincronizzazione per la sincronizzazione dati SQL | Microsoft Docs'
description: Script di esempio di Azure PowerShell per aggiornare lo schema di sincronizzazione per la sincronizzazione dati SQL
services: sql-database
documentationcenter: sql-database
author: jognanay
manager: craigg
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: load & move data, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 01/10/2018
ms.author: jognanay
ms.reviewer: douglasl
ms.openlocfilehash: 66bf084f585b86979e6521321daf466c571de10c
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Usare PowerShell per aggiornare lo schema di sincronizzazione in un gruppo di sincronizzazione esistente

In questo esempio di PowerShell viene aggiornato lo schema di sincronizzazione in un gruppo di sincronizzazione esistente. Quando si sincronizzano più tabelle, questo script consente di aggiornare lo schema di sincronizzazione in modo efficiente.

Questo esempio illustra l'uso dello script **UpdateSyncSchema**, disponibile su GitHub come [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

Per una panoramica della sincronizzazione dati SQL, vedere [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure (anteprima)](../sql-database-sync-data.md).
## <a name="prerequisites"></a>Prerequisiti

Questo esempio richiede il modulo Azure PowerShell 4.2 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).
 
Eseguire `Login-AzureRmAccount` per creare una connessione con Azure.

## <a name="examples"></a>Esempi

### <a name="example-1---add-all-tables-to-the-sync-schema"></a>Esempio 1: aggiungere tutte le tabelle allo schema di sincronizzazione

Nell'esempio seguente viene aggiornato lo schema del database e vengono aggiunte tutte le tabelle valide nel database hub allo schema di sincronizzazione.

```powershell
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="example-2---add-and-remove-tables-and-columns"></a>Esempio 2: aggiungere e rimuovere tabelle e colonne

L'esempio seguente vengono aggiunte la tabella `[dbo].[Table1]` e la colonna `[dbo].[Table2].[Column1]` allo schema di sincronizzazione e viene rimossa la tabella `[dbo].[Table3]`.

```powershell
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Parametri di script

Lo script **UpdateSyncSchema** include i parametri seguenti:

| Parametro | Note |
|---|---|
| $SubscriptionId | Sottoscrizione in cui viene creato il gruppo di sincronizzazione. |
| $ResourceGroupName | Gruppo di risorse in cui viene creato il gruppo di sincronizzazione.|
| $ServerName | Nome server del database hub.|
| $DatabaseName | Nome del database hub. |
| $SyncGroupName | Nome del gruppo di sincronizzazione. |
| $MemberName | Per caricare lo schema del database dal membro di sincronizzazione anziché dal database hub, specificare il nome del membro. Per caricare lo schema del database dall'hub, lasciare vuoto questo parametro. |
| $TimeoutInSeconds | Timeout durante l'aggiornamento dello schema del database da parte dello script. Il valore predefinito è 900 secondi. |
| $RefreshDatabaseSchema | Specificare se lo script deve aggiornare lo schema del database. Se lo schema del database è stato modificato rispetto alla configurazione precedente, ad esempio se è stata aggiunta una nuova tabella o una nuova colonna, è necessario aggiornarlo prima di riconfigurarlo. Il valore predefinito è false. |
| $AddAllTables | Se questo valore è true, tutte le colonne e le tabelle valide vengono aggiunte allo schema di sincronizzazione. I valori di $TablesAndColumnsToAdd e $TablesAndColumnsToRemove vengono ignorati. |
| $TablesAndColumnsToAdd | Specificare le tabelle e le colonne da aggiungere allo schema di sincronizzazione. Ogni nome di tabella o di colonna deve essere completamente delimitato dal nome dello schema. Ad esempio: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. È possibile specificare più nomi di tabella o di colonna, separati da virgole (,). |
| $TablesAndColumnsToRemove | Specificare le tabelle o le colonne da rimuovere dallo schema di sincronizzazione. Ogni nome di tabella o di colonna deve essere completamente delimitato dal nome dello schema. Ad esempio: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. È possibile specificare più nomi di tabella o di colonna, separati da virgole (,). |
|||

## <a name="script-explanation"></a>Spiegazione dello script

Lo script **UpdateSyncSchema** usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Get-AzureRmSqlSyncGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncgroup) | Restituisce informazioni su un gruppo di sincronizzazione. |
| [Update-AzureRmSqlSyncGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/update-azurermsqlsyncgroup) | Aggiorna un gruppo di sincronizzazione. |
| [Get-AzureRmSqlSyncMember](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncmember) | Restituisce informazioni su un membro di sincronizzazione. |
| [Get-AzureRmSqlSyncSchema](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncschema) | Restituisce informazioni su uno schema di sincronizzazione. |
| [Update-AzureRmSqlSyncSchema](https://docs.microsoft.com/powershell/module/azurerm.sql/update-azurermsqlsyncschema) | Aggiorna uno schema di sincronizzazione. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Per altri esempi, vedere tra gli [script di PowerShell per database SQL di Azure](../sql-database-powershell-samples.md).

Per altre informazioni sulla sincronizzazione dati SQL, vedere:

-   [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure](../sql-database-sync-data.md)
-   [Configurare la sincronizzazione dati SQL](../sql-database-get-started-sql-data-sync.md)
-   [Procedure consigliate per la sincronizzazione dati SQL di Azure](../sql-database-best-practices-data-sync.md)
-   [Monitorare la sincronizzazione dati SQL di Azure con OMS Log Analytics](../sql-database-sync-monitor-oms.md)
-   [Risolvere i problemi della sincronizzazione dati SQL di Azure](../sql-database-troubleshoot-data-sync.md)

-   Esempi di PowerShell completi che illustrano come configurare la sincronizzazione dati SQL:
    -   [Usare PowerShell per sincronizzare più database SQL di Azure](sql-database-sync-data-between-sql-databases.md)
    -   [Usare PowerShell per la sincronizzazione tra un database SQL di Azure e un database locale di SQL Server](sql-database-sync-data-between-azure-onprem.md)

-   [Scaricare la documentazione dell'API REST di sincronizzazione dati SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Per altre informazioni sul database SQL, vedere:

-   [Panoramica del database SQL](../sql-database-technical-overview.md)
-   [Gestione del ciclo di vita del database](https://msdn.microsoft.com/library/jj907294.aspx)
