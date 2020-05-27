---
title: 'Azure AD Connect: Considerazioni sulle identità ibride per il cloud di Azure per enti pubblici'
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
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115490"
---
# <a name="hybrid-identity-considerations-for-the-azure-government-cloud"></a>Considerazioni sulle identità ibride per il cloud di Azure per enti pubblici

Questo articolo contiene considerazioni per l'integrazione di un ambiente ibrido con il cloud di Microsoft Azure per enti pubblici. Queste informazioni costituiscono un riferimento per gli amministratori e i progettisti che lavorano con il cloud di Azure per enti pubblici.

> [!NOTE]
> Per integrare un ambiente locale di Microsoft Azure Active Directory (Azure AD) con il cloud di Azure per enti pubblici, è necessario eseguire l'aggiornamento alla versione più recente di [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

Per un elenco completo degli endpoint del Dipartimento della Difesa del governo degli Stati Uniti, vedere la [documentazione](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints).

## <a name="azure-ad-pass-through-authentication"></a>Autenticazione pass-through di Azure AD

Le informazioni seguenti descrivono l'implementazione dell'autenticazione pass-through e il cloud di Azure per enti pubblici.

### <a name="allow-access-to-urls"></a>Consentire l'accesso agli URL

Prima di distribuire l'agente di autenticazione pass-through, verificare se esiste un firewall tra i server e Azure AD. Se il firewall o il proxy consentono programmi Domain Name System (DNS) bloccati o sicuri, aggiungere le connessioni seguenti.

> [!NOTE]
> Le linee guida seguenti si applicano anche all'installazione del [connettore del proxy dell'applicazione di Azure AD](https://aka.ms/whyappproxy) per gli ambienti di Azure per enti pubblici.

|URL |Uso|
|-----|-----|
|&#42;.msappproxy.us</br>&#42;.servicebus.usgovcloudapi.net|L'agente usa questi URL per comunicare con il servizio cloud di Azure AD. |
|mscrl.microsoft.us:80 </br>crl.microsoft.us:80 </br>ocsp.msocsp.us:80 </br>www.microsoft.us:80| L'agente usa questi URL per verificare i certificati.|
|login.windows.us </br>secure.aadcdn.microsoftonline-p.com </br>&#42;.microsoftonline.us </br>&#42;.microsoftonline-p.us </br>&#42;.msauth.net </br>&#42;.msauthimages.net </br>&#42;.msecnd.net</br>&#42;.msftauth.net </br>&#42;.msftauthimages.net</br>&#42;.phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| L'agente usa questi URL durante il processo di registrazione.

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Installare l'agente per il cloud di Azure per enti pubblici

Seguire questa procedura per installare l'agente per il cloud di Azure per enti pubblici:

1. Nel terminale della riga di comando passare alla cartella contenente il file eseguibile di installazione dell'agente.
1. Eseguire i comandi seguenti per specificare che si tratta di un'installazione per Azure per enti pubblici.

   Per l'autenticazione pass-through:

   ```
   AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
   ```

   Per il proxy dell'applicazione:

   ```
   AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
   ```

## <a name="single-sign-on"></a>Single sign-on

### <a name="set-up-your-azure-ad-connect-server"></a>Configurare il server di Azure AD Connect

Se come metodo di accesso si usa l'autenticazione pass-through, non sono necessari altri controlli dei prerequisiti. Se si usa invece la sincronizzazione dell'hash delle password e se è presente un firewall tra Azure AD Connect e Azure AD:

- Usare Azure AD Connect versione 1.1.644.0 o successiva.
- Se il firewall o il proxy consentono programmi DNS bloccati o sicuri, aggiungere le connessioni agli URL &#42;.msappproxy.us tramite la porta 443.

  In caso contrario, è necessario consentire l'accesso agli intervalli IP del data center di Azure, che vengono aggiornati ogni settimana. Questo prerequisito si applica solo quando si abilita la funzionalità. Non è necessario per gli accessi utente effettivi.

### <a name="roll-out-seamless-single-sign-on"></a>Implementare l'accesso Single Sign-On facile

È possibile implementare gradualmente l'accesso Single Sign-on facile di Azure AD per gli utenti usando le istruzioni seguenti. Prima di tutto, aggiungere l'URL di Azure AD [https://autologon.microsoft.us](https://autologon.microsoft.us) alle impostazioni dell'area Intranet di tutti gli utenti o di quelli selezionati usando Criteri di gruppo in Active Directory.

È inoltre necessario abilitare l'impostazione **Consenti aggiornamenti alla barra di stato tramite script con Criteri di gruppo** relativa ai criteri dell'area Intranet.

## <a name="browser-considerations"></a>Considerazioni sui browser

### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (tutte le piattaforme)

Mozilla Firefox non usa automaticamente l'autenticazione Kerberos. Ogni utente deve aggiungere manualmente l'URL di Azure AD alle impostazioni di Firefox seguendo questi passaggi:

1. Eseguire Firefox e immettere  **about:config** nella barra degli indirizzi. Ignorare tutte le eventuali notifiche visualizzate.
1. Cercare la preferenza **network.negotiate-auth.trusted-uris** . Questa preferenza elenca i siti considerati attendibili da Firefox per l'autenticazione Kerberos.
1. Fare clic con il pulsante destro del mouse sul nome della preferenza e scegliere  **Modifica**.
1. Immettere [ **https://autologon.microsoft.us** ](https://autologon.microsoft.us**)  nella casella.
1. Fare clic su  **OK** e riaprire il browser.

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge basato su Chromium (tutte le piattaforme)

Se è stato eseguito l'override delle impostazioni di criteri `AuthNegotiateDelegateAllowlist` o `AuthServerAllowlist` nell'ambiente, assicurarsi di aggiungere gli URL di Azure AD [https://autologon.microsoft.us](https://autologon.microsoft.us).

### <a name="google-chrome-all-platforms"></a>Google Chrome (tutte le piattaforme)

Se è stato eseguito l'override delle impostazioni di criteri `AuthNegotiateDelegateWhitelist` o `AuthServerWhitelist` nell'ambiente, assicurarsi di aggiungere gli URL di Azure AD [https://autologon.microsoft.us](https://autologon.microsoft.us).

## <a name="next-steps"></a>Passaggi successivi

- [Autenticazione pass-through](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
- [Single Sign-On](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)
