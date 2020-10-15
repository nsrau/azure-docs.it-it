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
ms.topic: how-to
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d72b70248e317d1caee4527be38fe304cfe7f16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89658344"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Accesso Single Sign-On facile di Azure Active Directory: Domande frequenti

Questo articolo risponde ad alcune domande frequenti relative all'accesso Single Sign-On facile (SSO facile) di Azure Active Directory. Visitare questa pagina regolarmente per nuovi contenuti.

**D: Con quali metodi di accesso funziona l'accesso SSO facile?**

L'accesso SSO facile può essere combinato con i metodi di accesso che usano la [sincronizzazione dell'hash delle password](how-to-connect-password-hash-synchronization.md) o l'[autenticazione pass-through](how-to-connect-pta.md). Tuttavia, questa funzionalità non può essere usata con Active Directory Federation Services (AD FS).

**D: L'accesso SSO facile è una funzionalità gratuita?**

L'accesso SSO facile è una funzionalità gratuita e non serve alcuna delle edizioni a pagamento di Azure AD per usarlo.

**D: L'accesso SSO facile è disponibile nel [cloud Microsoft Azure Germania](https://www.microsoft.de/cloud-deutschland) e nel [cloud Microsoft Azure per enti pubblici](https://azure.microsoft.com/features/gov/)?**

L'accesso SSO facile è disponibile per il cloud di Azure per enti pubblici. Per informazioni dettagliate, vedere [Considerazioni sulle identità ibride per Azure per enti pubblici](./reference-connect-government-cloud.md).

**D: Quali applicazioni possono sfruttare le capacità dei parametri `domain_hint` e `login_hint` dell'accesso SSO facile?**

Di seguito è riportato un elenco non completo delle applicazioni che possono inviare questi parametri ad Azure AD, consentendo agli utenti un'esperienza di accesso automatico tramite l'accesso Single Sign-On facile (ad esempio, senza che utenti debbano immettere il proprio nome utente o la password):

| Nome applicazione | URL applicazione da usare |
| -- | -- |
| Pannello di accesso | https:\//myapps.microsoft.com/contoso.com |
| Outlook nel Web | https:\//outlook.office365.com/contoso.com |
| Portali di Office 365 | https:\//portal.office.com?domain_hint=contoso.com, https:\//www.office.com?domain_hint=contoso.com |

L'esperienza di accesso è inoltre invisibile all'utente se un'applicazione invia richieste di accesso agli endpoint di Azure AD configurati come tenant, ovvero https:\//login.microsoftonline.com/contoso.com/<..> o https:\//login.microsoftonline.com/<tenant_ID>/<..>, invece che all'endpoint comune di Azure AD, ovvero https:\//login.microsoftonline.com/common/<...>. Di seguito viene fornito un elenco, non completo, di applicazioni che generano questi tipi di richieste di accesso.

| Nome applicazione | URL applicazione da usare |
| -- | -- |
| SharePoint Online | https:\//contoso.sharepoint.com |
| Portale di Azure | https:\//portal.azure.com/contoso.com |

Nelle tabelle precedenti sostituire "contoso.com" con il nome di dominio per ottenere gli URL dell'applicazione corretti per il tenant.

Se si desidera che altre applicazioni utilizzino la nostra esperienza di accesso automatico, si prega di segnalarlo nella sezione commenti e suggerimenti.

**D: L'accesso SSO facile supporta `Alternate ID` invece di `userPrincipalName` come nome utente?**

Sì. L'accesso SSO facile supporta `Alternate ID` come nome utente quando è configurato in Azure AD Connect, come illustrato [qui](how-to-connect-install-custom.md). Non tutte le applicazioni Microsoft 365 supportano `Alternate ID` . Fare riferimento alla documentazione dell'applicazione specifica per sapere se è supportato.

**D: Qual è la differenza tra l'esperienza di accesso Single Sign-On offerta da [Aggiunta ad Azure AD](../devices/overview.md) e dall'accesso SSO facile?**

[Aggiunta ad Azure AD](../devices/overview.md) offre l'accesso SSO agli utenti se i dispositivi sono registrati con Azure AD. Questi dispositivi non devono necessariamente essere aggiunti a un dominio. L'accesso SSO viene fornito usando *token di aggiornamento primari* o *PRT* e non Kerberos. L'esperienza utente è ottimale sui dispositivi Windows 10. L'accesso SSO viene eseguito automaticamente nel browser Microsoft Edge. Funziona anche in Chrome con l'uso di un'estensione del browser.

È possibile usare sia Aggiunta ad Azure AD che Single Sign-On facile nel tenant. Queste due funzionalità sono complementari. Se entrambe le funzionalità sono attivate, l'accesso SSO di Aggiunta ad Azure AD ha la precedenza su Single Sign-On facile.

**D: Per registrare dispositivi non Windows 10 con Azure AD, senza l'uso di AD FS, è possibile usare l'accesso SSO facile?**

Sì, per questo scenario è necessaria la versione 2.1 o versione successiva del [client Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554).

**D: Come è possibile rinnovare la chiave di decrittografia di Kerberos dell'account computer `AZUREADSSO`?**

È importante rinnovare spesso la chiave di decrittografia di Kerberos dell'account computer `AZUREADSSO`, che rappresenta Azure AD, creato nella foresta di AD locale.

>[!IMPORTANT]
>È consigliabile rinnovare la chiave di decrittografia di Kerberos almeno ogni 30 giorni.

Seguire questa procedura nel server locale in cui si esegue Azure AD Connect:

   **Passaggio 1. Ottenere l'elenco delle foreste di Active Directory in cui è stata abilitato l'accesso SSO facile**

   1. Prima, scaricare e installare [Azure AD PowerShell](/powershell/azure/active-directory/overview).
   2. Passare alla cartella `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importare il modulo di PowerShell Seamless SSO usando il comando seguente: `Import-Module .\AzureADSSO.psd1`.
   4. Eseguire PowerShell come amministratore. In PowerShell eseguire la chiamata a `New-AzureADSSOAuthenticationContext`. Il comando dovrebbe far sì che venga visualizzata una finestra popup per l'immissione delle credenziali dell'amministratore globale del tenant.
   5. Chiamare `Get-AzureADSSOStatus | ConvertFrom-Json`. ll comando consente di visualizzare l'elenco di foreste di Active Directory, ovvero l'elenco "Domini", in cui è stata abilitata questa funzionalità.

   **Passaggio 2. Aggiornare la chiave di decrittografia di Kerberos in ogni foresta di Active Directory in cui è stata impostata**

   1. Chiamare `$creds = Get-Credential`. Quando richiesto, immettere le credenziali dell'amministratore di dominio per la foresta di Active Directory da usare.

   > [!NOTE]
   >Il nome utente delle credenziali di amministratore di dominio deve essere specificato nel formato del nome dell'account SAM (CONTOSO\johndoe oppure contoso.com\johndoe). Usare la parte del dominio del nome utente per individuare il controller di dominio dell'amministratore di dominio usando DNS.

   >[!NOTE]
   >L'account amministratore di dominio usato non deve essere un membro del gruppo Utenti protetti, in caso contrario l'operazione non riesce.

   2. Chiamare `Update-AzureADSSOForest -OnPremCredentials $creds`. Questo comando aggiorna la chiave di decrittografia di Kerberos per l'account computer `AZUREADSSO` in questa foresta di AD specifica e la aggiorna in Azure AD.
   
   >[!NOTE]
   >Se non si è un amministratore di dominio ed è stata assegnata l'autorizzazione all'amministratore di dominio, è necessario chiamare `Update-AzureADSSOForest -OnPremCredentials $creds -PreserveCustomPermissionsOnDesktopSsoAccount`
   
   3. Ripetere i passaggi precedenti per ogni foresta di Active Directory in cui è stata configurata la funzionalità.

   >[!IMPORTANT]
   >Assicurarsi di _non_ eseguire il comando `Update-AzureADSSOForest` più di una volta. In caso contrario, la funzionalità si interrompe fino alla scadenza dei ticket Kerberos degli utenti e fino a quando non vengono nuovamente inviati da Active Directory locale.

**D: Come è possibile disabilitare l'accesso SSO facile?**

   **Passaggio 1. Disabilitare la funzionalità nel tenant**

   **Opzione A: Disabilitare tramite Azure AD Connect**
    
   1. Eseguire Azure AD Connect, scegliere la pagina **Cambia l'accesso utente** e fare clic su **Avanti**.
   2. Deselezionare l'opzione **Abilita Single Sign-On**. Continuare la procedura guidata.

   Al termine della procedura guidata, l'accesso Single Sign-On facile è disabilitato nel tenant. Verrà tuttavia visualizzato un messaggio con il testo seguente:

   "L'accesso Single Sign-On è ora disabilitato, ma è necessario eseguire altri passaggi manuali per completare la pulizia. Altre informazioni"

   Per completare il processo di eliminazione, seguire i passaggi 2 e 3 nel server locale in cui si esegue Azure AD Connect.

   **Opzione B: Disabilitare tramite PowerShell**

   Seguire questa procedura nel server locale in cui si esegue Azure AD Connect:

   1. Prima, scaricare e installare [Azure AD PowerShell](/powershell/azure/active-directory/overview).
   2. Passare alla cartella `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importare il modulo di PowerShell Seamless SSO usando il comando seguente: `Import-Module .\AzureADSSO.psd1`.
   4. Eseguire PowerShell come amministratore. In PowerShell eseguire la chiamata a `New-AzureADSSOAuthenticationContext`. Il comando dovrebbe far sì che venga visualizzata una finestra popup per l'immissione delle credenziali dell'amministratore globale del tenant.
   5. Chiamare `Enable-AzureADSSO -Enable $false`.
   
   A questo punto l'accesso Single Sign-on facile è disabilitato, ma i domini rimarranno configurati nel caso in cui si desideri abilitare l'accesso SSO semplice. Se si desidera rimuovere completamente i domini dalla configurazione seamless SSO, chiamare il cmdlet seguente dopo aver completato il passaggio 5 precedente: `Disable-AzureADSSOForest -DomainFqdn <fqdn>` .

   >[!IMPORTANT]
   >Disabilitando l'accesso Single Sign-On facile tramite PowerShell, non verrà modificato lo stato in Azure AD Connect. L'accesso Single Sign-On facile verrà visualizzato come abilitato nella pagina **Cambia l'accesso utente**.

   **Passaggio 2. Ottenere l'elenco delle foreste di Active Directory in cui è stata abilitato l'accesso SSO facile**

   Seguire le attività da 1 a 4 se l'accesso Single Sign-On facile è stato disabilitato tramite Azure AD Connect. Se invece l'accesso Single Sign-On facile è stato disabilitato tramite PowerShell, passare all'attività 5.

   1. Prima, scaricare e installare [Azure AD PowerShell](/powershell/azure/active-directory/overview).
   2. Passare alla cartella `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importare il modulo di PowerShell Seamless SSO usando il comando seguente: `Import-Module .\AzureADSSO.psd1`.
   4. Eseguire PowerShell come amministratore. In PowerShell eseguire la chiamata a `New-AzureADSSOAuthenticationContext`. Il comando dovrebbe far sì che venga visualizzata una finestra popup per l'immissione delle credenziali dell'amministratore globale del tenant.
   5. Chiamare `Get-AzureADSSOStatus | ConvertFrom-Json`. ll comando consente di visualizzare l'elenco di foreste di Active Directory, ovvero l'elenco "Domini", in cui è stata abilitata questa funzionalità.

   **Passaggio 3. Eliminare manualmente l'account computer `AZUREADSSO` da ogni foresta di AD elencata.**

## <a name="next-steps"></a>Passaggi successivi

- [**Avvio rapido**](how-to-connect-sso-quick-start.md): iniziare a usare subito l'accesso SSO facile di Azure AD.
- [**Approfondimento tecnico**](how-to-connect-sso-how-it-works.md): informazioni sul funzionamento di questa funzionalità.
- [**Risoluzione dei problemi**](tshoot-connect-sso.md): informazioni su come risolvere i problemi comuni relativi a questa funzionalità.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): per l'invio di richieste di nuove funzionalità.