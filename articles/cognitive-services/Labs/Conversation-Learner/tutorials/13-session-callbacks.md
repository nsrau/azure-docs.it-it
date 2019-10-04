---
title: Come usare i callback di sessione con un modello di Conversation Learner - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare i callback di sessione con un modello di Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 656d028082d56b8f094e83363e5189b163581c53
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703958"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Come usare i callback di sessione con un modello di Conversation Learner

Questa esercitazione illustra le sessioni e come vengono gestiti i callback di sessione onSessionStart e onSessionEnd di Conversation Learner.

## <a name="video"></a>Video

[![Anteprima dell'esercitazione sui callback di sessione](https://aka.ms/cl_Tutorial_v3_SessionCallbacks_Preview)](https://aka.ms/cl_Tutorial_v3_SessionCallbacks)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot "tutorialSessionCallbacks" sia in esecuzione.

    npm run tutorial-session-callbacks

## <a name="details"></a>Dettagli
Questa esercitazione illustra il concetto di sessione, il modo in cui le sessioni vengono gestite per impostazione predefinita e come è possibile eseguire l'override di tale comportamento.

In Conversation Learner una sessione è uno scambio interattivo ininterrotto con il bot. Le sessioni possono includere più turni, ma possono essere terminate a livello programmatico in caso di inattività per più di trenta minuti.  Per informazioni su come modificare la durata predefinita del timeout di sessione, vedere la pagina della guida relativi ai limiti.

Questo lungo periodo di inattività farà sì che il bot crei una nuova sessione e reimposti la rete neurale ricorrente sullo stato iniziale. Per impostazione predefinita tutti i valori delle entità verranno cancellati. È possibile modificare questo comportamento predefinito nel modo illustrato di seguito.

### <a name="load-the-demo-model"></a>Caricare il modello demo

Nell'interfaccia utente Web fare clic su "Import Tutorials" (Importa esercitazioni) e selezionare il modello denominato "Tutorial-13-SessionCallbacks".

### <a name="code-for-the-callbacks"></a>Codice per i callback

Il codice di esempio per i due callback di questo modello è disponibile in: `c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts`.

![](../media/tutorial11_code.PNG)

- OnSessionStartCallback: questo metodo imposta l'entità BotName.
- OnSessionEndCallback: è possibile specificare ciò che si vuole conservare. In questo caso verranno cancellate tutte le entità tranne il nome e il numero di telefono dell'utente.

Ogni callback è facoltativo.

### <a name="actions"></a>Azioni

Nel modello sono state definite quattro azioni. Le azioni esistenti vengono indicate nella visualizzazione griglia per "Actions" (Azioni).

![](../media/tutorial11_actions.PNG)

### <a name="creating-an-end-session-action-for-callback-invocation"></a>Creazione di un'azione di fine sessione (per la chiamata di callback)

1. Nel riquadro a sinistra fare clic su "Actions" (Azioni), quindi sul pulsante "New Action" (Nuova azione).
2. Selezionare "ENDSESSION" per "Entity Type" (Tipo di entità).
3. Nel campo "Data..." (Dati...) digitare "Done".
4. Fare clic sul pulsante "Create" (Crea).

### <a name="edit-an-existing-action"></a>Modificare un'azione esistente

1. Selezionare l'azione "So, $UserName, you are in $UserLocation" nella visualizzazione griglia.
2. Deselezionare la casella di controllo "Wait for Response" (Attendi risposta).
3. Fare clic sul pulsante "Save" (Salva).

### <a name="chaining-actions"></a>Concatenamento di azioni

1. Nel riquadro a sinistra fare clic su "Train Dialogs" (Dialoghi di training), quindi sul pulsante "New Train Dialog" (Nuovo dialogo di training).
2. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "hi".
3. Fare clic su "Score Actions" (Punteggio azioni).
4. Selezionare la risposta "Hi, I'm Botty. What's your name?".
5. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "Lars".
6. Selezionare la risposta "Hi Lars. What is your phone number?".
7. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "555-555-5555".
8. Fare clic su "Score Actions" (Punteggio azioni).
9. Selezionare la risposta "Can you tell Botty your location, Lars?".
10. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "Seattle".
11. Fare clic su "Score Actions" (Punteggio azioni).
12. Selezionare la risposta "So, Lars, you are in Seattle".
13. Selezionare la risposta "Done".
14. Fare clic sul pulsante "Save" (Salva).

### <a name="testing-the-model"></a>Test del modello

1. Nel riquadro a sinistra fare clic su "Log Dialogs" (Dialoghi di log), quindi sul pulsante "New Log Dialog" (Nuovo dialogo di log).
2. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "hi".
3. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "Lars".
4. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "555-555-5555".
5. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "Seattle".
    - A questo punto tutti i valori delle entità, esclusa la posizione, dovrebbero essere stati mantenuti.
6. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "hello".
7. Nel pannello della chat, dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "Detroit".
8. Fare clic sul pulsante "Session Timeout" (Timeout sessione).
    - Facendo clic su questo pulsante si esercita la risposta del bot per lunghi periodi di inattività.
9. Fare clic sul pulsante "OK".
10. Fare clic sul pulsante "Done Testing" (Test completato).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Chiamate API](./14-api-calls.md)
