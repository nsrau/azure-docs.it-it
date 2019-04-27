---
title: Come usare le entità multivalore con un modello di Conversation Learner - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare le entità multivalore con un modello di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 07550ed9c78aa584e55403d225eef4bcff33b057
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708116"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Come usare le entità multivalore con un modello di Conversation Learner
Questa esercitazione illustra la proprietà "multivalore" dell'entità.

## <a name="video"></a>Video

[![Anteprima Tutorial con più valori entità](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione.

    npm run tutorial-general

## <a name="details"></a>Dettagli
Le entità multivalore accumulano i valori in un elenco, anziché archiviare un valore singolo.  Queste entità sono utili quando gli utenti possono specificare più valori. Ad esempio i condimenti su una pizza.

Per le entità contrassegnate come multivalore ogni istanza riconosciuta dell'entità sarà accodata a un elenco nella memoria del bot. I riconoscimenti successivi vengono accodati al valore dell'entità anziché sovrascritti.

## <a name="steps"></a>Passaggi

Per iniziare, passare alla home page nell'interfaccia utente Web.

### <a name="create-the-model"></a>Creare il modello

1. Selezionare **nuovo modello**.
2. Immettere **MultiValueEntities** per **nome**.
3. Selezionare **Create**.

### <a name="entity-creation"></a>Creazione di entità

1. Selezionare **Entities** nel riquadro sinistro, quindi **nuova entità**.
2. Selezionare **Custom sottoposto a training** per **tipo di entità**.
3. Immettere **condimenti** per **nome entità**.
4. Controllare **multivalore** per abilitare l'entità si accumulano uno o più valori.
5. Controllare **negabile**.
6. Selezionare **Create**.

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>Creare la prima azione

1. Selezionare **azioni** nel riquadro sinistro, quindi **nuova azione**.
2. Immettere **di seguito sono i condimenti: $toppings** per **risposta del Bot...** . Il segno di dollaro iniziale indica un riferimento all'entità.
3. Selezionare **Create**.

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>Creare la seconda azione

1. Selezionare **azioni** nel riquadro sinistro, quindi **nuova azione**.
2. Immettere **quali condimenti preferisci?** per **risposta del Bot...** .
3. Immettere **condimenti** per **qualificato dà diritto**.
4. Selezionare **Create**.

Sono ora disponibili due azioni.

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>Eseguire il training del modello

1. Selezionare **Train Dialogs** nel riquadro sinistro, quindi **nuova finestra di dialogo Train**.
2. Immettere **Ciao** per utterance dell'utente nel Pannello di chat a sinistra.
3. Selezionare **assegnare un punteggio azioni**.
4. Selezionare **quali condimenti preferisci?** nell'elenco di azioni. Il percentile è 100% come l'unica azione valida in base ai vincoli.
5. Immettere **formaggio e funghi** per utterance dell'utente nel Pannello di chat a sinistra.
6. Evidenziare **formaggio** quindi selezionare **+ condimenti**.
7. Evidenziare **funghi** quindi selezionare **+ condimenti**.
8. Selezionare **assegnare un punteggio azioni**.
9. Selezionare **di seguito sono i condimenti: $toppings** nell'elenco di azioni.
10. Immettere **aggiungere pepper** per utterance successivo dell'utente nel Pannello di chat a sinistra.
11. Evidenziare **pepper** quindi selezionare **+ condimenti**.
12. Selezionare **assegnare un punteggio azioni**.
13. Selezionare **di seguito sono i condimenti: $toppings** nell'elenco di azioni.
14. Immettere **rimuovere formaggio** per utterance terza dell'utente nel Pannello di chat a sinistra.
15. Evidenziare **formaggio** quindi selezionare **-condimenti**.
16. Selezionare **assegnare un punteggio azioni**.
17. Selezionare **di seguito sono i condimenti: $toppings** nell'elenco di azioni.

![](../media/T07_training.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Entità con training preliminare](./08-pre-trained-entities.md)
