---
title: Rapporto di accesso e uso per Azure MFA | Microsoft Docs
description: Viene descritto come usare la funzionalità dei report di Azure Multi-Factor Authentication.
services: multi-factor-authentication
documentationcenter: ''
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
ms.openlocfilehash: 3a29d1b17924b519167f362e6116f405e182bf75
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Report in Azure Multi-Factor Authentication

Azure Multi-Factor Authentication offre diversi report che possono essere usati dall'utente e dall'organizzazione e ai quali è possibile accedere tramite il portale di Azure. La tabella seguente elenca i report disponibili:

| Report | Località | DESCRIZIONE |
|:--- |:--- |:--- |
| Cronologia utenti bloccati | Azure AD > Server MFA > Blocca/Sblocca utenti | Consente di visualizzare la cronologia delle richieste di blocco o sblocco degli utenti. |
| Avvisi di illecito e utilizzo | Azure AD > Accessi | Fornisce informazioni su utilizzo complessivo, riepilogo utenti e dettagli utente; nonché una cronologia degli avvisi di illecito inviati durante l'intervallo di date specificato. |
| Utilizzo di componenti locali | Azure AD > Server MFA > Report attività | Fornisce informazioni sull'utilizzo complessivo di Multi-Factor Authentication tramite l'estensione del server dei criteri di rete, ADFS e il server MFA. |
| Cronologia utenti bypass | Azure AD > Server MFA > Bypass monouso | Fornisce una cronologia delle richieste di bypass di Multi-Factor Authentication per un utente. |
| Stato del server | Azure AD > Server MFA > Stato del server | Consente di visualizzare lo stato dei server di Multi-Factor Authentication associati all'account. |

## <a name="view-reports"></a>Visualizzazione dei report 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra, selezionare **Azure Active Directory** > **Server MFA**.
3. Selezionare il report che si vuole visualizzare.

   <center>![Cloud](./media/howto-mfa-reporting/report.png)</center>

## <a name="powershell-reporting"></a>Report di PowerShell

Identificare gli utenti che hanno eseguito la registrazione per MFA usando il codice di PowerShell seguente.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identificare gli utenti che non hanno eseguito la registrazione per MFA usando il codice di PowerShell seguente.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>Passaggi successivi

* [Per gli utenti](../../multi-factor-authentication/end-user/multi-factor-authentication-end-user.md)
* [Dove eseguire la distribuzione](concept-mfa-whichversion.md)
