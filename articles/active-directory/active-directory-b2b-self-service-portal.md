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
ms.date: 04/11/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: f629eeb6f12c8785cab2585190f70e98b02fa5b4
ms.lasthandoff: 04/12/2017


---


# <a name="self-service-sign-up-portal-for-azure-ad-b2b-collaboration"></a>Portale di iscrizione self-service per Collaborazione B2B di Azure AD

I clienti possono eseguire molte operazioni con le funzionalità incorporate nei prodotti ed esposte tramite le nostre esperienze di amministrazione IT nel [portale di Azure](https://portal.azure.com) e il [pannello di accesso alle applicazioni](https://myapps.microsoft.com) per utenti non amministratori. Le aziende hanno tuttavia bisogno di personalizzare il flusso di lavoro di onboarding per gli utenti B2B in base alle esigenze della propria organizzazione. È possibile farlo con la [nostra API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Nel corso delle conversazioni intrattenute con molti clienti, un'esigenza comune è risultata più evidente rispetto a tutte le altre: l'organizzazione che invia gli inviti potrebbe non sapere (o vuole sapere) in anticipo quali singoli collaboratori esterni hanno bisogno dell’accesso alle proprie risorse. In questo caso è necessario un modo che consenta agli utenti delle aziende partner possano eseguire l’iscrizione automatica mediante un set di criteri controllati dall’organizzazione che li invita. Ciò è possibile tramite le API, pertanto è stato pubblicato in Github un progetto del tipo: [progetto di esempio Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

Il progetto Github illustra come le organizzazioni possano usare le API e offrire ai partner di fiducia una funzionalità di iscrizione self-service basata su criteri, con regole che determinano per quali app devono ottenere l’accesso. In questo modo, gli utenti partner possono ottenere l'accesso alle giuste risorse in qualsiasi momento e in modo sicuro, ma senza che qualcuno all’interno dell’organizzazione che li invita debba eseguire manualmente il loro onboarding. È possibile distribuire facilmente il progetto con un solo clic su un pulsante in una sottoscrizione di Azure di propria scelta. Provalo!

## <a name="as-is-code"></a>Codice cosi com’è

Tenere presente che questo codice viene fornito come esempio per illustrare l'uso dell'API di invito B2B di Azure Active Directory. Deve essere preferibilmente personalizzato dal team di sviluppo o da un partner ed esaminato prima di essere distribuito in uno scenario di produzione.

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
