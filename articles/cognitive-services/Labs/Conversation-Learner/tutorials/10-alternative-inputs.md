---
title: Come usare input alternativi con Conversation Learner - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare input alternativi con Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: b6ea1ee4eb8b55d2da4069ef19a268ec68f49cb4
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796562"
---
# <a name="how-to-use-alternative-inputs"></a>Come usare input alternativi

Questa esercitazione illustra come usare il campo degli input alternativi per le espressioni dell'utente nell'interfaccia di training.

## <a name="video"></a>Video

[![Anteprima dell'esercitazione degli input alternativi](https://aka.ms/cl_Tutorial_v3_AlternativeInputs_Preview)](https://aka.ms/cl_Tutorial_v3_AlternativeInputs)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione

    npm run tutorial-general

## <a name="details"></a>Dettagli
Gli input alternativi sono espressioni alternative, semanticamente equivalenti, che potrebbero essere usate dall'utente in un determinato punto di un dialogo di training. Questi input alternativi consentono di specificare in modo più compatto le varianti delle espressioni senza dover elencare ogni variante nei dialoghi di training separati.

## <a name="steps"></a>Passaggi

### <a name="create-the-model"></a>Creare il modello

1. Nell'interfaccia utente Web fare clic su "New Model" (Nuovo modello).
2. Nel campo "Name" (Nome) digitare "AlternativeInputs" e premere INVIO.
3. Fare clic sul pulsante "Create" (Crea).

### <a name="entity-creation"></a>Creazione di entità

1. Nel riquadro a sinistra fare clic su "Entities" (Entità), quindi sul pulsante "New Entity" (Nuova entità).
2. Selezionare "Custom Trained" (Con training personalizzato) per "Entity Type" (Tipo di entità).
3. Digitare "city" per "Entity Name" (Nome entità).
4. Fare clic sul pulsante "Create" (Crea).

A questo punto è possibile creare tre azioni.

### <a name="create-the-first-action"></a>Creare la prima azione

1. Nel riquadro a sinistra fare clic su "Actions" (Azioni), quindi sul pulsante "New Action" (Nuova azione).
2. NeI campo "Bot's response..." (Risposta del bot...) digitare "Which city?".
3. Nel campo "Expected Entity in User reply..." (Entità prevista nella risposta utente...) digitare "city".
4. Nel campo "Disqualifying Entities" (Entità non qualificanti) digitare "city".
5. Fare clic sul pulsante "Create" (Crea).

### <a name="create-the-second-action"></a>Creare la seconda azione

1. Nel riquadro a sinistra fare clic su "Actions" (Azioni), quindi sul pulsante "New Action" (Nuova azione).
2. Nel campo "Bot's response..." (Risposta del bot...) digitare "The weather in the $city is probably sunny".
3. Fare clic sul pulsante "Create" (Crea).

### <a name="create-the-third-action"></a>Creare la terza azione

1. Nel riquadro a sinistra fare clic su "Actions" (Azioni), quindi sul pulsante "New Action" (Nuova azione).
2. NeI campo "Bot's response..." (Risposta del bot...) digitare "Try asking for the weather".
3. Nel campo "Disqualifying Entities" (Entità non qualificanti) digitare "city".
4. Fare clic sul pulsante "Create" (Crea).

Sono ora disponibili tre azioni.

### <a name="train-the-model"></a>Eseguire il training del modello

1. Nel riquadro a sinistra fare clic su "Train Dialogs" (Dialoghi di training), quindi sul pulsante "New Train Dialog" (Nuovo dialogo di training).
2. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "What's the weather?".
3. Fare clic su "Score Actions" (Punteggio azioni).
4. Selezionare la risposta "Which city?".
5. Nel pannello della chat, dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "Denver".
6. Fare clic su "Score Actions" (Punteggio azioni).
7. Selezionare la risposta "The weather in Denver is probably sunny".
8. Fare clic sul pulsante "Save" (Salva).

Ora si eseguirà altro training mediante la creazione di un altro dialogo di training.

### <a name="second-model-train-dialog"></a>Secondo dialogo di training del modello

1. Nel riquadro a sinistra fare clic su "Train Dialogs" (Dialoghi di training), quindi sul pulsante "New Train Dialog" (Nuovo dialogo di training).
2. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "What can you do?".
3. Fare clic su "Score Actions" (Punteggio azioni).
4. Selezionare la risposta "Try asking for weather".
5. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "What's the weather in Seattle?".
6. Fare clic su "Seattle", quindi su "city" nell'elenco di entità.
7. Fare clic su "Score Actions" (Punteggio azioni).
8. Selezionare la risposta "The weather in Seattle is probably sunny".
9. Fare clic sul pulsante "Save" (Salva).

### <a name="third-model-train-dialog-using-alternative-input"></a>Terzo dialogo di training del modello usando un input alternativo

1. Nel riquadro a sinistra fare clic su "Train Dialogs" (Dialoghi di training), quindi sul pulsante "New Train Dialog" (Nuovo dialogo di training).
2. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "help".
3. Fare clic su "Score Actions" (Punteggio azioni).
    - Il modello è incerto rispetto all'opzione migliore e quindi verrebbe usato il percentile più alto per impostazione predefinita.
4. Fare clic sul pulsante "Abandon Teaching" (Abbandona training), quindi sul pulsante "Confirm" (Conferma).

![](../media/tutorial8_closescores.png)

È possibile ottimizzare il sistema usando gli input alternativi. È possibile aggiungere input alternativi durante il training o successivamente.

5. Nel riquadro a sinistra fare clic su "Train Dialogs" (Dialoghi di training) e quindi selezionare "What can you do?" nell'elenco di dialoghi di training.
6. Fare clic sull'espressione "What can you do?" nel pannello della chat.
7. Nel campo "Add alternative input..." (Aggiungi input alternativo...) digitare "help" e premere INVIO.
8. Fare clic sul pulsante "Save changes" (Salva modifiche).

![](../media/tutorial8_helpalternates.png)

È possibile aggiungere un altro input alternativo per gestire Houston.

9. Fare clic sull'espressione "What's the weather in Seattle?" nel pannello della chat.
10. Nel campo "Add alternative input..." (Aggiungi input alternativo...) digitare "forecast for Houston" e premere INVIO.
    - Il messaggio di errore evidenzia che gli input alternativi devono essere equivalenti semanticamente e contenere le stesse entità dell'espressione originale; non semplicemente gli stessi valori di entità. È necessario che siano presenti le stesse entità.
11. Fare clic su "Houston" e selezionare "city" nell'elenco di entità.
12. Nel campo "Add alternative input..." (Aggiungi input alternativo...) digitare "forecast for Seattle" e premere INVIO.
13. Fare clic su "Seattle" e selezionare "city" nell'elenco di entità.
14. Fare clic sul pulsante "Save changes" (Salva modifiche).
15. Fare clic sul pulsante "Save Edit" (Salva modifiche).

### <a name="testing-the-model"></a>Test del modello

1. Nel riquadro a sinistra fare clic su "Log Dialogs" (Dialoghi di log), quindi su "New Log Dialog" (Nuovo dialogo di log).
2. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "help me".
3. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "forecast for Denver".

![](../media/tutorial8_altcities.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrare dialoghi](./11-log-dialogs.md)
