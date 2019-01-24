---
title: 'Esempio di PowerShell: aggiornare lo schema di sincronizzazione per la sincronizzazione dati SQL | Microsoft Docs'
description: Script di esempio di Azure PowerShell per aggiornare lo schema di sincronizzazione per la sincronizzazione dati SQL
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 01/17/2019
ms.openlocfilehash: 7a83153d4439fe25d92f149b7efe5231151b4dc4
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2019
ms.locfileid: "54390731"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Usare PowerShell per aggiornare lo schema di sincronizzazione in un gruppo di sincronizzazione esistente

In questo esempio di PowerShell viene aggiornato lo schema di sincronizzazione in un gruppo di sincronizzazione dati SQL esistente. Quando si sincronizzano più tabelle, questo script consente di aggiornare lo schema di sincronizzazione in modo efficiente. Questo esempio illustra l'uso dello script **UpdateSyncSchema**, disponibile su GitHub come [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell versione 5.7.0 o successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzureRmAccount` per creare una connessione con Azure.

Per una panoramica della sincronizzazione dati SQL, vedere [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure](../sql-database-sync-data.md).

> [!IMPORTANT]
> Al momento la sincronizzazione dati SQL di Azure **non** supporta Istanza gestita di database SQL di Azure.

## <a name="sample-script"></a>Script di esempio

### <a name="example-1---add-all-tables-to-the-sync-schema"></a>Esempio 1: aggiungere tutte le tabelle allo schema di sincronizzazione

Nell'esempio seguente viene aggiornato lo schema del database e vengono aggiunte tutte le tabelle valide nel database hub allo schema di sincronizzazione.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="example-2---add-and-remove-tables-and-columns"></a>Esempio 2: aggiungere e rimuovere tabelle e colonne

L'esempio seguente vengono aggiunte la tabella `[dbo].[Table1]` e la colonna `[dbo].[Table2].[Column1]` allo schema di sincronizzazione e viene rimossa la tabella `[dbo].[Table3]`.

```powershell-interactive
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

-   Panoramica: [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure](../sql-database-sync-data.md)
-   Configurare la sincronizzazione dati
    - Nel portale - [Esercitazione: Configurare la sincronizzazione dati SQL per sincronizzare i dati tra il database SQL di Azure e SQL Server in locale](../sql-database-get-started-sql-data-sync.md)
    - Con PowerShell
        -  [Usare PowerShell per sincronizzare più database SQL di Azure](sql-database-sync-data-between-sql-databases.md)
        -  [Usare PowerShell per la sincronizzazione tra un database SQL di Azure e un database locale di SQL Server](sql-database-sync-data-between-azure-onprem.md)
-   Agente di sincronizzazione dei dati: [Agente di sincronizzazione dei dati per la sincronizzazione dati SQL di Azure](../sql-database-data-sync-agent.md)
-   Procedure consigliate: [Procedure consigliate per la sincronizzazione dati SQL di Azure](../sql-database-best-practices-data-sync.md)
-   Monitoraggio: [Monitorare la sincronizzazione dati SQL con Log Analytics](../sql-database-sync-monitor-oms.md)
-   Risoluzione dei problemi: [Risolvere i problemi della sincronizzazione dati SQL di Azure](../sql-database-troubleshoot-data-sync.md)
-   Aggiornare lo schema di sincronizzazione
    -   Con Transact-SQL: [Automatizzare la replica delle modifiche dello schema nella sincronizzazione dati SQL di Azure](../sql-database-update-sync-schema.md)

Per altre informazioni sul database SQL, vedere:

-   [Panoramica del database SQL](../sql-database-technical-overview.md)
-   [Gestione del ciclo di vita del database](https://msdn.microsoft.com/library/jj907294.aspx)
