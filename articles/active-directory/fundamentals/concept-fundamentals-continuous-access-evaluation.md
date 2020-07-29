---
title: Valutazione dell'accesso continuo in Azure AD
description: Risposta alle modifiche nello stato utente più velocemente con la valutazione dell'accesso continuo in Azure AD
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
ms.openlocfilehash: f0cb402741163c657b3e7961eb5a4f9c8e18dafd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84673021"
---
# <a name="continuous-access-evaluation"></a>Valutazione continua dell'accesso

I servizi Microsoft, ad esempio Azure Active Directory (Azure AD) e Office 365, utilizzano standard e protocolli aperti per ottimizzare l'interoperabilità. Uno dei più importanti è Open ID Connect (OIDC). Quando un'applicazione client come Outlook si connette a un servizio come Exchange Online, le richieste API vengono autorizzate usando i token di accesso OAuth 2,0. Per impostazione predefinita, i token di accesso sono validi per un'ora. Alla scadenza, il client viene reindirizzato di nuovo a Azure AD per aggiornarli. Questo consente anche di rivalutare i criteri per l'accesso utente. è possibile scegliere di non aggiornare il token a causa di un criterio di accesso condizionale oppure perché l'utente è stato disabilitato nella directory. 

La scadenza e l'aggiornamento dei token è un meccanismo standard nel settore. Ciò premesso, i clienti hanno espresso dubbi sul ritardo tra la modifica delle condizioni di rischio per l'utente (ad esempio, il passaggio dall'ufficio aziendale alla caffetteria locale o le credenziali utente individuate sul mercato nero) e quando è possibile applicare i criteri correlati a tale modifica. È stato sperimentato l'approccio "oggetto smussato" per la durata dei token ridotta, ma è stato rilevato che possono compromettere l'esperienza utente e l'affidabilità senza eliminare i rischi.

Una risposta tempestiva alle violazioni dei criteri o ai problemi di sicurezza richiede effettivamente una "conversazione" tra l'emittente del token, ad esempio Azure AD e il relying party, ad esempio Exchange Online. Questa conversazione bidirezionale ci offre due importanti funzionalità. Il relying party può rilevare le modifiche apportate, ad esempio un client proveniente da una nuova posizione, e indicare all'emittente del token. Fornisce inoltre all'emittente del token un modo per indicare al relying party di arrestare il rispetto dei token per un determinato utente a causa di compromissione dell'account, disabilitazione o altre problematiche. Il meccanismo per questa conversazione è il CAE (Continuous Access Evaluation).

Microsoft ha partecipato a un primo partecipante all'iniziativa CAEP (Continuous Access Evaluation Protocol) come parte del gruppo di lavoro dei [segnali e degli eventi condivisi](https://openid.net/wg/sse/) in OpenID Foundation. I provider di identità e le relying party saranno in grado di sfruttare gli eventi e i segnali di sicurezza definiti dal gruppo di lavoro per autorizzare o terminare l'accesso. Si tratta di un lavoro entusiasmante che migliorerà la sicurezza in molte piattaforme e applicazioni.

Poiché i vantaggi di sicurezza sono così eccezionali, viene implementata un'implementazione iniziale specifica di Microsoft in parallelo al nostro lavoro continuo all'interno dei corpi degli standard. Man mano che collaboriamo con la distribuzione di queste funzionalità di valutazione dell'accesso continuo (CAE) tra i servizi Microsoft, abbiamo imparato molto e condividiamo queste informazioni con la community degli standard. Ci auguriamo che la nostra esperienza nella distribuzione possa aiutare a offrire uno standard del settore ancora migliore e si impegna a implementare tale standard una volta ratificato, consentendo a tutti i servizi partecipanti di trarre vantaggio.

## <a name="how-does-cae-work-in-microsoft-services"></a>In che modo CAE funziona nei servizi Microsoft?

Stiamo concentrando la nostra implementazione iniziale della valutazione dell'accesso continuo a Exchange e ai team. Ci auguriamo di ampliare il supporto per altri servizi Microsoft in futuro. Si inizierà ad abilitare la valutazione dell'accesso continuo solo per i tenant senza criteri di accesso condizionale. Microsoft userà le nostre informazioni di questa fase del CAE per informare l'implementazione continuativa del CAE.

## <a name="service-side-requirements"></a>Requisiti lato servizio

La valutazione dell'accesso continuo viene implementata consentendo ai servizi (provider di risorse) di sottoscrivere eventi critici in Azure AD in modo che tali eventi possano essere valutati e applicati quasi in tempo reale. In questa implementazione iniziale di CAE verranno applicati gli eventi seguenti:

- L'account utente è stato eliminato o disabilitato
- La password per un utente è stata modificata o reimpostata
- Autenticazione a più fattori abilitata per l'utente
- L'amministratore revoca in modo esplicito tutti i token di aggiornamento per un utente
- Rischi utente elevati rilevati da Azure AD Identity Protection

In futuro ci auguriamo di aggiungere altri eventi, inclusi eventi come la posizione e le modifiche dello stato del dispositivo. **Anche se l'obiettivo è quello di imporre l'applicazione in modo immediato, in alcuni casi è possibile che si verifichi una latenza di un massimo di 15 minuti a causa del tempo di propagazione dell'evento**. 

## <a name="client-side-claim-challenge"></a>Richiesta di attestazione lato client

Prima della valutazione dell'accesso continuo, i client tentavano sempre di riprodurre il token di accesso dalla cache, purché non fosse scaduto. Con CAE, viene introdotto un nuovo caso che un provider di risorse può rifiutare un token anche quando non è scaduto. Per informare i client di ignorare la cache anche se i token memorizzati nella cache non sono scaduti, viene introdotto un meccanismo denominato **Challenge Claim**. CAE richiede un aggiornamento del client per comprendere la richiesta di attestazione. La versione più recente delle seguenti applicazioni supporta la richiesta di attestazione:

- Outlook per Windows 
- Outlook per iOS 
- Outlook per Android 
- Outlook per Mac 
- Team per Windows
- Team per iOS 
- Team per Android 
- Team per Mac 

## <a name="token-lifetime"></a>Durata del token

Poiché i rischi e i criteri vengono valutati in tempo reale, i client che negoziano sessioni compatibili con la valutazione dell'accesso continuo si basano su CAE anziché sui criteri di durata dei token di accesso statici esistenti, il che significa che i criteri di durata dei token configurabili non verranno più rispettati per i client con supporto per CAE che negoziano sessioni compatibili con CAE

Si aumenterà la durata del token di accesso a 24 ore nelle sessioni CAE. La revoca è determinata da eventi critici e valutazione dei criteri, non da un periodo di tempo arbitrario. Questa modifica aumenta la stabilità delle applicazioni senza influire sul comportamento di sicurezza. 

## <a name="example-flows"></a>Flussi di esempio

### <a name="user-revocation-event-flow"></a>Flusso eventi di revoca utente:

![Flusso eventi di revoca utente](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. Un client in grado di supportare CAE presenta le credenziali o un token di aggiornamento ad AAD per richiedere un token di accesso per una risorsa.
1. Viene restituito un token di accesso insieme ad altri artefatti al client.
1. Un amministratore revoca in modo esplicito [tutti i token di aggiornamento per l'utente](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Un evento di revoca verrà inviato al provider di risorse da Azure AD.
1. Un token di accesso viene presentato al provider di risorse. Il provider di risorse valuta la validità del token e controlla se è presente un evento di revoca per l'utente. Il provider di risorse utilizza queste informazioni per decidere di concedere l'accesso alla risorsa.
1. In questo caso, il provider di risorse nega l'accesso e invia una richiesta di attestazione 401 + al client
1. Il client in grado di supportare CAE riconosce la richiesta di attestazione 401 +. Ignora le cache e torna al passaggio 1, inviando il token di aggiornamento insieme alla richiesta di attestazione al Azure AD. Azure AD valuterà quindi tutte le condizioni e chiederà all'utente di eseguire nuovamente l'autenticazione in questo caso.

## <a name="faqs"></a>Domande frequenti

### <a name="what-is-the-lifetime-of-my-access-token"></a>Qual è la durata del token di accesso?

Se non si usano client con supporto per CAE, la durata dei token di accesso predefinita sarà ancora di 1 ora, a meno che non sia stata configurata la durata del token di accesso con la funzionalità di anteprima della [durata del token configurabile (CTL)](../develop/active-directory-configurable-token-lifetimes.md) .

Se si usano client che supportano CAE che negoziano sessioni compatibili con CAE, le impostazioni CTL per la durata dei token di accesso verranno sovrascritte e la durata dei token di accesso sarà di 24 ore.

### <a name="how-quick-is-enforcement"></a>Quanto è veloce l'imposizione?

Anche se l'obiettivo è quello di imporre l'applicazione in modo immediato, in alcuni casi è possibile che si verifichi una latenza di un massimo di 15 minuti a causa del tempo di propagazione dell'evento.

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Come funziona CAE con la frequenza di accesso?

La frequenza di accesso verrà rispettata con o senza CAE.

## <a name="next-steps"></a>Passaggi successivi

[Annuncio della valutazione di accesso continuo](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
