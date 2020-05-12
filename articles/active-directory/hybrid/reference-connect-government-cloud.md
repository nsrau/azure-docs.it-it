---
title: 'Azure AD Connect: considerazioni sulle identità ibride per il cloud di Azure per enti pubblici'
description: Considerazioni speciali per la distribuzione di Azure AD Connect con il cloud di Azure per enti pubblici.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: acdc99ca50255bd9b75828f0a051f364c5218471
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115490"
---
# <a name="hybrid-identity-considerations-for-the-azure-government-cloud"></a>Considerazioni sulle identità ibride per il cloud di Azure per enti pubblici

Questo articolo descrive le considerazioni per l'integrazione di un ambiente ibrido con il cloud Microsoft Azure per enti pubblici. Queste informazioni vengono fornite come riferimento per gli amministratori e i progettisti che lavorano con il cloud di Azure per enti pubblici.

> [!NOTE]
> Per integrare un ambiente di Microsoft Azure Active Directory locale (Azure AD) con il cloud di Azure per enti pubblici, è necessario eseguire l'aggiornamento alla versione più recente di [Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594).

Per un elenco completo degli endpoint di Stati Uniti Government Department of Defense, vedere la [documentazione](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints).

## <a name="azure-ad-pass-through-authentication"></a>Autenticazione pass-through di Azure AD

Le informazioni seguenti descrivono l'implementazione dell'autenticazione pass-through e il cloud di Azure per enti pubblici.

### <a name="allow-access-to-urls"></a>Consentire l'accesso agli URL

Prima di distribuire l'agente di autenticazione pass-through, verificare se esiste un firewall tra i server e Azure AD. Se il firewall o il proxy consente la Domain Name System (DNS) di programmi bloccati o sicuri, aggiungere le connessioni seguenti.

> [!NOTE]
> Le linee guida seguenti si applicano anche all'installazione del [connettore del proxy di applicazione Azure ad](https://aka.ms/whyappproxy) per gli ambienti di Azure per enti pubblici.

|URL |Uso|
|-----|-----|
|&#42;. msappproxy.us</br>&#42;.servicebus.usgovcloudapi.net|L'agente usa questi URL per comunicare con il servizio Cloud Azure AD. |
|mscrl.microsoft.us:80 </br>crl.microsoft.us:80 </br>ocsp.msocsp.us:80 </br>www.microsoft.us:80| L'agente utilizza questi URL per verificare i certificati.|
|login.windows.us </br>secure.aadcdn.microsoftonline-p.com </br>&#42;. microsoftonline.us </br>&#42;. microsoftonline-p.us </br>&#42;. msauth.net </br>&#42;. msauthimages.net </br>&#42;. msecnd.net</br>&#42;. msftauth.net </br>&#42;. msftauthimages.net</br>&#42;. phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| L'agente utilizza questi URL durante il processo di registrazione.

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Installare l'agente per il cloud di Azure per enti pubblici

Per installare l'agente per il cloud di Azure per enti pubblici, seguire questa procedura:

1. Nel terminale della riga di comando passare alla cartella che contiene il file eseguibile che installa l'agente.
1. Eseguire i comandi seguenti, che specificano che l'installazione è per Azure per enti pubblici.

   Per l'autenticazione pass-through:

   ```
   AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
   ```

   Per il proxy di applicazione:

   ```
   AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
   ```

## <a name="single-sign-on"></a>Single sign-on

### <a name="set-up-your-azure-ad-connect-server"></a>Configurare il server di Azure AD Connect

Se si usa l'autenticazione pass-through come metodo di accesso, non è necessario alcun controllo dei prerequisiti aggiuntivo. Se si usa la sincronizzazione dell'hash delle password come metodo di accesso ed esiste un firewall tra Azure AD Connect e Azure AD, assicurarsi che:

- Usare Azure AD Connect versione 1.1.644.0 o successiva.
- Se il firewall o il proxy consente i programmi DNS bloccati o sicuri, aggiungere le connessioni agli URL &#42;. msappproxy.us sulla porta 443.

  In caso contrario, consentire l'accesso agli intervalli IP del Data Center di Azure, che vengono aggiornati ogni settimana. Questo prerequisito è applicabile solo quando si Abilita la funzionalità. Non è necessario per gli effettivi accessi dell'utente.

### <a name="roll-out-seamless-single-sign-on"></a>Implementazione di Single Sign-on facile

È possibile implementare gradualmente Azure AD accesso Single Sign-on facile agli utenti usando le istruzioni seguenti. Per iniziare, è necessario aggiungere l'URL Azure AD [https://autologon.microsoft.us](https://autologon.microsoft.us) a tutte le impostazioni dell'area Intranet di tutti o di utenti selezionati usando criteri di gruppo in Active Directory.

È anche necessario abilitare l'impostazione dei criteri di area Intranet **Consenti aggiornamenti alla barra di stato tramite script tramite criteri di gruppo**.

## <a name="browser-considerations"></a>Considerazioni sui browser

### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (tutte le piattaforme)

Mozilla Firefox non usa automaticamente l'autenticazione Kerberos. Ogni utente deve aggiungere manualmente l'URL Azure AD alle impostazioni di Firefox attenendosi alla procedura seguente:

1. Eseguire Firefox e immettere **about: config**   nella barra degli indirizzi. Ignorare tutte le notifiche che potrebbero essere visualizzate.
1. Cercare la preferenza **Network. Negotiate-auth. trusted-URI**   . Questa preferenza elenca i siti considerati attendibili da Firefox per l'autenticazione Kerberos.
1. Fare clic con il pulsante destro del mouse sul nome della preferenza e quindi scegliere **modifica**.
1. Immettere  [**https://autologon.microsoft.us**](https://autologon.microsoft.us**)   nella casella.
1. Selezionare **OK**   e quindi riaprire il browser.

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge basato su cromo (tutte le piattaforme)

Se sono state sostituite le  `AuthNegotiateDelegateAllowlist`   `AuthServerAllowlist`   impostazioni dei criteri o nell'ambiente in uso, assicurarsi di aggiungervi il Azure ad URL [https://autologon.microsoft.us](https://autologon.microsoft.us) .

### <a name="google-chrome-all-platforms"></a>Google Chrome (tutte le piattaforme)

Se sono state sostituite le  `AuthNegotiateDelegateWhitelist`   `AuthServerWhitelist`   impostazioni dei criteri o nell'ambiente in uso, assicurarsi di aggiungervi il Azure ad URL [https://autologon.microsoft.us](https://autologon.microsoft.us) .

## <a name="next-steps"></a>Passaggi successivi

- [Autenticazione pass-through](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
- [Single Sign-on](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)
