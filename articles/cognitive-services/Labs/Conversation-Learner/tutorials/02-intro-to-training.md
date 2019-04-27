---
title: Introduzione al training di un modello di Conversation Learner - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Informazioni su come eseguire il training di un modello, incluse la creazione come ramo di un nuovo training da un training precedente o la modifica di un training precedente con Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 1e6dea521207ec00efe948a879c705bd28a75d57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707632"
---
# <a name="introduction-to-training"></a>Introduzione al training

Questa esercitazione illustra le nozioni di base del training di un modello, la creazione di un nuovo training in base a un training precedente e la modifica di una risposta del bot per modificare il training precedente.

## <a name="video"></a>Video

[![Introduzione all'anteprima dell'esercitazione sul training](https://aka.ms/cl_Tutorial_v3_IntroTraining_Preview)](https://aka.ms/cl_Tutorial_v3_IntroTraining)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione

    npm run tutorial-general

## <a name="details"></a>Dettagli

- Azioni: Una risposta del bot all'input dell'utente.
- Training: il modo in cui si prepara un bot a rispondere agli input dell'utente.
- Creazione di un ramo: la modifica dell'input di un utente all'interno di un dialogo di training salvato per creare un nuovo dialogo di training che inizia come quello originale, ma conduce la conversazione in una direzione diversa.

## <a name="steps"></a>Passaggi

### <a name="create-a-new-model"></a>Creare un nuovo modello

1. Nell'interfaccia utente Web fare clic su New Model (Nuovo modello)
2. Per "Name" (Nome) digitare "Inspire Bot". Fare quindi clic su Crea.

### <a name="create-an-action"></a>Creare un'azione

1. Nel riquadro a sinistra fare clic su "Actions" (Azioni), quindi sul pulsante "New Action" (Nuova azione).
2. Nel campo "Bot's response" (Risposta del bot...) digitare "Hi!". Would you like to be inspired today?".
    - Lasciare l'impostazione predefinita in tutti gli altri campi e nelle caselle di controllo.
3. Fare clic su Crea.

### <a name="first-training-and-creating-another-action-while-training"></a>Primo training e creazione di un'altra azione durante il training

1. Nel riquadro a sinistra fare clic su "Train Dialogs" (Dialoghi di training), quindi sul pulsante "New Train Dialog" (Nuovo dialogo di training).
2. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "hello". 
    - In questo modo si simula il lato dell'utente della conversazione.
3. Fare clic su "Score Actions" (Punteggio azioni).
4. Selezionare la risposta "Hi! Would you like to be inspired today?".
5. Come utente, rispondere "yes".
6. Fare clic su "Score Actions" (Punteggio azioni).
7. Fare clic sul pulsante "+ Action" (+ Azione). 
    - Verrà visualizzata la tipica finestra di dialogo "Create an Action" (Crea un'azione).
8. Digitare la risposta del bot come "You're awesome!".
9. Fare clic su Crea.
10. Si noti che il bot risponde immediatamente.
11. Fare clic sul pulsante "Save" (Salva).

### <a name="branch-a-second-training-off-of-the-first-training"></a>Creare come ramo un secondo training dal primo training
1. Fare clic sulla riga della griglia che riepiloga il primo training. 
    - In questo modo è possibile visualizzare e modificare il training esistente.
2. Fare clic sulla risposta dell'utente "yes". 
    - Verranno presentati i controlli per la modifica.
3. Fare clic sull'icona del ramo. 
    - Verrà visualizzata la richiesta di un input utente differente per una nuova conversazione.
4. Digitare "no", premere INVIO o fare clic sul pulsante "Create" (Crea). 
    - A questo punto sarà disponibile una nuova istanza di un dialogo di training, lasciando invariato il dialogo originale.
5. Fare clic su "Score Actions" (Punteggio azioni).
6. Fare clic sulla risposta non corretta del bot appena visualizzata.
7. Fare clic sul pulsante "+ Action" (+ Azione) 
    - in modo che sia possibile creare una nuova azione che verrà usata dal bot come risposta.
8. Digitare la risposta del bot come "No problem!" Have a great day!".
9. Fare clic su Crea
10. Si noti che il bot risponde immediatamente.
11. Fare clic sul pulsante "Save" (Salva).

### <a name="test-the-trainings"></a>Testare i training
1. Nel riquadro a sinistra fare clic su "Log Dialogs" (Dialoghi di log), quindi su "New Log Dialog" (Nuovo dialogo di log).
2. Digitare il messaggio "hi". 
3. Si noti che il bot risponde automaticamente in base al training eseguito.
4. Digitare la risposta dell'utente "yes".
5. Si noti la risposta del bot che indica che il primo training sta funzionando.
6. Fare clic sul pulsante "Session Timeout" (Timeout sessione). In questo modo si indica a Conversation Learner di ricominciare, ignorando i turni di conversazione appena completati.
7. Digitare il messaggio "hi". 
8. Si noti che il bot risponde automaticamente in base al training eseguito.
9. Digitare la risposta dell'utente "no".
10. Si noti la risposta del bot che indica che il secondo training sta funzionando.
11. Fare clic sul pulsante "Done Testing" (Test completato).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Azioni con attesa e senza attesa](./03-wait-vs-nonwait-actions.md)
