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
ms.topic: conceptual
ms.date: 11/14/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96d0253e1e656f4bdb5180af0d57824aceb4f0dd
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2019
ms.locfileid: "71176654"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Accesso Single Sign-On facile di Azure Active Directory: Domande frequenti

Questo articolo risponde ad alcune domande frequenti relative all'accesso Single Sign-On facile (SSO facile) di Azure Active Directory. Visitare questa pagina regolarmente per nuovi contenuti.

**D: Come funzionano i metodi di accesso SSO senza problemi**

L'accesso SSO facile può essere combinato con i metodi di accesso che usano la [sincronizzazione dell'hash delle password](how-to-connect-password-hash-synchronization.md) o l'[autenticazione pass-through](how-to-connect-pta.md). Tuttavia, questa funzionalità non può essere usata con Active Directory Federation Services (AD FS).

**D: L'accesso SSO facile è una funzionalità gratuita?**

La funzionalità Accesso SSO facile è una funzionalità gratuita e non serve alcuna delle edizioni a pagamento di Azure AD per usarla.

**D: L'accesso SSO facile è disponibile nel [cloud Microsoft Azure Germania](https://www.microsoft.de/cloud-deutschland) e nel cloud [Microsoft Azure per enti pubblici](https://azure.microsoft.com/features/gov/)?**

No. L'accesso Single Sign-On facile è disponibile solo nell'istanza di Azure AD a livello mondiale.

**D: Quali applicazioni sfruttano i `domain_hint` vantaggi `login_hint` di o la funzionalità dei parametri di seamless SSO?**

Di seguito è riportato un elenco non completo delle applicazioni che possono inviare questi parametri ad Azure AD, consentendo agli utenti un'esperienza di accesso automatico tramite l'accesso Single Sign-On facile (ad esempio, senza che utenti debbano immettere il proprio nome utente o la password):

| Nome applicazione | URL applicazione da usare |
| -- | -- |
| Riquadro di accesso | https:\//MyApps.Microsoft.com/contoso.com |
| Outlook nel Web | https:\//outlook.office365.com/contoso.com |
| Portali di Office 365 | https:\//Portal.Office.com? domain_hint = contoso. com, https:\//www.Office.com? domain_hint = contoso. com |

Inoltre, gli utenti ottengono un'esperienza di accesso invisibile all'utente se un'applicazione invia le richieste di accesso agli endpoint di Azure ad impostati come tenant, ovvero https:\//login.microsoftonline.com/contoso.com/<. > o https:\//login.microsoftonline.com/<tenant_ID>/<.. >, anziché l'endpoint comune di Azure ad, ovvero https:\//login.microsoftonline.com/common/<... >. Di seguito viene fornito un elenco, non completo, di applicazioni che generano questi tipi di richieste di accesso.

| Nome applicazione | URL applicazione da usare |
| -- | -- |
| SharePoint Online | https:\//contoso.SharePoint.com |
| Portale di Azure | https:\//Portal.Azure.com/contoso.com |

Nelle tabelle precedenti sostituire "contoso.com" con il nome di dominio per ottenere gli URL dell'applicazione corretti per il tenant.

Se si desidera che altre applicazioni utilizzino la nostra esperienza di accesso automatico, si prega di segnalarlo nella sezione commenti e suggerimenti.

**D: Il supporto `Alternate ID` dell'accesso SSO facile è il nome utente `userPrincipalName`, anziché?**

Sì. L'accesso SSO facile supporta `Alternate ID` come nome utente quando è configurato in Azure AD Connect, come illustrato [qui](how-to-connect-install-custom.md). Non tutte le applicazioni di Office 365 supportano `Alternate ID`. Fare riferimento alla documentazione dell'applicazione specifica per sapere se è supportato.

**D: Qual è la differenza tra l'esperienza Single Sign-On fornita da [Azure ad join](../active-directory-azureadjoin-overview.md) e l'accesso SSO facile?**

[Aggiunta ad Azure AD](../active-directory-azureadjoin-overview.md) offre l'accesso SSO agli utenti se i dispositivi sono registrati con Azure AD. Questi dispositivi non devono necessariamente essere aggiunti a un dominio. L'accesso SSO viene fornito usando *token di aggiornamento primari* o *PRT* e non Kerberos. L'esperienza utente è ottimale sui dispositivi Windows 10. L'accesso SSO viene eseguito automaticamente nel browser Microsoft Edge. Funziona anche in Chrome con l'uso di un'estensione del browser.

È possibile usare sia Aggiunta ad Azure AD che Single Sign-On facile nel tenant. Queste due funzionalità sono complementari. Se entrambe le funzionalità sono attivate, l'accesso SSO di Aggiunta ad Azure AD ha la precedenza su Single Sign-On facile.

**D: Se si vuole registrare dispositivi non Windows 10 con Azure AD, senza l'uso di AD FS, È invece possibile usare l'accesso SSO facile?**

Sì, per questo scenario è necessaria la versione 2.1 o versione successiva del [client Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554).

**D: Come è possibile eseguire il rollover della chiave di decrittografia di `AZUREADSSOACC` Kerberos dell'account computer?**

È importante rinnovare spesso la chiave di decrittografia di Kerberos dell'account computer `AZUREADSSOACC`, che rappresenta Azure AD, creato nella foresta di AD locale.

>[!IMPORTANT]
>È consigliabile rinnovare la chiave di decrittografia di Kerberos almeno ogni 30 giorni.

Seguire questa procedura nel server locale in cui si esegue Azure AD Connect:

    **Step 1. Get list of AD forests where Seamless SSO has been enabled**

    1. Prima, scaricare e installare [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
    2. Passare alla cartella `%programfiles%\Microsoft Azure Active Directory Connect`.
    3. Importare il modulo di PowerShell Seamless SSO usando il comando seguente: `Import-Module .\AzureADSSO.psd1`.
    4. Eseguire PowerShell come amministratore. In PowerShell eseguire la chiamata a `New-AzureADSSOAuthenticationContext`. Il comando dovrebbe far sì che venga visualizzata una finestra popup per l'immissione delle credenziali dell'amministratore globale del tenant.
    5. Eseguire la chiamata a `Get-AzureADSSOStatus | ConvertFrom-Json`. ll comando consente di visualizzare l'elenco di foreste di Active Directory, ovvero l'elenco "Domini", in cui è stata abilitata questa funzionalità.

    **Passaggio 2. Aggiornare la chiave di decrittografia Kerberos in ogni foresta di Active Directory in cui è stata configurata**

    1. Eseguire la chiamata a `$creds = Get-Credential`. Quando richiesto, immettere le credenziali dell'amministratore di dominio per la foresta di Active Directory da usare.

    > [!NOTE]
    > Per trovare la foresta di Active Directory desiderata, usare il nome utente dell'amministratore di dominio fornito nel formato di nome dell'identità utente (UPN) (johndoe@contoso.com) o il nome dominio completo dell'account SAM (contoso\johndoe o contoso.com\johndoe). Se si usa il formato di nome dominio completo dell'account SAM, usare la parte del dominio del nome utente per [individuare il controller di dominio dell'amministratore di dominio usando DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Se invece si usa il formato UPN, [convertirlo in un nome dominio completo dell'account SAM](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) prima di individuare il controller di dominio appropriato.

    2. Eseguire la chiamata a `Update-AzureADSSOForest -OnPremCredentials $creds`. Questo comando aggiorna la chiave di decrittografia di Kerberos per l'account computer `AZUREADSSOACC` in questa foresta di AD specifica e la aggiorna in Azure AD.
    3. Ripetere i passaggi precedenti per ogni foresta di Active Directory in cui è stata configurata la funzionalità.

    >[!IMPORTANT]
    >Assicurarsi di _non_ eseguire il comando `Update-AzureADSSOForest` più di una volta. In caso contrario, la funzionalità si interrompe fino alla scadenza dei ticket Kerberos degli utenti e fino a quando non vengono nuovamente inviati da Active Directory locale.

**D: Come è possibile disabilitare seamless SSO?**

    **Step 1. Disable the feature on your tenant**

        **Option A: Disable using Azure AD Connect**

        1. Run Azure AD Connect, choose **Change user sign-in page** and click **Next**.
        2. Uncheck the **Enable single sign on** option. Continue through the wizard.

        After completing the wizard, Seamless SSO will be disabled on your tenant. However, you will see a message on screen that reads as follows:

        "Single sign-on is now disabled, but there are additional manual steps to perform in order to complete clean-up. Learn more"

        To complete the clean-up process, follow steps 2 and 3 on the on-premises server where you are running Azure AD Connect.

        **Option B: Disable using PowerShell**

        Run the following steps on the on-premises server where you are running Azure AD Connect:

        1. First, download, and install [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
        2. Navigate to the `%programfiles%\Microsoft Azure Active Directory Connect` folder.
        3. Import the Seamless SSO PowerShell module using this command: `Import-Module .\AzureADSSO.psd1`.
        4. Run PowerShell as an Administrator. In PowerShell, call `New-AzureADSSOAuthenticationContext`. This command should give you a popup to enter your tenant's Global Administrator credentials.
        5. Call `Enable-AzureADSSO -Enable $false`.

        >[!IMPORTANT]
        >Disabling Seamless SSO using PowerShell will not change the state in Azure AD Connect. Seamless SSO will show as enabled in the **Change user sign-in** page.

    **Step 2. Get list of AD forests where Seamless SSO has been enabled**

    Follow tasks 1 through 4 below if you have disabled Seamless SSO using Azure AD Connect. If you have disabled Seamless SSO using PowerShell instead, jump ahead to task 5 below.

    1. Prima, scaricare e installare [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
    2. Passare alla cartella `%programfiles%\Microsoft Azure Active Directory Connect`.
    3. Importare il modulo di PowerShell Seamless SSO usando il comando seguente: `Import-Module .\AzureADSSO.psd1`.
    4. Eseguire PowerShell come amministratore. In PowerShell eseguire la chiamata a `New-AzureADSSOAuthenticationContext`. Il comando dovrebbe far sì che venga visualizzata una finestra popup per l'immissione delle credenziali dell'amministratore globale del tenant.
    5. Eseguire la chiamata a `Get-AzureADSSOStatus | ConvertFrom-Json`. ll comando consente di visualizzare l'elenco di foreste di Active Directory, ovvero l'elenco "Domini", in cui è stata abilitata questa funzionalità.

    **Passaggio 3. Eliminare manualmente l' `AZUREADSSOACCT` account computer da ogni foresta di Active Directory visualizzata nell'elenco.**

## <a name="next-steps"></a>Passaggi successivi

- [**Avvio rapido**](how-to-connect-sso-quick-start.md): avvio ed esecuzione di Accesso SSO facile di Azure AD.
- [**Approfondimento tecnico**](how-to-connect-sso-how-it-works.md): informazioni sul funzionamento di questa funzionalità.
- [**Risoluzione dei problemi**](tshoot-connect-sso.md): informazioni su come risolvere i problemi comuni relativi a questa funzionalità.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): per l'invio di richieste di nuove funzionalità.
