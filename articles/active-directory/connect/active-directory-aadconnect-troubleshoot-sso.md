---
title: 'Azure AD Connect: risolvere i problemi relativi all''accesso Single Sign-On facile | Microsoft Docs'
description: Questo argomento descrive come risolvere i problemi dell'accesso Single Sign-On (SSO) facile di Azure Active Directory.
services: active-directory
keywords: "che cos'è Azure AD Connect, installare Active Directory, componenti richiesti per Azure AD, SSO, Single Sign-On"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 39dd859d60e7f1dcf697e3c59b8f084e400bbae0
ms.contentlocale: it-it
ms.lasthandoff: 07/25/2017

---

# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Risolvere i problemi relativi all'accesso Single Sign-On facile di Azure Active Directory

Questo articolo consente di trovare informazioni utili per risolvere i problemi comuni relativi all'accesso Single Sign-On facile di Azure AD.

## <a name="known-issues"></a>Problemi noti

- Se si esegue la sincronizzazione di 30 o più foreste di Active Directory, non è possibile abilitare l'accesso SSO facile usando Azure AD Connect. Per risolvere il problema, è possibile [abilitare manualmente](#manual-reset-of-azure-ad-seamless-sso) la funzionalità nel tenant in uso.
- L'aggiunta degli URL del servizio Azure AD (https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net) alla zona "Siti attendibili" anziché alla zona "Intranet locale" impedisce agli utenti di eseguire l'accesso.
- L'accesso SSO facile non funziona in modalità di esplorazione privata in Firefox.

## <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center"></a>Motivi degli errori di accesso dell'interfaccia di amministrazione di Azure Active Directory

Un buon punto di partenza per la risoluzione dei problemi relativi all'accesso eseguito con la funzionalità di accesso SSO facile è esaminare il [report sull'attività di accesso](../active-directory-reporting-activity-sign-ins.md) nell'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com/).

![Report sugli accessi](./media/active-directory-aadconnect-sso/sso9.png)

Passare ad **Azure Active Directory** -> **Accessi** nell'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com/) e fare clic sull'attività di accesso di un utente specifico. Individuare il campo **CODICE ERRORE DI ACCESSO**. Eseguire il mapping del valore del campo a un motivo e una risoluzione dell'errore usando la tabella seguente:

|Codice dell'errore di accesso|Motivo dell'errore di accesso|Risoluzione
| --- | --- | ---
| 81001 | Il ticket Kerberos dell'utente è troppo grande. | Ridurre l'appartenenza a gruppi dell'utente e riprovare.
| 81002 | Impossibile convalidare il ticket Kerberos dell'utente. | Vedere l'[elenco di controllo per la risoluzione dei problemi](#troubleshooting-checklist).
| 81003 | Impossibile convalidare il ticket Kerberos dell'utente. | Vedere l'[elenco di controllo per la risoluzione dei problemi](#troubleshooting-checklist).
| 81004 | Tentativo di autenticazione Kerberos non riuscito. | Vedere l'[elenco di controllo per la risoluzione dei problemi](#troubleshooting-checklist).
| 81008 | Impossibile convalidare il ticket Kerberos dell'utente. | Vedere l'[elenco di controllo per la risoluzione dei problemi](#troubleshooting-checklist).
| 81009 | Impossibile convalidare il ticket Kerberos dell'utente. | Vedere l'[elenco di controllo per la risoluzione dei problemi](#troubleshooting-checklist).
| 81010 | Non è stato possibile usare l'accesso SSO facile perché il ticket Kerberos dell'utente è scaduto o non è valido. | L'utente deve eseguire l'accesso da un dispositivo aggiunto a un dominio all'interno della rete aziendale.
| 81011 | Impossibile trovare l'oggetto utente in base alle informazioni nel ticket Kerberos dell'utente. | Usare Azure AD Connect per sincronizzare le informazioni dell'utente in Azure AD.
| 81012 | L'utente che sta tentando di accedere ad Azure AD è diverso dall'utente che ha eseguito l'accesso al dispositivo. | Accedere da un altro dispositivo.
| 81013 | Impossibile trovare l'oggetto utente in base alle informazioni nel ticket Kerberos dell'utente. |Usare Azure AD Connect per sincronizzare le informazioni dell'utente in Azure AD. 

## <a name="troubleshooting-checklist"></a>Elenco di controllo per la risoluzione dei problemi

Per la risoluzione dei problemi dell'accesso SSO facile, usare il seguente elenco di controllo:

- Verificare se l'accesso SSO facile è abilitato in Azure AD Connect. Se non è possibile abilitare la funzionalità, ad esempio a causa di una porta bloccata, verificare che tutti i [prerequisiti](active-directory-aadconnect-sso-quick-start.md#step-1-check-prerequisites) siano soddisfatti.
- Verificare che entrambi gli URL di Azure AD https://autologon.microsoftazuread-sso.com e https://aadg.windows.net.nsatc.net facciano parte delle impostazioni della zona Intranet dell'utente.
- Verificare che il dispositivo aziendale sia aggiunto al dominio AD.
- Verificare che l'utente sia connesso al dispositivo con un account del dominio AD.
- Verificare che l'account dell'utente sia presente in una foresta di Active Directory in cui è stato configurato l'accesso SSO facile.
- Verificare che il dispositivo sia connesso alla rete aziendale.
- Verificare che l'ora del dispositivo sia sincronizzata con quella di Active Directory e dei controller di dominio e che si discosti al massimo di 5 minuti dalle stesse.
- Indicare i ticket Kerberos presenti nel dispositivo usando il comando **klist** da un prompt dei comandi. Verificare se sono presenti i ticket emessi per l'account del computer `AZUREADSSOACCT`. I ticket Kerberos degli utenti sono in genere validi per 12 ore. È possibile che siano in uso impostazioni diverse in Active Directory.
- Eliminare i ticket Kerberos dal dispositivo usando il comando **klist purge** e riprovare.
- Esaminare i registri della console del browser (in "Strumenti di sviluppo") per determinare se esistono problemi relativi a JavaScript.
- Esaminare anche i [log del controller di dominio](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Log del controller di dominio

Se il controllo delle operazioni non riuscite è abilitato nel controller di dominio, ogni volta che un utente esegue l'accesso usando la funzionalità di accesso SSO facile, viene registrata una voce di sicurezza nel registro eventi. È possibile trovare questi eventi di sicurezza usando la query seguente (cercare l'evento **4769** associato all'account computer **AzureADSSOAcc$**):

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-azure-ad-seamless-sso"></a>Reimpostare manualmente la funzionalità Seamless SSO di Azure Active Directory

Se il problema persiste, è possibile reimpostare manualmente la funzionalità nel tenant. Seguire questa procedura nel server locale in cui si esegue Azure AD Connect:

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Passaggio 1: importare il modulo di PowerShell per l'accesso SSO facile

1. Scaricare e installare prima l'[Assistente per l'accesso ai Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Scaricare e installare quindi il [modulo di Azure Active Directory a 64 bit per Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Passare alla cartella `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Importare il modulo di PowerShell Seamless SSO usando il comando seguente: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-ad-forests-on-which-seamless-sso-has-been-enabled"></a>Passaggio 2: ottenere l'elenco di foreste di Active Directory in cui è stata abilitato l'accesso SSO facile

1. Eseguire PowerShell come amministratore. In PowerShell eseguire la chiamata a `New-AzureADSSOAuthenticationContext`. Quando richiesto, immettere le credenziali di amministratore globale del tenant.
2. Eseguire la chiamata a `Get-AzureADSSOStatus`. ll comando consente di visualizzare l'elenco di foreste di Active Directory, ovvero l'elenco "Domini", in cui è stata abilitata questa funzionalità.

### <a name="step-3-disable-seamless-sso-for-each-ad-forest-that-it-was-set-it-up-on"></a>Passaggio 3: disabilitare l'accesso SSO facile per ogni foresta di Active Directory in cui era impostato

1. Eseguire la chiamata a `$creds = Get-Credential`. Quando richiesto, immettere le credenziali dell'amministratore di dominio per la foresta di Active Directory da usare.
2. Eseguire la chiamata a `Disable-AzureADSSOForest -OnPremCredentials $creds`. Questo comando rimuove l'account computer `AZUREADSSOACCT` dal controller di dominio locale per questa foresta di Active Directory specifica.
3. Ripetere i passaggi precedenti per ogni foresta di Active Directory in cui è stata configurata la funzionalità.

### <a name="step-4-enable-seamless-sso-for-each-ad-forest"></a>Passaggio 4: abilitare l'accesso SSO facile per ogni foresta di Active Directory

1. Eseguire la chiamata a `Enable-AzureADSSOForest`. Quando richiesto, immettere le credenziali dell'amministratore di dominio per la foresta di Active Directory da usare.
2. Ripetere i passaggi precedenti per ogni foresta di Active Directory in cui si vuole configurare la funzionalità.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Passaggio 5. Abilitare la funzionalità nel tenant

Eseguire la chiamata a `Enable-AzureADSSO` e digitare "true" al prompt `Enable: ` per attivare la funzionalità nel tenant in uso.

