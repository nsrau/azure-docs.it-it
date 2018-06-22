---
title: Gestire Azure Analysis Services con PowerShell | Documentazione Microsoft
description: Gestione di Azure Analysis Services con PowerShell.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 05/22/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b4e819bdce971e92e4b2d99e68f51ddbf8a22182
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597468"
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

Le operazioni del database Azure Analysis Services usano lo stesso modulo [SqlServer](https://www.powershellgallery.com/packages/SqlServer) di SQL Server Analysis Services. Non tutti i cmdlet tuttavia sono supportati in Azure Analysis Services. 

Il modulo SqlServer fornisce cmdlet di gestione database specifici dell'attività, oltre al cmdlet Invoke-ASCmd per utilizzo generico che accetta una query o uno script TMSL (Tabular Model Scripting Language). Azure Analysis Services supporta i cmdlet seguenti nel modulo SqlServer.

  
|Cmdlet|DESCRIZIONE|
|------------|-----------------| 
|[Add-RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Aggiunge un membro a un ruolo del database.| 
|[Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet)|Esegue il backup di un database di Analysis Services.|  
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Rimuove un membro da un ruolo del database.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Esegue uno script TMSL.|
|[Invoke-ProcessASDatabase](https://msdn.microsoft.com/library/mt651773.aspx)|Elabora un database.|  
|[Invoke-ProcessPartition](https://msdn.microsoft.com/library/hh510164.aspx)|Elabora una partizione.| 
|[Invoke-ProcessTable](https://msdn.microsoft.com/library/mt651774.aspx)|Elabora una tabella.|  
|[Merge-Partition](https://msdn.microsoft.com/library/hh479576.aspx)|Unisce una partizione.|  
|[Restore-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet)|Ripristina un database di Analysis Services.| 
  

## <a name="related-information"></a>Informazioni correlate

* [Scaricare il modulo PowerShell di SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Scaricare SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Modulo SqlServer in PowerShell Gallery](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabular Model Programming for Compatibility Level 1200](https://msdn.microsoft.com/library/mt712541.aspx) (Programmazione di modelli tabulari per il livello di compatibilità 1200)
