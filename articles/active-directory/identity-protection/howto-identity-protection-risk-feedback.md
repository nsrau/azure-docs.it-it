---
title: Fornire feedback sul rischio in Azure Active Directory Identity Protection
description: Come e perché inviare commenti e suggerimenti sui rilevamenti dei rischi di Identity Protection.
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
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382085"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Procedura: fornire feedback sui rischi in Azure AD Identity Protection

Azure AD Identity Protection consente di inviare commenti e suggerimenti sulla valutazione dei rischi. Il documento seguente elenca gli scenari in cui si desidera inviare commenti e suggerimenti sulla valutazione dei rischi di Azure AD Identity Protection e sul modo in cui viene incorporata.

## <a name="what-is-a-detection"></a>Che cos'è un rilevamento?

Un rilevamento di Identity Protection è un indicatore di attività sospette dal punto di vista del rischio di identità. Queste attività sospette sono denominate rilevamenti dei rischi. Questi rilevamenti basati su identità possono essere basati su euristica, Machine Learning o possono provenire da prodotti partner. Questi rilevamenti vengono usati per determinare il rischio di accesso e i rischi dell'utente.

* Il rischio utente rappresenta la probabilità che un'identità venga compromessa.
* Il rischio di accesso rappresenta la probabilità che un accesso venga compromesso (ad esempio, l'accesso non è autorizzato dal proprietario dell'identità).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Perché è necessario fornire un feedback sui rischi per le valutazioni dei rischi di Azure AD? 

Esistono diversi motivi per cui è necessario fornire Azure AD feedback sui rischi:

- **È stato rilevato che la valutazione dei rischi di accesso e dell'utente di Azure ad non è corretta**. Ad esempio, un accesso visualizzato nel report "accessi a rischio" è stato benigno e tutti i rilevamenti su tale accesso erano falsi positivi.
- **È stato convalidato che l'utente Azure ad o la valutazione del rischio di accesso è stata corretta**. Ad esempio, un accesso visualizzato nel report "accessi a rischio" è stato effettivamente dannoso e si vuole che Azure AD sappia che tutti i rilevamenti su tale accesso erano veri positivi.
- **È stato risolto il rischio per tale utente al di fuori di Azure ad Identity Protection** e si desidera che il livello di rischio dell'utente venga aggiornato.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>In che modo Azure AD usa il feedback sui rischi?

Azure AD usa il feedback per aggiornare il rischio dell'utente sottostante e/o dell'accesso e l'accuratezza di questi eventi. Questo feedback contribuisce a proteggere l'utente finale. Se, ad esempio, si conferma che un accesso è compromesso, Azure AD aumenta immediatamente il rischio aggregato del rischio e dell'accesso dell'utente (non il rischio in tempo reale) a un livello elevato. Se questo utente è incluso nei criteri di rischio utente per forzare gli utenti ad alto rischio a reimpostare le password in modo sicuro, l'utente verrà automaticamente aggiornato al successivo accesso.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>In che modo è possibile fornire feedback sui rischi e cosa accade dietro le quinte?

Di seguito sono riportati gli scenari e i meccanismi per fornire commenti e suggerimenti sui rischi per Azure AD.

| Scenario | Come inviare commenti e suggerimenti? | Che cosa succede dietro le quinte? | note |
| --- | --- | --- | --- |
| **Accesso non compromesso (falso positivo)** <br> Il report ' accessi a rischio ' Mostra un accesso a rischio [stato rischio = a rischio] ma l'accesso non è stato compromesso. | Selezionare l'accesso e fare clic su "conferma accesso sicuro". | Azure AD sposterà il rischio aggregato di accesso su None [stato rischio = confermato sicuro; Livello di rischio (aggregato) =-] e inverterà il suo effetto sul rischio utente. | Attualmente, l'opzione "conferma accesso sicuro" è disponibile solo nel report "accessi a rischio". |
| **Accesso compromesso (vero positivo)** <br> Il report ' accessi a rischio ' Mostra un accesso a rischio [stato rischio = a rischio] con basso rischio [livello di rischio (aggregato) = basso] e tale accesso è stato effettivamente compromesso. | Selezionare l'accesso e fare clic su' conferma accesso compromesso '. | Azure AD sposterà il rischio aggregato di accesso e i rischi per l'utente in alto [stato rischio = confermato compromesso; Livello di rischio = alta]. | Attualmente, l'opzione ' conferma accesso compromesso ' è disponibile solo nel report "accessi a rischio". |
| **Utente compromesso (vero positivo)** <br> Il report ' utenti a rischio ' Mostra un utente a rischio [stato rischio = a rischio] con basso rischio [livello di rischio = basso] e tale utente è stato effettivamente compromesso. | Selezionare l'utente e fare clic su' conferma utente compromesso '. | Azure AD sposterà il rischio utente in alto [stato rischio = confermato compromesso; Livello di rischio = alta] e aggiungerà un nuovo rilevamento ' amministratore confermato utente compromesso '. | Attualmente, l'opzione ' conferma utente compromesso ' è disponibile solo nel report ' utenti a rischio '. <br> Il rilevamento ' amministratore confermato utente compromesso ' viene visualizzato nella scheda ' rilevamenti dei rischi non collegati a un accesso ' nel report ' utenti a rischio '. |
| **L'utente è stato aggiornato al di fuori della Azure AD Identity Protection (vero positivo + correzione)** <br> Il report ' utenti a rischio ' Mostra un utente a rischio e in seguito è stato eseguito il monitoraggio e l'aggiornamento dell'utente al di fuori della Azure AD Identity Protection. | 1. Selezionare l'utente e fare clic su' conferma utente compromesso '. Questo processo conferma la Azure AD che l'utente è stato effettivamente compromesso. <br> 2. attendere che il livello di rischio dell'utente passi a alto. Questa volta fornisce Azure AD il tempo necessario per l'invio del feedback precedente al motore di rischio. <br> 3. Selezionare l'utente e fare clic su' ignora il rischio utente '. Questo processo conferma Azure AD che l'utente non è più compromesso. |  Azure AD sposta il rischio utente in nessuno [stato di rischio = non è stato eliminato; Livello di rischio =-] e chiude il rischio per tutti gli accessi esistenti con rischio attivo. | Se si fa clic su' Ignora rischio utente ', si chiuderanno tutti i rischi per l'utente e gli accessi passati. Questa azione non può essere annullata. |
| **Utente non compromesso (falso positivo)** <br> Il report ' utenti a rischio ' viene visualizzato all'utente a rischio, ma l'utente non è compromesso. | Selezionare l'utente e fare clic su' ignora il rischio utente '. Questo processo conferma la Azure AD che l'utente non è compromesso. | Azure AD sposta il rischio utente in nessuno [stato di rischio = non è stato eliminato; Livello di rischio =-]. | Se si fa clic su' Ignora rischio utente ', si chiuderanno tutti i rischi per l'utente e gli accessi passati. Questa azione non può essere annullata. |
| Si desidera chiudere il rischio utente, ma non si è certi che l'utente sia compromesso/sicuro. | Selezionare l'utente e fare clic su' ignora il rischio utente '. Questo processo conferma Azure AD che l'utente non è più compromesso. | Azure AD sposta il rischio utente in nessuno [stato di rischio = non è stato eliminato; Livello di rischio =-]. | Se si fa clic su' Ignora rischio utente ', si chiuderanno tutti i rischi per l'utente e gli accessi passati. Questa azione non può essere annullata. È consigliabile correggere l'utente facendo clic su "Reimposta password" o richiedere all'utente di reimpostare o modificare le credenziali in modo sicuro. |

Il feedback sui rilevamenti dei rischi utente in Identity Protection viene elaborato offline e potrebbe richiedere del tempo per l'aggiornamento. La colonna Stato elaborazione rischio fornirà lo stato corrente dell'elaborazione dei commenti.

![Stato di elaborazione dei rischi per un report utente rischioso](./media/howto-identity-protection-risk-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Passaggi successivi

- [Riferimento al rilevamento del rischio Azure Active Directory Identity Protection](risk-events-reference.md)
