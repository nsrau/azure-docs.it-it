---
title: 'Fornire commenti e suggerimenti per gli eventi di rischio in Azure AD Identity Protection: Azure Active Directory'
description: Come e perché è necessario fornire feedback sugli eventi di rischio di Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66d53590e89afb1a903b22ff60e32871a1502ada
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827906"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Procedura: Inviare commenti e suggerimenti dei rischi in Azure AD Identity Protection

Azure AD Identity Protection consente di inviare commenti e suggerimenti sulla relativa valutazione dei rischi. Il documento seguente sono elencati gli scenari in cui si desidera inviare commenti e suggerimenti sulla valutazione dei rischi di Azure AD Identity Protection e modo in cui incorporiamo.

## <a name="what-is-a-detection"></a>Che cos'è un rilevamento?

Un'individuazione di Identity Protection è un indicatore di attività sospette da una prospettiva di rischio di identity. Queste attività sospette vengono chiamate gli eventi di rischio. Questi rilevamenti basati su identità possono essere basati sull'euristica, machine learning o possono provenire da prodotti di terze parti. I rilevamenti vengono utilizzati per determinare il rischio utente e il rischio di accesso

* Rischio utente rappresenta la probabilità che un'identità sia compromessa.
* Rischio di accesso rappresenta la probabilità viene compromesso un accesso aggiuntivo (ad esempio, accesso non è autorizzato dal proprietario dell'identità).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Perché dovrei fornisca un riscontro rischio a valutazioni dei rischi di Azure AD? 

Esistono diversi motivi per cui è necessario assegnare commenti e suggerimenti dei rischi di Azure AD:

1. **È possibile trovare valutazione dei rischi utente o di accesso di Azure AD corretto**. Ad esempio, un accesso mostrato nel report "Accessi a rischio" è non dannoso e tutti i rilevamenti in fase di tale accesso erano i falsi positivi.
1. **Si verificato utente di Azure AD o la valutazione dei rischi di accesso è stato corretta**. Ad esempio, un accesso mostrato nel report "Accessi a rischio" è stato in effetti dannosi e si vuole che Azure AD per sapere che tutti i rilevamenti in fase di tale accesso erano veri positivi.
1. **Corretto il rischio di quell'utente all'esterno di Azure AD Identity Protection** e si desidera che il livello di rischio dell'utente da aggiornare.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Come Azure AD Usa i miei commenti rischio?

Per aggiornare il rischio che il sottostante utente e/o Accedi, Azure AD Usa i tuoi commenti. Questi commenti e suggerimenti aiuta a proteggere l'utente finale. Ad esempio, dopo aver verificato il che accesso è compromesso, immediatamente AD Azure aumenta il rischio dell'utente e dell'aggregazione rischio di accesso (non in tempo reale rischio) su un valore elevato. Se l'utente è incluso nei criteri di rischio utente per forzare gli utenti ad alto rischio di reimpostare in modo sicuro le password, l'utente correggerà automaticamente stesso la volta successiva che accede.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Come devo concedere rischio commenti e suggerimenti e ciò che accade dietro le quinte?

Ecco gli scenari e i meccanismi per inviare commenti e suggerimenti dei rischi in Azure AD.

| Scenario | Come fornire commenti e suggerimenti? | Che cosa succede dietro le quinte? | Note |
| --- | --- | --- | --- |
| **Accedi non vengano compromessi (falso positivo)** <br> Report "Accessi a rischio" Mostra un accesso aggiuntivo a rischio [lo stato di rischio = a rischio] ma tale accesso non è stato compromesso. | Selezionare accesso e fare clic su 'Confirm Accedi safe'. | Azure AD sposterà dell'aggregazione rischio di accesso su Nessuno [lo stato di rischio = Confirmed-safe. (Aggregazione) a livello di rischio =-] e invertirà l'impatto sul rischio utente. | Attualmente, l'opzione 'Confirm Accedi safe' è disponibile solo nel report "Accessi a rischio". |
| **Accedi compromesso (vero positivo)** <br> Report "Accessi a rischio" Mostra un accesso aggiuntivo a rischio [lo stato di rischio = a rischio] con a basso rischio [(aggregazione) a livello di rischio = Low] e sign-in effetti è stata compromessa. | Selezionare accesso e fare clic su 'Confirm Accedi compromesso'. | Azure AD sposterà dell'aggregazione rischio di accesso e il rischio utente su un valore elevato [lo stato di rischio = Confirmed compromesso; Livello di rischio = High]. | Attualmente, l'opzione 'Confirm Accedi compromesso' è disponibile solo nel report "Accessi a rischio". |
| **Utente compromesso (vero positivo)** <br> Report "Utenti a rischio" Mostra un utente a rischio [lo stato di rischio = a rischio] con a basso rischio [livello di rischio = Low] e che l'utente è stato effettivamente compromesso. | Selezionare l'utente e fare clic su 'Confirm utente compromessi'. | Azure AD sposterà il rischio utente su un valore elevato [lo stato di rischio = Confirmed compromesso; Livello di rischio = High] e verrà aggiunto un nuovo rilevamento 'Amministratore confermata utente compromesso'. | Attualmente, l'opzione 'Confirm utente compromessi' è disponibile solo nel report "Utenti a rischio". <br> Il rilevamento 'Amministratore confermata utente compromesso' viene visualizzata nella scheda "Non è collegati a un accesso aggiuntivo degli eventi di rischio" nel report "Utenti a rischio". |
| **Utente risolti all'esterno di Azure AD Identity Protection (vero positivo + Remediated)** <br> Report "Utenti a rischio" Mostra un utente a rischio e ho risolti successivamente l'utente all'esterno di Azure AD Identity Protection. | 1. Selezionare l'utente e fare clic su 'Confirm utente compromessi'. (Questo processo conferma ad Azure AD che l'utente è stato effettivamente compromessa.) <br> 2. Attendere 'livello di rischio"il suo per passare alla elevata. (Questa fase consente di Azure AD, il tempo necessario per richiedere il feedback precedente al motore di rischio.) <br> 3. Selezionare l'utente e fare clic su 'Elimina il rischio utente'. (Questo processo conferma ad Azure AD che l'utente non è più venga compromesso.) |  Azure AD consente di spostare il rischio utente su none [lo stato di rischio = Dismissed; Livello di rischio =-] e chiude il rischio di tutti esistente gli accessi con rischio attivi. | Facendo clic su 'Elimina il rischio utente' chiuderà tutti i rischi per l'utente e dopo gli accessi. Questa azione non può essere annullata. |
| **Utente non vengano compromessi (falso positivo)** <br> Report "Utenti a rischio" Mostra all'utente a rischio, ma l'utente non venga compromessa. | Selezionare l'utente e fare clic su 'Elimina il rischio utente'. (Questo processo conferma ad Azure AD che l'utente non venga compromessa.) | Azure AD consente di spostare il rischio utente su none [lo stato di rischio = Dismissed; Livello di rischio =-]. | Facendo clic su 'Elimina il rischio utente' chiuderà tutti i rischi per l'utente e dopo gli accessi. Questa azione non può essere annullata. |
| Vuole chiudere il rischio utente, ma non sono sicuro che l'utente sia compromesso / sicuri. | Selezionare l'utente e fare clic su 'Elimina il rischio utente'. (Questo processo conferma ad Azure AD che l'utente non è più venga compromesso.) | Azure AD consente di spostare il rischio utente su none [lo stato di rischio = Dismissed; Livello di rischio =-]. | Facendo clic su 'Elimina il rischio utente' chiuderà tutti i rischi per l'utente e dopo gli accessi. Questa azione non può essere annullata. Si consiglia di monitorare e aggiornare l'utente facendo clic su 'Reimposta password' o richiedere all'utente di reimpostare o modificare in modo sicuro le proprie credenziali. |

Commenti e suggerimenti su eventi di rischio utente di Identity Protection viene elaborato in modalità offline e potrebbero richiedere alcuni minuti per l'aggiornamento. Il rischio che l'elaborazione della colonna di stato fornisce lo stato corrente dell'elaborazione di commenti e suggerimenti.

![Stato di elaborazione per report per l'utente a rischio di rischio](./media/howto-provide-risk-event-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Passaggi successivi

[Fare riferimento a eventi di rischio di Azure Active Directory Identity Protection](risk-events-reference.md)