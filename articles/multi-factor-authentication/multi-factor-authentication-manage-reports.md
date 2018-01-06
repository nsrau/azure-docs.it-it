---
title: Rapporto di accesso e uso per Azure MFA | Microsoft Docs
description: "Viene descritto come usare la funzionalità dei report di Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 3f6b33c4-04c8-47d4-aecb-aa39a61c4189
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 696f4ae3cb479a208e73e53a9a9a437caeabd294
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2018
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Report in Azure Multi-Factor Authentication

Azure multi-Factor Authentication fornisce diversi report che può essere utilizzato per la propria organizzazione accessibile tramite il portale di Azure. La tabella seguente elenca i report disponibili:

| Report | Località | DESCRIZIONE |
|:--- |:--- |:--- |
| Cronologia utenti bloccati | Azure AD > Server di autenticazione a più fattori > Blocca/Sblocca utenti | Mostra la cronologia delle richieste di blocco o sblocco degli utenti. |
| Avvisi di illecito e di utilizzo | Azure AD > accessi | Vengono fornite informazioni sull'utilizzo complessivo, riepilogo utenti e i dettagli dell'utente; nonché una cronologia degli avvisi di illecito inviati durante l'intervallo di date specificato. |
| Utilizzo per i componenti locali | Azure AD > Server di autenticazione a più fattori > Report di attività | Fornisce informazioni sull'utilizzo complessivo per l'autenticazione a più fattori tramite l'estensione dei criteri di rete, ADFS e il server di autenticazione a più fattori. |
| Cronologia utenti bypass | Azure AD > Server di autenticazione a più fattori > bypass monouso | Fornisce una cronologia delle richieste di bypass di multi-Factor Authentication per un utente. |
| Stato del server | Azure AD > Server di autenticazione a più fattori > lo stato del Server | Visualizza che lo stato dei server multi-Factor Authentication associato all'account. |

## <a name="view-reports"></a>Visualizzazione dei report 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra, selezionare **Azure Active Directory** > **Server MFA**.
3. Selezionare il report che si desidera visualizzare.

   <center>![Cloud](./media/multi-factor-authentication-manage-reports/report.png)</center>

## <a name="powershell-reporting"></a>Reporting di PowerShell

Identificare gli utenti che hanno registrato per l'autenticazione a più fattori di PowerShell che segue.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identificare gli utenti che non hanno registrato per l'autenticazione a più fattori di PowerShell che segue.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>Passaggi successivi

* [Per gli utenti](end-user/multi-factor-authentication-end-user.md)
* [Destinazione della distribuzione](multi-factor-authentication-get-started.md)
