---
title: Valutazione dell'accesso continuo in Azure ADContinuous access evaluation in Azure AD
description: Risposta alle modifiche dello stato utente più rapidamente con la valutazione dell'accesso continuo in Azure ADResponding to changes in user state faster with continuous access evaluation in Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5b70c11cd6bc24f945b437decf22586cfb97557
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873303"
---
# <a name="continuous-access-evaluation"></a>Valutazione dell'accesso continuo

I servizi Microsoft, come Azure Active Directory (Azure AD) e Office 365, usano standard e protocolli aperti per ottimizzare l'interoperabilità. Uno dei più critici è Open ID Connect (OIDC). Quando un'applicazione client come Outlook si connette a un servizio come Exchange Online, le richieste API vengono autorizzate utilizzando token di accesso OAuth 2.0. Per impostazione predefinita, tali token di accesso sono validi per un'ora. Quando scadono, il client viene reindirizzato ad Azure AD per aggiornarli. Ciò offre anche l'opportunità di rivalutare i criteri per l'accesso utente: è possibile scegliere di non aggiornare il token a causa di criteri di accesso condizionale o perché l'utente è stato disabilitato nella directory. 

Abbiamo sentito il feedback travolgente dai nostri clienti: un ritardo di un'ora dovuto alla durata del token di accesso per riapplicare i criteri di accesso condizionale e le modifiche nello stato dell'utente (ad esempio: disabilitato a causa di furlough) non è abbastanza buono.

Microsoft è stata una delle prime partecipanti all'iniziativa il protocollo CAEP (Continuous Access Evaluation Protocol) come parte del gruppo di lavoro [Shared Signals and Events](https://openid.net/wg/sse/) presso la OpenID Foundation. I provider di identità e le relying party saranno in grado di sfruttare gli eventi di sicurezza e i segnali definiti dal gruppo di lavoro per autorizzare nuovamente o terminare l'accesso. È un lavoro entusiasmante e migliorerà la sicurezza su molte piattaforme e applicazioni.

Poiché i vantaggi in termini di sicurezza sono così elevati, stiamo implementando un'implementazione iniziale specifica di Microsoft in parallelo al nostro continuo lavoro all'interno degli organismi di standardizzazione. Mentre lavoriamo per distribuire queste funzionalità di valutazione dell'accesso continuo (CAE) tra i servizi Microsoft, abbiamo imparato molto e stiamo condividendo queste informazioni con la comunità di standard. Ci auguriamo che la nostra esperienza nella distribuzione possa contribuire a informare uno standard di settore ancora migliore e ci impegniamo a implementare tale standard una volta ratificato, consentendo a tutti i servizi partecipanti di trarne vantaggio.

## <a name="how-does-cae-work-in-microsoft-services"></a>Come funziona CAE nei servizi Microsoft?

Stiamo concentrando la nostra implementazione iniziale della valutazione dell'accesso continuo a Exchange e Teams. Speriamo di espandere il supporto ad altri servizi Microsoft in futuro. Si inizierà ad abilitare la valutazione dell'accesso continuo solo per i tenant senza criteri di accesso condizionale. Utilizzeremo i nostri insegnamenti da questa fase di CAE per informare la nostra implementazione in corso di CAE.

## <a name="service-side-requirements"></a>Requisiti sul lato del servizio

La valutazione dell'accesso continuo viene implementata consentendo ai servizi (provider di risorse) di sottoscrivere eventi critici in Azure AD in modo che tali eventi possano essere valutati e applicati quasi in tempo reale. In questa implementazione iniziale di CAE verranno applicati i seguenti eventi:

- L'account utente viene eliminato o disabilitato
- La password di un utente viene modificata o reimpostata
- L'amministratore revoca in modo esplicito tutti i token di aggiornamento per un utenteAdmin explicitly revokes all refresh tokens for a user
- Rischio elevato per gli utenti rilevati da Azure AD Identity Protection

In futuro speriamo di aggiungere altri eventi, inclusi eventi come la posizione e i cambiamenti di stato del dispositivo. **Mentre il nostro obiettivo è che l'applicazione sia immediata, in alcuni casi può essere osservata una latenza fino a 15 minuti a causa**del tempo di propagazione dell'evento . 

## <a name="client-side-claim-challenge"></a>Richiesta di rimborso lato client

Prima della valutazione dell'accesso continuo, i client tentavano sempre di riprodurre il token di accesso dalla cache finché non era scaduto. Con CAE, stiamo introducendo un nuovo caso che un provider di risorse può rifiutare un token anche quando non è scaduto. Per informare i client di ignorare la cache anche se i token memorizzati nella cache non sono scaduti, introduciamo un meccanismo chiamato **claim challenge**. CAE richiede un aggiornamento del client per comprendere la richiesta di verifica delle attestazioni. L'ultima versione delle seguenti applicazioni di supporto rivendicare sfida:

- Outlook per Windows 
- Outlook iOS 
- Outlook Android 
- Outlook Mac 
- Team per Windows
- Squadre iOS 
- Squadre Android 
- Squadre Mac 

## <a name="token-lifetime"></a>Durata del token

Poiché i rischi e i criteri vengono valutati in tempo reale, i client che negoziano sessioni in grado di riconoscere la valutazione dell'accesso continuo si baseranno su criteri di durata del token di accesso statico esistenti, il che significa che i criteri di durata dei token configurabili non verranno più rispettati per i client che supportano CAE che negoziano le sessioni in grado di riconoscere CAE.

Aumenteremo la durata del token di accesso a 24 ore nelle sessioni CAE. La revoca è guidata da eventi critici e dalla valutazione dei criteri, non da un periodo di tempo arbitrario. Questa modifica aumenta la stabilità delle applicazioni senza influire sulla sicurezza. 

## <a name="example-flows"></a>Flussi di esempio

### <a name="user-revocation-event-flow"></a>Flusso degli eventi di revoca utente:User revocation event flow:

![Flusso degli eventi di revoca utenteUser revocation event flow](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. Un client che supporta CAE presenta le credenziali o un token di aggiornamento ad AAD che richiede un token di accesso per una risorsa.
1. Viene restituito un token di accesso insieme ad altri elementi al client.
1. Un amministratore revoca in modo esplicito tutti i token di [aggiornamento per l'utente.](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) Un evento di revoca verrà inviato al provider di risorse da Azure AD.
1. Un token di accesso viene presentato al provider di risorse. Il provider di risorse valuta la validità del token e controlla se è presente un evento di revoca per l'utente. Il provider di risorse utilizza queste informazioni per decidere di concedere o meno l'accesso alla risorsa.
1. In questo caso, il provider di risorse nega l'accesso e invia una richiesta di richiesta di attestazione di 401 e più
1. Il client che supporta CAE comprende la richiesta di verifica di 401. Ignora le cache e torna al passaggio 1, inviando il token di aggiornamento insieme alla richiesta di attestazione in Azure AD. Azure AD rivaluta quindi tutte le condizioni e richiede all'utente di eseguire nuovamente l'autenticazione in questo caso.
 
## <a name="faqs"></a>Domande frequenti

### <a name="what-is-the-lifetime-of-my-access-token"></a>Qual è la durata del token di accesso?

Se non si utilizzano client compatibili con CAE, la durata predefinita del token di accesso sarà ancora di 1 ora, a meno che non sia stata configurata la durata del token di accesso con la funzionalità di anteprima della durata del [token configurabile (CTL).](../develop/active-directory-configurable-token-lifetimes.md)

Se si utilizzano client compatibili con CAE che negoziano le sessioni compatibili con CAE, le impostazioni CTL per la durata del token di accesso verranno sovrascritte e la durata del token di accesso sarà di 24 ore.

### <a name="how-quick-is-enforcement"></a>Quanto è rapida l'applicazione?

Mentre il nostro obiettivo è che l'applicazione sia immediata, in alcuni casi la latenza può essere osservata fino a 15 minuti a causa del tempo di propagazione dell'evento.

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Come funzionerà CAE con la frequenza di accesso?

La frequenza di accesso verrà rispettata con o senza CAE.

## <a name="next-steps"></a>Passaggi successivi

[Annuncio della valutazione dell'accesso continuo](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
