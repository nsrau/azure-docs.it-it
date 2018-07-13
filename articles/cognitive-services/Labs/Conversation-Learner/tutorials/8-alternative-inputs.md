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
ms.openlocfilehash: 2214436b193932e5b3b80c190f7754a0436b7ed8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376388"
---
# <a name="how-to-use-alternative-inputs"></a>Come usare input alternativi

Questa esercitazione illustra come usare il campo degli input alternativi per l'input utente nell'interfaccia di training.

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione.

    npm run tutorial-general

## <a name="details"></a>Dettagli
Gli "input alternativi" sono espressioni alternative che potrebbero essere usate dall'utente in un determinato punto di un dialogo di training. Gli input alternativi consentono di specificare in modo più compatto le varianti che potrebbero essere usate da un utente senza dover elencare ogni variante in un dialogo di training separato.

## <a name="steps"></a>Passaggi

### <a name="create-the-application"></a>Creazione dell'applicazione

1. Nell'interfaccia utente Web fare clic su New App (Nuova App).
2. In Name (Nome) immettere "AlternativeInputs". Fare quindi clic su Create (Crea).

### <a name="create-an-entity"></a>Creare un'entità

1. Fare clic su Entities (Entità) e quindi su New Entity (Nuova entità).
2. In Entity Name (Nome entità) immettere "city".
3. Fare clic su Crea.

### <a name="create-three-actions"></a>Creare tre azioni

1. Fare clic su Actions (Azioni) e quindi su New Action (Nuova azione).
2. In Response (Risposta) digitare "Which city do you want?".
3. In Disqualifying Entities (Entità non qualificanti) immettere "$city".
3. Click Create

Creare quindi la seconda azione:

1. Fare clic su Actions (Azioni) e quindi su New Action (Nuova azione).
3. In Response (Risposta) digitare "The weather in $city is probably sunny".
4. In Required Entities (Entità obbligatorie) immettere "$city".
4. Fare clic su Crea.

Creare la terza azione:

1. Fare clic su Actions (Azioni) e quindi su New Action (Nuova azione).
3. In Response (Risposta) digitare "Try asking for the weather".
    - Questa sarà la risposta a una domanda dell'utente come "what can the system do?".
4. In Disqualifying Entities (Entità non qualificanti) immettere "$city".
4. Click Create

Sono ora disponibili tre azioni.

### <a name="train-the-bot"></a>Eseguire il training del bot

1. Fare clic su Train Dialogs (Dialoghi di training) e quindi su New Train Dialog (Nuovo dialogo di training).
2. Digitare "what's the weather".
3. Fare clic su Score Actions (Punteggio azioni) e selezionare "Which city do you want?".
2. Immettere "denver".
3. Fare doppio clic su "denver" e selezionare "city".
    - La parola viene così contrassegnata come entità city.
5. Fare clic su Score Actions (Punteggio azioni).
    - Si noti che "denver" è ora presente nell'entità city. 
6. Selezionare "The weather in $city is probably sunny".
7. Fare clic su Done Teaching (Training completato).

Aggiungere un altro dialogo di esempio:

1. Fare clic su New Action (Nuova azione) e quindi su New Train Dialog (Nuovo dialogo di training).
2. Digitare "what can you do?".
3. Fare clic su Score Actions (Punteggio azioni) e selezionare "Try asking for the weather".
2. Immettere "What's the weather in seattle".
3. Fare doppio clic su "seattle" e selezionare "city".
    - La parola viene così contrassegnata come entità city.
5. Fare clic su Score Actions (Punteggio azioni).
    - Si noti che "seattle" è ora presente nell'entità city. 
6. Selezionare "The weather in $city is probably sunny".
7. Fare clic su Done Teaching (Training completato).

Si osserverà ora che cosa accade se l'utente usa un'espressione semanticamente simile a quanto sopra:

1. Fare clic su New Action (Nuova azione) e quindi su New Train Dialog (Nuovo dialogo di training).
2. Digitare "help".
3. Fare clic su Score Actions (Punteggio azioni).
    - Si noti che i punteggi per le due potenziali risposte sono molto simili. Questo indica che il modello non individua chiaramente il limite tra le due azioni.
6. Fare clic su Abandon Teaching (Abbandona training) e confermare.

![](../media/tutorial8_closescores.png)

In questo caso sarà utile aggiungere input alternativi ai dialoghi. È possibile aggiungerli mentre si esegue il training oppure tornare indietro e aggiungerli in seguito.

2. Fare clic su "'what can you do?" in Train Dialogs (Dialoghi di training).
2. Nel dialogo fare clic su "what can you do?" per selezionare tale frase.
    1. In Entity Detection (Rilevamento entità) nel riquadro destro immettere alcune alternative in Add alternative input (Aggiungi input alternativo):
    1. Immettere "what are my choices?".
    2. Immettere "Tell me my choices".
    3. Immettere "help".
    1. Fare clic su Submit Changes (Invia modifiche).


![](../media/tutorial8_helpalternates.png)

2. Fare ora clic su "What's the weather in seattle".
    1. In Add alternative input (Aggiungi input alternativo) immettere "forecast for seattle".
    2. Fare doppio clic su "seattle" e selezionare "city". Si noti che le entità per gli input alternativi devono essere presenti e includere lo stesso set di entità. Il contenuto delle entità può però essere diverso.
    3. In Add alternative input (Aggiungi input alternativo) immettere "will it rain today in denver".
    4. Fare clic su "denver" e selezionare "city".
    5. Fare clic su Submit Changes (Invia modifiche) e quindi su Done (Fine).


Si aggiungeranno ora input alternativi al primo dialogo:

1. Fare clic su Train Dialogs (Dialoghi di training).
2. Fare clic sul dialogo che inizia con "what's the weather".
2. Fare clic per selezionare "what's the weather" nel riquadro sinistro:
    1. In Add alternative input (Aggiungi input alternativo) immettere "weather forecast".
    2. Immettere "will it rain?".
    3. Fare clic su Submit Changes (Invia modifiche).
4. Fare clic per selezionare "denver" nel riquadro sinistro:
    1. In Add alternative input (Aggiungi input alternativo) immettere "for denver".
    2. Immettere "forecast for austin".
        - Si noti che l'intera frase viene evidenziata. Fare clic sulla frase e quindi sulla X rossa. Selezionare quindi "austin" e fare clic su "city".
        - Fare clic su Submit Changes (Invia modifiche).
    1. Fare clic su Done (Fine) in modo da ripetere il training del modello.

![](../media/tutorial8_altcities.png)

Si proveranno ora le varianti:

1. Fare clic su New Train Dialog (Nuovo dialogo di training).
2. Digitare "what are you capabilities".
3. Fare clic su Score Actions (Punteggio azioni).
    - Si noti che i punteggi sono ora più decisivi per l'azione successiva e questo indica la certezza del modello.
2. Selezionare "Try asking for weather".
6. Fare clic su Done Teaching (Training completato).

È stato illustrato come è possibile usare input alternativi per indicare altre possibili espressioni dell'utente. Si può così evitare di creare molti dialoghi in gran parte uguali comprimendoli in un singolo dialogo ed enumerando le possibili frasi dell'utente.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrare dialoghi](./9-log-dialogs.md)
