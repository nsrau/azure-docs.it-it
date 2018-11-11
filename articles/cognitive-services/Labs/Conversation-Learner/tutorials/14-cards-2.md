---
title: 'Come usare le schede con un modello di Conversation Learner: parte 2 - Servizi cognitivi Microsoft| Microsoft Docs'
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
ms.openlocfilehash: 99c474978d4da1d80669505330b2dc6220d7ca5f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51226635"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Come usare le schede (parte 2 di 2)
Questa esercitazione illustra come aggiungere una scheda con un modulo compilabile al bot e come i campi del modulo vengono trasferiti nelle entità.

Conversation Learner prevede che i file di definizione delle schede si trovino in una directory denominata "cards" presente nella directory in cui viene avviato il bot.

## <a name="video"></a>Video

[![Anteprima esercitazione 14](https://aka.ms/cl-tutorial-14-preview)](https://aka.ms/blis-tutorial-14)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione

    npm run tutorial-general

## <a name="details"></a>Dettagli

Le schede sono elementi dell'interfaccia utente che consentono all'utente di selezionare un'opzione nella conversazione. 

### <a name="open-the-demo"></a>Aprire la demo

Nell'elenco dei modelli dell'interfaccia utente Web fare clic su Tutorial-14-Cards-2. 

### <a name="the-card"></a>Scheda

La definizione della scheda si trova nel percorso seguente: C:\<percorsoinstallazione\>\src\cards\shippingAddress.json.

Questa scheda raccoglie tre campi dell'indirizzo di spedizione: città, via e stato.

![](../media/tutorial14_card.PNG)

### <a name="actions"></a>Azioni

Sono state create tre azioni. Come illustrato di seguito, la prima azione è una scheda.

![](../media/tutorial14_actions.PNG)

L'azione di tipo scheda è stata creata come segue:

- Si notino Address-Street, con tipo Input.text, e il relativo ID.
- Analogamente, sono presenti Address-City e un elenco a discesa con ID Address-State.

Gli ID sono importanti perché sono i nomi delle entità che riceveranno questi valori nel bot quando i campi verranno popolati e inviati.

## <a name="entities"></a>Entità
Sono state definite tre entità corrispondenti alla scheda illustrata sopra.

![](../media/tutorial14_entities.PNG)

## <a name="actions"></a>Azioni

Sono state definite due azioni.

![](../media/tutorial14_actions.PNG)

- La prima è la scheda dell'indirizzo di spedizione, in cui il tipo di azione è CARD (SCHEDA) e il modello viene selezionato nell'elenco a discesa come shippingAddress.
- La seconda è un'azione semplice per la lettura dell'indirizzo di spedizione.

![](../media/tutorial14_sa_card.PNG)

### <a name="train-dialog"></a>Dialogo di training

Verrà ora esaminato un dialogo di training.

1. Fare clic su Train Dialogs (Dialoghi di training) e quindi su New Train Dialog (Nuovo dialogo di training).
1. Immettere 'hi'.
2. Fare clic su Score Action (Punteggio azione).
3. Fare clic per selezionare "Shipping Address".
4. Compilare la scheda e inviare.
    - Si noti che i valori sono ora stati trasferiti nella memoria delle entità. Non è necessaria alcuna analisi perché gli input sono già stati partizionati dal modulo.
5. Fare clic su Score Actions (Punteggio azioni).
3. Fare clic per selezionare "Shipping to $Address...".
4. Fare clic su Done Testing (Test completato).

![](../media/tutorial14_train_dialog.PNG)

È stato illustrato come ottenere i valori dalla scheda contenente campi compilabili ed elenchi a discesa e come acquisire e raccogliere tali valori nelle entità del bot.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creazione di rami e annullamento](./15-branching-and-undo.md)
