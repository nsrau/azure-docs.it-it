---
title: Come usare operazioni di diramazione e annullamento con un modello di Conversation Learner - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Imparare a usare le operazioni di diramazione e annullamento con un modello di Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 6ffa0881df07e453c8beb175b8580deebbfc1ec9
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389900"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Come usare le operazioni di creazione di un ramo e annullamento
In questa esercitazione verranno presentate le operazioni di annullamento e diramazione.


## <a name="details"></a>Dettagli
### <a name="undo"></a>Annullamento
Consente allo sviluppatore di "annullare" l'ultimo input dell'utente o l'ultima scelta di un'azione. Dietro le quinte, l'operazione di annullamento crea in effetti un nuovo dialogo e lo riproduce fino al passaggio precedente.  Questi significa che verranno chiamati di nuovo il callback di rilevamento delle entità e le chiamate API nel dialogo.

### <a name="branch"></a>Ramo
Crea un nuovo dialogo di training che inizia allo stesso modo di un dialogo di training esistente, evitando così di dover immettere di nuovo manualmente i turni del dialogo. Dietro le quinte, il "ramo" crea un nuovo dialogo e riproduce il dialogo di training esistente fino al passaggio selezionato.  Questi significa che verranno chiamati di nuovo il callback di rilevamento delle entità e le chiamate API nel dialogo.


## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot per l'ordinazione di pizza sia in esecuzione:

    npm run demo-pizza

### <a name="open-or-import-the-demo"></a>Aprire o importare la demo

Se è già stata usata l'esercitazione per ordinare la pizza, aprire semplicemente il modello specifico nell'elenco nell'interfaccia utente Web. In caso contrario è necessario fare clic su "Import Tutorials" (Importa esercitazioni) e selezionare il modello denominato "Demo-PizzaOrder".

## <a name="undo"></a>Annullamento

Ecco un esempio della funzione `Undo`:

### <a name="training-dialogs"></a>Dialoghi di training
1. Nel riquadro a sinistra fare clic su "Train Dialogs" (Dialoghi di training) e quindi sul pulsante `New Train Dialog` (Nuovo dialogo di training).
2. Digitare "Order a pizza".
3. Fare clic sul pulsante `Score Actions`.
4. Fare clic per selezionare "What would you like on your pizza?".
5. Digitare "anything".
6. Fare clic sul pulsante `Undo`.
    - L'ultima voce viene rimossa, lasciando l'ultima risposta del bot "What would you like on your pizza?".

## <a name="branch"></a>Ramo

Per questo demo verrà aperto un dialogo di training esistente e si creerà un altro dialogo di training tramite la creazione di un ramo.

1. Nel riquadro a sinistra fare clic su "Train Dialogs" (Dialoghi di training).
2. Nella griglia dovrebbe essere visibile solo un training che inizia con "new order".
3. Nella griglia fare clic su "new order" per aprire il dialogo di training esistente.
4. Fare clic sull'ultima risposta "no" nel dialogo.
5. Fare clic sull'icona del "ramo", indicata con un cerchio in rosso in questa immagine:
    - ![](../media/tutorial15_branch.PNG)
    - Il dialogo di training completo prima di "no" viene copiato in un nuovo dialogo di training.
    - In questo modo è possibile evitare di reimmettere i turni precedenti per esplorare un nuovo "ramo" della conversazione a partire da questo punto.
6. Digitare "yes", premere INVIO.
7. Fare clic sul pulsante `Score Actions`.
    - A questo punto il bot sceglie automaticamente una risposta, ma è possibile modificare le risposte non soddisfacenti.
8. Fare clic sull'ultima risposta del bot.
    - Questa azione permette di selezionare una risposta diversa.
9. Selezionare "UseLastToppings".
10. Fare clic sul pulsante `Score Actions`.
    - Anche in questo caso il bot sceglie automaticamente una risposta. Dovrebbe essere "You have sausage, cheese and mushrooms on your pizza". 
    - In questo caso la risposta è soddisfacente e verrà mantenuta.
11. Fare clic sul pulsante `Score Actions`.
    - Anche in questo caso il bot sceglie automaticamente una risposta, che dovrebbe essere "Would you like anything else?".
12. Digitare "no".
13. Fare clic sul pulsante `Save Branch`.
14. Si noti che la griglia include ora due training che iniziano con "new order".
    - Uno è quello usato per creare il nuovo ramo.
    - L'altro è la versione creata come ramo appena salvata.
    - Fare clic su ognuno dei training per verificare.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Assegnazione di tag e controllo delle versioni](./18-version-tag.md)
