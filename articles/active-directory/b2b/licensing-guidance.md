---
title: Linee guida sulla Collaborazione B2B di Azure Active Directory | Microsoft Docs
description: La Collaborazione B2B di Azure Active Directory non richiede le licenze di Azure AD a pagamento, ma è anche possibile ottenere le funzionalità a pagamento per gli utenti guest B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0032eb1e6b92abb130521945f64fda3133d9b7fc
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198882"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Linee guida sulla Collaborazione B2B di Azure Active Directory

Con la collaborazione business-to-business (B2B) di Azure Active Directory (Azure AD), è possibile invitare utenti esterni (o "utenti guest") a usare i servizi di Azure AD a pagamento. Per ogni licenza di Azure AD a pagamento assegnata a un utente è possibile invitare fino a cinque utenti guest in basse al limite utenti esterni.

Le licenze degli utenti guest B2B vengono calcolate e segnalate automaticamente in base al rapporto 1:5. Attualmente, non è possibile assegnare licenze utente guest B2B direttamente agli utenti guest.

In più, gli utenti guest possono usare le funzionalità gratuite di Azure AD senza alcun requisito di licenza aggiuntiva. Gli utenti guest hanno accesso alle funzionalità gratuite di Azure AD anche se non si hanno licenze di Azure AD a pagamento. 

## <a name="examples-calculating-guest-user-licenses"></a>Esempi: Calcolare le licenze utente guest
Dopo aver determinato il numero di utenti guest necessario ad accedere ai servizi di Azure AD a pagamento, assicurarsi di avere licenze a pagamento di Azure AD sufficienti a coprire gli utenti guest nel rapporto 1: 5 obbligatorio. Di seguito sono riportati alcuni esempi:

- Si vogliono invitare 100 utenti guest nelle app o nei servizi di Azure AD e si vuole assegnare a tutti gli utenti guest il provisioning e la gestione dell'accesso. Si vogliono anche richiedere l'autenticazione a più fattori e l'accesso condizionale per 50 di quegli utenti guest. Per includere questa combinazione, sono necessarie 10 licenze di Azure AD Basic e 10 licenze P1 di Azure AD Premium. Se si prevede di usare le funzionalità di Identity Protection con gli utenti guest, per includerli saranno necessarie le licenze P2 di Azure AD Premium con lo stesso rapporto 1:5.
- Si vogliono invitare 60 utenti guest che richiedono tutti l'autenticazione a più fattori, quindi è necessario avere almeno 12 licenze P1 di Azure AD Premium. Si hanno 10 dipendenti con licenze P1 di Azure AD Premium, che consentono fino a 50 utenti guest con il rapporto licenze 1:5. È necessario acquistare due licenze Premium P1 aggiuntive per includere 10 utenti guest aggiuntivi.

## <a name="next-steps"></a>Passaggi successivi

Vedere le risorse seguenti su Collaborazione B2B di Azure AD:

* [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
* [Domande frequenti su Collaborazione B2B di Azure Active Directory](faq.md)
