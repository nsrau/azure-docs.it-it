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
ms.openlocfilehash: fb83e957a206bff29132973d2dd3e9a7b5f9f060
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Report in Azure Multi-Factor Authentication

Azure multi-Factor Authentication fornisce diversi report che può essere utilizzato per la propria organizzazione accessibile tramite il portale di Azure. La tabella seguente elenca i report disponibili:

| Report | Località | DESCRIZIONE |
|:--- |:--- |:--- |
| Cronologia utenti bloccati | Azure AD > Server di autenticazione a più fattori > Blocca/Sblocca utenti | Mostra la cronologia delle richieste di blocco o sblocco degli utenti. |
| Avvisi di illecito e di utilizzo | Azure AD > accessi | Vengono fornite informazioni sull'utilizzo complessivo, riepilogo utenti e i dettagli dell'utente; nonché una cronologia degli avvisi di illecito inviati durante l'intervallo di date specificato. |
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
