---
title: "Azure AD Connect: Domande frequenti sull'accesso Single Sign-On facile | Microsoft Docs"
description: Risposte alle domande frequenti sull'accesso Single Sign-On facile di Azure Active Directory.
services: active-directory
keywords: che cos'è Azure AD Connect, installare Active Directory, componenti richiesti per Azure AD, SSO, Single Sign-On
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 64c56113c3bd7c07629e0177242837c1cafab02a
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54472909"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Accesso Single Sign-On facile di Azure Active Directory: Domande frequenti

Questo articolo risponde ad alcune domande frequenti relative all'accesso Single Sign-On facile (SSO facile) di Azure Active Directory. Visitare questa pagina regolarmente per nuovi contenuti.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>Con quali metodi di accesso funziona l'accesso SSO facile?

L'accesso SSO facile può essere combinato con i metodi di accesso che usano la [sincronizzazione dell'hash delle password](how-to-connect-password-hash-synchronization.md) o l'[autenticazione pass-through](how-to-connect-pta.md). Tuttavia, questa funzionalità non può essere usata con Active Directory Federation Services (AD FS).

## <a name="is-seamless-sso-a-free-feature"></a>La funzionalità Accesso SSO facile è disponibile gratuitamente?

La funzionalità Accesso SSO facile è una funzionalità gratuita e non serve alcuna delle edizioni a pagamento di Azure AD per usarla.

## <a name="is-seamless-sso-available-in-the-microsoft-azure-germany-cloudhttpswwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>L'accesso Single Sign-On facile è disponibile nel [cloud Microsoft Azure Germania](https://www.microsoft.de/cloud-deutschland) e nel [cloud Microsoft Azure per enti pubblici](https://azure.microsoft.com/features/gov/)?

 No. L'accesso Single Sign-On facile è disponibile solo nell'istanza di Azure AD a livello mondiale.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>Quali applicazioni possono sfruttare le capacità dei parametri `domain_hint` o `login_hint` dell'accesso SSO facile?

Di seguito è riportato un elenco non completo delle applicazioni che possono inviare questi parametri ad Azure AD, consentendo agli utenti un'esperienza di accesso automatico tramite l'accesso Single Sign-On facile (ad esempio, senza che utenti debbano immettere il proprio nome utente o la password):

| Nome dell'applicazione | URL applicazione da usare |
| -- | -- |
| Pannello di accesso | https://myapps.microsoft.com/contoso.com |
| Outlook nel Web | https://outlook.office365.com/contoso.com |
| Portali di Office 365 | https://portal.office.com?domain_hint=contoso.com, https://www.office.com?domain_hint=contoso.com |

In aggiunta, gli utenti possono usufruire di un'esperienza di accesso automatico nei casi in cui l'applicazione invia le richieste di accesso agli endpoint con tenant di Azure AD, ovvero https://login.microsoftonline.com/contoso.com/<..> o https://login.microsoftonline.com/<tenant_ID>/<..>, anziché all'endpoint comune di Azure AD (https://login.microsoftonline.com/common/<...>). Di seguito viene fornito un elenco, non completo, di applicazioni che generano questi tipi di richieste di accesso.

| Nome dell'applicazione | URL applicazione da usare |
| -- | -- |
| SharePoint Online | https://contoso.sharepoint.com |
| Portale di Azure | https://portal.azure.com/contoso.com |

Nelle tabelle precedenti sostituire "contoso.com" con il nome di dominio per ottenere gli URL dell'applicazione corretti per il tenant.

Se si desidera che altre applicazioni utilizzino la nostra esperienza di accesso automatico, si prega di segnalarlo nella sezione commenti e suggerimenti.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>L'accesso SSO facile supporta `Alternate ID` come nome utente, anziché `userPrincipalName`?

Sì. L'accesso SSO facile supporta `Alternate ID` come nome utente quando è configurato in Azure AD Connect, come illustrato [qui](how-to-connect-install-custom.md). Non tutte le applicazioni di Office 365 supportano `Alternate ID`. Fare riferimento alla documentazione dell'applicazione specifica per sapere se è supportato.

## <a name="what-is-the-difference-between-the-single-sign-on-experience-provided-by-azure-ad-joinactive-directory-azureadjoin-overviewmd-and-seamless-sso"></a>Qual è la differenza tra l'esperienza Single Sign-On offerta da [Aggiunta ad Azure AD](../active-directory-azureadjoin-overview.md) e dall'accesso Single Sign-On facile?

[Aggiunta ad Azure AD](../active-directory-azureadjoin-overview.md) offre l'accesso SSO agli utenti se i dispositivi sono registrati con Azure AD. Questi dispositivi non devono necessariamente essere aggiunti a un dominio. L'accesso SSO viene fornito usando *token di aggiornamento primari* o *PRT* e non Kerberos. L'esperienza utente è ottimale sui dispositivi Windows 10. L'accesso SSO viene eseguito automaticamente nel browser Microsoft Edge. Funziona anche in Chrome con l'uso di un'estensione del browser.

È possibile usare sia Aggiunta ad Azure AD che Single Sign-On facile nel tenant. Queste due funzionalità sono complementari. Se entrambe le funzionalità sono attivate, l'accesso SSO di Aggiunta ad Azure AD ha la precedenza su Single Sign-On facile.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Se si vuole registrare dispositivi non Windows 10 con Azure AD, senza l'uso di AD FS, è possibile usare l'accesso SSO facile?

Sì, per questo scenario è necessaria la versione 2.1 o versione successiva del [client Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>Come è possibile rinnovare la chiave di decrittografia di Kerberos dell'account computer `AZUREADSSOACC`?

È importante rinnovare spesso la chiave di decrittografia di Kerberos dell'account computer `AZUREADSSOACC`, che rappresenta Azure AD, creato nella foresta di AD locale.

>[!IMPORTANT]
>È consigliabile rinnovare la chiave di decrittografia di Kerberos almeno ogni 30 giorni.

Seguire questa procedura nel server locale in cui si esegue Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Passaggio 1. Ottenere l'elenco delle foreste di Active Directory in cui è stata abilitata la funzionalità Accesso SSO facile

1. Prima, scaricare e installare [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Passare alla cartella `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importare il modulo di PowerShell Seamless SSO usando il comando seguente: `Import-Module .\AzureADSSO.psd1`.
4. Eseguire PowerShell come amministratore. In PowerShell eseguire la chiamata a `New-AzureADSSOAuthenticationContext`. Il comando dovrebbe far sì che venga visualizzata una finestra popup per l'immissione delle credenziali dell'amministratore globale del tenant.
5. Eseguire la chiamata a `Get-AzureADSSOStatus`. ll comando consente di visualizzare l'elenco di foreste di Active Directory, ovvero l'elenco "Domini", in cui è stata abilitata questa funzionalità.

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>Passaggio 2. Aggiornare la chiave di decrittografia di Kerberos in ogni foresta di Active Directory in cui è stata impostata

1. Eseguire la chiamata a `$creds = Get-Credential`. Quando richiesto, immettere le credenziali dell'amministratore di dominio per la foresta di Active Directory da usare.

    >[!NOTE]
    >Per trovare la foresta di Active Directory desiderata, usare il nome utente dell'amministratore di dominio fornito nel formato di nome dell'identità utente (UPN) (johndoe@contoso.com) o il nome dominio completo dell'account SAM (contoso\johndoe o contoso.com\johndoe). Se si usa il formato di nome dominio completo dell'account SAM, usare la parte del dominio del nome utente per [individuare il controller di dominio dell'amministratore di dominio usando DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Se invece si usa il formato UPN, [convertirlo in un nome dominio completo dell'account SAM](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) prima di individuare il controller di dominio appropriato.

2. Eseguire la chiamata a `Update-AzureADSSOForest -OnPremCredentials $creds`. Questo comando aggiorna la chiave di decrittografia di Kerberos per l'account computer `AZUREADSSOACC` in questa foresta di AD specifica e la aggiorna in Azure AD.
3. Ripetere i passaggi precedenti per ogni foresta di Active Directory in cui è stata configurata la funzionalità.

>[!IMPORTANT]
>Assicurarsi di _non_ eseguire il comando `Update-AzureADSSOForest` più di una volta. In caso contrario, la funzionalità si interrompe fino alla scadenza dei ticket Kerberos degli utenti e fino a quando non vengono nuovamente inviati da Active Directory locale.

## <a name="how-can-i-disable-seamless-sso"></a>Come è possibile disabilitare l'accesso SSO facile?

### <a name="step-1-disable-the-feature-on-your-tenant"></a>Passaggio 1. Disabilitare la funzionalità nel tenant

#### <a name="option-a-disable-using-azure-ad-connect"></a>Opzione A: Disabilitare tramite Azure AD Connect

1. Eseguire Azure AD Connect, scegliere la pagina **Cambia l'accesso utente** e fare clic su **Avanti**.
2. Deselezionare l'opzione **Abilita Single Sign-On**. Continuare la procedura guidata.

Al termine della procedura guidata, l'accesso Single Sign-On facile è disabilitato nel tenant. Verrà tuttavia visualizzato un messaggio con il testo seguente:

"L'accesso Single Sign-On è ora disabilitato, ma è necessario eseguire altri passaggi manuali per completare la pulizia. Altre informazioni"

Per completare il processo di eliminazione, seguire i passaggi 2 e 3 nel server locale in cui si esegue Azure AD Connect.

#### <a name="option-b-disable-using-powershell"></a>Opzione B: Disabilitare tramite PowerShell

Seguire questa procedura nel server locale in cui si esegue Azure AD Connect:

1. Prima, scaricare e installare [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Passare alla cartella `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importare il modulo di PowerShell Seamless SSO usando il comando seguente: `Import-Module .\AzureADSSO.psd1`.
4. Eseguire PowerShell come amministratore. In PowerShell eseguire la chiamata a `New-AzureADSSOAuthenticationContext`. Il comando dovrebbe far sì che venga visualizzata una finestra popup per l'immissione delle credenziali dell'amministratore globale del tenant.
5. Eseguire la chiamata a `Enable-AzureADSSO -Enable $false`.

>[!IMPORTANT]
>Disabilitando l'accesso Single Sign-On facile tramite PowerShell, non verrà modificato lo stato in Azure AD Connect. L'accesso Single Sign-On facile verrà visualizzato come abilitato nella pagina **Cambia l'accesso utente**.

### <a name="step-2-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Passaggio 2. Ottenere l'elenco delle foreste di Active Directory in cui è stata abilitata la funzionalità Accesso SSO facile

Seguire le attività da 1 a 4 se l'accesso Single Sign-On facile è stato disabilitato tramite Azure AD Connect. Se invece l'accesso Single Sign-On facile è stato disabilitato tramite PowerShell, passare all'attività 5.

1. Prima, scaricare e installare [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Passare alla cartella `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importare il modulo di PowerShell Seamless SSO usando il comando seguente: `Import-Module .\AzureADSSO.psd1`.
4. Eseguire PowerShell come amministratore. In PowerShell eseguire la chiamata a `New-AzureADSSOAuthenticationContext`. Il comando dovrebbe far sì che venga visualizzata una finestra popup per l'immissione delle credenziali dell'amministratore globale del tenant.
5. Eseguire la chiamata a `Get-AzureADSSOStatus`. ll comando consente di visualizzare l'elenco di foreste di Active Directory, ovvero l'elenco "Domini", in cui è stata abilitata questa funzionalità.

### <a name="step-3-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>Passaggio 3. Eliminare manualmente l'account computer `AZUREADSSOACCT` da ogni foresta di AD elencata.

## <a name="next-steps"></a>Passaggi successivi

- [**Avvio rapido**](how-to-connect-sso-quick-start.md): avvio ed esecuzione di Accesso SSO facile di Azure AD.
- [**Approfondimento tecnico**](how-to-connect-sso-how-it-works.md): informazioni sul funzionamento di questa funzionalità.
- [**Risoluzione dei problemi**](tshoot-connect-sso.md): informazioni su come risolvere i problemi comuni relativi a questa funzionalità.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): per l'invio di richieste di nuove funzionalità.
