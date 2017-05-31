---
title: "Azure AD Connect: risoluzione dei problemi relativi alla funzionalità Seamless Single Sign-On | Microsoft Docs"
description: "Questo argomento descrive come risolvere i problemi della funzionalità Seamless Single Sign-On di Azure Active Directory (Seamless SSO di Azure AD)."
services: active-directory
keywords: "che cos&quot;è Azure AD Connect, installare Active Directory, componenti richiesti per Azure AD, SSO, Single Sign-On"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: a543be452abbbe3057a5e275612968cd52c8b7aa
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017

---

# <a name="how-to-troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Come risolvere i problemi relativi alla funzionalità Seamless Single Sign-On di Azure Active Directory

## <a name="known-issues"></a>Problemi noti

- Se si esegue la sincronizzazione di più di 30 foreste di Active Directory con Azure AD Connect, la procedura guidata usata per configurare la funzionalità Seamless SSO non funziona correttamente. Per risolvere il problema, è possibile [abilitare manualmente](#manual-reset-of-azure-ad-seamless-sso) la funzionalità Seamless SSO nel tenant in uso.
- Aggiunta degli URL del servizio Azure AD (https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net) all'area dei "Siti attendibili" anziché all'area "Intranet locale".

## <a name="troubleshooting-checklist"></a>Elenco di controllo per la risoluzione dei problemi

Per la risoluzione dei problemi relativi alla funzionalità Seamless SSO di Azure AD, usare l'elenco di controllo seguente:

1. Controllare se è abilitata la funzionalità Seamless SSO nel tenant nello strumento Azure AD Connect. Se non è possibile abilitare la funzionalità (ad esempio a causa di una porta bloccata), assicurarsi che tutti i [prerequisiti](active-directory-aadconnect-sso.md#pre-requisites) siano soddisfatti. Se i problemi con l'abilitazione della funzionalità persistono, contattare il supporto Microsoft.
2. Entrambi gli URL di assistenza, https://autologon.microsoftazuread-sso.com e https://aadg.windows.net.nsatc.net, sono definiti all'interno dell'area Intranet.
3. Assicurarsi che il desktop aziendale sia aggiunto al dominio AD.
4. Assicurarsi che l'utente sia connesso al desktop mediante un account del dominio AD.
5. Assicurarsi che l'account dell'utente sia presente in una foresta di Active Directory in cui è stata configurata la funzionalità Seamless SSO.
6. Assicurarsi che il computer sia connesso alla rete aziendale.
7. Assicurarsi che l'ora del computer sia sincronizzata con quella di Active Directory e dei controller di dominio e che si discosti di un massimo di 5 minuti da esse.
8. Eliminare i ticket Kerberos dal loro desktop. È possibile farlo eseguendo il comando **klist purge** al prompt dei comandi. I ticket Kerberos degli utenti sono in genere validi per 12 ore. Possono tuttavia essere configurati in modo diverso in Active Directory.
9. Esaminare i log di console del browser (in "Strumenti di sviluppo") per determinare i potenziali problemi.
10. Esaminare anche i [log del controller di dominio](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Log del controller di dominio

Se il controllo delle operazioni non riuscite è abilitato sul controller di dominio, ogni volta che un utente esegue l'accesso utilizzando la funzionalità Seamless SSO una voce di sicurezza (evento 4769 associato con l'account computer **AzureADSSOAcc$**) viene registrata nel log eventi. È possibile trovare questi eventi di sicurezza usando la query seguente:

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-azure-ad-seamless-sso"></a>Reimpostare manualmente la funzionalità Seamless SSO di Azure Active Directory

Se il problema persiste, seguire questa procedura per reimpostare o abilitare manualmente la funzionalità nel tenant:

### <a name="1-import-the-seamless-sso-powershell-module"></a>1. Importare il modulo di PowerShell Seamless SSO

- Scaricare e installare prima l' [Assistente per l'accesso ai Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
- Scaricare e installare quindi il [modulo di Azure Active Directory a 64 bit per Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
- Passare alla cartella `%programfiles%\Microsoft Azure Active Directory Connect`.
- Importare il modulo di PowerShell Seamless SSO usando il comando seguente: `Import-Module .\AzureADSSO.psd1`.

### <a name="2-get-the-list-of-ad-forests-on-which-seamless-sso-has-been-enabled"></a>2. Ottenere l'elenco di foreste di Active Directory in cui è stata abilitata la funzionalità Seamless SSO

- In PowerShell eseguire la chiamata a `New-AzureADSSOAuthenticationContext`. Dovrebbe essere visualizzata una finestra popup per l'immissione delle credenziali dell'amministratore del tenant di Azure AD.
- Eseguire la chiamata a `Get-AzureADSSOStatus`. Verrà visualizzato l'elenco di foreste di Active Directory, ovvero l'elenco "Domini", in cui è stata abilitata questa funzionalità.

### <a name="3-disable-seamless-sso-for-each-ad-forest-that-it-was-set-it-up-on"></a>3. Disabilitare la funzionalità Seamless SSO per ogni foresta di Active Directory in cui era impostata

- In PowerShell eseguire la chiamata a `New-AzureADSSOAuthenticationContext`. Dovrebbe essere visualizzata una finestra popup per l'immissione delle credenziali dell'amministratore del tenant di Azure AD.
- Eseguire la chiamata a `$creds = Get-Credential`. Dovrebbe essere visualizzata una finestra popup per l'immissione delle credenziali dell'amministratore di dominio per la foresta di Active Directory desiderata.
- Eseguire la chiamata a `Disable-AzureADSSOForest -OnPremCredentials $creds`. Questa operazione rimuoverà l'account del computer AZUREADSSOACCT dal controller di dominio locale e disabiliterà questa funzionalità per la foresta di Active Directory specifica.
- Ripetere i passaggi precedenti per ogni foresta di Active Directory in cui è stata configurata la funzionalità.

### <a name="4-enable-seamless-sso-for-each-ad-forest"></a>4. Abilitare la funzionalità Seamless SSO per ogni foresta di Active Directory

- Eseguire la chiamata a `New-AzureADSSOAuthenticationContext`. Dovrebbe essere visualizzata una finestra popup per l'immissione delle credenziali dell'amministratore del tenant di Azure AD.
- Eseguire la chiamata a `Enable-AzureADSSOForest`. Dovrebbe essere visualizzata una finestra popup per l'immissione delle credenziali dell'amministratore di dominio per la foresta di Active Directory desiderata.
- Ripetere i passaggi precedenti per ogni foresta di Active Directory in cui si desidera configurare la funzionalità.

### <a name="5-enable-seamless-sso-on-your-tenant"></a>5. Abilitare la funzionalità Seamless SSO nel tenant

- Eseguire la chiamata a `New-AzureADSSOAuthenticationContext`. Dovrebbe essere visualizzata una finestra popup per l'immissione delle credenziali dell'amministratore del tenant di Azure AD.
- Eseguire la chiamata a `Enable-AzureADSSO` e digitare "true" al prompt `Enable: ` per attivare la funzionalità nel tenant in uso.

