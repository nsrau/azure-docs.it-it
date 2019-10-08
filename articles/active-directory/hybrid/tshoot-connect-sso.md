---
title: "Azure Active Directory Connect: Risolvere i problemi relativi all'accesso Single Sign-On facile | Microsoft Docs"
description: Questo argomento descrive come risolvere i problemi dell'accesso Single Sign-On (SSO) facile di Azure Active Directory
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1293bbf6d2966caf7e6e095c1721e29890a57b76
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025812"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Risolvere i problemi relativi all'accesso Single Sign-On facile di Azure Active Directory

Questo articolo consente di trovare informazioni utili per risolvere i problemi comuni relativi all'accesso Single Sign-On (SSO) facile di Azure Active Directory (Azure AD).

## <a name="known-issues"></a>Problemi noti

- In alcuni casi, l'abilitazione dell'accesso Single Sign-On facile può richiedere fino a 30 minuti.
- Se si disabilita e si abilita di nuovo l'accesso Single Sign-On facile nel tenant, gli utenti non potranno usare l'accesso Single Sign-On fino alla scadenza dei ticket Kerberos memorizzati nella cache, validi in genere per 10 ore.
- Se l'accesso SSO facile ha esito positivo, l'utente non ha la possibilità di scegliere **Mantieni l'accesso**. A causa di questo comportamento, [gli scenari di mapping di SharePoint e OneDrive](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec) non funzionano.
- I client Office 365 Win32 (Outlook, Word, Excel e altri) con le versioni 16.0.8730.xxxx e successive sono supportati tramite un flusso non interattivo. Altre versioni non sono supportate. In tali versioni, per effettuare l'accesso gli utenti immetteranno i nomi utente, ma non le password. Per OneDrive, è necessario attivare la [funzionalità di configurazione automatica di OneDrive](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) per un'esperienza di accesso automatico.
- L'accesso SSO facile non funziona in modalità di esplorazione privata in Firefox.
- L'accesso Single Sign-On facile non funziona in Internet Explorer quando è attiva la modalità di protezione avanzata.
- L'accesso Single Sign-On facile non funziona nei browser per dispositivi mobili basati su iOS e Android.
- Se un utente fa parte di troppi gruppi in Active Directory, il ticket Kerberos dell'utente sarà probabilmente troppo grande per l'elaborazione e ciò causerà l'esito negativo dell'accesso Single Sign-On facile. Le richieste HTTPS di Azure AD possono avere intestazioni con una dimensione massima di 50 KB. I ticket Kerberos devono essere più piccoli rispetto a tale dimensione per contenere altri elementi di Azure AD (in genere, 2 - 5 KB), come i cookie. Si consiglia di ridurre le appartenenze a gruppi dell'utente e riprovare.
- Se si esegue la sincronizzazione di 30 o più foreste di Active Directory, non è possibile abilitare l'accesso SSO facile usando Azure AD Connect. Per risolvere il problema, è possibile [abilitare manualmente](#manual-reset-of-the-feature) la funzionalità nel tenant in uso.
- L'aggiunta degli URL del servizio Azure AD (https://autologon.microsoftazuread-sso.com) alla zona Siti attendibili anziché alla zona Intranet locale *impedisce agli utenti di eseguire l'accesso*.
- Seamless SSO supporta i tipi di crittografia AES256_HMAC_SHA1, AES128_HMAC_SHA1 e RC4_HMAC_MD5 per Kerberos. È consigliabile che il tipo di crittografia per l'account AzureADSSOAcc $ sia impostato su AES256_HMAC_SHA1 o su uno dei tipi AES rispetto a RC4 per una maggiore sicurezza. Il tipo di crittografia viene archiviato nell'attributo msDS-SupportedEncryptionTypes dell'account nel Active Directory.  Se il tipo di crittografia dell'account AzureADSSOAcc $ è impostato su RC4_HMAC_MD5 e si desidera modificarlo in uno dei tipi di crittografia AES, assicurarsi di eseguire prima il rollover della chiave di decrittografia Kerberos dell'account AzureADSSOAcc $ come illustrato nel [documento di domande frequenti ](how-to-connect-sso-faq.md)in caso contrario, l'accesso SSO facile non sarà possibile.

## <a name="check-status-of-feature"></a>Controllare lo stato della funzionalità

Assicurarsi che la funzionalità di accesso SSO facile sia ancora **abilitata** nel tenant. Per verificare lo stato, passare al riquadro **Azure AD Connect** nell'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com/).

![Interfaccia di amministrazione di Azure Active Directory: riquadro di Azure AD Connect](./media/tshoot-connect-sso/sso10.png)

Fare clic per visualizzare tutte le foreste di AD abilitate per l'accesso SSO facile.

![Interfaccia di amministrazione di Azure Active Directory: Riquadro Accesso Single Sign-On facile](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Motivi degli errori di accesso nell'interfaccia di amministrazione di Azure Active Directory (necessaria licenza Premium)

Se al tenant è associata una licenza di Azure AD Premium, è anche possibile esaminare il [report delle attività di accesso](../reports-monitoring/concept-sign-ins.md) nell'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com/).

![Interfaccia di amministrazione di Azure Active Directory: Report sugli accessi](./media/tshoot-connect-sso/sso9.png)

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

- Verificare se l'accesso SSO facile è abilitato in Azure AD Connect. Se non è possibile abilitare la funzionalità, ad esempio a causa di una porta bloccata, verificare che tutti i [prerequisiti](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) siano soddisfatti.
- Se nel tenant sono stati abilitati sia [Aggiunta ad Azure AD](../active-directory-azureadjoin-overview.md) che l'accesso Single Sign-On facile, assicurarsi che il problema non dipenda da Aggiunta ad Azure AD. SSO da Aggiunta ad Azure AD ha la precedenza su SSO facile se il dispositivo è sia registrato con Azure AD che aggiunto a un dominio. Con SSO da Aggiunta ad Azure AD l'utente visualizza un riquadro di accesso con il messaggio "Connesso a Windows".
- Verificare che l'URL di Azure AD (https://autologon.microsoftazuread-sso.com) faccia parte delle impostazioni della zona Intranet dell'utente.
- Verificare che il dispositivo aziendale sia aggiunto al dominio Active Directory. Per poter usare l'accesso Single Sign-on facile, _non_ è necessario che il dispositivo sia [aggiunto ad Azure AD](../active-directory-azureadjoin-overview.md).
- Verificare che l'utente sia connesso al dispositivo tramite un account di dominio di Active Directory.
- Verificare che l'account dell'utente sia presente in una foresta di Active Directory in cui è stato configurato l'accesso SSO facile.
- Verificare che il dispositivo sia connesso alla rete aziendale.
- Verificare che l'ora del dispositivo sia sincronizzata con quella di Active Directory e dei controller di dominio e che si discosti al massimo di 5 minuti dalle stesse.
- Assicurarsi che l'account di computer `AZUREADSSOACC` sia presente e attivato in ogni foresta di AD per la quale si desidera che sia attivato il Single Sign-On facile. Se l'account del computer è stato eliminato o non è presente, è possibile usare i [cmdlet di PowerShell](#manual-reset-of-the-feature) per ricrearlo.
- Indicare i ticket Kerberos presenti nel dispositivo usando il comando `klist` da un prompt dei comandi. Verificare se sono presenti i ticket emessi per l'account del computer `AZUREADSSOACC`. I ticket Kerberos degli utenti sono in genere validi per 10 ore. È possibile che siano in uso impostazioni diverse in Active Directory.
- Se si è disabilitato e abilitato di nuovo l'accesso Single Sign-On facile nel tenant, gli utenti non potranno usare l'accesso Single Sign-On fino alla scadenza dei ticket Kerberos memorizzati nella cache.
- Eliminare i ticket Kerberos dal dispositivo usando il comando `klist purge` e riprovare.
- Esaminare i log della console del browser (in **Strumenti di sviluppo**) per determinare se esistono problemi relativi a JavaScript.
- Esaminare i [log del controller di dominio](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Log del controller di dominio

Se si abilita il controllo delle operazioni riuscite nel controller di dominio, ogni volta che un utente esegue l'accesso usando la funzionalità di accesso SSO facile, viene registrata una voce di sicurezza nel log eventi. È possibile trovare questi eventi di sicurezza usando la query seguente. Cercare l'evento **4769** associato all'account computer **AzureADSSOAcc$** .

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Reimpostazione manuale della funzionalità

Se il problema persiste, è possibile reimpostare manualmente la funzionalità nel tenant. Seguire questa procedura nel server locale in cui si esegue Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Passaggio 1: Importare il modulo di PowerShell Seamless SSO

1. Prima, scaricare e installare [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Passare alla cartella `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importare il modulo di PowerShell Seamless SSO usando il comando seguente: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Passaggio 2: Ottenere l'elenco di foreste di Active Directory in cui è stato abilitato l'accesso SSO facile

1. Eseguire PowerShell come amministratore. In PowerShell eseguire la chiamata a `New-AzureADSSOAuthenticationContext`. Quando richiesto, immettere le credenziali di amministratore globale del tenant.
2. Eseguire la chiamata a `Get-AzureADSSOStatus`. Questo comando consente di visualizzare l'elenco di foreste di Active Directory, ovvero l'elenco "Domini", in cui è stata abilitata questa funzionalità.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Passaggio 3: Disabilitare l'accesso SSO facile per ogni foresta di Active Directory in cui la funzionalità è configurata

1. Eseguire la chiamata a `$creds = Get-Credential`. Quando richiesto, immettere le credenziali dell'amministratore di dominio per la foresta di Active Directory da usare.

   > [!NOTE]
   >Il nome utente delle credenziali di amministratore di dominio deve essere specificato nel formato del nome dell'account SAM (CONTOSO\johndoe o contoso. com\johndoe). Viene utilizzata la parte di dominio del nome utente per individuare il controller di dominio dell'amministratore di dominio tramite DNS.

   >[!NOTE]
   >L'account amministratore di dominio utilizzato non deve essere un membro del gruppo utenti protetti. In tal caso, l'operazione avrà esito negativo.

2. Eseguire la chiamata a `Disable-AzureADSSOForest -OnPremCredentials $creds`. Questo comando rimuove l'account computer `AZUREADSSOACC` dal controller di dominio locale per questa foresta di Active Directory specifica.
3. Ripetere la procedura precedente per ogni foresta di Active Directory in cui la funzionalità è configurata.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Passaggio 4: Abilitare l'accesso SSO facile per ogni foresta di Active Directory

1. Eseguire la chiamata a `Enable-AzureADSSOForest`. Quando richiesto, immettere le credenziali dell'amministratore di dominio per la foresta di Active Directory da usare.

   > [!NOTE]
   >Il nome utente delle credenziali di amministratore di dominio deve essere specificato nel formato del nome dell'account SAM (CONTOSO\johndoe o contoso. com\johndoe). Viene utilizzata la parte di dominio del nome utente per individuare il controller di dominio dell'amministratore di dominio tramite DNS.

   >[!NOTE]
   >L'account amministratore di dominio utilizzato non deve essere un membro del gruppo utenti protetti. In tal caso, l'operazione avrà esito negativo.

2. Ripetere la procedura precedente per ogni foresta di Active Directory in cui si desidera configurare la funzionalità.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Passaggio 5. Abilitare la funzionalità nel tenant

Per abilitare la funzionalità nel tenant, chiamare `Enable-AzureADSSO -Enable $true`.
