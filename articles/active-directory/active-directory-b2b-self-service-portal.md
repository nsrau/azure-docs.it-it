---
title: Portale di iscrizione self-service per Collaborazione B2B di Azure Active Directory | Microsoft Docs
description: "La collaborazione B2B di Azure Active Directory supporta le relazioni tra società abilitando i partner commerciali ad accedere in modo selettivo alle applicazioni aziendali"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/24/2017
ms.author: sasubram
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: 307373c75bbb87cec683f7a3097f8f159c9d5e61
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017


---


# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Portale self-service per l'iscrizione a Collaborazione B2B di Azure AD

I clienti possono eseguire molte operazioni con le funzionalità predefinite esposte tramite il [portale di Azure](https://portal.azure.com) per gli amministratori IT e il [pannello di accesso alle applicazioni](https://myapps.microsoft.com) per gli utenti finali. Le aziende hanno tuttavia bisogno di personalizzare il flusso di lavoro di onboarding per gli utenti B2B in base alle esigenze della propria organizzazione. È possibile farlo con la [nostra API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Nel corso delle conversazioni intrattenute con i clienti, un'esigenza comune risulta più evidente rispetto a tutte le altre. L'organizzazione che emette inviti potrebbe non sapere in anticipo quali singoli collaboratori esterni avranno bisogno di accedere alle risorse. È quindi necessario un modo che consenta agli utenti delle aziende partner di effettuare l'iscrizione con un set di criteri controllati dall'organizzazione che emette gli inviti. Questo scenario è possibile tramite le API ed è quindi stato pubblicato un [progetto Github di esempio](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) a tale scopo.

Il progetto Github illustra come le organizzazioni possono usare le API e offrire ai partner attendibili una funzionalità di iscrizione self-service basata su criteri, con regole che determinano le app a cui potranno accedere. Gli utenti partner possono ottenere l'accesso alle risorse quando necessario, in modo sicuro, senza che l'organizzazione che emette l'invito debba eseguirne manualmente l'onboarding. È possibile distribuire facilmente il progetto in una sottoscrizione di Azure di propria scelta.

## <a name="as-is-code"></a>Codice cosi com’è

Tenere presente che questo codice viene reso disponibile come esempio per illustrare l'utilizzo dell'API di invito B2B di Azure Active Directory. Deve essere preferibilmente personalizzato dal team di sviluppo o da un partner ed esaminato prima di essere distribuito in uno scenario di produzione.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.
* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Procedura per aggiungere utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](active-directory-b2b-admin-add-users.md)
* [Procedura di aggiunta di utenti di Collaborazione B2B da parte di information worker](active-directory-b2b-iw-add-users.md)
* [Elementi del messaggio di posta elettronica di invito per la Collaborazione B2B](active-directory-b2b-invitation-email.md)
* [Riscatto dell'invito di Collaborazione B2B](active-directory-b2b-redemption-experience.md)
* [Licenze per la Collaborazione B2B di Azure AD](active-directory-b2b-licensing.md)
* [Risoluzione dei problemi di Collaborazione B2B di Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Domande frequenti su Collaborazione B2B di Azure Active Directory](active-directory-b2b-faq.md)
* [Autenticazione a più fattori per utenti di Collaborazione B2B](active-directory-b2b-mfa-instructions.md)
* [Aggiungere gli utenti per la Collaborazione B2B senza un invito](active-directory-b2b-add-user-without-invite.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
