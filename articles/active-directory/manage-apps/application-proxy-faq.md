---
title: Domande frequenti sul proxy di applicazione di Azure AD Documenti Microsoft
description: Informazioni su risposte alle domande frequenti sull'uso del proxy di applicazione di Azure AD per pubblicare applicazioni interne locali per gli utenti remoti.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: ec9eeb0c35d96ee777771260686178faa536e909
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877304"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Domande frequenti sul proxy di applicazione di Active Directory (Azure AD)

Questa pagina risponde alle domande frequenti sul proxy di applicazione di Azure Active Directory (Azure AD).

## <a name="enabling-azure-ad-application-proxy"></a>Abilitazione del proxy di applicazione di Azure AD

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Quale licenza è necessaria per usare il proxy di applicazione di Azure AD?

Per usare il proxy di applicazione di Azure AD, è necessario disporre di una licenza di Azure AD Premium P1 o P2.To use Azure AD Application Proxy, you must have an Azure AD Premium P1 or P2 license. Per altre informazioni sulle licenze, vedere Prezzi di [Azure Active DirectoryFor more](https://azure.microsoft.com/pricing/details/active-directory/) information about licensing, see Azure Active Directory Pricing

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>Perché il pulsante "Abilita proxy di applicazione è disattivato?

Assicurarsi di avere installato almeno una licenza di Azure AD Premium P1 o P2 e un connettore proxy di applicazione di Azure AD. Dopo aver installato correttamente il primo connettore, il servizio proxy di applicazione di Azure AD verrà abilitato automaticamente.

## <a name="connector-configuration"></a>Configurazione del connettore

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>I servizi Application Proxy Connector possono essere eseguiti in un contesto utente diverso da quello predefinito?

No, questo scenario non è supportato. Le impostazioni predefinite sono le seguenti:

- Connettore proxy applicazioni AAD Microsoft - WAPCSvc - Servizio di rete
- Aggiornamento del connettore proxy di applicazione di Microsoft AAD - WAPCUpdaterSvc - Nt Authority

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>L'applicazione back-end è ospitata su più server Web e richiede la persistenza della sessione utente (appiccicoso). Come è possibile ottenere la persistenza della sessione? 

Per suggerimenti, vedere [Disponibilità elevata e bilanciamento del carico dei connettori e](application-proxy-high-availability-load-balancing.md)delle applicazioni del proxy di applicazione.

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>La terminazione TLS (ispezione o accelerazione TLS/HTTPS) sul traffico dai server del connettore ad Azure è supportata?

Il connettore proxy di applicazione esegue l'autenticazione basata su certificati in Azure.The Application Proxy Connector performs certificate-based authentication to Azure. Terminazione TLS (ispezione o accelerazione TLS/HTTPS) interrompe questo metodo di autenticazione e non è supportato. Il traffico dal connettore ad Azure deve ignorare tutti i dispositivi che eseguono la terminazione TLS.  

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>È necessario creare un account dedicato per registrare il connettore con il proxy di applicazione di Azure AD?

Non c'è motivo di farlo. Qualsiasi account amministratore globale o amministratore dell'applicazione funzionerà. Le credenziali immesse durante l'installazione non vengono utilizzate dopo il processo di registrazione. Al contrario, viene rilasciato un certificato al connettore, che viene utilizzato per l'autenticazione da quel punto in quel momento in cui.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Come è possibile monitorare le prestazioni del connettore proxy di applicazione di Azure AD?

Esistono contatori di Performance Monitor installati insieme al connettore. A tale scopo:  

1. Selezionare **Start**, digitare "Perfmon" e premere INVIO.
2. Selezionare **Performance Monitor** **+** e fare clic sull'icona verde.
3. Aggiungere i contatori di **Microsoft AAD Application Proxy Connector** che si desidera monitorare.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Il connettore del proxy di applicazione di Azure AD deve trovarsi nella stessa subnet della risorsa?

Non è necessario che il connettore si trovasse nella stessa subnet. Tuttavia, richiede la risoluzione dei nomi (DNS, file hosts) per la risorsa e la connettività di rete necessaria (routing alla risorsa, porte aperte sulla risorsa e così via). Per suggerimenti, vedere Considerazioni sulla topologia di rete quando si usa il proxy di applicazione di [Azure Active Directory.](application-proxy-network-topology.md)

## <a name="application-configuration"></a>Configurazione dell'applicazione

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Qual è la lunghezza del timeout back-end predefinito e "lungo"? Il timeout può essere prolungato?

La lunghezza predefinita è 85 secondi. L'impostazione "lunga" è 180 secondi. Il limite di timeout non può essere esteso.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Come faccio a cambiare la pagina di destinazione caricata dalla mia applicazione?

Dalla pagina Registrazioni applicazioni, è possibile modificare l'URL della home page con l'URL esterno desiderato della pagina di destinazione. La pagina specificata verrà caricata quando l'applicazione viene avviata da App personali o dal portale di Office 365. Per i passaggi di configurazione, vedere [Impostare una home page personalizzata per le app pubblicate usando](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page) il proxy di applicazione di Azure ADFor configuration steps, see Set a custom home page for published apps by using Azure AD Application Proxy

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>È possibile pubblicare solo applicazioni basate su IIS? Che dire delle applicazioni Web in esecuzione su server web non Windows? Il connettore deve essere installato in un server in cui è installato IIS?

No, non esiste alcun requisito di IIS per le applicazioni pubblicate. È possibile pubblicare applicazioni Web in esecuzione su server diversi da Windows Server. Tuttavia, potrebbe non essere possibile utilizzare la preautenticazione con un server non Windows, a seconda se il server web supporta Negozia (autenticazione Kerberos). IIS non è necessario nel server in cui è installato il connettore.

## <a name="integrated-windows-authentication"></a>Autenticazione integrata di Windows

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>Quando è necessario utilizzare il metodo PrincipalsAllowedToDelegateToAccount durante l'impostazione della delega vincolata Kerberos (KCD)?

Il metodo PrincipalsAllowedToDelegateToAccount viene utilizzato quando i server del connettore si trovano in un dominio diverso dall'account del servizio dell'applicazione Web. Richiede l'uso della delega vincolata basata sulle risorse.
Se i server dei connettori e l'account del servizio dell'applicazione Web si trovano nello stesso dominio, è possibile utilizzare Utenti e computer di Active Directory per configurare le impostazioni di delega in ogni account del computer connettore, consentendo loro di delegare al nome SPN di destinazione.

Se i server connettore e l'account del servizio dell'applicazione Web si trovano in domini diversi, viene utilizzata la delega basata sulle risorse. Le autorizzazioni di delega vengono configurate nell'account del servizio del server Web e dell'applicazione Web di destinazione. Questo metodo di delega vincolata è relativamente nuovo. Il metodo è stato introdotto in Windows Server 2012, che supporta la delega tra domini consentendo al proprietario della risorsa (servizio Web) di controllare quali account computer e di servizio possono delegare. Non è disponibile alcuna interfaccia utente per facilitare questa configurazione, pertanto è necessario usare PowerShell.There's no UI to assist with this configuration, so you'll need to use PowerShell.
Per ulteriori informazioni, vedere il white paper [Informazioni sulla delega vincolata Kerberos con il proxy di applicazione](https://aka.ms/kcdpaper).

## <a name="pass-through-authentication"></a>Autenticazione pass-through

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>È possibile utilizzare i criteri di accesso condizionale per le applicazioni pubblicate con l'autenticazione pass-through?

I criteri di accesso condizionale vengono applicati solo per gli utenti pre-autenticati correttamente in Azure AD. L'autenticazione pass-through non attiva l'autenticazione di Azure AD, pertanto i criteri di accesso condizionale non possono essere applicati. Con l'autenticazione pass-through, i criteri di autenticazione a più fattori devono essere implementati nel server locale, se possibile, o abilitando la preautenticazione con il proxy di applicazione di Azure AD.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>È possibile pubblicare un'applicazione Web con requisiti di autenticazione del certificato client?

No, questo scenario non è supportato perché il proxy di applicazione terminerà il traffico TLS.  

## <a name="remote-desktop-gateway-publishing"></a>Pubblicazione di Gateway Desktop remoto

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Come è possibile pubblicare Gateway Desktop remoto tramite il proxy di applicazione di Azure AD?

Fare riferimento a [Pubblica desktop remoto con il proxy di applicazione](application-proxy-integrate-with-remote-desktop-services.md)di Azure AD .

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>È possibile utilizzare la delega vincolata Kerberos (Single Sign-On - Autenticazione integrata di Windows) nello scenario di pubblicazione di Gateway Desktop remoto?

No, questo scenario non è supportato.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>Gli utenti non utilizzano Internet Explorer 11 e lo scenario di pre-autenticazione non funziona per loro. È normale?

Sì, ci si aspetta. Lo scenario di preautenticazione richiede un controllo ActiveX, che non è supportato nei browser di terze parti.

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>È supportato Remote Desktop Web Client (HTML5)?

No, questo scenario non è attualmente supportato. Segui il nostro forum di commenti e suggerimenti [UserVoice](https://aka.ms/aadapuservoice) per gli aggiornamenti su questa funzionalità.

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>Dopo aver configurato lo scenario di pre-autenticazione, mi sono reso conto che l'utente deve eseguire l'autenticazione due volte: prima nel modulo di accesso di Azure AD e quindi nel modulo di accesso RDWeb. È normale? Come posso ridurlo a un unico accesso?

Sì, ci si aspetta. Se il computer dell'utente è aggiunto ad Azure AD, l'utente accede automaticamente ad Azure AD. L'utente deve fornire le proprie credenziali solo nel modulo di accesso RDWeb.

## <a name="sharepoint-publishing"></a>Pubblicazione di SharePoint

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Come è possibile pubblicare SharePoint tramite il proxy di applicazione di Azure AD?

Fare riferimento a [Abilitare l'accesso remoto a SharePoint con il proxy di applicazione](application-proxy-integrate-with-sharepoint-server.md)di Azure AD .

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>È possibile usare l'app SharePoint per dispositivi mobili (iOS/Android) per accedere a un server SharePoint pubblicato?

[L'app SharePoint per dispositivi mobili](https://docs.microsoft.com/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises) non supporta attualmente la preautenticazione di Azure Active Directory.

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Pubblicazione di Active Directory Federation Services (ADFS) 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>È possibile usare il proxy di applicazione di Azure AD come proxy AD FS (ad esempio Proxy applicazione Web)?

No. Il proxy di applicazione di Azure AD è progettato per funzionare con Azure AD e non soddisfa i requisiti per fungere da proxy AD FS.

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>WebSocket supporta applicazioni diverse da QlikSense?

Attualmente, il supporto del protocollo WebSocket è ancora in anteprima pubblica e potrebbe non funzionare per altre applicazioni. Alcuni clienti hanno avuto un successo misto utilizzando il protocollo WebSocket con altre applicazioni. Se si testa tali scenari, ci piacerebbe sentire i risultati. Vi preghiamo di aadapfeedback@microsoft.cominviarci il vostro feedback a .

Le funzionalità (registri eventi, PowerShell e Servizi Desktop remoto) nell'interfaccia di amministrazione di Windows (WAC) o nel client Web Desktop remoto (HTML5) non funzionano attualmente tramite il proxy di applicazione di Azure AD.

## <a name="link-translation"></a>Traduzione dei link

### <a name="does-using-link-translation-affect-performance"></a>L'utilizzo della conversione dei collegamenti influisce sulle prestazioni?

Sì. La conversione dei link influisce sulle prestazioni. Il servizio proxy di applicazione analizza l'applicazione alla ricerca di collegamenti hardcoded e li sostituisce con i rispettivi URL esterni pubblicati prima di presentarli all'utente. 

Per ottenere prestazioni ottimali, è consigliabile utilizzare URL interni ed esterni identici configurando [domini personalizzati.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain) Se l'utilizzo di domini personalizzati non è possibile, è possibile migliorare le prestazioni di conversione dei collegamenti utilizzando l'estensione My Apps Secure Sign in Extension o Microsoft Edge Browser su dispositivi mobili. Vedere [Reindirizzare i collegamenti hardcoded per le app pubblicate con](application-proxy-configure-hard-coded-link-translation.md)il proxy di applicazione di Azure AD.

## <a name="wildcards"></a>Caratteri jolly

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Come si usano i caratteri jolly per pubblicare due applicazioni con lo stesso nome di dominio personalizzato ma con protocolli diversi, uno per HTTP e uno per HTTPS?

Questo scenario non è supportato direttamente. Le opzioni per questo scenario sono:

1. Pubblicare entrambi gli URL HTTP e HTTPS come applicazioni separate con un carattere jolly, ma assegnare a ciascuno di essi un dominio personalizzato diverso. Questa configurazione funzionerà poiché hanno URL esterni diversi.

2. Pubblicare l'URL HTTPS tramite un'applicazione con caratteri jolly. Pubblicare le applicazioni HTTP separatamente utilizzando i cmdlet powerShell del proxy di applicazione seguenti:Publish the HTTP applications separately using these Application Proxy PowerShell cmdlets:
   - [Gestione applicazione proxy applicazione applicazione](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [Gestione connettore proxy di applicazione](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
