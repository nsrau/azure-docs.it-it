---
title: Creare un report della cronologia delle modifiche relative all'accesso | Microsoft Docs
description: Generare un report che elenca tutte le modifiche nell'accesso alle sottoscrizioni di Azure con il controllo degli accessi in base al ruolo negli ultimi 90 giorni.
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/03/2016
ms.author: kgremban

---
# Creare un report della cronologia delle modifiche relative all'accesso
Ogni volta che un utente concede o revoca l'accesso all'interno delle sottoscrizioni, le modifiche vengono registrate negli eventi di Azure. È possibile creare report della cronologia delle modifiche relative all'accesso per visualizzare tutte le modifiche degli ultimi 90 giorni.

## Creare un rapporto con Azure PowerShell
Per creare un report della cronologia delle modifiche relative all'accesso in PowerShell, usare il comando `Get-AzureRMAuthorizationChangeLog`. Ulteriori dettagli su questo cmdlet sono disponibili in [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureRM.Storage/1.0.6/Content/ResourceManagerStartup.ps1).

Quando si chiama questo comando, è possibile specificare la proprietà delle assegnazioni da elencare, ad esempio:

| Proprietà | Descrizione |
| --- | --- |
| **Azione** |Indica se l'accesso è stato concesso o revocato. |
| **Chiamante** |Proprietario responsabile della modifica all'accesso. |
| **Data** |Data e ora in cui l'accesso è stato modificato. |
| **DirectoryName** |Directory di Azure Active Directory. |
| **PrincipalName** |Nome dell'utente, del gruppo o dell'applicazione. |
| **PrincipalType** |Indica se l'assegnazione era destinata a un utente, un gruppo o un'applicazione |
| **RoleId** |GUID del ruolo concesso o revocato. |
| **RoleName** |Ruolo concesso o revocato. |
| **ScopeName** |Nome della sottoscrizione, del gruppo di risorse o della risorsa. |
| **ScopeType** |Indica se l'assegnazione era a livello di ambito della sottoscrizione, del gruppo di risorse e della risorsa. |
| **SubscriptionId** |GUID della sottoscrizione di Azure. |
| **SubscriptionName** |Nome della sottoscrizione di Azure. |

Questo comando di esempio elenca tutte le modifiche relative all'accesso nella sottoscrizione per gli ultimi 7 giorni.

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get-AzureRMAuthorizationChangeLog - Schermata](./media/role-based-access-control-configure/access-change-history.png)  

## Creare un rapporto con l’interfaccia di riga di comando di Azure
Per creare un report della cronologia delle modifiche relative all'accesso nell'interfaccia della riga di comando, usare il comando `azure role assignment changelog list`.

## Esportare in un foglio di calcolo
Per salvare il report o modificare i dati, esportare le modifiche relative all'accesso in un file CSV. Sarà quindi possibile visualizzare il report in un foglio di calcolo per la revisione.

![Log delle modifiche visualizzato come foglio di calcolo - Schermata](./media/role-based-access-control-configure/change-history-spreadsheet.png)  

## Vedere anche
* Introduzione al [Controllo degli accessi in base al ruolo di Azure](role-based-access-control-configure.md)
* Utilizzare i [ruoli personalizzati nel Controllo degli accessi in base al ruolo di Azure](role-based-access-control-custom-roles.md)

<!---HONumber=AcomDC_0810_2016-->