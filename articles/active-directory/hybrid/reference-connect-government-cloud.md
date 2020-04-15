---
title: 'Azure AD Connect: Hybrid identity considerations for Azure Government'
description: Considerazioni speciali per la distribuzione di Azure AD Connect con il cloud governativo.
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378925"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>Considerazioni sull'identità ibrida per Azure per enti pubbliciHybrid identity considerations for Azure Government 
Il documento seguente descrive le considerazioni per l'implementazione di un ambiente ibrido con il cloud di Azure per enti pubblici.  Queste informazioni vengono fornite come riferimento per gli amministratori e gli architetti che utilizzano il cloud di Azure per enti pubblici.  
> [!NOTE] 
> Per integrare un ambiente AD locale con il cloud Azure Governemnt, è necessario eseguire l'aggiornamento alla versione più recente di [Azure AD Connect.](https://www.microsoft.com/download/details.aspx?id=47594) 

> [!NOTE] 
> Per un elenco completo degli endpoint DoD governativi degli Stati Uniti, consultare la [documentazione](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) 

## <a name="pass-through-authentication"></a>Autenticazione pass-through 
Le informazioni seguenti vengono fornite per l'implementazione dell'autenticazione pass-through (PTA) e del cloud di Azure per enti pubblici.

### <a name="allow-access-to-urls"></a>Consentire l'accesso agli URL  
Prima di distribuire l'agente di autenticazione pass-through, verificare se è presente un firewall tra i server e Azure AD. Se il firewall o il proxy consente l'inserimento nella whitelist DNS, aggiungere le connessioni seguenti: 
> [!NOTE]
> Le indicazioni seguenti si applicano anche all'installazione del [connettore proxy](https://aka.ms/whyappproxy) di applicazione per gli ambienti Azure per enti pubblici.

|URL |Uso|
|-----|-----| 
|.msappproxy.us .servicebus.usgovcloudapi.net|Comunicazione tra l'agente e il servizio cloud di Azure AD |
|crl.microsoft.us:80 mscrl.microsoft.us:80 </br>www.microsoft.us:80 ocsp.msocsp.us:80| L'agente utilizza questi URL per verificare i certificati.| 
|login.windows.us secure.aadcdn.microsoftonline-p.com *.microsoftonline-p.us microsoftonline.us </br> * </br>*.msauth.net </br> *.msauthimages.net </br>*.msecnd.net</br>*.msftauth.net </br>*File</br>con estensione msftauthimages.net*.phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| L'agente utilizza questi URL durante il processo di registrazione.| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Installare l'agente per il cloud di Azure per enti pubbliciInstall the agent for the Azure Government cloud 
Per installare l'agente per il cloud di Azure per enti pubblici, è necessario seguire questi passaggi specifici: nel terminale della riga di comando passare alla cartella in cui si trova l'eseguibile per l'installazione dell'agente. Eseguire il comando seguente che specifica che l'installazione è per Azure per enti pubblici. 

Per l'autenticazione passthrough: 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

Per il proxy di applicazione:
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>Single Sign-On 
Configurare il server Azure AD Connect: se si usa l'autenticazione pass-through come metodo di accesso, non è necessario alcun controllo dei prerequisiti aggiuntivo. Se si usa la sincronizzazione dell'hash delle password come metodo di accesso e se è presente un firewall tra Azure AD Connect e Azure AD, verificare che:If you use password hash synchronization as your di-in method, and if there is a firewall between Azure AD Connect and Azure AD, ensure that:
- Usare Azure AD Connect 1.1.644.0 o versioni successive. 
- Se il firewall o il proxy consente l'inserimento nella whitelist DNS, aggiungere le connessioni agli URL con estensione msapproxy.us tramite la porta 443. In caso contrario, consentire l'accesso agli intervalli IP del data center di Azure, che vengono aggiornati settimanalmente. Questo prerequisito è applicabile solo quando si abilita la funzionalità. Non è necessario per gli accessi utente effettivi. 

### <a name="rolling-out-seamless-sso"></a>Implementazione di SSO senza interruzioni 
È possibile distribuire gradualmente la funzionalità Accesso Single Sign-On facile agli utenti usando le istruzioni seguenti. Per iniziare, aggiungere il seguente URL di Azure AD alle impostazioni dell'area Intranet di tutti gli utenti o di tutti gli utenti selezionati tramite Criteri di gruppo in Active Directory:https://autologon.microsoft.us 

Inoltre, è necessario abilitare un'impostazione dei criteri dell'area Intranet denominata Consenti aggiornamenti alla barra di stato tramite script tramite Criteri di gruppo. Considerazioni sul browser Mozilla Firefox (tutte le piattaforme) Mozilla Firefox non utilizza automaticamente l'autenticazione Kerberos. Ogni utente deve aggiungere manualmente l'URL di Azure AD alle impostazioni di Firefox eseguendo questi passaggi: 
1. Eseguire Firefox e immettere about:config nella barra degli indirizzi. Eliminare tutte le notifiche visualizzate. 
2. Cercare la preferenza network.negotiate-auth.trusted-uris. Questa preferenza elenca i siti attendibili di Firefox per l'autenticazione Kerberos. 
3. Fare clic con il pulsante destro del mouse e selezionare Modifica. 
4. Immettere https://autologon.microsoft.us nel campo.
5. Selezionare OK e riaprire il browser. 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge basato su Chromium (tutte le piattaforme) 
Se è stato `AuthNegotiateDelegateAllowlist` eseguito `AuthServerAllowlist` l'override delle impostazioni o dei criteri nell'ambiente, assicurarsi di aggiungere anche l'URL di Azure AD (anchehttps://autologon.microsoft.us) a loro. 

### <a name="google-chrome-all-platforms"></a>Google Chrome (tutte le piattaforme) 
Se è stato `AuthNegotiateDelegateWhitelist` eseguito `AuthServerWhitelist` l'override delle impostazioni o dei criteri nell'ambiente, assicurarsi di aggiungere anche l'URL di Azure AD (anchehttps://autologon.microsoft.us) a loro. 

## <a name="next-steps"></a>Passaggi successivi
[Pass-through Authentication](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
[Single Sign-On](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) per l'autenticazione pass-through 
