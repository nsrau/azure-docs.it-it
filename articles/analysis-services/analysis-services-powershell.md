---
title: Gestire Azure Analysis Services con PowerShell | Microsoft Docs
description: Gestione di Azure Analysis Services con PowerShell.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 06/25/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5c347a024af385e04bfdf3631ddcbaec89df4f40
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937365"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Gestire Azure Analysis Services con PowerShell

Questo articolo descrive i cmdlet di PowerShell usati per eseguire le attività di gestione del server e del database Azure Analysis Services. 

Le attività di gestione del server, ad esempio la creazione o l'eliminazione di un server, la sospensione o la ripresa delle operazioni di un server oppure la modifica del livello di servizio, usano i cmdlet di Azure Resource Manager (risorsa) e i cmdlet di Analysis Services (server). Le altre attività per la gestione dei database, ad esempio l'aggiunta o la rimozione dei membri di un ruolo, l'elaborazione o il partizionamento, usano i cmdlet inclusi nello stesso modulo SqlServer di SQL Server Analysis Services.

## <a name="permissions"></a>Autorizzazioni
Per la maggior parte delle attività di PowerShell, sono necessari i privilegi di amministratore per il server Analysis Services gestito. Le attività di PowerShell pianificate sono operazioni automatiche. L'account o l'entità servizio che esegue l'utilità di pianificazione deve avere i privilegi di amministratore per il server Analysis Services. 

Per le operazioni server con i cmdlet AzureRm, anche l'account o l'account che esegue l'utilità di pianificazione deve appartenere al ruolo di proprietario per la risorsa in [il controllo degli accessi in base al ruolo di Azure (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-management-operations"></a>Operazioni di gestione risorse 
Modulo - [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices)

|Cmdlet|DESCRIZIONE| 
|------------|-----------------| 
|[Get-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/get-azurermanalysisservicesserver)|Ottiene i dettagli di un'istanza del server.|  
|[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver)|Crea un'istanza del server.|   
|[New-AzureRmAnalysisServicesFirewallConfig](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesfirewallconfig)|Crea una nuova configurazione di firewall di Analysis Services.|   
|[New-AzureRmAnalysisServicesFirewallRule](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesfirewallrule)|Crea una nuova regola di firewall di Analysis Services.|   
|[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/remove-azurermanalysisservicesserver)|Rimuove un'istanza del server.|  
|[Resume-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver)|Riprende un'istanza del server.|  
|[Suspend-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver)|Sospende un'istanza del server.| 
|[Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)|Modifica un'istanza del server.|   
|[Test-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/test-azurermanalysisservicesserver)|Testa l'esistenza di un'istanza del server.| 

## <a name="server-management-operations"></a>Operazioni di gestione del server

Modulo - [Azure.AnalysisServices](https://www.powershellgallery.com/packages/Azure.AnalysisServices)

|Cmdlet|DESCRIZIONE| 
|------------|-----------------| 
|[Add-AzureAnalysisServicesAccount](/powershell/module/azure.analysisservices/add-azureanalysisservicesaccount)|Aggiunge un account autenticato da usare per le richieste server con cmdlet di Azure Analysis Services.| 
|[Export-AzureAnalysisServicesInstance]()|Esporta un logo da un'istanza del server di Analysis Services nell'ambiente attualmente connesso specificato nel comando Add-AzureAnalysisServicesAccount|  
|[Restart-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Riavvia un'istanza del server di Analysis Services nell'ambiente attualmente connesso specificato nel comando Add-AzureAnalysisServicesAccount.|  
|[Sync-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Sincronizza un database specificato nell'istanza specificata del server Analysis Services con tutte le istanze di scalabilità delle query nell'ambiente attualmente connesso specificato nel comando Add-AzureAnalysisServicesAccount|  

## <a name="database-operations"></a>Operazioni del database

Le operazioni del database Azure Analysis Services usano lo stesso [modulo SqlServer](https://www.powershellgallery.com/packages/SqlServer) di SQL Server Analysis Services. Non tutti i cmdlet tuttavia sono supportati in Azure Analysis Services. Per altre informazioni vedere [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell).

Il modulo SqlServer fornisce cmdlet di gestione database specifici dell'attività, oltre al cmdlet Invoke-ASCmd per utilizzo generico che accetta una query o uno script TMSL (Tabular Model Scripting Language). Azure Analysis Services supporta i cmdlet seguenti nel modulo SqlServer.

  
|Cmdlet|DESCRIZIONE|
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

* [Scaricare il modulo PowerShell di SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Scaricare SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Modulo SqlServer in PowerShell Gallery](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabular Model Programming for Compatibility Level 1200](https://msdn.microsoft.com/library/mt712541.aspx) (Programmazione di modelli tabulari per il livello di compatibilità 1200)
