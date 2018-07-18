---
title: Come usare le azioni con attesa e senza attesa con un'applicazione di Conversation Learner - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare le azioni con attesa e senza attesa con un'applicazione di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: bb2cbd55b6c8be51213095926bb592169613d1fc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376156"
---
# <a name="wait-and-non-wait-actions"></a>Azioni con attesa e senza attesa

Questa esercitazione illustra la differenza tra azioni con attesa e senza attesa in Conversation Learner.

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione.

    npm run tutorial-general

## <a name="details"></a>Dettagli

- Azione con attesa: quando il sistema esegue un'azione "con attesa", interrompe l'esecuzione di azioni e attende un input utente.
- Azione senza attesa: quando il sistema esegue un'azione "senza attesa", sceglie immediatamente un'altra azione, senza attendere prima un input utente.

## <a name="steps"></a>Passaggi

### <a name="create-a-new-app"></a>Creare una nuova app

1. Nell'interfaccia utente Web fare clic su New App (Nuova App).
2. In Name (Nome) immettere "WaitNonWait". Fare quindi clic su Create (Crea).

### <a name="create-the-first-wait-action"></a>Creare la prima azione con attesa

1. Fare clic su Actions (Azioni) e quindi su New Action (Nuova azione).
2. In Response (Risposta) immettere "Which animal do you want?".
    - Dato che questa è un'azione con attesa, lasciare selezionata la casella Wait for Response (Attendi risposta).
3. Fare clic su Done (Fine).

### <a name="create-a-non-wait-action"></a>Creare un'azione senza attesa

1. Fare clic su New Action (Nuova azione).
2. In Response (Risposta) digitare "Cows say moo".
3. Deselezionare la casella di controllo Wait for Response (Attendi risposta).
4. Click Create

### <a name="create-a-second-non-wait-action"></a>Creare una seconda azione senza attesa

1. Fare clic su New Action (Nuova azione).
2. In Response (Risposta) digitare "Ducks say quack".
3. Deselezionare la casella di controllo Wait for Response (Attendi risposta).
4. Click Create

![](../media/tutorial2_actions.PNG)

### <a name="train-the-bot"></a>Eseguire il training del bot

1. Fare clic su Train Dialogs (Dialoghi di training) e quindi su New Train Dialog (Nuovo dialogo di training).
2. Digitare "hello".
3. Fare clic su Score Actions (Punteggio azioni) e selezionare "Which animal do you want?".
4. Immettere "cow".
5. Fare clic su Score Actions (Punteggio azioni) e selezionare "Cows say moo".
    - Si noti che il bot non attenderà input ed eseguirà l'azione successiva.
2. Selezionare "Which animal do you want?".
3. Immettere "duck".
5. Fare clic su Score Actions (Punteggio azioni) e selezionare "Ducks say quack".

![](../media/tutorial2_dialogs.PNG)

Si noti la sequenza delle risposte del bot in relazione alle azioni con attesa e senza attesa.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione alle entità](./3-introduction-to-entities.md)
