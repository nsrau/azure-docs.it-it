---
title: Correggere i rischi e sbloccare gli utenti in Azure AD Identity Protection
description: Informazioni sulle opzioni di rilevamento dei rischi attivi di chiusura.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 350e7b37d36be70cea345db52cdfb639b2f1c1a8
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382109"
---
# <a name="remediate-risks-and-unblock-users"></a>Correggere i rischi e sbloccare gli utenti

Dopo aver completato l' [analisi](howto-identity-protection-investigate-risk.md), è opportuno intervenire per correggere il rischio o sbloccare gli utenti. Le organizzazioni hanno anche la possibilità di abilitare la correzione automatica usando i [criteri di rischio](howto-identity-protection-configure-risk-policies.md). Le organizzazioni devono provare a chiudere tutti i rilevamenti dei rischi presentati con in un periodo di tempo in cui l'organizzazione ha dimestichezza. Microsoft consiglia di chiudere gli eventi il prima possibile, perché il tempo è importante quando si lavora con i rischi.

## <a name="remediation"></a>Correzione

Tutti i rilevamenti dei rischi attivi contribuiscono al calcolo di un valore denominato livello di rischio utente. Il livello di rischio utente è un indicatore (basso, medio, elevato) della probabilità che un account sia stato compromesso. In qualità di amministratore, si desidera che tutti i rilevamenti dei rischi siano chiusi, in modo che gli utenti interessati non siano più a rischio.

Alcuni rischi rilevati possono essere contrassegnati da Identity Protection come "closed (sistema)" perché gli eventi non sono più considerati rischiosi.

Gli amministratori hanno le opzioni seguenti per correggere:

- Correzione automatica con criteri di rischio
- Reimpostazione manuale della password
- Ignorare il rischio utente
- Chiudi manualmente i singoli rilevamenti di rischi

### <a name="self-remediation-with-risk-policy"></a>Correzione automatica con criteri di rischio

Se si consente agli utenti di eseguire autonomamente la correzione, con Azure Multi-Factor Authentication (autenticazione a più fattori) e la reimpostazione della password self-service (SSPR) nei criteri di rischio, possono sbloccarsi quando viene rilevato il rischio. Questi rilevamenti vengono quindi considerati chiusi. Gli utenti devono essere registrati in precedenza per l'autenticazione a più fattori di Azure e SSPR per poter usare quando viene rilevato il rischio.

Alcuni rilevamenti potrebbero non generare rischi per il livello in cui è necessaria una correzione automatica degli utenti, ma gli amministratori dovrebbero comunque valutare questi rilevamenti. Gli amministratori possono determinare che sono necessarie misure aggiuntive, ad esempio il [blocco dell'accesso dalle posizioni](../conditional-access/howto-conditional-access-policy-location.md) o la riduzione del rischio accettabile nei criteri.

### <a name="manual-password-reset"></a>Reimpostazione manuale della password

Se la richiesta di reimpostazione della password tramite criteri di rischio utente non è un'opzione, gli amministratori possono chiudere tutti i rilevamenti dei rischi per un utente con una reimpostazione manuale della password.

Agli amministratori vengono concesse due opzioni per la reimpostazione di una password per gli utenti:

- **Generare una password provvisoria**: generando una password provvisoria, è possibile ripristinare immediatamente un'identità a uno stato sicuro. Questo metodo richiede il contatto con gli utenti interessati perché è necessario conoscere la password temporanea. Poiché la password è temporanea, all'utente viene richiesto di modificare la password in un nuovo elemento durante il successivo accesso.

- **Richiedere all'utente di reimpostare la password**: richiedendo agli utenti di reimpostare le password, è possibile eseguire il ripristino in modo autonomo, senza contattare l'help desk o l'amministratore. Questo metodo si applica solo agli utenti registrati per l'autenticazione a più fattori di Azure e SSPR. Questa opzione non è disponibile per gli utenti che non sono stati registrati.

### <a name="dismiss-user-risk"></a>Ignorare il rischio utente

Se la reimpostazione della password non è un'opzione, perché ad esempio l'utente è stato eliminato, è possibile scegliere di ignorare i rilevamenti dei rischi utente.

Quando si fa clic su **Ignora il rischio utente**, tutti gli eventi vengono chiusi e l'utente interessato non è più a rischio. Questo metodo, tuttavia, non ha alcun impatto sulla password esistente e, pertanto, non ripristina l'identità correlata a uno stato sicuro. 

### <a name="close-individual-risk-detections-manually"></a>Chiudi manualmente i singoli rilevamenti di rischi

È possibile chiudere manualmente i singoli rilevamenti di rischi. Chiudendo manualmente i rilevamenti dei rischi, è possibile ridurre il livello di rischio utente. In genere, i rilevamenti di rischio vengono chiusi manualmente in risposta a un'analisi correlata. Ad esempio, quando si comunica con un utente, viene rivelato che non è più necessario un rilevamento attivo dei rischi. 
 
Quando si chiudono manualmente i rilevamenti dei rischi, è possibile scegliere di eseguire una delle azioni seguenti per modificare lo stato di un rilevamento del rischio:

- Conferma compromissione utente
- Ignorare il rischio utente
- Conferma l'accesso sicuro
- Conferma dell'accesso compromesso

## <a name="unblocking-users"></a>Sblocco degli utenti

Un amministratore può scegliere di bloccare l'accesso in base ai criteri di rischio o alle indagini. Un blocco può verificarsi in base al rischio di accesso o all'utente.

### <a name="unblocking-based-on-user-risk"></a>Sblocco in base al rischio dell'utente

Per sbloccare un account bloccato a causa del rischio utente, gli amministratori hanno le opzioni seguenti:

1. **Reimpostazione della password** : è possibile reimpostare la password dell'utente.
1. **Ignorare i rischi** per gli utenti: il criterio di rischio utente blocca un utente se è stato raggiunto il livello di rischio utente configurato per bloccare l'accesso. È possibile ridurre il livello di rischio di un utente evitando il rischio utente o chiudendo manualmente i rilevamenti dei rischi segnalati.
1. **Escludere l'utente dal criterio** : se si ritiene che la configurazione corrente dei criteri di accesso causi problemi per utenti specifici, è possibile escludere gli utenti dal criterio. Per ulteriori informazioni, vedere la sezione esclusioni nell'articolo [procedura: configurare e abilitare i criteri di rischio](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Disabilitazione del criterio** : se si ritiene che la configurazione del criterio provochi problemi per tutti gli utenti, è possibile disabilitare il criterio. Per altre informazioni, vedere l'articolo [procedura: configurare e abilitare i criteri di rischio](howto-identity-protection-configure-risk-policies.md).

### <a name="unblocking-based-on-sign-in-risk"></a>Sblocco in base al rischio di accesso

Per sbloccare un account in base al rischio di accesso, gli amministratori hanno a disposizione le opzioni seguenti:

1. **Accesso da una località o un dispositivo familiare**: un motivo comune del blocco di accessi sospetti è costituito da tentativi di accesso da località o dispositivi non familiari. Gli utenti possono determinare rapidamente se questo motivo è il motivo del blocco tentando di accedere da una posizione o un dispositivo familiare.
1. **Escludere l'utente dal criterio** : se si ritiene che la configurazione corrente dei criteri di accesso causi problemi per utenti specifici, è possibile escludere gli utenti dal criterio. Per ulteriori informazioni, vedere la sezione esclusioni nell'articolo [procedura: configurare e abilitare i criteri di rischio](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Disabilitazione del criterio** : se si ritiene che la configurazione del criterio provochi problemi per tutti gli utenti, è possibile disabilitare il criterio. Per altre informazioni, vedere l'articolo [procedura: configurare e abilitare i criteri di rischio](howto-identity-protection-configure-risk-policies.md).

## <a name="next-steps"></a>Passaggi successivi

Per ottenere una panoramica di Azure AD Identity Protection, vedere l'articolo di [panoramica di Azure AD Identity Protection](overview-identity-protection.md).
