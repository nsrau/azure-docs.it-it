---
title: Linee guida sulla Collaborazione B2B di Azure Active Directory | Microsoft Docs
description: La Collaborazione B2B di Azure Active Directory non richiede le licenze di Azure AD a pagamento, ma è anche possibile ottenere le funzionalità a pagamento per gli utenti guest B2B
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: mal
ms.openlocfilehash: d80794511f334cd6dc5af418e24fc774b7d8728f
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867511"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Linee guida sulla Collaborazione B2B di Azure Active Directory

Con la collaborazione business-to-business (B2B) di Azure Active Directory (Azure AD), è possibile invitare utenti esterni (o "utenti guest") a usare i servizi di Azure AD a pagamento. Per ogni licenza di Azure AD a pagamento assegnata a un utente è possibile invitare fino a cinque utenti guest in basse al limite utenti esterni.

Un utente guest è una persona che non è un membro della propria organizzazione o di alcuna delle sue consociate. Gli utenti guest sono definiti in base alla loro relazione con l'organizzazione, e non in base alle credenziali usate per l'accesso. In effetti, un utente guest può accedere con un'identità esterna o con credenziali di proprietà dell'organizzazione.

I seguenti *non* sono utenti guest:
- I dipendenti, i terzisti in sede o gli agenti in sede
- I dipendenti, i terzisti in sede o gli agenti in sede delle consociate

Le licenze degli utenti guest B2B vengono calcolate e segnalate automaticamente in base al rapporto 1:5. Attualmente, non è possibile assegnare licenze utente guest B2B direttamente agli utenti guest.

Esistono alcune situazioni in cui non viene segnalato un utente guest usando il limite di utenti esterni 1:5. Se un utente guest ha già una licenza di Azure AD a pagamento nell'organizzazione dell'utente, questi non utilizza una delle licenze utente guest B2B. In più, gli utenti guest possono usare le funzionalità gratuite di Azure AD senza alcun requisito di licenza aggiuntiva. Gli utenti guest hanno accesso alle funzionalità gratuite di Azure AD anche se non si hanno licenze di Azure AD a pagamento. 

## <a name="examples-calculating-guest-user-licenses"></a>Esempi: Calcolare le licenze utente guest
Dopo aver determinato il numero di utenti guest necessario ad accedere ai servizi di Azure AD a pagamento, assicurarsi di avere licenze a pagamento di Azure AD sufficienti a coprire gli utenti guest nel rapporto 1: 5 obbligatorio. Di seguito sono riportati alcuni esempi:

- Si vogliono invitare 100 utenti guest nelle app o nei servizi di Azure AD e si vuole assegnare a tutti gli utenti guest il provisioning e la gestione dell'accesso. Si vogliono anche richiedere l'autenticazione a più fattori e l'accesso condizionale per 50 di quegli utenti guest. Per includere questa combinazione, sono necessarie 10 licenze di Azure AD Basic e 10 licenze P1 di Azure AD Premium. Se si prevede di usare le funzionalità di Identity Protection con gli utenti guest, per includerli saranno necessarie le licenze P2 di Azure AD Premium con lo stesso rapporto 1:5.
- Si vogliono invitare 60 utenti guest che richiedono tutti l'autenticazione a più fattori, quindi è necessario avere almeno 12 licenze P1 di Azure AD Premium. Si hanno 10 dipendenti con licenze P1 di Azure AD Premium, che consentono fino a 50 utenti guest con il rapporto licenze 1:5. È necessario acquistare due licenze Premium P1 aggiuntive per includere 10 utenti guest aggiuntivi.

## <a name="using-the-b2b-collaboration-api-to-invite-users-from-your-affiliates"></a>Uso dell'API di collaborazione B2B per invitare gli utenti dalle consociate

Per definizione, un utente guest B2B è un utente esterno che si invita a usare le app e i servizi di Azure AD a pagamento. Un dipendente, un terzista in sede o un agente in sede dell'azienda o di una delle consociate non si qualifica per la collaborazione B2B, neanche se si usano funzionalità B2B. Di seguito sono riportati alcuni esempi: 
- Si vogliono usare credenziali esterne (ad esempio un'identità di social networking) per invitare un utente che è dipendente della propria organizzazione. Questo scenario non è conforme ai requisiti di licenza e non è consentito. Le credenziali esterne non fanno di un dipendente un utente esterno.  
- Si vogliono usare le API B2B per invitare un utente da una delle consociate dell'organizzazione. Nonostante questo scenario usi le API B2B per invitare l'utente, non è considerato una collaborazione B2B. Non è conforme ai requisiti di licenza perché un utente dalla consociata non è un utente esterno. 

In entrambi questi scenari, la soluzione migliore consiste nell'usare l'API di B2B per invitare gli utenti come membri (invitedUserType = Member) e assegnare una licenza Azure AD a ciascuno di essi. 

## <a name="next-steps"></a>Passaggi successivi

Vedere le risorse seguenti su Collaborazione B2B di Azure AD:

* [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
* [Domande frequenti su Collaborazione B2B di Azure Active Directory](faq.md)
