---
title: Domande frequenti sul proxy di applicazione Azure AD | Microsoft Docs
description: Informazioni sulle risposte alle domande frequenti sull'uso del proxy di applicazione Azure AD per pubblicare applicazioni locali locali per gli utenti remoti.
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
ms.openlocfilehash: 612b6caf47ec4764aa2bbef162592100198ed0c4
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832204"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Domande frequenti sul proxy di applicazione Active Directory (Azure AD)

In questa pagina vengono fornite le risposte alle domande frequenti sul proxy di applicazione Azure Active Directory (Azure AD).

## <a name="enabling-azure-ad-application-proxy"></a>Abilitazione del proxy dell'applicazione Azure AD

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Quale licenza è necessaria per usare Azure AD proxy di applicazione?

Per usare Azure AD proxy di applicazione, è necessario disporre di una licenza Azure AD Premium P1 o P2. Per ulteriori informazioni sulle licenze, vedere [Azure Active Directory prezzi](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>Perché il pulsante di abilitazione del proxy di applicazione è disattivato?

Assicurarsi di avere almeno una licenza Azure AD Premium P1 o P2 e un connettore proxy di applicazione Azure AD installato. Dopo aver installato correttamente il primo connettore, il servizio proxy applicazione Azure AD verrà abilitato automaticamente.

## <a name="connector-configuration"></a>Configurazione connettore

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>I servizi del connettore proxy di applicazione possono essere eseguiti in un contesto utente diverso da quello predefinito?

No, questo scenario non è supportato. Le impostazioni predefinite sono le seguenti:

- Connettore del proxy di applicazione di Microsoft AAD-WAPCSvc-servizio di rete
- Microsoft AAD Application Proxy Connector Updater-WAPCUpdaterSvc-NT Authority\System

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>L'applicazione back-end è ospitata in più server Web e richiede la persistenza della sessione utente (appiccicosa). Come è possibile ottenere la persistenza della sessione? 

Per indicazioni, vedere [disponibilità elevata e bilanciamento del carico delle applicazioni e dei connettori del proxy di applicazione](application-proxy-high-availability-load-balancing.md).

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>È possibile inserire un dispositivo proxy di inoltro tra i server del connettore e il server applicazioni back-end?

No, questo scenario non è supportato. Solo il connettore e i servizi di aggiornamento possono essere configurati per usare un proxy di inoltro per il traffico in uscita in Azure. Vedere [usare server proxy locali esistenti](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers)

### <a name="is-ssl-termination-sslhhtps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>La terminazione SSL (ispezione SSL/HHTPS o accelerazione) sul traffico dai server del connettore ad Azure è supportata?

Il connettore del proxy di applicazione esegue l'autenticazione basata sui certificati in Azure. Terminazione SSL (ispezione SSL/HHTPS o accelerazione) interrompe questo metodo di autenticazione e non è supportato. Il traffico dal connettore ad Azure deve ignorare tutti i dispositivi che eseguono la terminazione SSL.  

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>È necessario creare un account dedicato per registrare il connettore con Azure AD proxy di applicazione?

Nessun motivo. Tutti gli account di amministratore globale o di amministratore dell'applicazione funzioneranno. Le credenziali immesse durante l'installazione non vengono usate dopo il processo di registrazione. Al contrario, viene emesso un certificato al connettore, che viene usato per l'autenticazione da quel punto in poi.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Come è possibile monitorare le prestazioni del connettore del proxy di applicazione Azure AD?

Sono presenti contatori di performance monitor installati insieme al connettore. Per visualizzarli:  

1. Selezionare **Start**, digitare "perfmon" e premere INVIO.
2. Selezionare **Performance Monitor** e fare clic sull'icona verde **+** .
3. Aggiungere i contatori del **connettore del proxy di applicazione di Microsoft AAD** che si desidera monitorare.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Il connettore del proxy di applicazione Azure AD deve trovarsi nella stessa subnet della risorsa?

Il connettore non deve trovarsi nella stessa subnet. Tuttavia, richiede la risoluzione dei nomi (DNS, file hosts) per la risorsa e la connettività di rete necessaria (routing alla risorsa, porte aperte sulla risorsa e così via). Per indicazioni, vedere [considerazioni sulla topologia di rete quando si usa Azure Active Directory Application Proxy](application-proxy-network-topology.md).

## <a name="application-configuration"></a>Configurazione dell'applicazione

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Qual è la lunghezza del timeout di back-end predefinito e "Long"? Il timeout può essere esteso?

La lunghezza predefinita è pari a 85 secondi. L'impostazione "Long" è 180 secondi. Il limite di timeout non può essere esteso.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Ricerca per categorie modificare la pagina di destinazione caricata dall'applicazione?

Dalla pagina registrazioni applicazioni è possibile modificare l'URL della Home page dell'URL esterno desiderato della pagina di destinazione. La pagina specificata verrà caricata quando l'applicazione viene avviata da app personali o dal portale di Office 365. Per i passaggi di configurazione, vedere [impostare un Home page personalizzato per le app pubblicate usando Azure ad proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>È possibile pubblicare solo le applicazioni basate su IIS? Che cosa accade per le applicazioni Web in esecuzione su server Web non Windows? Il connettore deve essere installato in un server in cui è installato IIS?

No, non è previsto alcun requisito IIS per le applicazioni pubblicate. È possibile pubblicare applicazioni Web in esecuzione su server diversi da Windows Server. Tuttavia, potrebbe non essere possibile usare la preautenticazione con un server non Windows, a seconda che il server Web supporti la negoziazione (autenticazione Kerberos). IIS non è richiesto nel server in cui è installato il connettore.

## <a name="integrated-windows-authentication"></a>Autenticazione integrata di Windows

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>Quando è consigliabile usare il metodo PrincipalsAllowedToDelegateToAccount quando si configura la delega vincolata Kerberos (delega vincolata Kerberos)?

Il metodo PrincipalsAllowedToDelegateToAccount viene usato quando i server del connettore si trovano in un dominio diverso dall'account del servizio dell'applicazione Web. Richiede l'uso della delega vincolata basata sulle risorse.
Se i server del connettore e l'account del servizio dell'applicazione Web si trovano nello stesso dominio, è possibile utilizzare Active Directory utenti e computer per configurare le impostazioni di delega in ogni account del computer connettore, consentendo loro di delegare al nome SPN di destinazione.

Se i server del connettore e l'account del servizio dell'applicazione Web si trovano in domini diversi, viene utilizzata la delega basata sulle risorse. Le autorizzazioni di delega sono configurate nel server Web di destinazione e nell'account del servizio dell'applicazione Web. Questo metodo di delega vincolata è relativamente nuovo. Il metodo è stato introdotto in Windows Server 2012, che supporta la delega tra domini consentendo al proprietario della risorsa (servizio Web) di controllare quali account del computer e del servizio possono delegare. Non è disponibile alcuna interfaccia utente per semplificare questa configurazione, quindi è necessario usare PowerShell.
Per ulteriori informazioni, vedere il white paper informazioni sulla [delega vincolata Kerberos con proxy di applicazione](https://aka.ms/kcdpaper).

## <a name="pass-through-authentication"></a>Autenticazione pass-through

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>È possibile usare I criteri di accesso condizionale per le applicazioni pubblicate con l'autenticazione pass-through?

I criteri di accesso condizionale vengono applicati solo per gli utenti pre-autenticati correttamente in Azure AD. Con l'autenticazione pass-through non viene attivato Azure AD autenticazione, pertanto non è possibile applicare i criteri di accesso condizionale. Con l'autenticazione pass-through, è necessario implementare i criteri di autenticazione a più fattori nel server locale, se possibile, o abilitando la pre-autenticazione con Azure AD proxy di applicazione.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>È possibile pubblicare un'applicazione Web con il requisito di autenticazione del certificato client?

No, questo scenario non è supportato perché il proxy di applicazione terminerà il traffico TLS.  

## <a name="remote-desktop-gateway-publishing"></a>Pubblicazione del Gateway Desktop remoto

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Come è possibile pubblicare Desktop remoto Gateway su Azure AD proxy di applicazione?

Vedere [pubblicare desktop remoto con Azure ad proxy di applicazione](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="can-i-use-kerberos-constrained-delegation-in-the-remote-desktop-gateway-publishing-scenario"></a>È possibile usare la delega vincolata Kerberos nello scenario di pubblicazione del Gateway Desktop remoto?

No, questo scenario non è supportato.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>Gli utenti non usano Internet Explorer 11 e lo scenario di pre-autenticazione non funziona. È previsto?

Sì, è previsto. Lo scenario di pre-autenticazione richiede un controllo ActiveX, che non è supportato nei browser di terze parti.

### <a name="is-the-remote-desktop-web-client-supported"></a>Il client Web di Desktop remoto è supportato?

No, questo scenario non è attualmente supportato. Per gli aggiornamenti su questa funzionalità, seguire il forum dei commenti e suggerimenti [UserVoice](https://aka.ms/aadapuservoice) .

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>Dopo aver configurato lo scenario di pre-autenticazione, ho notato che l'utente deve eseguire l'autenticazione due volte: prima nel modulo di accesso Azure AD e quindi nel modulo di accesso RDWeb. È previsto? Come posso ridurlo a un accesso?

Sì, è previsto. Se il computer dell'utente è Azure AD aggiunto, l'utente accederà Azure AD automaticamente. L'utente deve fornire le proprie credenziali solo nel modulo di accesso RDWeb.

## <a name="sharepoint-publishing"></a>Pubblicazione di SharePoint

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Come è possibile pubblicare SharePoint su Azure AD proxy di applicazione?

Vedere [abilitare l'accesso remoto a SharePoint con Azure ad proxy di applicazione](application-proxy-integrate-with-sharepoint-server.md).

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Pubblicazione di Active Directory Federation Services (AD FS) 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>È possibile usare Azure AD proxy di applicazione come AD FS proxy (ad esempio proxy applicazione Web)?

No. Azure AD proxy di applicazione è progettato per funzionare con Azure AD e non soddisfa i requisiti per fungere da proxy di AD FS.

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>WebSocket supporta il lavoro per applicazioni diverse da Qlik Sense?

Attualmente, il supporto del protocollo WebSocket è ancora in anteprima pubblica e potrebbe non funzionare per altre applicazioni. Alcuni clienti hanno avuto un successo misto usando il protocollo WebSocket con altre applicazioni. Se si testano tali scenari, si apprezzeranno i risultati. Inviare commenti e suggerimenti all'aadapfeedback@microsoft.com.

## <a name="link-translation"></a>Conversione collegamento

### <a name="does-using-link-translation-affect-performance"></a>L'uso della traduzione del collegamento influisce sulle prestazioni?

Sì. La conversione del collegamento influiscono sulle prestazioni. Il servizio proxy applicazione esegue l'analisi dell'applicazione per i collegamenti hardcoded e li sostituisce con i rispettivi URL esterni pubblicati prima di presentarli all'utente. 

Per ottenere prestazioni ottimali, è consigliabile utilizzare URL interni ed esterni identici configurando [domini personalizzati](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain). Se l'uso di domini personalizzati non è possibile, è possibile migliorare le prestazioni di conversione dei collegamenti usando l'estensione di accesso sicuro app personali o il browser Microsoft Edge su dispositivi mobili. Vedere [Reindirizzamento dei collegamenti hardcoded per le app pubblicate con Azure ad proxy di applicazione](application-proxy-configure-hard-coded-link-translation.md).

## <a name="wildcards"></a>Caratteri jolly

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Ricerca per categorie utilizzare caratteri jolly per pubblicare due applicazioni con lo stesso nome di dominio personalizzato ma con protocolli diversi, uno per HTTP e uno per HTTPS?

Questo scenario non è supportato direttamente. Le opzioni per questo scenario sono:

1. Pubblicare gli URL HTTP e HTTPS come applicazioni separate con un carattere jolly, ma assegnare a ognuno di essi un dominio personalizzato diverso. Questa configurazione funzionerà poiché hanno URL esterni diversi.

2. Pubblicare l'URL HTTPS tramite un'applicazione con caratteri jolly. Pubblicare le applicazioni HTTP separatamente usando questi cmdlet di PowerShell del proxy di applicazione:
   - [Gestione applicazioni del proxy di applicazione](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [Gestione connettore del proxy di applicazione](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
