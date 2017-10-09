---
title: Come fornire l'accesso remoto sicuro alle applicazioni locali
description: Viene illustrato come utilizzare il proxy dell'applicazione di Azure AD per fornire l'accesso remoto sicuro alle applicazioni locali.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: 426056d394af0a9ded28202615cb80c7b50e59fc
ms.contentlocale: it-it
ms.lasthandoff: 09/28/2017

---

# <a name="how-to-provide-secure-remote-access-to-on-premises-applications"></a>Come fornire l'accesso remoto sicuro alle applicazioni locali

Oggi i dipendenti vogliono essere produttivi in qualsiasi luogo, in qualsiasi momento e da qualsiasi dispositivo. Vogliono lavorare con i propri dispositivi, che si tratti di tablet, di telefoni o di portatili, e si aspettano di poter accedere a tutte le applicazioni, sia le app SaaS nel cloud sia le app aziendali locali. Per fornire l'accesso alle applicazioni locali, sono sempre state necessarie reti private virtuali (VPN) o reti perimetrali . Queste soluzioni non sono solo complesse e difficili da proteggere, ma sono anche costose da configurare e gestire.

C'è una soluzione migliore.

Una forza lavoro moderna in un mondo dominato dai dispositivi mobili e basato sul cloud richiede una soluzione di accesso remoto moderna. Il proxy dell'applicazione di Azure AD è una funzionalità di Azure Active Directory che offre l'accesso remoto come servizio. Per questo è facile da distribuire, usare e gestire.

[!INCLUDE [identity](../../includes/azure-ad-licenses.md)]

## <a name="what-is-azure-active-directory-application-proxy"></a>Informazioni sul proxy dell'applicazione di Azure Active Directory
Il proxy dell'applicazione di Azure AD fornisce sia l'accesso Single Sign-On (SSO) sia l'accesso remoto sicuro per le applicazioni Web ospitate in locale, ad esempio siti di SharePoint, Outlook Web Access o qualsiasi altra applicazione Web line-of-business. Queste applicazioni Web locali sono integrate con Azure AD, la stessa piattaforma delle identità e di controllo usata da O365. Gli utenti finali possono accedere alle applicazioni locali nello stesso modo in cui accedono a Office 365 e alle altre app SaaS integrate in Azure AD. Non è necessario modificare l'infrastruttura di rete o richiedere una VPN per fornire questa soluzione agli utenti.

## <a name="why-is-application-proxy-a-better-solution"></a>Perché un Proxy dell'applicazione è una soluzione migliore?
Il proxy dell'applicazione di Azure AD fornisce una soluzione di accesso remoto semplice, sicura ed economicamente conveniente a tutte le applicazioni locali.

Il proxy di applicazione di Azure AD:

* **Simple**
   * Non è necessario modificare o aggiornare le applicazioni per usare il proxy di applicazione. 
   * Offre agli utenti un'esperienza di autenticazione coerente. Possono usare il portale MyApps per ottenere l'accesso Single Sign-On per entrambe le app SaaS nel cloud e nelle app locali. 
* **Proteggere**
   * Quando si pubblicano le app usando il proxy dell'applicazione di Azure AD, è possibile sfruttare l'analisi della sicurezza e i controlli di autorizzazione avanzati in Azure. Si ottiene sicurezza a livello di cloud e funzionalità di sicurezza Azure come l'accesso condizionale e la verifica in due passaggi.
   * Non è necessario aprire le connessioni in ingresso attraverso il firewall per consentire agli utenti l'accesso remoto. 
* **Convenienza**
   * Il proxy di applicazione opera nel cloud, facendo risparmiare tempo e denaro. Le soluzioni locali richiedono generalmente di configurare e gestire reti perimetrali, server perimetrali o altre infrastrutture complesse.  

## <a name="what-kind-of-applications-work-with-application-proxy"></a>Quale tipo di applicazione funziona con il proxy di applicazione?
Con il proxy dell'applicazione di Azure AD è possibile accedere a tipi diversi di applicazioni interne:

* Applicazioni Web che usano l'[autenticazione integrata di Windows](active-directory-application-proxy-sso-using-kcd.md) per l'autenticazione  
* Applicazioni Web che usano l'accesso basato su form o su [intestazione](application-proxy-ping-access.md)  
* API Web che si vuole esporre ad applicazioni avanzate in dispositivi diversi  
* Applicazioni ospitate dietro [Gateway Desktop remoto](application-proxy-publish-remote-desktop.md)  
* App rich client integrate con Active Directory Authentication Library (ADAL)

## <a name="how-does-application-proxy-work"></a>Come funziona il proxy di applicazione?
Vi sono due componenti da configurare per fare in modo che il proxy di applicazione funzioni: un connettore e un endpoint esterno. 

Il connettore è un agente semplice che si trova in un server di Windows all'interno della rete. Il connettore facilita il flusso del traffico del servizio Proxy di applicazione nel cloud per l'applicazione locale. Usa solo connessioni in uscita, per cui non è necessario aprire porte in ingresso né inserire nulla nella rete perimetrale. I connettori sono senza stato e prelevano le informazioni dal cloud in base alle esigenze. Per altre informazioni sui connettori, ad esempio come bilanciano il carico ed effettuano l'autenticazione, vedere [Comprendere i connettori del proxy applicazione Azure AD](application-proxy-understand-connectors.md). 

L'endpoint esterno rappresenta il metodo attraverso il quale gli utenti raggiungono le applicazioni all'esterno della rete. Possono proseguire direttamente a un URL esterno determinato oppure accedere all'applicazione tramite il portale MyApps. Quando gli utenti proseguono verso uno di questi endpoint, si autenticano in Azure AD e quindi vengono instradati tramite il connettore all'applicazione locale.

 ![Diagramma del proxy dell'applicazione di AzureAD](./media/active-directory-application-proxy-get-started/azureappproxxy.png)

1. L'utente accede all'applicazione tramite il servizio proxy di applicazione e viene reindirizzato alla pagina di accesso di Azure AD per l'autenticazione.
2. Dopo avere completato l'accesso, un token viene generato e inviato al dispositivo client.
3. Il client invia il token al servizio proxy di applicazione che recupera dal token il nome dell'entità utente (UPN) e il nome dell'entità di sicurezza (SPN) e indirizza la richiesta al connettore proxy di applicazione.
4. Se è stato configurato Single Sign-On, il connettore esegue le autenticazioni aggiuntive necessarie per conto dell'utente.
5. Il connettore invia la richiesta all'applicazione locale.  
6. La risposta viene inviata all'utente con il servizio e il connettore proxy di applicazione.

### <a name="single-sign-on"></a>Single sign-on
Il proxy dell'applicazione di Azure AD fornisce l'accesso Single Sign-On (SSO) alle applicazioni che usano l'autenticazione integrata di Windows o alle applicazioni che riescono a riconoscere le attestazioni. Se l'applicazione usa l'autenticazione integrata di Windows, il proxy dell'applicazione rappresenta l'utente tramite la delega vincolata Kerberos per fornire l'accesso Single Sign-On. Se invece si dispone di un'applicazione con riconoscimento delle attestazione che considera attendibile Azure Active Directory, l'accesso Single Sign-On funziona perché l'utente era già stato autenticato da Azure AD.

Per altre informazioni su Kerberos, vedere [All you want to know about Kerberos Constrained Delegation (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd) (Tutto quello che si desidera sapere sulla delega vincolata Kerberos (KCD)).

### <a name="managing-apps"></a>Gestione delle app
Dopo che è stata pubblicata con il proxy di applicazione, l'app può essere gestita come qualsiasi altra app aziendale nel portale di Azure. È possibile usare funzioni di sicurezza di Azure Active Directory come la verifica in due passaggi e l'accesso condizionale, controllare le autorizzazioni utente e personalizzare il branding dell'app. 

## <a name="get-started"></a>Introduzione

Prima di configurare Proxy di applicazione, assicurarsi di avere una [edizione di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) supportata e una directory di Azure AD di cui si è un amministratore globale.

Introduzione a Proxy di applicazione in due passaggi:

1. [Abilitazione del proxy dell'applicazione e configurazione del connettore](active-directory-application-proxy-enable.md).    
2. [Pubblicazione delle applicazioni](active-directory-application-proxy-publish.md) : usare la procedura guidata, veloce e intuitiva, per pubblicare applicazioni locali e renderle accessibili in remoto.

## <a name="whats-next"></a>Passaggi successivi
Dopo aver pubblicato la prima app, si può fare molto di più con il proxy di applicazione:

* [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
* [Pubblicare applicazioni mediante il proprio nome di dominio](active-directory-application-proxy-custom-domains.md)
* [Comprendere i connettori del proxy applicazione di Azure AD](application-proxy-understand-connectors.md)
* [Usare server proxy locali esistenti](application-proxy-working-with-proxy-servers.md) 
* [Impostare una home page personalizzata](application-proxy-office365-app-launcher.md)

Per le notizie e gli aggiornamenti più recenti, vedere [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/)


