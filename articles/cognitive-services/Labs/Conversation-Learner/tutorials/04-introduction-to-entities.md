---
title: Come usare le entità con un modello di Conversation Learner - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare le entità con un modello di Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: cba12b6c09c1bdbf4e8f7841676a609c34109d93
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707345"
---
# <a name="introduction-to-entities"></a>Introduzione alle entità

Questa esercitazione descrive le entità, le entità non qualificanti, le entità obbligatorie e il loro utilizzo in Conversation Learner.

## <a name="video"></a>Video

[![ Anteprima dell'esercitazione sull'introduzione alle entità](https://aka.ms/cl_Tutorial_v3_IntroEntities_Preview)](https://aka.ms/cl_Tutorial_v3_IntroEntities)

## <a name="requirements"></a>Requisiti

Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione.

    npm run tutorial-general

## <a name="details"></a>Dettagli

Le entità acquisiscono parti di informazioni necessarie al bot per eseguire le proprie attività, estraendole da espressioni utente o tramite assegnazione con codice personalizzato. Possono anche limitare la disponibilità dell'azione se vengono esplicitamente classificate come "Obbligatorie" o "Non qualificanti".

- Le entità obbligatorie devono essere presenti nella memoria del modello affinché l'azione sia disponibile
- Le entità non qualificanti *non* devono essere presenti nella memoria del modello affinché l'azione sia disponibile

L'esercitazione si incentra sulle entità personalizzate. Le entità con training preliminare, multivalore, negabili e a livello di codice vengono descritte in altre esercitazioni.

## <a name="steps"></a>Passaggi

### <a name="create-the-model"></a>Creare il modello

1. Nell'interfaccia utente Web fare clic su "New Model" (Nuovo modello).
2. Nel campo "Name" (Nome) digitare "IntroToEntities" e premere INVIO.
3. Fare clic sul pulsante "Create" (Crea).

### <a name="entity-creation"></a>Creazione di entità

1. Nel riquadro a sinistra fare clic su "Entities" (Entità), quindi sul pulsante "New Entity" (Nuova entità).
2. Selezionare "Custom Trained" (Con training personalizzato) per "Entity Type" (Tipo di entità).
3. Digitare "city" per "Entity Name" (Nome entità).
4. Fare clic sul pulsante "Create" (Crea).

> [!NOTE]
> Il tipo di entità con training personalizzato indica che questa entità può essere sottoposta a training, diversamente da altri tipi di entità.

### <a name="create-the-actions"></a>Creare le azioni

1. Nel riquadro a sinistra fare clic su "Actions" (Azioni), quindi sul pulsante "New Action" (Nuova azione).
2. Nel campo "Bot's response..." (Risposta del bot), digitare "I don't know what city you want".
3. Nel campo "Disqualifying Entities" (Entità non qualificanti) digitare "city".
4. Fare clic sul pulsante "Create" (Crea).

> [!NOTE]
> L'aggiunta dell'entità "city" alle "Entità non qualificanti" impedisce a questa azione di essere presa in considerazione dal bot quando l'entità "city" è definita nella memoria del bot.

Creare ora una seconda azione.

1. Nel riquadro a sinistra fare clic su "Actions" (Azioni), quindi sul pulsante "New Action" (Nuova azione).
2. Nel campo "Bot's Response..." (Risposta del bot...) digitare "The weather in the $city is probably sunny".
3. Fare clic sul pulsante "Create" (Crea).

> [!NOTE]
> L'entità "city" è stata aggiunta automaticamente all'elenco delle entità obbligatorie dal riferimento nella risposta.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-model"></a>Eseguire il training del modello

1. Nel riquadro a sinistra fare clic su "Train Dialogs" (Dialoghi di training), quindi sul pulsante "New Train Dialog" (Nuovo dialogo di training).
2. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "hello".
    - In questo modo si simula il lato dell'utente della conversazione.
3. Fare clic su "Score Actions" (Punteggio azioni).
4. Selezionare la risposta "I don't know what city you want".
5. Come utente, rispondere "Seattle".
6. Fare clic su "Score Actions" (Punteggio azioni).
7. Selezionare la risposta "The weather in $city is probably sunny".
8. Fare clic sul pulsante "Save" (Salva).

![](../media/tutorial3_entities.PNG)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Entità prevista](./05-expected-entity.md)
