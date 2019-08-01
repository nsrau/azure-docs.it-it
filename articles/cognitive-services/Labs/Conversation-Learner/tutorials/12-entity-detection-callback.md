---
title: Come usare il callback di rilevamento entità con un modello di Conversation Learner - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare il callback di rilevamento entità con un modello di Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 3f6094da7b103f1c99e9bb3fa9313354fcb2a8bc
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703980"
---
# <a name="how-to-use-entity-detection-callback"></a>Come usare il callback di rilevamento entità

Questa esercitazione illustra il callback di rilevamento entità e un modello comune per la risoluzione delle entità.

## <a name="video"></a>Video

[![Esercitazione dell'anteprima sui callback di rilevamento entità](https://aka.ms/cl_Tutorial_v3_EntityDetection_Preview)](https://aka.ms/cl_Tutorial_v3_EntityDetection)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot `tutorialEntityDetectionCallback` sia in esecuzione.

    npm run tutorial-entity-detection

## <a name="details"></a>Dettagli
I callback di rilevamento entità consentono di modificare il comportamento di rilevamento e di manipolazione di entità tramite il codice. La funzionalità viene illustrata esaminando un tipico modello di progettazione di callback per il rilevamento di entità. Un esempio è la risoluzione di "the big apple" in "new york".

## <a name="steps"></a>Passaggi

### <a name="create-the-model"></a>Creare il modello

1. Nell'interfaccia utente Web fare clic su "New Model" (Nuovo modello).
2. Nel campo "Name" (Nome) digitare "EntityDetectionCallback" e premere INVIO.
3. Fare clic sul pulsante "Create" (Crea).

In questo esempio sono necessarie tre entità che è possibile creare ora.

### <a name="create-the-custom-trained-entity"></a>Creare l'entità con training personalizzato

1. Nel riquadro a sinistra fare clic su "Entities" (Entità), quindi sul pulsante "New Entity" (Nuova entità).
2. Selezionare "Custom Trained" (Con training personalizzato) per "Entity Type" (Tipo di entità).
3. Digitare "city" per "Entity Name" (Nome entità).
4. Fare clic sul pulsante "Create" (Crea).

### <a name="create-the-first-programmatic-entity"></a>Creare la prima entità programmatica

1. Fare clic sul pulsante "New Entity" (Nuova entità).
2. Selezionare "Programmatic" per "Entity Type" (Tipo di entità).
3. Digitare "CityUnknown" per "Entity Name" (Nome entità).
4. Fare clic sul pulsante "Create" (Crea).

### <a name="create-the-first-programmatic-entity"></a>Creare la prima entità programmatica

1. Fare clic sul pulsante "New Entity" (Nuova entità).
2. Selezionare "Programmatic" per "Entity Type" (Tipo di entità).
3. Digitare "CityResolved" per "Entity Name" (Nome entità).
4. Fare clic sul pulsante "Create" (Crea).

A questo punto creare tre azioni.

### <a name="action-creation"></a>Creazione dell'azione

1. Nel riquadro a sinistra fare clic su "Actions" (Azioni), quindi sul pulsante "New Action" (Nuova azione).
2. NeI campo "Bot's response..." (Risposta del bot...) digitare "Which city do you want?".
3. Nel campo "Expected Entity in User reply..." (Entità prevista nella risposta utente...) digitare "City".
4. Nel campo "Disqualifying Entities" (Entità non qualificanti) digitare "City".
5. Fare clic sul pulsante "Create" (Crea).
6. Fare clic sul pulsante "New Action" (Nuova azione).
7. NeI campo "Bot's response..." (Risposta del bot...) digitare "Which city do you want?".
8. Nel campo "Expected Entity in User reply..." (Entità prevista nella risposta utente...) digitare "I don't know this city".
9. Fare clic sul pulsante "Create" (Crea).
10. Fare clic sul pulsante "New Action" (Nuova azione).
11. NeI campo "Bot's response..." (Risposta del bot...) digitare "$CityResolved is very nice".
12. Fare clic sul pulsante "Create" (Crea).

Ecco il codice di callback:

![](../media/tutorial10_callbackcode.PNG)

### <a name="train-the-model"></a>Eseguire il training del modello

1. Nel riquadro a sinistra fare clic su "Train Dialogs" (Dialoghi di training), quindi sul pulsante "New Train Dialog" (Nuovo dialogo di training).
2. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "hi".
3. Fare clic su "Score Actions" (Punteggio azioni).
4. Selezionare la risposta "Which city do you want?".
5. Nel pannello della chat, dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "big apple".
6. Fare clic su "Score Actions" (Punteggio azioni).
    - Facendo clic sul pulsante si attiva il callback di rilevamento entità.
    - Il codice di callback imposta il valore dell'Entità CityResolved correttamente su "new york".
7. Selezionare la risposta "new york is very nice" (New York è molto bella).

Questo criterio è tipico di molti scenari che usano bot. Le espressioni degli utenti e le entità estratte vengono fornite alla logica di business, che trasforma l'espressione in una forma canonica che viene quindi salvata in entità programmatiche per i successivi turni del dialogo.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Callback di sessione](./13-session-callbacks.md)
