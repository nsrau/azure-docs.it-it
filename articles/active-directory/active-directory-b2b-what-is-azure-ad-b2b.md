---
title: Informazioni su Collaborazione B2B di Azure Active Directory | Microsoft Docs
description: Collaborazione B2B di Azure Active Directory supporta le relazioni tra società consentendo ai partner commerciali di accedere in modo selettivo alle applicazioni aziendali.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/26/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 1eff04cf43260a12a50a08b9145e1975b51af0cc
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33928481"
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>Informazioni su Collaborazione B2B di Azure AD

Le funzionalità di Collaborazione B2B di Azure Active Directory (Azure AD) consentono a tutte le organizzazioni che usano Azure AD di lavorare in modo sicuro e protetto con gli utenti di qualsiasi altra organizzazione, piccola o grande che sia, Le organizzazioni possono essere con o senza Azure AD e non è necessario disporre di un reparto IT.

Le organizzazioni che usano Azure AD possono consentire l'accesso a documenti, risorse e applicazioni ai propri partner, pur mantenendo il totale controllo dei propri dati aziendali. Gli sviluppatori possono usare le API di B2B di Azure AD per scrivere applicazioni, in modo da mettere in contatto due organizzazioni in modo sicuro. Inoltre, per gli utenti finali la navigazione risulta semplice.

Il video seguente fornisce un'utile panoramica.
>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="key-benefits-of-azure-ad-b2b-collaboration"></a>Vantaggi principali di Collaborazione B2B di Azure AD

### <a name="work-with-any-user-from-any-partner"></a>Lavorare con tutti gli utenti di qualsiasi partner

- I partner usano le proprie credenziali
- Non sono necessari requisiti per i partner per usare Azure AD
- Non sono necessarie directory esterne e non è richiesta un'installazione complessa

### <a name="simple-and-secure-collaboration"></a>Collaborazione sicura e semplice

- Accesso concesso a qualsiasi app o dato aziendale applicando i criteri di autorizzazione avanzati e sofisticati di Azure AD
- Semplice per gli utenti
- Sicurezza di livello aziendale per app e dati

### <a name="no-management-overhead"></a>Nessun sovraccarico di gestione

- Nessuna gestione di password o account esterno
- Nessuna gestione del ciclo di vita degli account sincronizzata o manuale
- Nessun sovraccarico amministrativo esterno

## <a name="easily-add-b2b-collaboration-users"></a>Aggiungere con facilità gli utenti di collaborazione B2B

Come amministratore, è possibile aggiungere con facilità gli utenti (guest) di collaborazione B2B all'organizzazione nel [portale di Azure](https://portal.azure.com).

![Aggiungere utenti guest](media/active-directory-b2b-what-is-azure-ad-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>Abilitare i collaboratori per usare la propria identità

Gli utenti di Collaborazione B2B possono accedere con un'identità a scelta. Se l'utente non dispone di un account Microsoft o un account Azure AD, ne viene immediatamente creato uno al riscatto dell'offerta.

### <a name="delegate-to-application-and-group-owners"></a>Delegare a proprietari dell'applicazione e del gruppo

Il proprietario dell'applicazione o del gruppo può aggiungere direttamente gli utenti di B2B a qualsiasi propria applicazione, indipendentemente che sia o meno un'applicazione Microsoft. Gli amministratori possono delegare l'autorizzazione ad aggiungere utenti B2B a non amministratori, i quali, tramite il [pannello di accesso alle applicazioni di Azure AD](https://myapps.microsoft.com) possono aggiungere gli utenti di Collaborazione B2B ad applicazioni o gruppi.

![Pannello di accesso](media/active-directory-b2b-what-is-azure-ad-b2b/access-panel.png)

![Aggiungere un membro](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>Criteri di autorizzazione per proteggere i contenuti aziendali

Possono essere applicati criteri di accesso condizionale, ad esempio l'autenticazione a più fattori:
- A livello di tenant
- A livello di applicazione
- Per utenti specifici per proteggere dati e applicazioni aziendali

### <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Usare le API e il codice di esempio per compilare con facilità le applicazioni da caricare

È possibile personalizzare il caricamento di partner esterni in base alle esigenze dell'organizzazione.

Usare le [API di invito a Collaborazione B2B](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) per personalizzare le proprie esperienze di caricamento, inclusa la creazione di portali di iscrizione self-service. Viene presentato un codice di esempio per un portale self-service [su Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![Portale di iscrizione](media/active-directory-b2b-what-is-azure-ad-b2b/sign-up-portal.png)

Collaborazione B2B di Azure AD consente di sfruttare al massimo Azure AD per proteggere le relazioni con i partner in un modo che risulta semplice e intuitivo agli utenti finali.

## <a name="next-steps"></a>Passaggi successivi

- [Procedura per aggiungere utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](active-directory-b2b-admin-add-users.md)
- [Procedura per aggiungere utenti di Collaborazione B2B da parte di Information Worker](active-directory-b2b-iw-add-users.md)
- [Riscatto dell'invito di Collaborazione B2B](active-directory-b2b-redemption-experience.md)
- [Licenze per la Collaborazione B2B di Azure AD](active-directory-b2b-licensing.md)
- È sempre possibile mettersi in contatto con il team di prodotto per eventuali commenti, confronti e suggerimenti tramite [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).