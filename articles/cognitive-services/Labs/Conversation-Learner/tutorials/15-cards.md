---
title: 'Come usare le schede con un modello di Conversation Learner: parte 1 - Servizi cognitivi Microsoft| Microsoft Docs'
titleSuffix: Azure
description: Informazioni su come usare le schede con un modello di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a2db74e4c5688c0a2a3975ff828caf20c3cf7904
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60406360"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Come usare le schede (parte 2 di 2)

Questa esercitazione illustra come aggiungere e usare una scheda semplice nel bot.

> [!NOTE]
> Attualmente Conversation Learner prevede che i file di definizione delle schede si trovino in una directory denominata "cards" presente nella directory in cui viene avviato il bot.

## <a name="video"></a>Video

[![Anteprima dell'esercitazione sulle schede](https://aka.ms/cl_Tutorial_v3_Cards_Preview)](https://aka.ms/cl_Tutorial_v3_Cards)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione

    npm run tutorial-general

## <a name="details"></a>Dettagli

Le schede sono elementi dell'interfaccia utente che consentono all'utente di selezionare un'opzione nella conversazione. 

### <a name="open-the-demo"></a>Aprire la demo

Nell'interfaccia utente Web fare clic su "Import tutorials" (Importa esercitazioni) e selezionare il modello denominato "Tutorial-15-Cards".

### <a name="the-card"></a>Scheda

La definizione della scheda si trova nella posizione seguente: C:\<installedpath\>\src\cards\prompt.json.

Il sistema prevede che le definizioni delle schede si trovino nella directory "cards".

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> Si noti il tipo di corpo "TextBlock" e il segnaposto "{{question}}" nel campo di testo.
> Sono presenti due pulsanti di invio e il testo inviato per ognuno.

### <a name="actions"></a>Azioni

Sono state create tre azioni. Come illustrato di seguito, la prima azione è una scheda.

![](../media/tutorial13_actions.PNG)

L'azione di tipo scheda è stata creata come segue:

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> La scheda contiene tre diversi parametri: domanda di input, pulsante 1 e pulsante 2, che costituiscono i riferimenti al modello nella scheda in cui si immettono la domanda e le rispettive risposte. È anche possibile fare riferimento a entità o a combinazioni di testo ed entità e usarle.

L'icona a forma di occhio consente di visualizzare l'aspetto della scheda.

### <a name="practicing-creating-card-actions"></a>Esercitazione sulla creazione di azioni scheda

1. Nel riquadro a sinistra fare clic su "Actions" (Azioni), quindi sul pulsante "New Action" (Nuova azione).
2. Selezionare "CARD" (Scheda) per "Action Type" (Tipo di azione).
3. Scegliere "prompt" dall'elenco "Template" (Modelli).
4. Nel campo "question" (Domanda), digitare "Go left or right"
5. Nel campo "button1", digitare "left"
6. Nel campo "button2", digitare "right"
7. Fare clic sul pulsante "Cancel" (Annulla).

### <a name="train-dialog-using-an-adaptive-card"></a>Eseguire il dialogo di training usando una scheda adattiva

1. Nel riquadro a sinistra fare clic su "Train Dialogs" (Dialoghi di training), quindi sul pulsante "New Train Dialog" (Nuovo dialogo di training).
2. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "hi".
3. Fare clic su "Score Actions" (Punteggio azioni).
4. Selezionare la risposta "prompt: question: Go left or right?"
    - L'icona a forma di occhio consente di visualizzare l'anteprima della scheda
5. Nel pannello della chat fare clic sul pulsante "Left" nel prompt visualizzato.
6. Fare clic su "Score Actions" (Punteggio azioni).
7. Selezionare la risposta "Left".
8. Fare clic sul pulsante "Save" (Salva).
9. Selezionare la risposta "prompt: question: Go left or right?"
10. Nel pannello della chat fare clic sul pulsante "Right" nel prompt visualizzato.
11. Fare clic su "Score Actions" (Punteggio azioni).
12. Selezionare la risposta "Right".

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Bot ibridi](./16-hybrid-bots.md)
