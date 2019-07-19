---
title: Identity Protection e utenti B2B-Azure Active Directory
description: Uso di Identity Protection con gli utenti B2B come funziona e limitazioni note
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.date: 07/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32b664fcf12db4e75afc5ab32c7f9868d4d7870f
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278546"
---
# <a name="identity-protection-and-b2b-users"></a>Identity Protection e utenti B2B

Con Azure AD collaborazione B2B, le organizzazioni possono applicare criteri basati sul rischio per gli utenti B2B che usano Identity Protection. Questi criteri vengono configurati in due modi:

- Gli amministratori possono configurare i criteri basati sul rischio di Identity Protection incorporati, che si applicano a tutte le app, che includono gli utenti guest.
- Gli amministratori possono configurare i criteri di accesso condizionale, usando il rischio di accesso come condizione, che include gli utenti guest.

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>Modalità di valutazione dei rischi per gli utenti di collaborazione B2B

Il rischio utente per gli utenti di collaborazione B2B viene valutato nella propria home directory. Il rischio di accesso in tempo reale per questi utenti viene valutato nella directory delle risorse quando tentano di accedere alla risorsa.

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>Limitazioni di Identity Protection per gli utenti di collaborazione B2B

Esistono limitazioni nell'implementazione di Identity Protection per gli utenti di collaborazione B2B in una directory di risorse a causa dell'identità esistente nella Home Directory. Di seguito sono riportate le limitazioni principali:

- Se un utente Guest attiva i criteri di rischio utente Identity Protection per forzare la reimpostazione della password, verranno bloccati. Questo blocco è dovuto all'impossibilità di reimpostare le password nella directory delle risorse.
- Gli utenti guest non vengono visualizzati nel report utenti rischiosi. Questa perdita di visibilità è causata dalla valutazione del rischio che si verifica nella home directory dell'utente B2B.
- Gli amministratori non possono ignorare o correggere un utente di collaborazione B2B a rischio nella propria directory delle risorse. Questa perdita di funzionalità è dovuta agli amministratori nella directory delle risorse che non hanno accesso alla Home directory dell'utente B2B.

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>Perché non è possibile correggere gli utenti di collaborazione B2B rischiosi nella mia directory?

La valutazione e la correzione dei rischi per gli utenti B2B si verificano nella propria home directory. A causa di questo fatto, gli utenti guest non vengono visualizzati nel report utenti rischiosi nella directory delle risorse e gli amministratori nella directory delle risorse non possono forzare la reimpostazione della password sicura per tali utenti.

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>Cosa fare se un utente di collaborazione B2B è stato bloccato a causa di criteri basati sul rischio nell'organizzazione?

Se un utente B2B a rischio nella directory è bloccato dai criteri basati sul rischio, l'utente dovrà correggere tale rischio nella propria home directory. Gli utenti possono correggere il rischio eseguendo una reimpostazione della password sicura nella Home Directory. Se nella propria home directory non è abilitata la reimpostazione della password self-service, sarà necessario contattare il personale IT dell'organizzazione per ignorare manualmente il rischio o reimpostare la password per un amministratore.

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>Ricerca per categorie impedire agli utenti di collaborazione B2B di essere interessati da criteri basati sul rischio?

Escludendo gli utenti B2B dai criteri di accesso condizionale basati sul rischio dell'organizzazione, gli utenti B2B non saranno interessati o bloccati dalla valutazione dei rischi. Per escludere questi utenti B2B, creare un gruppo in Azure AD che contenga tutti gli utenti Guest dell'organizzazione. Aggiungere quindi questo gruppo come esclusione per i criteri di rischio utente predefiniti per la protezione delle identità e per i criteri di rischio di accesso, nonché per i criteri di accesso condizionale che l'utente ha il rischio di accedere come condizione.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti su Collaborazione B2B di Azure AD:

- [Che cos'è Azure AD B2B Collaboration?](../b2b/what-is-b2b.md)
- [Che cos'è l'accesso condizionale?](../conditional-access/overview.md)
