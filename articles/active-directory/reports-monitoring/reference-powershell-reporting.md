---
title: Cmdlet di Azure AD PowerShell per la creazione di report | Microsoft Docs
description: Informazioni di riferimento sui cmdlet di Azure AD PowerShell per la creazione di report.
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
ms.openlocfilehash: f962cd9bc8c975ccaef90f61f20eea4cf1e4935e
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014327"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Cmdlet di Azure AD PowerShell per la creazione di report

> [!NOTE] 
> Questi cmdlet di PowerShell funzionano attualmente solo con il modulo [Azure ad Preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing) . Si noti che il modulo di anteprima non è consigliato per l'uso in produzione. 

Con Azure Active Directory (Azure AD) report è possibile ottenere informazioni dettagliate sulle attività relative a tutte le operazioni di scrittura nella direzione (log di controllo) e i dati di autenticazione (log di accesso). Sebbene le informazioni siano disponibili tramite il API Graph MS, ora è possibile recuperare gli stessi dati utilizzando i cmdlet di Azure AD PowerShell per la creazione di report.

Questo articolo fornisce una panoramica dei cmdlet di PowerShell da usare per i log di controllo e i log di accesso.

## <a name="audit-logs"></a>Log di controllo

I [log di controllo](concept-audit-logs.md) forniscono la tracciabilità tramite i log per tutte le modifiche apportate dalle diverse funzionalità all'interno Azure ad. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.

Per ottenere l'accesso ai log di controllo, usare il cmdlet "Get-AzureADAuditDirectoryLogs".


| Scenario                      | Comando di PowerShell |
| :--                           | :--                |
| Nome visualizzato dell'applicazione      | Get-AzureADAuditDirectoryLogs-Filter "initiatedBy/app/displayName EQ" Azure AD Cloud Sync "" |
| Categoria                      | Get-AzureADAuditDirectoryLogs-Filter "Category EQ ' Gestione applicazioni" |
| Data/ora attività            | Get-AzureADAuditDirectoryLogs-Filter "activityDateTime gt 2019-04-18" |
| Tutte le precedenti              | Get-AzureADAuditDirectoryLogs-Filter "initiatedBy/app/displayName EQ" Azure AD Cloud Sync "e Category EQ" Application Management "e activityDateTime gt 2019-04-18"|


Nell'immagine seguente viene illustrato un esempio di questo comando. 

![Pulsante per il riepilogo dati](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Log di accesso

I log degli [accessi](concept-sign-ins.md) forniscono informazioni sull'utilizzo delle applicazioni gestite e delle attività di accesso degli utenti.

Per ottenere l'accesso ai log di accesso, usare il cmdlet ' Get-AzureADAuditSignInLogs '.


| Scenario                      | Comando di PowerShell |
| :--                           | :--                |
| Nome visualizzato dell'utente             | Get-AzureADAuditSignInLogs-Filter "userDisplayName EQ ' Timothy Perkins '" |
| Data/ora di creazione              | Get-AzureADAuditSignInLogs-Filter "createdDateTime gt 2019-04-18T17:30:00.0 Z" (tutto dalle 5:30 PM su 4/18) |
| Stato                        | Get-AzureADAuditSignInLogs-Filter "status/errorCode EQ 50105" |
| Nome visualizzato dell'applicazione      | Get-AzureADAuditSignInLogs-Filter "appDisplayName EQ ' StoreFrontStudio [WSFED Enabled]'" |
| Tutte le precedenti              | Get-AzureADAuditSignInLogs-Filter "userDisplayName EQ ' Timothy Perkins ' and status/errorCode ne 0 e appDisplayName EQ ' StoreFrontStudio [WSFED Enabled]'" |


Nell'immagine seguente viene illustrato un esempio di questo comando. 

![Pulsante per il riepilogo dati](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dei report di Azure AD](overview-reports.md).
- [Report dei log di controllo](concept-audit-logs.md). 
- [Accesso programmatico ai report di Azure AD](concept-reporting-api.md)
