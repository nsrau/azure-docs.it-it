---
title: 'Azure Active Directory Connect: risolvere i problemi relativi all''accesso Single Sign-On facile | Microsoft Docs'
description: Questo argomento descrive come risolvere i problemi dell'accesso Single Sign-On (SSO) facile di Azure Active Directory
services: active-directory
keywords: "che cos'è Azure AD Connect, installare Active Directory, componenti richiesti per Azure AD, SSO, Single Sign-On"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: billmath
ms.openlocfilehash: aa28431c5926656ae97ded3f23b83f2a91c60487
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Risolvere i problemi relativi all'accesso Single Sign-On facile di Azure Active Directory

Questo articolo consente di trovare informazioni utili per risolvere i problemi comuni relativi all'accesso Single Sign-On (SSO) facile di Azure Active Directory (Azure AD).

## <a name="known-problems"></a>Problemi noti

- In alcuni casi, l'abilitazione dell'accesso Single Sign-On facile può richiedere fino a 30 minuti.
- Se si disabilita e si abilita di nuovo l'accesso Single Sign-On facile nel tenant, gli utenti non potranno usare l'accesso Single Sign-On fino alla scadenza dei ticket Kerberos memorizzati nella cache, validi in genere per 10 ore.
- Non è disponibile il supporto per il browser Edge.
- L'avvio dei client Office, soprattutto in scenari di computer condivisi, prevede procedure di accesso aggiuntive per gli utenti. Gli utenti devono immettere i nomi utente di frequente, ma non le relative password.
- Se l'accesso SSO facile ha esito positivo, l'utente non ha la possibilità di scegliere **Mantieni l'accesso**. A causa di questo comportamento, gli scenari di mapping di SharePoint e OneDrive non funzionano.
- L'accesso SSO facile non funziona in modalità di esplorazione privata in Firefox.
- L'accesso Single Sign-On facile non funziona in Internet Explorer quando è attiva la modalità di protezione avanzata.
- L'accesso Single Sign-On facile non funziona nei browser per dispositivi mobili basati su iOS e Android.
- Se si esegue la sincronizzazione di 30 o più foreste di Active Directory, non è possibile abilitare l'accesso SSO facile usando Azure AD Connect. Per risolvere il problema, è possibile [abilitare manualmente](#manual-reset-of-azure-ad-seamless-sso) la funzionalità nel tenant in uso.
- L'aggiunta degli URL del servizio Azure AD (https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net) alla zona Siti attendibili invece che alla zona Intranet locale *impedisce agli utenti di eseguire l'accesso*.

## <a name="check-the-status-of-the-feature"></a>Controllare lo stato della funzionalità

Assicurarsi che la funzionalità di accesso SSO facile sia ancora **abilitata** nel tenant. Per verificare lo stato, passare al riquadro **Azure AD Connect** nell'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com/).

![Interfaccia di amministrazione di Azure Active Directory: riquadro Azure AD Connect](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Motivi degli errori di accesso nell'interfaccia di amministrazione di Azure Active Directory (necessaria licenza Premium)

Se al tenant è associata una licenza di Azure AD Premium, è anche possibile esaminare il [report delle attività di accesso](../active-directory-reporting-activity-sign-ins.md) nell'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com/).

![Interfaccia di amministrazione di Azure Active Directory: report sugli accessi](./media/active-directory-aadconnect-sso/sso9.png)

Passare ad **Azure Active Directory** > **Accessi** nell'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com/) e quindi fare clic sull'attività di accesso di un utente specifico. Individuare il campo **CODICE ERRORE DI ACCESSO**. Eseguire il mapping del valore del campo a un motivo e una risoluzione dell'errore usando la tabella seguente:

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
| 81012 | L'utente che sta tentando di accedere ad Azure AD è diverso dall'utente che ha eseguito l'accesso al dispositivo. | L'utente deve eseguire l'accesso da un altro dispositivo.
| 81013 | Impossibile trovare l'oggetto utente in base alle informazioni nel ticket Kerberos dell'utente. |Usare Azure AD Connect per sincronizzare le informazioni dell'utente in Azure AD. 

## <a name="troubleshooting-checklist"></a>Elenco di controllo per la risoluzione dei problemi

Per la risoluzione dei problemi dell'accesso SSO facile, usare il seguente elenco di controllo:

- Verificare se l'accesso SSO facile è abilitato in Azure AD Connect. Se non è possibile abilitare la funzionalità, ad esempio a causa di una porta bloccata, verificare che tutti i [prerequisiti](active-directory-aadconnect-sso-quick-start.md#step-1-check-the-prerequisites) siano soddisfatti.
- Se nel tenant sono stati abilitati sia [Aggiunta ad Azure AD](../active-directory-azureadjoin-overview.md) che l'accesso Single Sign-On facile, assicurarsi che il problema non dipenda da Aggiunta ad Azure AD. SSO da Aggiunta ad Azure AD ha la precedenza su SSO facile se il dispositivo è sia registrato con Azure AD che aggiunto a un dominio. Con SSO da Aggiunta ad Azure AD l'utente visualizza un riquadro di accesso con il messaggio "Connesso a Windows".
- Verificare che entrambi gli URL di Azure AD https://autologon.microsoftazuread-sso.com e https://aadg.windows.net.nsatc.net facciano parte delle impostazioni della zona Intranet dell'utente.
- Verificare che il dispositivo aziendale sia aggiunto al dominio Active Directory.
- Verificare che l'utente sia connesso al dispositivo tramite un account di dominio di Active Directory.
- Verificare che l'account dell'utente sia presente in una foresta di Active Directory in cui è stato configurato l'accesso SSO facile.
- Verificare che il dispositivo sia connesso alla rete aziendale.
- Verificare che l'ora del dispositivo sia sincronizzata con quella di Active Directory e dei controller di dominio e che si discosti al massimo di 5 minuti dalle stesse.
- Indicare i ticket Kerberos presenti nel dispositivo usando il comando `klist` da un prompt dei comandi. Verificare se sono presenti i ticket emessi per l'account del computer `AZUREADSSOACCT`. I ticket Kerberos degli utenti sono in genere validi per 10 ore. È possibile che siano in uso impostazioni diverse in Active Directory.
- Se si è disabilitato e abilitato di nuovo l'accesso Single Sign-On facile nel tenant, gli utenti non potranno usare l'accesso Single Sign-On fino alla scadenza dei ticket Kerberos memorizzati nella cache.
- Eliminare i ticket Kerberos dal dispositivo usando il comando `klist purge` e riprovare.
- Esaminare i log della console del browser (in **Strumenti di sviluppo**) per determinare se esistono problemi relativi a JavaScript.
- Esaminare i [log del controller di dominio](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Log del controller di dominio

Se si abilita il controllo delle operazioni riuscite nel controller di dominio, ogni volta che un utente esegue l'accesso usando la funzionalità di accesso SSO facile, viene registrata una voce di sicurezza nel log eventi. È possibile trovare questi eventi di sicurezza usando la query seguente. Cercare l'evento **4769** associato all'account computer **AzureADSSOAcc$**.

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Reimpostazione manuale della funzionalità

Se il problema persiste, è possibile reimpostare manualmente la funzionalità nel tenant. Seguire questa procedura nel server locale in cui si esegue Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Passaggio 1: importare il modulo di PowerShell per l'accesso SSO facile

1. Scaricare e installare l'[Assistente per l'accesso ai Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Scaricare e installare il [modulo di Azure Active Directory a 64 bit per Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Passare alla cartella `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Importare il modulo di PowerShell Seamless SSO usando il comando seguente: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Passaggio 2: ottenere l'elenco di foreste di Active Directory in cui è stata abilitato l'accesso SSO facile

1. Eseguire PowerShell come amministratore. In PowerShell eseguire la chiamata a `New-AzureADSSOAuthenticationContext`. Quando richiesto, immettere le credenziali di amministratore globale del tenant.
2. Eseguire la chiamata a `Get-AzureADSSOStatus`. Questo comando consente di visualizzare l'elenco di foreste di Active Directory, ovvero l'elenco "Domini", in cui è stata abilitata questa funzionalità.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Passaggio 3: disabilitare l'accesso SSO facile per ogni foresta di Active Directory in cui la funzionalità è configurata

1. Eseguire la chiamata a `$creds = Get-Credential`. Quando richiesto, immettere le credenziali dell'amministratore di dominio per la foresta di Active Directory da usare.
2. Eseguire la chiamata a `Disable-AzureADSSOForest -OnPremCredentials $creds`. Questo comando rimuove l'account computer `AZUREADSSOACCT` dal controller di dominio locale per questa foresta di Active Directory specifica.
3. Ripetere la procedura precedente per ogni foresta di Active Directory in cui la funzionalità è configurata.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Passaggio 4: abilitare l'accesso SSO facile per ogni foresta di Active Directory

1. Eseguire la chiamata a `Enable-AzureADSSOForest`. Quando richiesto, immettere le credenziali dell'amministratore di dominio per la foresta di Active Directory da usare.
2. Ripetere la procedura precedente per ogni foresta di Active Directory in cui si desidera configurare la funzionalità.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Passaggio 5. Abilitare la funzionalità nel tenant

Per abilitare la funzionalità nel tenant corrente, eseguire la chiamata a `Enable-AzureADSSO` e immettere **true** al prompt `Enable:`.
