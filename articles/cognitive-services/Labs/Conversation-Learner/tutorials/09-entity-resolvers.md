---
title: Resolver di entità in un modello di Conversation Learner - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare i resolver di entità in Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e6a671470a87f4509e466bcdfe7845b7bf7ec8dc
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55209150"
---
# <a name="entity-resolvers"></a>Resolver di entità

Questa esercitazione mostra come usare i resolver di entità in Conversation Learner.

## <a name="video"></a>Video

[![Anteprima dell'esercitazione sui resolver di entità](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione

    npm run tutorial-general

## <a name="details"></a>Dettagli

- Resolver Type è una proprietà facoltativa delle entità personalizzate.
- I resolver di entità usano le potenzialità dei sistemi di riconoscimento delle entità con training preliminare in LUIS per fornire dettagli e chiarezza aggiuntiva all'entità personalizzata.

## <a name="steps"></a>Passaggi

### <a name="create-a-new-model"></a>Creare un nuovo modello

1. Nell'interfaccia utente Web fare clic sul pulsante "New Model" (Nuovo modello).
2. Nel campo "Name" (Nome) digitare "Entity Resolvers", premere INVIO o fare clic sul pulsante "Create" (Crea).

### <a name="create-a-pair-of-entities"></a>Creare una coppia di entità

1. Nel riquadro a sinistra fare clic su "Entities" (Entità), quindi sul pulsante "New Entity" (Nuova entità).
2. Nel campo "Entity Name" (Nome entità), digitare "departure".
3. Nell'elenco a discesa "Resolver Type" (Tipo di resolver), selezionare "datetimeV2".
4. Fare clic sul pulsante "Create" (Crea).
5. Dopo aver letto il popup informativo, fare clic sul pulsante "OK".
6. Seguendo la stessa procedura, creare un'altra entità denominata "return" che presenta lo stesso tipo di resolver "datetimeV2".

### <a name="create-a-pair-of-actions"></a>Creare una coppia di azioni

1. Nel riquadro a sinistra fare clic su "Actions" (Azioni), quindi sul pulsante "New Action" (Nuova azione).
2. Nel campo "Bot's response" (Risposta del bot), digitare "You are leaving on $departure and returning on $return".
    - IMPORTANTE. Quando si digita in "$[entityName]", è necessario fare clic o premere INVIO sull'entità nell'elenco a discesa, in caso contrario Conversation Learner considera questo come testo anziché come entità.
    - Si noti che anche il campo "Required Entities" (Entità obbligatorie) otterrà queste entità, che non possono essere rimosse. Ciò impedisce all'azione di diventare disponibile fino a quando non saranno presenti entrambe le entità obbligatorie.
3. Fare clic sul pulsante "Create" (Crea).
4. Fare nuovamente clic sul pulsante "New Action" (Nuova azione) per creare una seconda azione.
5. Nel campo "Bot's response" (Risposta del bot) digitare "When are you planning to travel?".
6. Nel campo "Disqualifying Entities" (Entità non qualificanti) digitare "departure" e anche "return".
    - Questi elementi indicano al bot di NON eseguire questa operazione se una delle entità contiene un valore.
7. Fare clic sul pulsante "Create" (Crea).


### <a name="training"></a>Formazione

1. Osservare lo stato "Training: [Status]" nell'angolo in alto a sinistra della pagina e attenderne il completamento.
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
    - Passare il mouse sulle date e osservare che le entità sono oggetti data che consentono di acquisire in modo chiaro la data di calendario effettiva anziché "Sunday" o "tomorrow".
10. Selezionare la risposta del bot "You are leaving on..."
11. Fare clic sul pulsante "Save" (Salva).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Input alternativi](./10-alternative-inputs.md)
