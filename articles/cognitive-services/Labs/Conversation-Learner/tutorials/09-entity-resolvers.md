---
title: Sistemi di risoluzione entità in un modello di apprendimento di conversazioni - servizi cognitivi di Azure | Microsoft Docs
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
ms.openlocfilehash: 51f74f504f0ad70c8c7f73be8ee6a05add685824
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475747"
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
2. Immettere **entità resolver** per **nome**.
3. Selezionare **Create**.

### <a name="create-a-pair-of-entities"></a>Creare una coppia di entità

1. Selezionare **Entities** nel riquadro sinistro, quindi **nuova entità**.
2. Immettere **partenze** per **nome entità**.
3. Selezionare **datetimeV2** per **tipo di Resolver**.
4. Selezionare **Create**. Chiudere il popup informativo selezionando **OK**.
5. Ripetere i passaggi da 1 a 4 per creare una seconda entità denominate **restituire** con **datetimeV2** tipo di resolver.

![](../media/T09_entities.png)

### <a name="create-a-pair-of-actions"></a>Creare una coppia di azioni

1. Selezionare **azioni** nel riquadro sinistro, quindi **nuova azione**.
2. Immettere **lasciando nei $departure e restituzione per $return** per **risposta del Bot...** .
    - IMPORTANTE - quando si digita $ [entityName] è necessario sottoporre a hit immettere o fare clic sull'entità nell'elenco a discesa dello strumento di apprendimento di conversazioni in caso contrario, prenderà in considerazione questo sia il testo anziché come un'entità.
    - Si noti che il **le entità necessarie** campo potranno inoltre ottenere tali entità e non possono essere rimossi. Ciò impedisce all'azione di diventare disponibile fino a quando non saranno presenti entrambe le entità obbligatorie.
3. Selezionare **Create**.
4. Selezionare **nuova azione** per creare una seconda azione.
5. Immettere **quando si prevede di viaggio?** per **risposta del Bot...** .
6. Immettere **partenze** e **restituire** per **entità qualificato**. Questi elementi indicano al bot di NON eseguire questa operazione se una delle entità contiene un valore.
7. Selezionare **Create**.

![](../media/T09_actions.png)

### <a name="training"></a>Formazione

1. Guarda il **formazione: [Status]** nell'angolo superiore sinistro per **Completed**.
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
    - Passare il mouse su ciascuno di essi e osservare come le entità sono oggetti di data che consentono di acquisire in modo chiaro la data di calendario effettivo anziché "Sunday" o "tomorrow".
10. Selezionare la risposta del bot "You are leaving on..."
11. Fare clic sul pulsante "Save" (Salva).

![](../media/T09_training.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Entità di enum](./tutorial-enum-set-entity.md)
