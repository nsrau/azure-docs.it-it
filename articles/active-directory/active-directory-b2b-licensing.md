---
title: Linee guida sulla Collaborazione B2B di Azure Active Directory | Microsoft Docs
description: "La Collaborazione B2B di Azure Active Directory non richiede le licenze di Azure AD a pagamento, ma è anche possibile ottenere le funzionalità a pagamento per gli utenti guest B2B"
services: active-directory
documentationcenter: 
author: sasubram
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 08/09/2017
ms.author: curtand
ms.reviewer: sasubram
ms.custom: it-pro
ms.openlocfilehash: 664398eb71501ff450b785928992729f91740a19
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Linee guida sulla Collaborazione B2B di Azure Active Directory

È possibile usare le funzionalità di Collaborazione B2B di Azure AD per invitare utenti guest nel tenant di Azure AD e consentire a tali utenti di accedere ai servizi di Azure AD e ad altre risorse dell'organizzazione. Se si vuole fornire l'accesso alle funzionalità a pagamento di Azure AD, gli utenti guest di Collaborazione B2B devono avere le licenze appropriate di Azure AD. 

In particolare:
* Le funzionalità di Azure AD Free sono disponibili per gli utenti guest senza licenze aggiuntive.
* Se si vuole offrire agli utenti B2B l'accesso alle funzionalità a pagamento di Azure AD, è necessario avere un numero di licenze sufficiente per supportare tali utenti guest B2B.
* Un tenant che invia gli inviti con una licenza a pagamento di Azure AD ha diritti utente di Collaborazione B2B per altri cinque utenti guest B2B invitati ad accedere al tenant.
* Il cliente che possiede il tenant che invia gli inviti deve essere quello che determina quanti utenti di Collaborazione B2B necessitano delle funzionalità a pagamento di Azure AD. A seconda delle funzionalità a pagamento di Azure AD da assegnare agli utenti guest, è necessario disporre di licenze a pagamento di Azure AD sufficienti per coprire gli utenti di Collaborazione B2B nello stesso rapporto 5:1.

Un utente guest di Collaborazione B2B viene aggiunto come utente da una società partner, non come dipendente dell'organizzazione o dipendente di un'azienda affiliata diversa. Un utente guest B2B può accedere con credenziali aggiuntive o con credenziali di proprietà dell'organizzazione, come illustrato in questo articolo. 

In altre parole, le licenze B2B vengono configurate non in base alla modalità di autenticazione dell'utente ma in base alla relazione dell'utente con l'organizzazione. Se gli utenti non sono partner, vengono considerati in modo diverso a livello di licenza. Non vengono considerati utenti di Collaborazione B2B per finalità relative alla licenza, anche se il rispettivo valore UserType è "Guest". È necessario assegnare a questi utenti licenze in base alla procedura normale, ovvero una licenza per utente. Questi utenti includono:
* Dipendenti
* Personale che accede con identità esterne
* Dipendenti di un'azienda affiliata diversa


## <a name="licensing-examples"></a>Esempi di licenza
- Un cliente vuole invitare 100 utenti di Collaborazione B2B al proprio tenant di Azure AD. Il cliente assegna la gestione degli accessi e il provisioning per tutti gli utenti, ma 50 utenti richiedono anche l'autenticazione a più fattori e l'accesso condizionale. Il cliente deve acquistare 10 licenze Basic e 10 licenze Premium P1 di Azure AD per includere correttamente questi utenti B2B. Se il cliente prevede di usare le funzioni di Identity Protection con gli utenti B2B, dovrà disporre delle licenze Premium P2 di Azure AD per includere gli utenti invitati con lo stesso rapporto 5:1.
- Un cliente dispone di 10 dipendenti, tutti coperti da una licenza Premium P1 di Azure AD, e vuole invitare 60 utenti B2B che hanno tutti bisogno dell'autenticazione a più fattori (MFA). Stando alla regola di rapporto 5:1 per le licenze, il cliente dovrà disporre di almeno 12 licenze Premium P1 di Azure AD per includere tutti e 60 gli utenti di Collaborazione B2B. Poiché dispone già di 10 licenze Premium P1 per i 10 dipendenti esistenti, ha diritto a invitare 50 utenti B2B con funzionalità Premium P1 come MFA. In questo esempio, il cliente dovrà acquistare 2 licenze Premium P1 aggiuntive per includere i rimanenti 10 utenti di Collaborazione B2B.

> [!NOTE]
> Non vi è alcun modo di assegnare le licenze direttamente agli utenti B2B o per abilitare tali diritti utente di Collaborazione B2B.

Il cliente che possiede il tenant che invia gli inviti deve essere quello che determina quanti utenti di Collaborazione B2B necessitano delle funzionalità a pagamento di Azure AD. A seconda delle funzionalità a pagamento di Azure AD che si vuole per gli utenti guest, è necessario disporre di licenze a pagamento di Azure AD sufficienti per coprire gli utenti di Collaborazione B2B nel rapporto 5:1. 

## <a name="additional-licensing-details"></a>Altri dettagli sulle licenze
- Non è necessario assegnare effettivamente le licenze agli account utente B2B. In base al rapporto 5:1, le licenze vengono calcolate e segnalate automaticamente.
- Se nel tenant non sono presenti licenze a pagamento di Azure AD, ogni utente invitato ottiene i diritti offerti dall'edizione Free di Azure AD.
- Se un utente di Collaborazione B2B dispone già di una licenza a pagamento di Azure AD dalla propria organizzazione, non userà le licenze di Collaborazione B2B del tenant che effettua l'invito.

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>Approfondimenti: quali considerazioni fare sulle licenze quando si aggiungono utenti da un'organizzazione conglomerata come "membri" usando le API?
Un utente guest B2B è un utente invitato da un'organizzazione partner che collabora con l'organizzazione host. In genere, casi diversi da questo non vengono riconosciuti come B2B anche se usano funzionalità B2B. Prendiamo in esame due casi specifici:

1. Se un host invita un dipendente usando un indirizzo cliente
  * Questo scenario non è conforme ai criteri di licenza Microsoft e non è consigliato.

2. Se un'organizzazione host aggiunge un utente da un'altra organizzazione conglomerata
  1. In questo caso l'utente viene invitato usando le API di B2B, ma non si tratta in genere di B2B. In teoria, queste organizzazioni dovrebbero invitare gli utenti di altre organizzazioni come membri (l'API lo consente). In questo caso, è necessario assegnare le licenze a questi membri perché possano accedere alle risorse dell'organizzazione che li invita.

  2. È possibile che alcune organizzazioni vogliano aggiungere gli utenti di altre organizzazioni come "Guest" come criterio. Di seguito sono spiegati due casi:
      * L'organizzazione conglomerata usa già Azure AD e agli utenti invitati sono concesse le licenze nell'altra organizzazione. In questo caso, gli utenti invitati non devono seguire la formula 1:5 esposta in precedenza in questo documento. 

      * L'organizzazione conglomerata non usa Azure AD o non ha le licenze adeguate. In questo caso, è necessario seguire la formula 1:5 esposta in precedenza in questo documento.

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
