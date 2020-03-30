---
title: Cmdlet di Azure AD PowerShell per la creazione di report Documenti Microsoft
description: Riferimento dei cmdlet di Azure AD PowerShell per la creazione di report.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/12/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2192c472e00d123780ec6bc5574e7b9fe326258b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495318"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Cmdlet di Azure AD PowerShell per la creazione di report

> [!NOTE] 
> Questi cmdlet di Powershell attualmente funzionano solo con il modulo di anteprima di [Azure AD.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing) Si prega di notare che il modulo di anteprima non è consigliato per l'uso in produzione. 

Per installare la versione di anteprima pubblica, utilizzare quanto segue. 

```powershell
Install-module AzureADPreview
```
Per altre informazioni su come connettersi ad Azure AD tramite Powershell, vedere l'articolo [Azure AD Powershell for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).  

Con i report di Azure Active Directory (Azure AD) è possibile ottenere dettagli sulle attività relative a tutte le operazioni di scrittura nella direzione (log di controllo) e ai dati di autenticazione (log di accesso). Anche se le informazioni sono disponibili tramite l'API MS Graph, è ora possibile recuperare gli stessi dati usando i cmdlet PowerShell di Azure AD per la creazione di report.

In questo articolo viene fornita una panoramica dei cmdlet di PowerShell da utilizzare per i log di controllo e i log di accesso.

## <a name="audit-logs"></a>Log di controllo

[I log](concept-audit-logs.md) di controllo forniscono la tracciabilità tramite log per tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.

È possibile accedere ai log di controllo utilizzando il cmdlet 'Get-AzureADAuditDirectoryLogs.You get access to the audit logs using the 'Get-AzureADAuditDirectoryLogs cmdlet.


| Scenario                      | Comando di PowerShell |
| :--                           | :--                |
| Nome visualizzato dell'applicazione      | Get-AzureADAuditDirectoryLogs -Filtro "initiatedBy/app/displayName eq 'Azure AD Cloud Sync'" |
| Category                      | Get-AzureADAuditDirectoryLogs -Filtro "categoria eq 'Gestione applicazioni'" |
| Data attività Ora            | Get-AzureADAuditDirectoryLogs -Filtro "activityDateTime gt 2019-04-18" |
| Tutte le precedenti              | Get-AzureADAuditDirectoryLogs -Filter "initiatedBy/app/displayName eq 'Azure AD Cloud Sync' e categoria eq 'Application Management' e activityDateTime gt 2019-04-18"|


L'immagine seguente mostra un esempio per questo comando. 

![Pulsante per il riepilogo dati](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Log di accesso

I log di [accesso](concept-sign-ins.md) forniscono informazioni sull'utilizzo delle applicazioni gestite e delle attività di accesso degli utenti.

È possibile accedere ai log di accesso utilizzando il cmdlet 'Get-AzureADAuditSignInLogs.You get access to the sign-in logs using the 'Get-AzureADAuditSignInLogs cmdlet.


| Scenario                      | Comando di PowerShell |
| :--                           | :--                |
| Nome utente visualizzato             | Get-AzureADAuditSignInLogs -Filter "userDisplayName eq 'Timothy Perkins'" |
| Crea data ora              | Get-AzureADAuditSignInLogs -Filter "createdDateTime gt 2019-04-18T17:30:00.0" (Tutto dalle 17:30 del 4/18) |
| Stato                        | Get-AzureADAuditSignInLogs -Filtro "stato/codiceerrore eq 50105" |
| Nome visualizzato dell'applicazione      | Get-AzureADAuditSignInLogs -Filtro "appDisplayName eq 'StoreFrontStudio [wsfed abilitato]'" |
| Tutte le precedenti              | Get-AzureADAuditSignInLogs -Filter "userDisplayName eq 'Timothy Perkins' e status/errorCode ne 0 e appDisplayName eq 'StoreFrontStudio [wsfed abilitato]'" |


L'immagine seguente mostra un esempio per questo comando. 

![Pulsante per il riepilogo dati](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dei report di Azure AD](overview-reports.md).
- [Report dei registri di controllo](concept-audit-logs.md). 
- [Accesso programmatico ai report di Azure AD](concept-reporting-api.md)
