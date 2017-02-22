---
title: Gestire Azure Analysis Services con PowerShell | Documentazione Microsoft
description: Gestione di Azure Analysis Services con PowerShell.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: ef3f31c633eeba92f343e2126626bd029aebbf64
ms.openlocfilehash: 170657601a0ea6b0c0ebabfd34befdce290cebd8


---

# <a name="manage-azure-analysis-services-with-powershell"></a>Gestire Azure Analysis Services con PowerShell

Questo articolo descrive i cmdlet di PowerShell usati per eseguire le attività di gestione del server e del database Azure Analysis Services. 

Le attività di gestione del server, ad esempio la creazione o l'eliminazione di un server, la sospensione o la ripresa delle operazioni di un server oppure la modifica del livello di servizio, usano i cmdlet di Azure Resource Manager (AzureRM). Le altre attività per la gestione dei database, ad esempio l'aggiunta o la rimozione dei membri di un ruolo, l'elaborazione o il partizionamento, usano gli stessi cmdlet nel modulo [SQLASCMDLETS](https://msdn.microsoft.com/library/hh758425.aspx) di SQL Server Analysis Services.

## <a name="permissions"></a>Autorizzazioni
Per la maggior parte delle attività di PowerShell, sono necessari i privilegi di amministratore per il server Analysis Services gestito. Le attività di PowerShell pianificate sono operazioni automatiche. L'account che esegue l'utilità di pianificazione deve avere i privilegi di amministratore per il server Analysis Services. 

Per le operazioni server con i cmdlet AzureRm, anche l'account o l'account che esegue l'utilità di pianificazione deve appartenere al ruolo di proprietario per la risorsa in [il controllo degli accessi in base al ruolo di Azure (RBAC)](../active-directory/role-based-access-control-what-is.md). 

## <a name="server-operations"></a>Operazioni del server 
I cmdlet di Azure Analysis Services sono inclusi nel modulo del componente [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices). Per installare i moduli dei cmdlet di AzureRM, vedere [Azure Resource Manager cmdlets](https://docs.microsoft.com/powershell/resourcemanager/) (Cmdlet di Azure Resource Manager) in PowerShell Gallery.

|Cmdlet|Descrizione| 
|------------|-----------------| 
|Get-AzureRmAnalysisServicesServer|Ottiene i dettagli di un'istanza del server.|  
|New-AzureRmAnalysisServicesServer|Crea una nuova istanza del server.|
|Remove-AzureRmAnalysisServicesServer|Rimuove un'istanza del server.|  
|Suspend-AzureRmAnalysisServicesServe|Sospende un'istanza del server.| 
|Resume-AzureRmAnalysisServicesServer|Riprende un'istanza del server.|  
|Set-AzureRmAnalysisServicesServer|Modifica un'istanza del server.|   
|Test-AzureRmAnalysisServicesServer|Testa l'esistenza di un'istanza del server.| 

## <a name="database-operations"></a>Operazioni del database
Le operazioni del database Azure Analysis Services usano lo stesso modulo [SQLASCMDLETS](https://msdn.microsoft.com/library/hh758425.aspx) di SQL Server Analysis Services. Non tutti i cmdlet tuttavia sono supportati per l'anteprima di Azure Analysis Services. 

Il modulo SQLASCMDLETS fornisce cmdlet di gestione database specifici dell'attività, oltre al cmdlet Invoke-ASCmd per utilizzo generico che accetta una query o uno script TMSL (Tabular Model Scripting Language). I cmdlet seguenti nel modulo SQLASCMDLETS sono supportati per l'anteprima di Azure Analysis Services.
  
|Cmdlet|Descrizione|
|------------|-----------------| 
|[Add-RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Aggiunge un membro a un ruolo del database.| 
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Rimuove un membro da un ruolo del database.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Esegue uno script TMSL.|
|[Invoke-ProcessASDatabase](https://msdn.microsoft.com/library/mt651773.aspx)|Elabora un database.|  
|[Invoke-ProcessPartition](https://msdn.microsoft.com/library/hh510164.aspx)|Elabora una partizione.| 
|[Invoke-ProcessTable](https://msdn.microsoft.com/library/mt651774.aspx)|Elabora una tabella.|  
|[Merge-Partition](https://msdn.microsoft.com/library/hh479576.aspx)|Unisce una partizione.|  
  

## <a name="related-information"></a>Informazioni correlate
* [Scripting di PowerShell in Analysis Services](https://msdn.microsoft.com/library/hh213141.aspx).
* [Tabular Model Programming for Compatibility Level 1200](https://msdn.microsoft.com/library/mt712541.aspx) (Programmazione di modelli tabulari per il livello di compatibilità 1200)


<!--HONumber=Jan17_HO5-->


