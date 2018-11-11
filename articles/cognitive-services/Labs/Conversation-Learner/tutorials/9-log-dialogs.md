---
title: Come registrare i dialoghi in un modello di Conversation Learner - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Informazioni su come registrare i dialoghi in un modello di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 5369e16e0f1adc48eb019f3790dc900577c144af
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243660"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Informazioni su come registrare i dialoghi in un modello di Conversation Learner

Questa esercitazione illustra come eseguire test per l'utente finale nell'interfaccia di Conversation Learner, come vengono registrati i dialoghi e come apportare correzioni ai dialoghi registrati per migliorare il modello.

## <a name="video"></a>Video

[![Anteprima esercitazione 9](https://aka.ms/cl-tutorial-09-preview)](https://aka.ms/blis-tutorial-09)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione

    npm run tutorial-general

## <a name="details"></a>Dettagli
È possibile usare i dialoghi di log per esaminare e correggere i dialoghi condotti con gli utenti finali.  In particolare, si possono correggere le etichette delle entità e le selezioni delle azioni in modo da migliorare le prestazioni del modello sottoposto a training e del sistema complessivo. 

## <a name="steps"></a>Passaggi

### <a name="create-the-model"></a>Creare il modello

1. Nell'interfaccia utente Web fare clic su New Model (Nuovo modello)
2. In Name (Nome) immettere "LogDialogs". Fare quindi clic su Create (Crea).

### <a name="create-an-entity"></a>Creare un'entità

1. Fare clic su Entities (Entità) e quindi su New Entity (Nuova entità).
2. In Entity Name (Nome entità) immettere "city".
3. Fare clic su Crea.

### <a name="create-two-actions"></a>Creare due azioni

1. Fare clic su Actions (Azioni) e quindi su New Action (Nuova azione).
2. In Response (Risposta) digitare "Which city?".
3. In Disqualifying Entities (Entità non qualificanti) immettere "$city".
3. Fare clic su Crea

Creare quindi la seconda azione:

1. Fare clic su Actions (Azioni) e quindi su New Action (Nuova azione).
3. In Response (Risposta) digitare "The weather in $city is probably sunny".
4. In Required Entities (Entità obbligatorie) immettere "$city".
4. Fare clic su Crea.

Sono ora disponibili due azioni.

### <a name="train-the-bot"></a>Eseguire il training del bot

1. Fare clic su Train Dialogs (Dialoghi di training) e quindi su New Train Dialog (Nuovo dialogo di training).
2. Digitare "what's the weather".
3. Fare clic su Score Actions (Punteggio azioni) e selezionare "Which city?".
2. Immettere "Seattle".
3. Fare doppio clic su "Seattle" e selezionare "city".
    - La parola viene così contrassegnata come entità city.
5. Fare clic su Score Actions (Punteggio azioni).
6. Selezionare "The weather in $city is probably sunny".
7. Fare clic su Done Teaching (Training completato).

Aggiungere un altro dialogo di esempio:

1. Fare clic su Train Dialogs (Dialoghi di training) e quindi su New Train Dialog (Nuovo dialogo di training).
2. Digitare "what's the weather in Seattle?". Si noti che la parola "Seattle" viene contrassegnata come entità.
5. Fare clic su Score Actions (Punteggio azioni). 
6. Selezionare "The weather in $city is probably sunny".
7. Fare clic su Done Teaching (Training completato).

### <a name="try-the-bot-as-the-user"></a>Provare il bot come utente
Si immagini che questo bot sia stato distribuito agli utenti.

1. Fare clic su Log Dialogs (Dialoghi di log).
2. Fare clic su New Log Dialog (Nuovo dialogo di log).
    - Il bot viene così presentato come disponibile nell'esperienza utente, nel controllo chat nella parte sinistra dell'interfaccia utente. Si può ignorare l'area con lo spazio vuoto a destra.
3. Digitare "hello".
4. La risposta del bot è "which city?".
4. Digitare "Boston".
5. La risposta del bot è "which city?".
    - Qualcosa non funziona. Salvare quindi questo dialogo.
2. Fare clic su Done Testing (Test completato).

Avviare una nuova sessione:

2. Fare clic su New Log Dialog (Nuovo dialogo di log).
3. Digitare "forecast for Boston".
4. La risposta del bot è "which city?".
2. Fare clic su Done Testing (Test completato).

Apportare ora correzioni al secondo dialogo:

1. Fare clic su "forecast for Boston" in Log Dialogs (Dialoghi di log).
    - Verrà così aperta la conversazione.
    - Facendo clic sul lato dell'utente della conversazione (in questo caso, su "forecast for Boston"), è possibile modificare le etichette delle entità.
    - Facendo clic sul lato del sistema (in questo caso, su "which city"), è possibile modificare l'azione selezionata.
5. Fare clic su "forecast for Boston". 
    - La causa radice in questo caso risiede nel fatto che la parola "Boston" non è stata contrassegnata come entità. È necessario modificare questo aspetto.
    - Fare doppio clic su "Boston" e quindi selezionare "city".
    - Fare clic su Submit Changes (Invia modifiche) e quindi su Save (Salva). Verrà così creato un dialogo di training in base alle modifiche apportate, passando ai dialoghi di training nel punto della modifica.
6. Selezionare "The weather in $city is probably sunny".
7. Fare clic su Done Teaching (Training completato). Se si passa ora a Train Dialogs (Dialoghi di training), è possibile osservare che è stata aggiunta la nuova azione.

![](../media/tutorial9_logdiag1.PNG)

Apportare ora correzioni all'altro dialogo:

1. Fare clic su "hello" in Log Dialogs (Dialoghi di log).
    - Verrà così aperta la conversazione.
3. La risposta a "hello" è "which city". Si vuole tuttavia modificare tale risposta in una più appropriata. Una risposta migliore sarebbe, ad esempio, "hello, I'm the weather bot". Dato che non è disponibile alcuna azione a tale scopo, è necessario crearne una.
4. Fare clic su Action (Azione).
    - In Response (Risposta) digitare "I'm the weather bot. I can help with forecasts".
6. Deselezionare la casella di controllo Wait for Response (Attendi risposta) per impostare l'azione come senza attesa.
7. Fare clic su Crea.
8. Fare quindi clic per selezionare questa nuova azione. Fare quindi clic su Salva.
    - Si tornerà così al punto corrispondente nella sessione di training.
6. Fare clic per selezionare "which city?".
7. Digitare "Boston". Fare doppio clic in modo che la parola "Boston" venga contrassegnata come entità, se non lo è già.
8. Fare clic su Score Actions (Punteggio azioni).
9. Fare clic per selezionare "The weather in $city is probably sunny".
10. Fare clic su Done Teaching (Training completato).

![](../media/tutorial9_addnewaction.PNG)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Callback di rilevamento entità](./10-entity-detection-callback.md)
