---
title: Informazioni su Collaborazione B2B di Azure Active Directory | Documentazione Microsoft
description: "Collaborazione B2B di Azure Active Directory supporta le relazioni tra società consentendo ai partner commerciali di accedere in modo selettivo alle applicazioni aziendali."
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 1464387b-ee8b-4c7c-94b3-2c5567224c27
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 06/27/2017
ms.author: curtand
ms.custom: aaddev
ms.reviewer: sasubram
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: fbc12a52555b190d43b5e953fd4d19923a25b0ed
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017


---

# <a name="what-is-azure-ad-b2b-collaboration"></a>Informazioni su Collaborazione B2B di Azure AD

<iframe width="560" height="315" src="https://www.youtube.com/embed/AhwrweCBdsc" frameborder="0" allowfullscreen></iframe>

Le funzionalità di Collaborazione B2B di Azure Active Directory consentono a tutte le organizzazioni che usano Azure AD di lavorare in modo sicuro e protetto con gli utenti di qualsiasi altra organizzazione, piccola o grande che sia, che usi o meno Azure AD e che abbia o meno un'organizzazione IT. 

Le organizzazioni che usano Azure AD possono consentire l'accesso a documenti, risorse e applicazioni ai propri partner, pur mantenendo il totale controllo dei propri dati aziendali. Gli sviluppatori possono usare le API di B2B di Azure AD per scrivere applicazioni, in modo da mettere in contatto due organizzazioni in modo sicuro. Inoltre, per gli utenti finali la navigazione risulta particolarmente semplice.

Per il 97% dei clienti Microsoft, Collaborazione B2B di Azure AD è molto importante.

![Grafico a torta](media/active-directory-b2b-what-is-azure-ad-b2b/97-percent-support.png)

All'inizio di aprile 2017 erano già 3 milioni circa gli utenti che usavano le funzionalità di Collaborazione B2B di Azure AD. Oltre il 23% delle organizzazioni Azure AD con più di 10 utenti usano già queste funzionalità.

## <a name="the-key-benefits-of-azure-ad-b2b-collaboration-to-your-organization"></a>Vantaggi principali di Collaborazione B2B di Azure AD per l'organizzazione

### <a name="work-with-any-user-from-any-partner"></a>Lavorare con tutti gli utenti di qualsiasi partner

* I partner usano le proprie credenziali

* Non sono necessari requisiti per i partner per usare Azure AD

* Non sono necessarie directory esterne e non è richiesta un'installazione complessa

### <a name="simple-and-secure-collaboration"></a>Collaborazione sicura e semplice

* Accesso concesso a qualsiasi app o dato aziendale applicando i criteri di autorizzazione avanzati e sofisticati di Azure AD

* Semplice per gli utenti

* Sicurezza di livello aziendale per app e dati

### <a name="no-management-overhead"></a>Nessun sovraccarico di gestione

* Nessuna gestione di password o account esterno

* Nessuna gestione del ciclo di vita degli account sincronizzata o manuale

* Nessun sovraccarico amministrativo esterno

## <a name="you-can-easily-add-b2b-collaboration-users-to-your-organization"></a>Possibilità di aggiungere facilmente gli utenti di Collaborazione B2B all'organizzazione

Gli amministratori possono aggiungere gli utenti (guest) di Collaborazione B2B nel [portale di Azure](https://portal.azure.com).

![Aggiungere utenti guest](media/active-directory-b2b-what-is-azure-ad-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>Abilitare i collaboratori per usare la propria identità

Gli utenti di Collaborazione B2B possono accedere con un'identità a scelta. Se l'utente non dispone di un account Microsoft o un account Azure AD, ne viene immediatamente creato uno al riscatto dell'offerta.

![Scelta identità di accesso](media/active-directory-b2b-what-is-azure-ad-b2b/sign-in-identity-choice.png)

### <a name="delegate-to-application-and-group-owners"></a>Delegare a proprietari dell'applicazione e del gruppo 
I proprietari dell'applicazione e del gruppo possono aggiungere direttamente gli utenti di B2B a qualsiasi propria applicazione, indipendentemente che sia o meno un'applicazione Microsoft. Gli amministratori possono delegare l'autorizzazione ad aggiungere utenti B2B a non amministratori, i quali, tramite il [pannello di accesso alle applicazioni di Azure](https://myapps.microsoft.com) possono aggiungere gli utenti di Collaborazione B2B ad applicazioni o gruppi.

![Pannello di accesso](media/active-directory-b2b-what-is-azure-ad-b2b/access-panel.png)

![Aggiungere un membro](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>Criteri di autorizzazione per proteggere i contenuti aziendali

Possono essere applicati criteri di accesso condizionale, ad esempio l'autenticazione a più fattori:
- A livello di tenant
- A livello di applicazione
- Per utenti specifici per proteggere dati e applicazioni aziendali

![Aggiungere un membro](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="use-our-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Usare le API Microsoft e codice di esempio per compilare con facilità le applicazioni da caricare
È possibile personalizzare il caricamento di partner esterni in base alle esigenze dell'organizzazione.

Utilizzando le [API di invito a Collaborazione B2B](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation), è possibile personalizzare le proprie esperienze di caricamento, inclusa la creazione di portali di iscrizione self-service. Viene presentato un codice di esempio per un portale self-service [su Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![Portale di iscrizione](media/active-directory-b2b-what-is-azure-ad-b2b/sign-up-portal.png)

Collaborazione B2B di Azure AD consente di sfruttare al massimo Azure AD per proteggere le relazioni con i partner in un modo che risulta semplice e intuitivo agli utenti finali. Non resta che unire le migliaia di organizzazioni che già usano B2B di Azure AD per favorire la collaborazione esterna.

## <a name="next-steps"></a>Passaggi successivi

* Nel [portale di Azure](https://portal.azure.com) sono disponibili esperienze per amministratori.

* Nel [pannello di accesso](https://myapps.microsoft.com) sono disponibili esperienze per Information Worker.

* È sempre possibile mettersi in contatto con il team di prodotto per eventuali commenti, confronti e suggerimenti tramite [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).

Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Procedura per aggiungere utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](active-directory-b2b-admin-add-users.md)
* [Procedura di aggiunta di utenti di Collaborazione B2B da parte di information worker](active-directory-b2b-iw-add-users.md)
* [Elementi del messaggio di posta elettronica di invito per la Collaborazione B2B](active-directory-b2b-invitation-email.md)
* [Riscatto dell'invito di Collaborazione B2B](active-directory-b2b-redemption-experience.md)
* [Licenze per la Collaborazione B2B di Azure AD](active-directory-b2b-licensing.md)
* [Risoluzione dei problemi di Collaborazione B2B di Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Domande frequenti su Collaborazione B2B di Azure Active Directory](active-directory-b2b-faq.md)
* [API e personalizzazione per Collaborazione B2B di Azure Active Directory](active-directory-b2b-api.md)
* [Autenticazione a più fattori per utenti di Collaborazione B2B](active-directory-b2b-mfa-instructions.md)
* [Aggiungere gli utenti per la Collaborazione B2B senza un invito](active-directory-b2b-add-user-without-invite.md)
* [Controllo e creazione di report per un utente di Collaborazione B2B](active-directory-b2b-auditing-and-reporting.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)

