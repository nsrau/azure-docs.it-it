---
title: Come usare operazioni di diramazione e annullamento con un'applicazione di Conversation Learner - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Imparare a usare le operazioni di diramazione e annullamento con un'applicazione di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 724a9e47267e0bd7417130efe54c609ac7a465fb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376273"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Come usare operazioni di diramazione e annullamento
In questa esercitazione verranno presentate le operazioni di annullamento e diramazione.

## <a name="details"></a>Dettagli
- Annullamento: consente allo sviluppatore di "annullare" l'input di un utente o la scelta di un'azione. Dietro le quinte, l'operazione di annullamento crea in effetti un nuovo dialogo e lo riproduce fino al passaggio precedente.  Questi significa che verranno chiamati di nuovo il callback di rilevamento delle entità e le chiamate API nel dialogo.

- Diramazione: crea un nuovo dialogo di training che inizia allo stesso modo di un dialogo di training esistente. Ciò consente di evitare di immettere di nuovo manualmente le varie fasi del dialogo. Dietro le quinte, l'operazione di diramazione crea un nuovo dialogo e riproduce il dialogo esistente fino al passaggio selezionato.  Questi significa che verranno chiamati di nuovo il callback di rilevamento delle entità e le chiamate API nel dialogo.


## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot per l'ordinazione di pizza sia in esecuzione:

    npm run demo-pizza

### <a name="open-the-demo"></a>Aprire la demo

Nell'elenco delle app dell'interfaccia utente Web fare clic su TutorialDemo Pizza Order. 

Per informazioni dettagliate sulla demo Pizza Order, vedere l'esercitazione per l'ordinazione di pizza.

## <a name="undo"></a>Annullamento

Verrà annullata parte del dialogo e il dialogo verrà poi ricreato a partire da tale passaggio.

### <a name="training-dialogs"></a>Dialoghi di training
Per iniziare, avviare una sessione di training. 

1. Fare clic su Train Dialogs (Dialoghi di training) e quindi su New Train Dialog (Nuovo dialogo di training).
1. Immettere "order a pizza".
2. Fare clic su Score Action (Punteggio azione).
3. Fare clic per selezionare 'What would you like on your pizza?'.
4. Immettere 'mushrooms and cheese'.
5. Fare clic su Score Actions (Punteggio azioni).
3. Fare clic per selezionare 'You have $Toppings on your pizza'.
6. Selezionare 'Would you like anything else?'.
7. Immettere 'remove mushrooms and add peppers'.
    - Selezionare mushrooms e deselezionare l'entità Toppings. Viene così creata un'azione che verrà annullata.
2. Fare clic su Undo Step (Annulla passaggio).
    - Si noti che l'ultima voce viene rimossa, e si ritorna a 'Would you like anything else?'  (screenshot di seguito)
2. Immettere 'remove mushrooms and add peppers'.
8. Fare clic per selezionare 'you have $Toppings on your pizza'
    - Assicurarsi che entrambe le entità vengono selezionate in modo corretto.
2. Fare clic su Score Action (Punteggio azione). A questo punto è possibile continuare con il dialogo corretto.
4. Fare clic su Done Teaching (Training completato).

È stato illustrato come usare l'annullamento per rimuovere un input utente e un'azione.

![](../media/tutorial15_undo.PNG)

## <a name="branch"></a>Diramazione

A titolo di esempio verrà aperto un dialogo di training esistente e si creerà un altro dialogo di training tramite diramazione.

1. Fare clic su Train Dialogs (Dialoghi di training) e quindi su 'new order' per aprire il dialogo esistente. 
2. Fare clic sull'ultimo 'no' nel dialogo (vedere lo screenshot di seguito).
3. Fare clic su Branch (Dirama).
    - Si noti che 'no' viene rimosso e l'intero dialogo fino a quel punto viene copiato in un nuovo dialogo. 
    - In questo modo è possibile evitare di reimmettere i passaggi precedenti per esplorare un nuovo "ramo" a partire da questo punto.
1. Immettere 'yes'.
2. Fare clic su Score Action (Punteggio azione).
3. Selezionare 'You have $Toppings on your pizza'.
6. Selezionare 'Would you like anything else?'.
7. Immettere 'no'.
4. Fare clic su Done Teaching (Training completato).

![](../media/tutorial15_branch.PNG)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Assegnazione di tag e controllo delle versioni](./16-versioning-and-tagging.md)
