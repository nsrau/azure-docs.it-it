---
title: Accesso remoto ad app locali - Azure Active Directory Application Proxy | Microsoft Docx
description: Azure Active Directory Application Proxy consente l'accesso remoto sicuro ad applicazioni Web locali. Dopo un accesso singolo (SSO) ad Azure AD, gli utenti possono accedere sia alle applicazioni nel cloud che a quelle locali tramite un URL esterno o un portale per le applicazioni interno. Ad esempio, Application Proxy può rendere disponibile l'accesso remoto e Single Sign-On a Desktop remoto, SharePoint, Teams, Tableau, Qlik e applicazioni line-of-business (LOB).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb3a3d85c9cac85b6658167806cea59b6d5163de
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782857"
---
# <a name="remote-access-to-on-premises-applications-through-azure-active-directorys-application-proxy"></a>Accesso remoto ad applicazioni locali tramite Azure Active Directory Application Proxy 

Azure Active Directory Application Proxy consente l'accesso remoto sicuro ad applicazioni Web locali. Dopo un accesso singolo (SSO) ad Azure AD, gli utenti possono accedere sia alle applicazioni nel cloud che a quelle locali tramite un URL esterno o un portale per le applicazioni interno. Ad esempio, Application Proxy può rendere disponibile l'accesso remoto e Single Sign-On a Desktop remoto, SharePoint, Teams, Tableau, Qlik e applicazioni line-of-business (LOB).

Il proxy di applicazione di Azure AD:

- **Semplice da usare**. Gli utenti possono accedere alle applicazioni locali nello stesso modo in cui accedono a Office 365 e alle altre app SaaS integrate in Azure AD. Non è necessario modificare o aggiornare le applicazioni per usare il proxy di applicazione. 

- **Sicuro**. Le applicazioni locali possono usare i controlli di autorizzazione e le funzionalità di analisi della sicurezza di Azure. Ad esempio, le applicazioni locali possono usare l'accesso condizionale e la verifica in due passaggi. Application Proxy non richiede l'apertura delle connessioni in ingresso attraverso il firewall.
 
- **Convenienza**. Le soluzioni locali richiedono generalmente di configurare e gestire reti perimetrali, server perimetrali o altre infrastrutture complesse. Application Proxy viene eseguito nel cloud ed è quindi facile da usare. Per usare Application Proxy non è necessario modificare l'infrastruttura di rete o installare altre appliance nell'ambiente locale.

## <a name="what-is-application-proxy"></a>Che cos'è Application Proxy?
Application Proxy è una funzionalità di Azure AD che consente agli utenti di accedere alle applicazioni Web locali da un client remoto. Application Proxy include sia il servizio Application Proxy eseguito nel cloud, che il connettore Application Proxy eseguito in un server locale. Azure AD, il servizio Application Proxy e il connettore Application Proxy interagiscono per passare in modo sicuro il token di accesso dell'utente da Azure AD all'applicazione Web.

Application Proxy supporta:

* Applicazioni Web che usano l'[autenticazione integrata di Windows](application-proxy-configure-single-sign-on-with-kcd.md) per l'autenticazione  
* Applicazioni Web che usano l'accesso basato su form o su [intestazione](application-proxy-configure-single-sign-on-with-ping-access.md)  
* API Web che si vuole esporre ad applicazioni avanzate in dispositivi diversi  
* Applicazioni ospitate dietro [Gateway Desktop remoto](application-proxy-integrate-with-remote-desktop-services.md)  
* App rich client integrate con Active Directory Authentication Library (ADAL)

Application Proxy supporta Single Sign-On. Per altre informazioni sui metodi supportati, vedere [Scelta di un metodo di accesso Single Sign-On](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

Proxy dell'applicazione è consigliabile per consentire agli utenti remoti esterni l'accesso alle risorse interne. Proxy dell'applicazione sostituisce la necessità di una VPN o un proxy inverso. Non è destinato agli utenti interni nella rete aziendale.  Gli utenti interni che usano inutilmente il Proxy di applicazione possono introdurre problemi di prestazioni impreviste e indesiderate.

## <a name="how-application-proxy-works"></a>Come funziona Application Proxy

Il diagramma seguente illustra come interagiscono Azure AD e Application Proxy per offrire l'accesso Single Sign-On alle applicazioni locali.

![Diagramma del proxy dell'applicazione di AzureAD](./media/application-proxy/azureappproxxy.png)

1. Dopo che accede all'applicazione tramite un endpoint, l'utente viene reindirizzato alla pagina di accesso di Azure AD. 
2. Dopo un accesso corretto, Azure AD invia un token al dispositivo client dell'utente.
3. Il client invia il token al servizio Application Proxy che recupera dal token il nome dell'entità utente (UPN) e il nome dell'entità di sicurezza (SPN). Application Proxy invia quindi la richiesta al connettore Application Proxy.
4. Se è stato configurato Single Sign-On, il connettore esegue le autenticazioni aggiuntive necessarie per conto dell'utente.
5. Il connettore invia la richiesta all'applicazione locale.  
6. La risposta viene inviata all'utente tramite il servizio e il connettore Application Proxy.

| Componente | Descrizione |
| --------- | ----------- |
| Endpoint  | L'endpoint è un URL o un [portale per gli utenti finali](end-user-experiences.md). Gli utenti possono raggiungere le applicazioni all'esterno della rete accedendo a un URL esterno. Gli utenti all'interno della rete possono accedere all'applicazione tramite un URL o un portale per gli utenti finali. Quando gli utenti proseguono verso uno di questi endpoint, si autenticano in Azure AD e quindi vengono instradati tramite il connettore all'applicazione locale.|
| Azure AD | Azure AD esegue l'autenticazione usando la directory del tenant archiviata nel cloud. |
| Servizio Application Proxy | Il servizio Application Proxy viene eseguito nel cloud come parte di Azure AD. Il servizio passa il token di accesso dall'utente al connettore Application Proxy. Application Proxy inoltra eventuali intestazioni accessibili nella richiesta e imposta le intestazioni in base al relativo protocollo, all'indirizzo IP del client. Se la richiesta in ingresso per il proxy include già tale intestazione, l'indirizzo IP del client viene aggiunto alla fine dell'elenco delimitato da virgole che rappresenta il valore dell'intestazione.|
| Connettore Application Proxy | Il connettore è un agente semplice eseguito in un server Windows all'interno della rete. Il connettore gestisce la comunicazione tra il servizio Application Proxy nel cloud e l'applicazione locale. Il connettore usa solo connessioni in uscita, per cui non è necessario aprire porte in ingresso né inserire nulla nella rete perimetrale. I connettori sono senza stato e prelevano le informazioni dal cloud in base alle esigenze. Per altre informazioni sui connettori, ad esempio come bilanciano il carico ed effettuano l'autenticazione, vedere [Comprendere i connettori del proxy applicazione Azure AD](application-proxy-connectors.md).|
| Active Directory (AD) | Active Directory viene eseguito in locale per eseguire l'autenticazione per gli account di dominio. Quando si configura l'accesso Single Sign-On, il connettore comunica con AD per eseguire eventuali autenticazioni aggiuntive necessarie.
| Applicazione locale | Infine, l'utente è in grado di accedere a un'applicazione locale. 

## <a name="next-steps"></a>Passaggi successivi
Per iniziare a usare Application Proxy, vedere [Esercitazione: Aggiungere un'applicazione locale per l'accesso remoto tramite Application Proxy](application-proxy-add-on-premises-application.md). 

Per le notizie e gli aggiornamenti più recenti, vedere il [blog di Application Proxy](https://blogs.technet.com/b/applicationproxyblog/)


