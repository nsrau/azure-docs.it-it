---
title: Utenti di Protezione identità e B2B - Azure Active Directory
description: Utilizzo della protezione delle identità con gli utenti B2B sul funzionamento e le limitazioni note
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95ea7eb470a5880bc88b3df903d33854f363e974
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72881317"
---
# <a name="identity-protection-and-b2b-users"></a>Identity Protection e utenti B2B

Con la collaborazione B2B di Azure AD, le organizzazioni possono applicare criteri basati sul rischio per gli utenti B2B usando Identity Protection.With Azure AD B2B collaboration, organizations can enforce risk-based policies for B2B users using Identity Protection. Questi criteri devono essere configurati in due modi:These policies be configured in two ways:

- Gli amministratori possono configurare i criteri predefiniti basati sul rischio di Identity Protection, che si applicano a tutte le app, che includono gli utenti guest.
- Gli amministratori possono configurare i criteri di accesso condizionale, utilizzando il rischio di accesso come condizione, che include gli utenti guest.

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>Come viene valutato il rischio per gli utenti di Collaborazione B2B

Il rischio per gli utenti di Collaborazione B2B viene valutato nella loro home directory. Il rischio di accesso in tempo reale per questi utenti viene valutato nella directory delle risorse quando tentano di accedere alla risorsa.

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>Limitazioni della protezione delle identità per gli utenti di Collaborazione B2B

Esistono limitazioni nell'implementazione di Identity Protection per gli utenti di collaborazione B2B in una directory di risorse a causa della loro identità esistente nella loro home directory. Le principali limitazioni sono le seguenti:

- Se un utente guest attiva il criterio di rischio utente di Identity Protection per forzare la reimpostazione della password, **verrà bloccato.** Questo blocco è dovuto all'impossibilità di reimpostare le password nella directory delle risorse.
- **Gli utenti guest non vengono visualizzati nel report degli utenti rischiosi.** Questa perdita di visibilità è dovuta alla valutazione del rischio che si verifica nella home directory dell'utente B2B.
- Gli amministratori **non possono ignorare o correggere un utente di collaborazione B2B rischioso** nella propria directory delle risorse. Questa perdita di funzionalità è dovuta al fatto che gli amministratori nella directory delle risorse non hanno accesso alla home directory dell'utente B2B.

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>Perché non è possibile correggere gli utenti rischiosi di collaborazione B2B nella directory?

La valutazione dei rischi e la correzione per gli utenti B2B si verificano nella loro home directory. Di questo motivo, gli utenti guest non vengono visualizzati nel report degli utenti rischiosi nella directory delle risorse e gli amministratori nella directory delle risorse non possono forzare una reimpostazione sicura della password per questi utenti.

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>Cosa fare se un utente di collaborazione B2B è stato bloccato a causa di criteri basati sul rischio nell'organizzazione?

Se un utente B2B rischioso nella directory è bloccato dai criteri basati sul rischio, l'utente dovrà correggere tale rischio nella home directory. Gli utenti possono correggere il rischio eseguendo una reimpostazione sicura della password nella home directory. Se nella home directory non è abilitata la reimpostazione della password self-service, sarà necessario contattare il personale IT dell'organizzazione per fare in modo che un amministratore esprezioni manualmente il rischio o reimpostare la password.

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>In che modo è possibile impedire che gli utenti di Collaborazione B2B siano influenzati da criteri basati sul rischio?

L'esclusione degli utenti B2B dai criteri di accesso condizionale basati sul rischio dell'organizzazione impedirà agli utenti B2B di essere interessati o bloccati dalla valutazione dei rischi. Per escludere questi utenti B2B, creare un gruppo in Azure AD che contiene tutti gli utenti guest dell'organizzazione. Aggiungere quindi questo gruppo come esclusione per i criteri di rischio utente e di accesso di Identity Protection incorporati, nonché per tutti i criteri di accesso condizionale che utilizzano il rischio di accesso come condizione.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti su Collaborazione B2B di Azure AD:

- [Che cos'è la collaborazione B2B di Azure AD?](../b2b/what-is-b2b.md)
