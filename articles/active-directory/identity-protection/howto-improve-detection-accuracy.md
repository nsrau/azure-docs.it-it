---
title: Come migliorare la precisione del rilevamento in Azure Active Directory Identity Protection (procedura aggiornata) | Microsoft Docs
description: Come migliorare la precisione del rilevamento in Azure Active Directory Identity Protection (procedura aggiornata).
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, gestione applicazioni, sicurezza, rischio, livello di rischio, vulnerabilità, criteri di sicurezza
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 2ba6e3b8b844d72b762e9a66629f16b36193a69f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478390"
---
# <a name="how-to-improve-the-detection-accuracy"></a>Procedura: Migliorare la precisione del rilevamento 

Identity Protection offre meccanismi per inviare feedback ad Azure AD sui rilevamenti di rischi nell'ambiente in uso. Per fornire feedback, è possibile confermare lo stato dell’utente o dell’evento di accesso a rischio rilevato. Microsoft usa questi commenti e suggerimenti per intervenire sui rilevamenti di rischi correnti e migliorare la precisione dei rilevamenti futuri. 


## <a name="what-is-detection"></a>Che cos'è il rilevamento?

Il rilevamento è il processo di identificazione di attività sospette in combinazione con gli account utente. Le attività sospette che Azure AD può rilevare sono chiamate [eventi di rischio](../reports-monitoring/concept-risk-events.md). Il processo di rilevamento si basa sull'euristica e sugli algoritmi di apprendimento automatico adattivi per rilevare eventi di rischio relativi agli utenti.

I risultati del rilevamento vengono utilizzati per determinare se gli utenti e gli accessi sono a rischio. 


## <a name="how-can-i-improve-the-detection-accuracy"></a>Come si può migliorare la precisione del rilevamento?

Poiché il rilevamento è un processo automatizzato, è possibile che Azure AD segnali falsi positivi. È possibile migliorare la precisione del rilevamento fornendo ad Azure AD feedback riguardanti i risultati di rilevamento.

Esistono tre modi per migliorare la precisione del rilevamento: confermare un accesso compromesso, confermare un accesso sicuro e ignorare il rischio utente. È possibile farlo dai report seguenti:

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

- Il completamento di questa azione può richiedere alcuni minuti, motivo per cui non si consiglia di inviare nuovamente la richiesta.

- È possibile eseguire questa azione solo se AD gestisce le credenziali dell'utente. (Che cosa significa?)



## <a name="best-practices"></a>Procedure consigliate

Ignorare il rischio di un utente è un modo per sbloccarlo se è stato bloccato dai criteri di rischio utente e non è in grado di effettuare autonomamente la correzione perché non ha abilitato la reimpostazione della password e/o l’autenticazione a più fattori. In questo caso, è consigliabile verificare che l'utente si registri per la reimpostazione della password e l’autenticazione a più fattori in modo da poter correggere autonomamente tutti gli eventi di rischio futuri.


## <a name="next-steps"></a>Passaggi successivi

Per ottenere una panoramica di Azure AD Identity Protection, vedere l'articolo di [panoramica di Azure AD Identity Protection](overview-v2.md).


