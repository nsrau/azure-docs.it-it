---
title: Resolver di entità in un modello di Conversation Learner-Servizi cognitivi di Azure | Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare i resolver di entità in Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 60c4abf1590cb91fd460cc6a2a5ba75a225ebd80
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704032"
---
# <a name="entity-resolvers"></a>Resolver di entità

Questa esercitazione mostra come usare i resolver di entità in Conversation Learner.

## <a name="video"></a>Video

[![Anteprima dell'esercitazione sui resolver di entità](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione.

    npm run tutorial-general

## <a name="details"></a>Dettagli

- Resolver Type è una proprietà facoltativa delle entità personalizzate.
- I resolver di entità usano le potenzialità dei sistemi di riconoscimento delle entità con training preliminare in LUIS per fornire dettagli e chiarezza aggiuntiva all'entità personalizzata.

## <a name="steps"></a>Passaggi

Per iniziare, passare alla home page nell'interfaccia utente Web.

### <a name="create-the-model"></a>Creare il modello

1. Selezionare **nuovo modello**.
2. Immettere **resolver di entità** per **nome**.
3. Selezionare **Create**.

### <a name="create-a-pair-of-entities"></a>Creare una coppia di entità

1. Selezionare **entità** nel pannello a sinistra e quindi **nuova entità**.
2. Immettere la **partenza** per il **nome dell'entità**.
3. Selezionare **datetimeV2** per **tipo di resolver**.
4. Selezionare **Create**. Chiudere il popup informativo selezionando **OK**.
5. Ripetere i passaggi 1-4 per creare una seconda entità denominata **return** con tipo di resolver **datetimeV2** .

![](../media/T09_entities.png)

### <a name="create-a-pair-of-actions"></a>Creare una coppia di azioni

1. Selezionare **Actions (azioni** ) nel pannello sinistro, quindi **nuova azione**.
2. Immettere **il $Departure e tornare al $Return** per **la risposta del bot...** .
    - IMPORTANTE: quando si digita $ [EntityName] è necessario premere INVIO o fare clic sull'entità nell'elenco a discesa in caso contrario Conversation Learner considererà il testo anziché un'entità.
    - Si noti che il campo **entità obbligatorie** otterrà anche queste entità e non è possibile rimuoverle. Ciò impedisce all'azione di diventare disponibile fino a quando non saranno presenti entrambe le entità obbligatorie.
3. Selezionare **Create**.
4. Selezionare **nuova azione** per creare una seconda azione.
5. Immettere **quando si prevede di viaggiare?** per **la risposta del bot...** .
6. Immettere la **partenza** e **restituire** per le entità che non sono **idonee**. Questi elementi indicano al bot di NON eseguire questa operazione se una delle entità contiene un valore.
7. Selezionare **Create**.

![](../media/T09_actions.png)

### <a name="training"></a>Formazione

1. Guardare il **Training: [status]** nell'angolo superiore sinistro per **Completed**.
    - Fare clic sul collegamento "Refresh" (Aggiorna) se l'operazione richiede troppo tempo.
    - È necessario attendere che lo stato del training sia "Completed" (Completato) affinché i resolver delle entità funzionino quando si esegue il training del modello.

2. Nel riquadro a sinistra fare clic su "Train Dialogs" (Dialoghi di training), quindi sul pulsante "New Train Dialog" (Nuovo dialogo di training).
3. Nella prima espressione utente digitare "book me a flight". 
4. Fare clic su "Score Actions" (Punteggio azioni).
5. Selezionare la risposta "When are you planning to travel?".
6. Come utente, rispondere "leaving tomorrow and returning Sunday next week".
    - Si noti che Conversation Learner ha rilevato due entità "Pre-Training data" (Dati con training preliminare) nel turno dell'utente.
7. Nel riquadro "Entity Detection" (Rilevamento entità) selezionare il testo "tomorrow" ed etichettarlo come "departure".
8. Etichettare quindi il testo "Sunday next week" come "return".
9. Fare clic su "Score Actions" (Punteggio azioni).
    - Si noti come il riquadro "Memory" (Memoria) contiene le date corrispondenti alla partenza e al ritorno.
    - Passare il mouse su ciascuno di essi e osservare il modo in cui le entità sono oggetti data che acquisiscono chiaramente la data effettiva del calendario anziché "Sunday" o "Tomorrow".
10. Selezionare la risposta del bot "You are leaving on..."
11. Fare clic sul pulsante "Save" (Salva).

![](../media/T09_training.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Entità enum](./tutorial-enum-set-entity.md)
