---
title: 'Azure AD Connect: considerazioni sulle identità ibride per Azure per enti pubblici'
description: Considerazioni speciali per la distribuzione di Azure AD Connect con il cloud per enti pubblici.
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
ms.openlocfilehash: 2ad28beb68afb5207e7b36c5d76c4dac808c5114
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81378925"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>Considerazioni sulle identità ibride per Azure per enti pubblici 
Il documento seguente descrive le considerazioni per l'implementazione di un ambiente ibrido con il cloud di Azure per enti pubblici.  Queste informazioni vengono fornite come riferimento per gli amministratori e i progettisti che lavorano con il cloud di Azure per enti pubblici.  
> [!NOTE] 
> Per integrare un ambiente AD locale con Azure governemnt cloud, è necessario eseguire l'aggiornamento alla versione più recente di [Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594). 

> [!NOTE] 
> Per un elenco completo degli endpoint DoD del governo degli Stati Uniti, vedere la [documentazione](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) 

## <a name="pass-through-authentication"></a>Autenticazione pass-through 
Per l'implementazione dell'autenticazione pass-through (PTA) e del cloud di Azure per enti pubblici vengono fornite le informazioni seguenti.

### <a name="allow-access-to-urls"></a>Consentire l'accesso agli URL  
Prima di distribuire l'agente di autenticazione pass-through, verificare se è presente un firewall tra i server e Azure AD. Se il firewall o il proxy consente l'elenco elementi consentiti DNS, aggiungere le connessioni seguenti: 
> [!NOTE]
> Le linee guida seguenti si applicano anche all'installazione del [connettore proxy di applicazione](https://aka.ms/whyappproxy) per gli ambienti di Azure per enti pubblici.

|URL |Uso|
|-----|-----| 
|*. msappproxy.us *. servicebus.usgovcloudapi.net|Comunicazione tra l'agente e il servizio Cloud Azure AD |
|crl.microsoft.us:80 mscrl.microsoft.us:80 </br>www.microsoft.us:80 ocsp.msocsp.us:80| L'agente utilizza questi URL per verificare i certificati.| 
|login.Windows.US Secure.aadcdn.microsoftonline-p.com *. microsoftonline.US </br> *. microsoftonline-p.US </br>*. msauth.NET </br> *. msauthimages.NET </br>*. msecnd.NET</br>*. msftauth.NET </br>*. msftauthimages.NET</br>*. phonefactor.NET </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| L'agente utilizza questi URL durante il processo di registrazione.| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Installare l'agente per il cloud di Azure per enti pubblici 
Per installare l'agente per il cloud di Azure per enti pubblici, è necessario seguire questa procedura specifica: nel terminale della riga di comando passare alla cartella in cui si trova il file eseguibile per l'installazione dell'agente. Eseguire il comando seguente, che specifica che l'installazione è per Azure per enti pubblici. 

Per l'autenticazione pass-through: 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

Per il proxy di applicazione:
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>Single Sign-On 
Configurare il server Azure AD Connect: se si usa l'autenticazione pass-through come metodo di accesso, non è necessario alcun controllo dei prerequisiti aggiuntivo. Se si usa la sincronizzazione dell'hash delle password come metodo di accesso e se è presente un firewall tra Azure AD Connect e Azure AD, assicurarsi che:
- Usare Azure AD Connect 1.1.644.0 o versioni successive. 
- Se il firewall o il proxy consente l'elenco elementi consentiti DNS, aggiungere le connessioni agli URL *. msapproxy.us sulla porta 443. In caso contrario, consentire l'accesso agli intervalli IP del Data Center di Azure, che vengono aggiornati ogni settimana. Questo prerequisito è applicabile solo quando si abilita la funzionalità. Non è necessario per gli accessi utente effettivi. 

### <a name="rolling-out-seamless-sso"></a>Implementazione di seamless SSO 
È possibile distribuire gradualmente la funzionalità Accesso Single Sign-On facile agli utenti usando le istruzioni seguenti. Per iniziare, aggiungere l'URL di Azure AD seguente a tutte le impostazioni dell'area Intranet di o di tutti gli utenti selezionati usando Criteri di gruppo in Active Directory:https://autologon.microsoft.us 

Inoltre, è necessario abilitare un'impostazione dei criteri di area Intranet denominata Consenti aggiornamenti alla barra di stato tramite script tramite Criteri di gruppo. Considerazioni sul browser Mozilla Firefox (tutte le piattaforme) Mozilla Firefox non usa automaticamente l'autenticazione Kerberos. Ogni utente deve aggiungere manualmente l'URL di Azure AD alle impostazioni di Firefox eseguendo questi passaggi: 
1. Eseguire Firefox e immettere about: config nella barra degli indirizzi. Eliminare tutte le notifiche visualizzate. 
2. Cercare la preferenza network. Negotiate-auth. trusted-URI. Questa preferenza elenca i siti attendibili di Firefox per l'autenticazione Kerberos. 
3. Fare clic con il pulsante destro del mouse e scegliere modifica. 
4. Immettere https://autologon.microsoft.us nel campo.
5. Selezionare OK e quindi riaprire il browser. 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge basato su cromo (tutte le piattaforme) 
 `AuthNegotiateDelegateAllowlist` Se è stato eseguito l'override di o `AuthServerAllowlist` delle impostazioni dei criteri nell'ambiente in uso, assicurarsihttps://autologon.microsoft.us) di aggiungervi anche l'URL del Azure ad. 

### <a name="google-chrome-all-platforms"></a>Google Chrome (tutte le piattaforme) 
 `AuthNegotiateDelegateWhitelist` Se è stato eseguito l'override di o `AuthServerWhitelist` delle impostazioni dei criteri nell'ambiente in uso, assicurarsihttps://autologon.microsoft.us) di aggiungervi anche l'URL del Azure ad. 

## <a name="next-steps"></a>Passaggi successivi
[Autenticazione pass-through](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
[Single Sign-on](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) 
