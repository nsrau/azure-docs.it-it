---
title: Linee guida sulla Collaborazione B2B di Azure Active Directory | Microsoft Docs
description: "Collaborazione B2B di Azure Active Directory può richiedere una licenza a pagamento di Azure AD in base alle funzionalità desiderate per gli utenti"
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
ms.date: 02/09/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 64af2509036d035c5802f4b1985c3f986b685545
ms.openlocfilehash: 2b677e684021a873c0bc4db751d8e60d9eaa6f9d
ms.lasthandoff: 02/24/2017


---

# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Linee guida sulla Collaborazione B2B di Azure Active Directory

Collaborazione B2B di Azure Active Directory (Azure AD) estende agli utenti guest invitati nel tenant di Azure AD un set selezionato di funzionalità di Azure AD esistenti. Di conseguenza, gli utenti guest di Collaborazione B2B di Azure AD useranno le licenze di Azure AD e si allineeranno ai livelli di licenza Free, Basic e Premium P1/P2 esistenti come indicato qui: https://azure.microsoft.com/en-us/pricing/details/active-directory/.

Non è previsto alcun addebito per l'invito degli utenti in B2B e per la loro assegnazione a un'applicazione in Azure AD. Inoltre, le prime 10 app per utente e guest e i primi 3 rapporti di base sono gratuiti per gli utenti B2B, poiché fanno parte del livello "Free" di Azure AD.
Eventuali funzionalità a pagamento di Azure AD estese agli utenti B2B tramite le funzionalità di Collaborazione B2B avranno bisogno di una licenza a pagamento di Azure AD Basic, Premium P1 o Premium P2 in base alle funzionalità. Il tenant che effettua l'invito otterrà 5 diritti utente B2B con ciascuna licenza a pagamento di Azure AD. In altre parole, ogni licenza a pagamento di Azure AD che fornisce diritti sulle funzioni a pagamento di Azure AD a un utente dipendente in un tenant includerà anche i diritti sulle stesse funzioni a pagamento di Azure AD a altri 5 utenti B2B invitati nel tenant.

## <a name="licensing-examples"></a>Esempi di licenza
- Un cliente desidera invitare 100 utenti B2B nel proprio tenant di Azure AD e userà la gestione degli accessi basata sui gruppi e il provisioning per tutti. Tuttavia, 50 tra quegli utenti avranno bisogno anche di MFA e accesso condizionale. Il cliente deve acquistare 10 licenze Basic e 10 licenze Premium P1 di Azure AD per includere correttamente tutti gli utenti B2B. Analogamente, se un tenant di invito prevede di usare le funzioni di Identity Protection con lgli utenti B2B, dovrà disporre di un numero sufficiente di licenze Premium P2 di Azure AD per includere tutti gli utenti B2B con un rapporto 5:1.
- Un cliente dispone di 10 dipendenti, tutti coperti da una licenza Premium P1 di Azure AD, e vuole invitare 60 utenti B2B che avranno tutti bisogno dell'autenticazione a più fattori (MFA). Stando alla regola di rapporto 5:1 per le licenze, il cliente dovrà disporre di almeno 12 licenze Premium P1 di Azure AD per includere tutti e 60 gli utenti di Collaborazione B2B. Poiché dispone già di 10 licenze Premium P1 per i 10 dipendenti esistenti, ha già diritto a invitare 50 utenti B2B con funzionalità Premium P1 come MFA. In questo esempio, il cliente dovrà acquistare 2 licenze Premium P1 aggiuntive per includere i rimanenti 10 utenti di Collaborazione B2B.

> [!NOTE]
> Non vi è alcuna necessità di assegnare le licenze agli utenti B2B o per abilitare tali diritti utente di Collaborazione B2B.

Il cliente proprietario del tenant da cui vengono inviati gli inviti deve determinare il numero di utenti di Collaborazione B2B che avrà bisogno di funzionalità a pagamento di Azure AD. A seconda del livello a cui tali funzionalità fanno capo (Basic, Premium P1 o Premium P2), i clienti dovranno avere un numero sufficiente di licenze a pagamento di Azure AD per includere tutti gli utenti di Collaborazione B2B con un rapporto di 5:1. Se un'azienda necessita di ulteriori diritti utente di Collaborazione B2B, dovrà acquistare le relative necessarie licenze a pagamento di Azure AD.

## <a name="additional-licensing-details"></a>Altri dettagli sulle licenze
- Collaborazione B2B può offrire varie funzionalità a diversi utenti di Collaborazione B2B in base al modello esistente delle edizioni di Azure AD.
- Ciascuna licenza a pagamento di Azure AD includerà i diritti per 5 utenti di Collaborazione B2B (modello 5:1).
- Non è necessario assegnare effettivamente le licenze agli account utente B2B. Calcoli e rapporti verranno eseguiti automaticamente.
- Ogni utente invitato ottiene i diritti offerti dall'edizione Free di Azure AD se non sono presenti licenze a pagamento di Azure AD nel tenant.
- Se un utente di Collaborazione B2B dispone di una licenza a pagamento di Azure AD come dipendente della propria organizzazione, non userà le licenze di Collaborazione B2B del tenant che effettua l'invito.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Procedura per aggiungere utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](active-directory-b2b-admin-add-users.md)
* [Procedura di aggiunta di utenti di Collaborazione B2B da parte di information worker](active-directory-b2b-iw-add-users.md)
* [Elementi del messaggio di posta elettronica di invito per la Collaborazione B2B](active-directory-b2b-invitation-email.md)
* [Riscatto dell'invito di Collaborazione B2B](active-directory-b2b-redemption-experience.md)
* [Risoluzione dei problemi di Collaborazione B2B di Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Domande frequenti su Collaborazione B2B di Azure Active Directory](active-directory-b2b-faq.md)
* [API e personalizzazione per Collaborazione B2B di Azure Active Directory](active-directory-b2b-api.md)
* [Autenticazione a più fattori per utenti di Collaborazione B2B](active-directory-b2b-mfa-instructions.md)
* [Aggiungere gli utenti per la Collaborazione B2B senza un invito](active-directory-b2b-add-user-without-invite.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)

