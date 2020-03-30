---
title: Correggere i rischi e sbloccare gli utenti in Azure AD Identity Protection
description: Informazioni sulle opzioni disponibili per i rilevamenti dei rischi attivi.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382109"
---
# <a name="remediate-risks-and-unblock-users"></a>Correggere i rischi e sbloccare gli utenti

Dopo aver completato [l'indagine,](howto-identity-protection-investigate-risk.md)si desidera intraprendere azioni per correggere il rischio o sbloccare gli utenti. Le organizzazioni hanno anche la possibilità di abilitare la correzione automatica utilizzando i propri criteri di [rischio.](howto-identity-protection-configure-risk-policies.md) Le organizzazioni devono tentare di chiudere tutti i rilevamenti dei rischi che vengono presentati con in un periodo di tempo in cui l'organizzazione è a suo agio. Microsoft consiglia di chiudere gli eventi il prima possibile perché il tempo è importante quando si lavora con il rischio.

## <a name="remediation"></a>Correzione

Tutti i rilevamenti dei rischi attivi contribuiscono al calcolo di un valore chiamato livello di rischio dell'utente. Il livello di rischio utente è un indicatore (basso, medio, elevato) della probabilità che un account sia stato compromesso. In qualità di amministratore, si desidera ottenere tutti i rilevamenti dei rischi chiusi, in modo che gli utenti interessati non siano più a rischio.

Alcuni rilevamenti di rischi possono essere contrassegnati da Protezione identità come "Chiuso (sistema)" perché gli eventi non sono più stati determinati come rischiosi.

Gli amministratori hanno le seguenti opzioni per risolvere il rimedio:

- Autocommifica con politica di rischio
- Reimpostazione manuale della password
- Ignorare il rischio utente
- Chiudere manualmente i singoli rilevamenti dei rischi

### <a name="self-remediation-with-risk-policy"></a>Autocommifica con politica di rischio

Se si consente agli utenti di correggere autonomamente, con Azure Multi-Factor Authentication (MFA) e la reimpostazione della password self-service (SSPR) nei criteri di rischio, possono sbloccarsi quando viene rilevato un rischio. Questi rilevamenti vengono quindi considerati chiusi. Gli utenti devono aver precedentemente registrato per Azure MFA e SSPR per poter essere utilizzati quando viene rilevato il rischio.

Alcuni rilevamenti potrebbero non aumentare i rischi al livello in cui sarebbe necessaria una correzione automatica degli utenti, ma gli amministratori devono comunque valutare tali rilevamenti. Gli amministratori possono determinare che sono necessarie misure aggiuntive, ad esempio [il blocco dell'accesso dalle località](../conditional-access/howto-conditional-access-policy-location.md) o la riduzione del rischio accettabile nei criteri.

### <a name="manual-password-reset"></a>Reimpostazione manuale della password

Se non è possibile eseguire la reimpostazione della password utilizzando criteri di rischio utente, gli amministratori possono chiudere tutti i rilevamenti dei rischi per un utente con una reimpostazione manuale della password.

Agli amministratori vengono assegnate due opzioni quando si reimposta una password per gli utenti:

- **Generare una password provvisoria**: generando una password provvisoria, è possibile ripristinare immediatamente un'identità a uno stato sicuro. Questo metodo richiede di contattare gli utenti interessati perché hanno bisogno di sapere qual è la password temporanea. Poiché la password è temporanea, all'utente viene richiesto di modificare la password in qualcosa di nuovo durante l'accesso successivo.

- **Richiedere all'utente di reimpostare la password**: richiedendo agli utenti di reimpostare le password, è possibile eseguire il ripristino in modo autonomo, senza contattare l'help desk o l'amministratore. Questo metodo si applica solo agli utenti registrati per Azure MFA e SSPR. Per gli utenti che non sono stati registrati, questa opzione non è disponibile.

### <a name="dismiss-user-risk"></a>Ignorare il rischio utente

Se la reimpostazione della password non è un'opzione per l'utente, poiché ad esempio l'utente è stato eliminato, è possibile scegliere di ignorare i rilevamenti dei rischi utente.

Quando si fa clic su **Ignora rischio utente**, tutti gli eventi vengono chiusi e l'utente interessato non è più a rischio. Questo metodo, tuttavia, non ha alcun impatto sulla password esistente e, pertanto, non ripristina l'identità correlata a uno stato sicuro. 

### <a name="close-individual-risk-detections-manually"></a>Chiudere manualmente i singoli rilevamenti dei rischi

È possibile chiudere manualmente i singoli rilevamenti dei rischi. Chiudendo manualmente i rilevamenti dei rischi, è possibile ridurre il livello di rischio per l'utente. In genere, i rilevamenti dei rischi vengono chiusi manualmente in risposta a un'indagine correlata. Ad esempio, quando si parla con un utente rivela che un rilevamento dei rischi attivo non è più necessario. 
 
Quando si chiudono manualmente i rilevamenti dei rischi, è possibile scegliere di eseguire una delle seguenti azioni per modificare lo stato di un rilevamento dei rischi:

- Conferma utente compromesso
- Ignorare il rischio utente
- Confermare la sicurezza dell'accesso
- Conferma accesso compromesso

## <a name="unblocking-users"></a>Sblocco degli utenti

Un amministratore può scegliere di bloccare un accesso in base ai criteri di rischio o alle indagini. Un blocco può verificarsi in base all'accesso o al rischio per l'utente.

### <a name="unblocking-based-on-user-risk"></a>Sblocco in base al rischio per gli utenti

Per sbloccare un account bloccato a causa di un rischio per l'utente, gli amministratori hanno le seguenti opzioni:

1. **Reimpostazione della password** : è possibile reimpostare la password dell'utente.
1. **Ignora rischio utente:** i criteri di rischio per l'utente bloccano un utente se è stato raggiunto il livello di rischio dell'utente configurato per il blocco dell'accesso. È possibile ridurre il livello di rischio di un utente ignorando il rischio dell'utente o chiudendo manualmente i rilevamenti dei rischi segnalati.
1. **Escludere l'utente dai criteri:** se si ritiene che la configurazione corrente dei criteri di accesso stia causando problemi a utenti specifici, è possibile escludere gli utenti da esso. Per ulteriori informazioni, vedere la sezione Esclusioni nell'articolo [Procedura: configurare e abilitare i criteri di rischio](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Disabilitazione del criterio** : se si ritiene che la configurazione del criterio provochi problemi per tutti gli utenti, è possibile disabilitare il criterio. Per ulteriori informazioni, vedere l'articolo [Procedura: configurare e abilitare i criteri di rischio](howto-identity-protection-configure-risk-policies.md).

### <a name="unblocking-based-on-sign-in-risk"></a>Sblocco in base al rischio di accesso

Per sbloccare un account in base al rischio di accesso, gli amministratori dispongono delle opzioni seguenti:

1. **Accesso da una località o un dispositivo familiare**: un motivo comune del blocco di accessi sospetti è costituito da tentativi di accesso da località o dispositivi non familiari. Gli utenti possono determinare rapidamente se questo motivo è il motivo del blocco tentando di accedere da una posizione o un dispositivo familiare.
1. **Escludere l'utente dai criteri:** se si ritiene che la configurazione corrente dei criteri di accesso stia causando problemi a utenti specifici, è possibile escludere gli utenti da esso. Per ulteriori informazioni, vedere la sezione Esclusioni nell'articolo [Procedura: configurare e abilitare i criteri di rischio](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Disabilitazione del criterio** : se si ritiene che la configurazione del criterio provochi problemi per tutti gli utenti, è possibile disabilitare il criterio. Per ulteriori informazioni, vedere l'articolo [Procedura: configurare e abilitare i criteri di rischio](howto-identity-protection-configure-risk-policies.md).

## <a name="next-steps"></a>Passaggi successivi

Per ottenere una panoramica di Azure AD Identity Protection, vedere l'articolo di [panoramica di Azure AD Identity Protection](overview-identity-protection.md).
