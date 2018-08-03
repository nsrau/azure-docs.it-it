---
title: 'Come usare le schede con un modello di Conversation Learner: parte 1 - Servizi cognitivi Microsoft| Microsoft Docs'
titleSuffix: Azure
description: Informazioni su come usare le schede con un modello di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 988a2433f098f41bca4796299825293efd4de44b
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171135"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Come usare le schede (parte 2 di 2)

Questa esercitazione illustra come aggiungere e usare una scheda semplice nel bot.

> [!NOTE]
> Attualmente Conversation Learner prevede che i file di definizione delle schede si trovino in una directory denominata "cards" presente nella directory in cui viene avviato il bot. Questa impostazione potrà essere configurata in futuro.

## <a name="video"></a>Video

[![Anteprima esercitazione 13](http://aka.ms/cl-tutorial-13-preview)](http://aka.ms/blis-tutorial-13)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione

    npm run tutorial-general

## <a name="details"></a>Dettagli

Le schede sono elementi dell'interfaccia utente che consentono all'utente di selezionare un'opzione nella conversazione. 

### <a name="open-the-demo"></a>Aprire la demo

Nell'elenco dei modelli dell'interfaccia utente Web fare clic su Tutorial-13-Cards-1. 

### <a name="the-card"></a>Scheda

La definizione della scheda si trova nel percorso seguente: C:\<percorsoinstallazione\>\src\cards\prompt.json.

Il sistema prevede che le definizioni delle schede si trovino nella directory cards.

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> Si notino il tipo di corpo `TextBlock` e il segnaposto `{{question}}` nel campo di testo.
> Sono presenti due pulsanti di invio e il testo inviato per ognuno.

### <a name="actions"></a>Azioni

Sono state create tre azioni. Come illustrato di seguito, la prima azione è una scheda.

![](../media/tutorial13_actions.PNG)

L'azione di tipo scheda è stata creata come segue:

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> La domanda di input e i pulsanti 1 e 2, che costituiscono i riferimenti al modello nella scheda in cui si immettono la domanda e le rispettive risposte. È anche possibile fare riferimento a entità o combinazioni di testo ed entità e usarle.

L'icona a forma di occhio consente di visualizzare l'aspetto della scheda.

### <a name="train-dialog"></a>Dialogo di training

Verrà ora esaminato un dialogo di training.

1. Fare clic su Train Dialogs (Dialoghi di training) e quindi su New Train Dialog (Nuovo dialogo di training).
1. Immettere 'hi'.
2. Fare clic su Score Action (Punteggio azione).
3. Fare clic per selezionare "Prompt go left or right".
    - Fare clic su "left" o "right" equivale a digitare rispettivamente "left" o "right" in qualità di utente. 
4. Fare clic su Score Actions (Punteggio azioni).
4. Fare clic per selezionare "left". Questa è un'azione senza attesa.
6. Fare clic per selezionare "Prompt go left or right".
4. Fare clic su "right".
5. Fare clic su Score Actions (Punteggio azioni).
3. Fare clic per selezionare "right".
6. Fare clic per selezionare "Prompt go left or right".
4. Fare clic su Done Testing (Test completato).

È stato illustrato il funzionamento delle schede, definite nella directory cards come modelli JSON. I modelli vengono presentati nell'interfaccia utente, in cui è possibile inserire una stringa, un'entità o una combinazione di entrambe.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Schede: parte 2](./14-cards-2.md)
