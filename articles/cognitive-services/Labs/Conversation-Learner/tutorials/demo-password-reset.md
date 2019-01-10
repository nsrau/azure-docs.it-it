---
title: 'Modello demo di Conversation Learner: reimpostazione della password - Servizi cognitivi Microsoft| Microsoft Docs'
titleSuffix: Azure
description: Informazioni su come creare un modello dimostrativo di apprendimento della conversazione.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 409647da146a2844384204cb03de5028d45e5763
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53792391"
---
# <a name="demo-password-reset"></a>Demo: Reimpostazione delle password
Questa esercitazione illustra un bot di supporto tecnico semplice che possa facilitare la reimpostazione della password con tecnologia Conversation Learner. Il modello di bot può apprendere flussi di dialogo non elementari e sequenze di più turni, includendo una classe fuori dominio. L'attività può essere eseguita senza codice o entità.

## <a name="video"></a>Video

[![Demo: Anteprima della password](https://aka.ms/cl_Tutorial_v3_DemoPassword_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPassword)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di reimpostazione della password sia in esecuzione.

    npm run demo-password

### <a name="open-the-demo"></a>Aprire la demo

Nell'elenco dei modelli dell'interfaccia utente Web fare clic su Tutorial Demo Password Reset. 

### <a name="actions"></a>Azioni

Il modello contiene un set di azioni progettate per consentire agli utenti di risolvere i problemi di password comuni.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Dialoghi di training

Il modello contiene anche diversi dialoghi di training fra cui alcuni che illustrano il training di classi fuori dominio. Ad esempio utenti che potrebbero richiedere indicazioni stradali. Il bot è stato sottoposto a un training limitato su questo a scopo dimostrativo e risponde semplicemente "can't help with that" (non posso aiutarti). L'elenco dei dialoghi di training esistenti è disponibile in "Train Dialogs" (Dialoghi di training) nel riquadro a sinistra.

![](../media/tutorial_pw_reset_entities.PNG)

1. Nel riquadro a sinistra fare clic su "Train Dialogs" (Dialoghi di training), quindi sul pulsante "New Train Dialog" (Nuovo dialogo di training).
2. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "i lost my password".
3. Fare clic su "Score Actions" (Punteggio azioni).
4. Selezionare la risposta "Is that for your local account or Microsoft account?" (È quella dell'account locale o dell'account Microsoft?)
5. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "local account please"
6. Fare clic su "Score Actions" (Punteggio azioni).
7. Selezionare la risposta "Which version of Windows do you have?" (Che versione di Windows hai?)
8. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "windows xp"
9. Fare clic su "Score Actions" (Punteggio azioni).
10. Fare clic sul pulsante "+Action" (+Azione).
11. Nel campo "Bot's Response..." (Risposta del bot...) digitare "SOLUTION: How to reset password on Windows 8".
12. Fare clic sul pulsante "Create" (Crea).

### <a name="training-dialogs-for-out-of-domain-scenarios"></a>Dialoghi di training per scenari fuori dominio

1. Nel riquadro a sinistra fare clic su "Train Dialogs" (Dialoghi di training) e quindi sul dialogo di training esistente "toy stores" (negozi di giocattoli).
2. Nel pannello della chat fare clic sull'espressione "toy stores" (negozi di giocattoli).
3. Nel campo "Add alternative input..." (Aggiungi input alternativo...) digitare "web search" e premere INVIO.
4. Nel campo "Add alternative input..." (Aggiungi input alternativo...) digitare "flight booking" e premere INVIO.
5. Fare clic sul pulsante "Save changes" (Salva modifiche).
6. Fare clic sul pulsante "Save Edit" (Salva modifiche).
7. Nel riquadro a sinistra fare clic su "Log Dialogs" (Dialoghi di log), quindi sul pulsante "New Log Dialog" (Nuovo dialogo di log).
8. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "i can't find my password"
9. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "Microsoft account"
10. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "thanks"
11. Fare clic sul pulsante "Done Testing" (Test completato).
12. Fare clic sul dialogo di log "i can't find my password" (non trovo la password) nella vista a griglia.
13. Nel pannello della chat fare clic sull'elemento con rendering non corretto "Solution: How to reset a Microsoft Account Password" response (Soluzione: Come reimpostare la password di un account Microsoft).
14. Fare clic sul pulsante "+Action" (+Azione).
15. Nel campo "Bot's response..." (Risposta del bot...) digitare "You are welcome"
16. Fare clic sul pulsante "Create" (Crea).
17. Fare clic sul pulsante "Save As Train Dialog" (Salva come dialogo di training).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Demo: Ordinazione pizza](./demo-pizza-order.md)
