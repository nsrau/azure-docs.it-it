---
title: Fornire commenti e suggerimenti sui rischi in Azure Active Directory Identity Protection
description: Come e perché fornire commenti e suggerimenti sui rilevamenti dei rischi di Identity Protection.
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
ms.openlocfilehash: 4be8290f12d64f0c556100c63ec159bd414c6fcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382085"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Procedura: inviare commenti e suggerimenti sui rischi in Azure AD Identity ProtectionHow To: Give risk feedback in Azure AD Identity Protection

Azure AD Identity Protection consente di inviare commenti e suggerimenti sulla valutazione dei rischi. Il documento seguente elenca gli scenari in cui si vuole inviare commenti e suggerimenti sulla valutazione dei rischi di Azure AD Identity Protection e su come viene incorporata.

## <a name="what-is-a-detection"></a>Che cos'è un rilevamento?

Un rilevamento di Identity Protection è un indicatore di attività sospette dal punto di vista del rischio di identità. Queste attività sospette sono chiamate rilevamenti dei rischi. Questi rilevamenti basati su identità possono essere basati su euristica, apprendimento automatico o possono provenire da prodotti partner. Questi rilevamenti vengono utilizzati per determinare il rischio di accesso e il rischio per gli utenti,

* Il rischio dell'utente rappresenta la probabilità che un'identità venga compromessa.
* Il rischio di accesso rappresenta la probabilità che un accesso venga compromesso (ad esempio, l'accesso non è autorizzato dal proprietario dell'identità).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Perché è consigliabile inviare commenti e suggerimenti sui rischi alle valutazioni dei rischi di Azure AD? 

Esistono diversi motivi per cui è consigliabile fornire commenti e suggerimenti sui rischi di Azure AD:There are several reasons why you should give Azure AD risk feedback:

- La valutazione dei rischi di **accesso o utente o di accesso di Azure AD non è corretta.** Ad esempio, un accesso mostrato nel report 'Accesso rischioso' era benigno e tutti i rilevamenti su tale accesso erano falsi positivi.
- È stato convalidato che la valutazione dei rischi per **l'accesso o l'utente o l'accesso di Azure AD è corretta.** Ad esempio, un accesso mostrato nel report "Accessi rischiosi" è stato effettivamente dannoso e si vuole che Azure AD sappia che tutti i rilevamenti in tale accesso erano veri positivi.
- **È stato verificato il rischio per tale utente al di fuori di Azure AD Identity Protection** e si vuole aggiornare il livello di rischio dell'utente.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>In che modo Azure AD usa il feedback sui rischi?

Azure AD usa il feedback per aggiornare il rischio dell'utente sottostante e/o dell'accesso e dell'accuratezza di questi eventi. Questo feedback consente di proteggere l'utente finale. Ad esempio, una volta confermato che un accesso è compromesso, Azure AD aumenta immediatamente il rischio dell'utente e il rischio aggregato dell'accesso (non il rischio in tempo reale) a Alto.For example, once you confirm a sign-in is compromised, Azure AD immediately increases the user's risk and sign-in aggregate risk (not real-time risk) to High. Se l'utente è incluso nei criteri di rischio utente per imporre agli utenti ad alto rischio di reimpostare in modo sicuro le password, l'utente si rileverà automaticamente al successivo accesso.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Come devo dare un feedback sul rischio e cosa succede sotto il cofano?

Ecco gli scenari e i meccanismi per inviare commenti e suggerimenti sui rischi ad Azure AD.

| Scenario | Come dare un feedback? | Cosa succede sotto il cofano? | Note |
| --- | --- | --- | --- |
| **Accesso non compromesso (falso positivo)** <br> Il report "Accesso rischioso" mostra un accesso a rischio [Stato di rischio - Rischio], ma tale accesso non è stato compromesso. | Seleziona l'accesso e fai clic su "Conferma l'accesso sicuro". | Azure AD sposterà il rischio aggregato dell'accesso su none [Stato del rischio - Confermato sicuro; Livello di rischio (Aggregazione) -] e invertirà il suo impatto sul rischio dell'utente. | Attualmente, l'opzione "Conferma accesso sicuro" è disponibile solo nel report 'Accesso rischioso'. |
| **Accesso compromesso (vero positivo)** <br> Il report "Accesso rischioso" mostra un accesso a rischio [Stato di rischio - A rischio] con basso rischio [Livello di rischio (aggregazione) - Basso] e che l'accesso è stato effettivamente compromesso. | Seleziona l'accesso e fai clic su "Conferma accesso compromesso". | Azure AD sposterà il rischio aggregato dell'accesso e il rischio utente in Alto [Stato rischio - Confermato compromesso; Livello di rischio - Alto]. | Attualmente, l'opzione "Conferma accesso compromesso" è disponibile solo nel report 'Accesso rischioso'. |
| **Utente compromesso (vero positivo)** <br> Il rapporto "Utenti rischiosi" mostra un utente a rischio [Stato di rischio - A rischio] con basso rischio [Livello di rischio - Basso] e che l'utente è stato effettivamente compromesso. | Selezionare l'utente e fare clic su 'Conferma utente compromesso'. | Azure AD sposterà il rischio utente su Alto [Stato rischio - Confermato compromesso; Livello di rischio: alto] e aggiungerà un nuovo rilevamento "Admin confirmed user compromised". | Attualmente, l'opzione "Conferma autenticazione compromessa" è disponibile solo nel rapporto "Utenti rischiosi". <br> Il rilevamento "Admin confirmed user compromised" viene visualizzato nella scheda "Rilevamenti dei rischi non collegati a un accesso" nel rapporto "Utenti rischiosi". |
| **Utente cordato al di fuori di Azure AD Identity Protection (True positive : Remediato)** <br> Il report "Utenti rischiosi" mostra un utente a rischio e in seguito ho verificato l'utente al di fuori di Azure AD Identity Protection. | 1. Selezionare l'utente e fare clic su 'Conferma utente compromesso'. Questo processo conferma ad Azure AD che l'utente è stato effettivamente compromesso. <br> 2. Attendere che il "Livello di rischio" dell'utente vada a Alta. Questa volta concede ad Azure AD il tempo necessario per ricevere il feedback precedente al motore dei rischi. <br> 3. Selezionare l'utente e fare clic su 'Elimina rischio utente'. Questo processo conferma ad Azure AD che l'utente non è più compromesso. |  Azure AD sposta il rischio per l'utente in nessuno [Stato di rischio - Ignorato; Livello di rischio - -] e chiude il rischio per tutti gli invii esistenti con rischio attivo. | Se si fa clic su "Ignora rischio utente", tutti i rischi per l'utente e gli egli precedenti verranno archiviati. Questa azione non può essere annullata. |
| **Utente non compromesso (falso positivo)** <br> Il rapporto "Utenti rischiosi" mostra l'utente a rischio, ma l'utente non è compromesso. | Selezionare l'utente e fare clic su 'Ignora rischio utente'. Questo processo conferma ad Azure AD che l'utente non è compromesso. | Azure AD sposta il rischio per l'utente in nessuno [Stato di rischio - Ignorato; Livello di rischio -]. | Se si fa clic su "Ignora rischio utente", tutti i rischi per l'utente e gli egli precedenti verranno archiviati. Questa azione non può essere annullata. |
| Voglio chiudere il rischio per l'utente, ma non sono sicuro se l'utente è compromesso / sicuro. | Selezionare l'utente e fare clic su 'Ignora rischio utente'. Questo processo conferma ad Azure AD che l'utente non è più compromesso. | Azure AD sposta il rischio per l'utente in nessuno [Stato di rischio - Ignorato; Livello di rischio -]. | Se si fa clic su "Ignora rischio utente", tutti i rischi per l'utente e gli egli precedenti verranno archiviati. Questa azione non può essere annullata. Si consiglia di correggere l'utente facendo clic su 'Reimposta password' o richiedere all'utente di reimpostare/modificare in modo sicuro le proprie credenziali. |

Il feedback sui rilevamenti dei rischi degli utenti in Identity Protection viene elaborato offline e potrebbe richiedere del tempo per l'aggiornamento. La colonna dello stato di elaborazione del rischio fornirà lo stato corrente dell'elaborazione del feedback.

![Stato di elaborazione del rischio per il report degli utenti rischiosi](./media/howto-identity-protection-risk-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni di riferimento sui rilevamenti dei rischi di Azure Active Directory Identity ProtectionAzure Active Directory Identity Protection risk detections reference](risk-events-reference.md)
