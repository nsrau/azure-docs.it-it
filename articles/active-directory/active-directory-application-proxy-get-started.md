---
title: Come fornire l&quot;accesso remoto sicuro alle applicazioni locali
description: "Viene illustrato come utilizzare il proxy dell’applicazione di Azure AD per fornire l&quot;accesso remoto sicuro alle applicazioni locali."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 9ee3ba5a5dee206c2358a76aaea680b998e994f8
ms.openlocfilehash: 732f9b135732c33364cab7200d43893d4cdda5f9


---
# <a name="how-to-provide-secure-remote-access-to-on-premises-applications"></a>Come fornire l'accesso remoto sicuro alle applicazioni locali
> [!NOTE]
> Il proxy dell’applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).
> 
> 

Oggi i dipendenti vogliono essere produttivi in qualsiasi luogo, in qualsiasi momento e da qualsiasi dispositivo. Vogliono lavorare con i propri dispositivi, che si tratti di tablet, di telefoni o di portatili, e si aspettano di poter accedere a tutte le applicazioni, sia le app SaaS nel cloud sia le app aziendali locali. Per fornire l'accesso alle applicazioni locali, sono sempre state necessarie reti private virtuali (VPN), reti perimetrali o proxy inversi locali. Queste soluzioni non sono solo complesse e difficili da proteggere, ma sono anche costose da configurare e gestire.

C'è una soluzione migliore.

Una forza lavoro moderna in un mondo dominato dai dispositivi mobili e basato sul cloud richiede una soluzione di accesso remoto moderna. Il proxy dell'applicazione di Azure AD è una funzionalità dell'offerta Azure Active Directory Premium che offre l'accesso remoto come servizio. Per questo è facile da distribuire, usare e gestire.

## <a name="what-is-azure-active-directory-application-proxy"></a>Informazioni sul proxy dell’applicazione di Azure Active Directory
Il proxy dell'applicazione di Azure AD fornisce sia l'accesso Single Sign-On (SSO) sia l'accesso remoto sicuro per le applicazioni Web ospitate in locale, ad esempio siti di SharePoint, Outlook Web Access o qualsiasi altra applicazione Web line-of-business. Queste applicazioni Web locali sono integrate con Azure AD, la stessa piattaforma delle identità e di controllo usata da O365. Gli utenti finali possono quindi accedere alle applicazioni locali nello stesso modo in cui accedono a Office&365; e alle altre app SaaS integrate in Azure AD. Non è necessario modificare l'infrastruttura di rete o richiedere una VPN per fornire questa soluzione agli utenti.

## <a name="why-is-this-a-better-solution"></a>Perché questa è una soluzione migliore?
Il proxy dell'applicazione di Azure AD fornisce una soluzione di accesso remoto semplice, sicura ed economicamente conveniente a tutte le applicazioni locali.

Il proxy dell'applicazione di Azure AD:  

* Opera nel cloud, facendo risparmiare tempo e denaro. Le soluzioni locali richiedono di configurare e gestire reti perimetrali, server perimetrali o altre infrastrutture complesse.  
* È più facile da configurare e da proteggere rispetto alle soluzioni locali perché non è necessario aprire connessioni in ingresso nel firewall.  
* Offre una maggiore sicurezza. Quando si pubblicano le app usando il proxy dell'applicazione di Azure AD, è possibile sfruttare l'analisi della sicurezza e i controlli di autorizzazione avanzati in Azure. Ciò significa che sono disponibili funzionalità di sicurezza avanzate per tutte le app esistenti senza doverne modificare nessuna.  
* Offre agli utenti un'esperienza di autenticazione coerente. Single Sign-On garantisce agli utenti finali la possibilità di accedere facilmente e semplicemente a tutte le app di cui hanno bisogno per essere produttivi con una sola password.  

## <a name="what-kind-of-applications-work-with-azure-ad-application-proxy"></a>Quale tipo di applicazione funziona con il proxy dell'applicazione di Azure AD?
Con il proxy dell'applicazione di Azure AD è possibile accedere a tipi diversi di applicazioni interne:

* Applicazioni Web che usano l'autenticazione integrata di Windows per l'autenticazione  
* Applicazioni Web che usano l'accesso basato su form  
* API Web che si vuole esporre ad applicazioni avanzate in dispositivi diversi  
* Applicazioni ospitate dietro Gateway Desktop remoto  

## <a name="how-does-the-service-work-with-connectors"></a>Come funziona il servizio con i connettori?
Il proxy dell'applicazione funziona installando nella rete un servizio slim di Windows Server chiamato connettore, con cui non è necessario aprire porte in ingresso né inserire nulla nella rete perimetrale. Se i volumi di traffico delle app sono elevati, è possibile aggiungere altri connettori e il servizio gestirà il bilanciamento del carico. I connettori sono senza stato e prelevano tutto dal cloud in base alle esigenze.

Per informazioni sui connettori, vedere [Informazioni sui connettori proxy di applicazione di Azure AD](application-proxy-understand-connectors.md). 

Quando gli utenti accedono alle applicazioni in remoto, si connettono all'endpoint pubblicato. Gli utenti si autenticano in Azure AD e quindi vengono instradati tramite il connettore all'applicazione locale.

 ![Diagramma del proxy dell'applicazione di AzureAD](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

1. L'utente accede all'applicazione tramite il proxy dell'applicazione e viene reindirizzato alla pagina di accesso di Azure AD per l'autenticazione.
2. Una volta riuscito l'accesso, un token viene generato e inviato all'utente.
3. L'utente invia il token al proxy dell'applicazione che recupera dal token il nome dell'entità utente (UPN) e il nome dell'entità sicurezza (SPN) e indirizza la richiesta al connettore.
4. Il connettore richiedere per conto dell'utente un ticket Kerberos che può essere usato per l'autenticazione interna (Windows). Questo passaggio è noto come delega vincolata Kerberos.
5. Active Directory recupera il ticket Kerberos.
6. Il ticket viene inviato al server applicazioni e verificato.
7. La risposta viene inviata all'utente con il proxy dell'applicazione.

### <a name="single-sign-on"></a>Single sign-on
Il proxy dell'applicazione di Azure AD fornisce l'accesso Single Sign-On (SSO) alle applicazioni che usano l'autenticazione integrata di Windows o alle applicazioni che riescono a riconoscere le attestazioni. Se l'applicazione usa l'autenticazione integrata di Windows, il proxy dell'applicazione rappresenta l'utente tramite la delega vincolata Kerberos per fornire l'accesso Single Sign-On. Se invece si dispone di un'applicazione con riconoscimento delle attestazione che considera attendibile Azure Active Directory, l'accesso Single Sign-On viene concesso perché l'utente era già stato autenticato da Azure AD.

## <a name="how-to-get-started"></a>Attività iniziali
Assicurarsi di avere una sottoscrizione di Azure AD Basic o Premium e di una directory di Azure ADdi cui si è amministratore globale. Sono inoltre necessarie licenze di base o premium di Microsoft Azure per l'amministratore della directory e gli utenti che accedono alle applicazioni. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md) .

La configurazione del proxy dell'applicazione viene eseguita in due passaggi:

1. [Abilitazione del proxy dell'applicazione e configurazione del connettore](active-directory-application-proxy-enable.md).    
2. [Pubblicazione delle applicazioni](active-directory-application-proxy-publish.md) : usare la procedura guidata, veloce e intuitiva, per pubblicare applicazioni locali e renderle accessibili in remoto.

## <a name="whats-next"></a>Passaggi successivi
Si può fare molto di più con il proxy dell'applicazione:

* [Pubblicare applicazioni mediante il proprio nome di dominio](active-directory-application-proxy-custom-domains.md)
* [Usare server proxy locali esistenti](application-proxy-working-with-proxy-servers.md) 
* [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
* [Lavorare con applicazioni grado di riconoscere attestazioni](active-directory-application-proxy-claims-aware-apps.md)
* [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)

Per le notizie e gli aggiornamenti più recenti, vedere [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/)




<!--HONumber=Feb17_HO1-->


