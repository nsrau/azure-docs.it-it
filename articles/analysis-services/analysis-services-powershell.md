---
title: Gestire Azure Analysis Services con PowerShell | Documentazione Microsoft
description: Vengono descritti i cmdlet di PowerShell di Azure Analysis Services per attività amministrative comuni, ad esempio la creazione di server, la sospensione delle operazioni o la modifica del livello di servizio.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2c8f4c0541d97a189087af692658cfe794eaaf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572702"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Gestire Azure Analysis Services con PowerShell

Questo articolo descrive i cmdlet di PowerShell usati per eseguire le attività di gestione del server e del database Azure Analysis Services. 

Attività di gestione delle risorse del server, ad esempio la creazione o l'eliminazione di un server, la sospensione o la ripresa delle operazioni del server o la modifica del livello di servizio (livello) usano i cmdlet di Azure Analysis Services.Server resource management tasks like creating or deleting a server, suspending or resuming server operations, or changing the service level (tier) use Azure Analysis Services cmdlets. Le altre attività per la gestione dei database, ad esempio l'aggiunta o la rimozione dei membri di un ruolo, l'elaborazione o il partizionamento, usano i cmdlet inclusi nello stesso modulo SqlServer di SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Autorizzazioni

Per la maggior parte delle attività di PowerShell, sono necessari i privilegi di amministratore per il server Analysis Services gestito. Le attività di PowerShell pianificate sono operazioni automatiche. L'account o l'entità servizio che esegue l'utilità di pianificazione deve avere i privilegi di amministratore per il server Analysis Services. 

Per le operazioni del server che usano i cmdlet di Azure PowerShell, l'account o l'account che esegue l'utilità di pianificazione deve appartenere anche al ruolo Proprietario per la risorsa nel controllo degli accessi in base al [ruolo di Azure.](../role-based-access-control/overview.md) 

## <a name="resource-and-server-operations"></a>Operazioni di risorse e server 

Modulo di installazione - [Az.AnalysisServicesInstall](https://www.powershellgallery.com/packages/Az.AnalysisServices) module - Az.AnalysisServices   
Documentazione - [Az.AnalysisServices reference](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Operazioni del database

Le operazioni del database Azure Analysis Services usano lo stesso modulo SqlServer di SQL Server Analysis Services. Non tutti i cmdlet tuttavia sono supportati in Azure Analysis Services. 

Il modulo SqlServer fornisce cmdlet di gestione database specifici dell'attività, oltre al cmdlet Invoke-ASCmd per utilizzo generico che accetta una query o uno script TMSL (Tabular Model Scripting Language). Azure Analysis Services supporta i cmdlet seguenti nel modulo SqlServer.

Modulo di installazione - [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
Documentazione - [Informazioni di riferimento su SqlServer](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Cmdlet supportati

|Cmdlet|Descrizione|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Aggiunge un membro a un ruolo del database.| 
|[Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Esegue il backup di un database di Analysis Services.|  
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Rimuove un membro da un ruolo del database.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Esegue uno script TMSL.|
|[Invoke-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Elabora un database.|  
|[Invoke-ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Elabora una partizione.| 
|[Invoke-ProcessTable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Elabora una tabella.|  
|[Merge-Partition](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Unisce una partizione.|  
|[Restore-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Ripristina un database di Analysis Services.| 
  

## <a name="related-information"></a>Informazioni correlate

* [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [Scaricare il modulo PowerShell di SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Scaricare SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Modulo SqlServer in PowerShell Gallery](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabular Model Programming for Compatibility Level 1200](https://docs.microsoft.com/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200) (Programmazione di modelli tabulari per il livello di compatibilità 1200)
