---
title: Come migliorare la precisione del rilevamento in Azure Active Directory Identity Protection (procedura aggiornata) | Microsoft Docs
description: Come migliorare la precisione del rilevamento in Azure Active Directory Identity Protection (procedura aggiornata).
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32bb8de7970fc167a6a95e9d9c3c71e4e1dc0150
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333947"
---
# <a name="how-to-improve-the-detection-accuracy"></a>Procedura: Migliorare la precisione del rilevamento 

Identity Protection offre meccanismi per inviare feedback ad Azure AD sui rilevamenti di rischi nell'ambiente in uso. Per fornire feedback, è possibile confermare lo stato dell’utente o dell’evento di accesso a rischio rilevato. Microsoft usa questi commenti e suggerimenti per intervenire sui rilevamenti di rischi correnti e migliorare la precisione dei rilevamenti futuri. 

## <a name="what-is-detection"></a>Che cos'è il rilevamento?

Il rilevamento è il processo di identificazione di attività sospette in combinazione con gli account utente. Le attività sospette che Azure AD può rilevare sono chiamate [eventi di rischio](../reports-monitoring/concept-risk-events.md). Il processo di rilevamento si basa sull'euristica e sugli algoritmi di apprendimento automatico adattivi per rilevare eventi di rischio relativi agli utenti.

I risultati del rilevamento vengono utilizzati per determinare se gli utenti e gli accessi sono a rischio. 

## <a name="how-can-i-improve-the-detection-accuracy"></a>Come si può migliorare la precisione del rilevamento?

Poiché il rilevamento è un processo automatizzato, è possibile che Azure AD segnali falsi positivi. È possibile migliorare la precisione del rilevamento fornendo ad Azure AD feedback riguardanti i risultati di rilevamento.

Sono disponibili tre modi per migliorare l'accuratezza del rilevamento: confermare l'accesso compromesso, confermare l'accesso sicuro e ignorare il rischio utente. È possibile farlo dai report seguenti:

- **Report sugli accessi a rischio -** Nel report sugli accessi a rischio, è possibile indicare se gli accessi sono sicuri o compromessi
- **Report sugli utenti a rischio -** Nel report sugli utenti a rischio, è possibile ignorare il rischio utente 

Il feedback fornito dall’utente viene elaborato da Azure AD per migliorare la precisione dei risultati di rilevamento. In genere, il feedback viene fornito nell’ambito di un analisi del rischio utente o del rischio di accesso. Per altre informazioni, vedere [Come analizzare utenti e accessi a rischio](howto-investigate-risky-users-signins.md).

## <a name="confirm-compromised"></a>Confermare un accesso compromesso

Confermando un evento di accesso come compromesso si segnala ad Azure AD che l’accesso non è stato autorizzato dal proprietario dell'identità. Quando si seleziona "Conferma compromesso", Azure AD

- Aumenterà il rischio utente dell'utente interessato, impostandolo su alto.
- Consentirà di ottimizzare l’apprendimento automatico che rileva gli eventi di rischio
- Adotterà misure aggiuntive per proteggere ulteriormente l'organizzazione

Per confermare un accesso compromesso:

- **Report accessi a rischio** - Questa opzione consente di confermare un accesso compromesso per uno o più eventi di accesso.

   ![Ignorare il rischio utente](./media/howto-improve-detection-accuracy/07.png)

- **Vista Dettagli del report accessi a rischio** - Questa opzione consente di confermare un account compromesso per l'evento di accesso selezionato nel report accessi a rischio. 

   ![Ignorare il rischio utente](./media/howto-improve-detection-accuracy/04.png)
 
## <a name="confirm-safe"></a>Confermare un accesso sicuro

Confermando un evento di accesso come sicuro si segnala ad Azure AD che l’accesso **è stato** autorizzato dal proprietario della rispettiva identità. Quando si seleziona "Conferma sicuro", Azure AD:

- Ripristinerà il contributo di rischio utente degli accessi selezionati
- Chiuderà gli eventi di rischio sottostanti
- Consentirà di ottimizzare l’apprendimento automatico che rileva gli eventi di rischio
- Adotterà misure aggiuntive per proteggere ulteriormente l'organizzazione
 
Confermare un accesso sicuro in:

- **Report accessi a rischio** - Questa opzione consente di confermare un accesso sicuro per uno o più eventi di accesso.

   ![Ignorare il rischio utente](./media/howto-improve-detection-accuracy/08.png)

- **Vista Dettagli del report accessi a rischio** - Questa opzione consente di confermare un accesso sicuro per l'evento di accesso selezionato nel report accessi a rischio. 

   ![Ignorare il rischio utente](./media/howto-improve-detection-accuracy/05.png)

## <a name="dismiss-user-risk"></a>Ignorare il rischio utente

Se sono già state adottate azioni di correzione per un utente a rischio, o se si ritiene che l’utente sia stato contrassegnato erroneamente come rischioso, è possibile ignorare il rischio utente. Ignorando il rischio utente si ripristina lo stato non rischioso dell’utente stesso. Tutti gli accessi rischiosi e gli eventi di rischio passati per l'utente selezionato verranno ignorati.

È possibile ignorare il rischio utente segnalato in:

- **Report utenti a rischio** - Questa opzione consente di ignorare il rischio utente per uno o più utenti selezionati.

   ![Ignorare il rischio utente](./media/howto-improve-detection-accuracy/02.png)

- **Vista Dettagli** - Questa opzione consente di ignorare il rischio utente per l'utente selezionato nel report dei rischi utente. 

   ![Ignorare il rischio utente](./media/howto-improve-detection-accuracy/01.png)

**Informazioni utili:**

- Non è possibile ripristinare questa azione.
- Potrebbero essere necessari alcuni minuti per il completamento di questa azione, motivo per cui non è necessario inviare nuovamente la richiesta.
- È possibile eseguire questa azione solo se AD gestisce le credenziali dell'utente. 

## <a name="best-practices"></a>Procedure consigliate

La rimozione del rischio di un utente è un modo per sbloccarli se sono stati bloccati dai criteri di rischio utente e non è in grado di eseguire la correzione automatica a causa della mancata reimpostazione della password e/o dell'autenticazione a più fattori abilitata. In questo caso, è consigliabile verificare che l'utente si registri per la reimpostazione della password e l’autenticazione a più fattori in modo da poter correggere autonomamente tutti gli eventi di rischio futuri.

## <a name="next-steps"></a>Passaggi successivi

Per ottenere una panoramica di Azure AD Identity Protection, vedere l'articolo di [panoramica di Azure AD Identity Protection](overview-v2.md).
