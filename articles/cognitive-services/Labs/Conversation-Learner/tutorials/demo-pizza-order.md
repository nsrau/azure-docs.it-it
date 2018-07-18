---
title: 'Applicazione demo di Conversation Learner: ordinazione pizza - Servizi cognitivi Microsoft| Microsoft Docs'
titleSuffix: Azure
description: Informazioni su come creare un'applicazione demo di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 99cd89c4f4430f2d65ed0963e3092d51a83842d7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376393"
---
# <a name="demo-pizza-order"></a>Demo: Ordinazione pizza
Questa demo illustra un bot per l'ordinazione di pizza. Supporta l'ordine di una singola pizza con queste funzionalità:

- Riconoscimento dei condimenti per la pizza nelle espressioni dell'utente
- Verifica della disponibilità o meno dei condimenti nelle scorte e risposta corrispondente
- Memorizzazione dei condimenti per pizza di un ordine precedente e offerta di avviare un nuovo ordine con gli stessi condimenti

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot per l'ordinazione di pizza sia in esecuzione.

    npm run demo-pizza

### <a name="open-the-demo"></a>Aprire la demo

Nell'elenco delle app dell'interfaccia utente Web fare clic su TutorialDemo Pizza Order. 

## <a name="entities"></a>Entità

Sono state create tre entità.

- Toppings: accumula i condimenti richiesti dall'utente. Include i condimenti validi disponibili nelle scorte e verifica se un condimento è presente o meno nelle scorte.
- OutofStock: questa entità viene usata per comunicare all'utente che il condimento selezionato non è disponibile nelle scorte.
- LastToppings: dopo che è stato effettuato un ordine, questa entità viene usata per offrire all'utente l'elenco dei condimenti nell'ordine.

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Azioni

È stato creato un set di azioni che include la richiesta all'utente dei condimenti desiderati sulla pizza, la comunicazione di quelli aggiunti fino a quel momento e così via.

Sono anche presenti due chiamate API.

- FinalizeOrder: per effettuare l'ordine della pizza
- UseLastToppings: per eseguire la migrazione dei condimenti dall'ordine precedente 

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Dialoghi di training
Sono stati definiti più dialoghi di training. 

![](../media/tutorial_pizza_dialogs.PNG)

A titolo di esempio, si proverà una sessione di training.

1. Fare clic su Train Dialogs (Dialoghi di training) e quindi su New Train Dialog (Nuovo dialogo di training).
1. Immettere "order a pizza".
2. Fare clic su Score Action (Punteggio azione).
3. Fare clic per selezionare "What would you like on your pizza?".
4. Immettere "mushrooms and cheese".
    - Entrambi sono stati etichettati da LUIS come Toppings. Se non fosse corretto, è possibile fare clic per evidenziare e quindi correggere.
    - Il segno "+" accanto all'entità indica che viene aggiunta al set di condimenti.
5. Fare clic su Score Actions (Punteggio azioni).
    - "mushrooms" e "cheese" non sono presenti in memoria per Toppings.
3. Fare clic per selezionare "You have $Toppings on your pizza".
    - È un'azione senza attesa, quindi il bot chiederà l'azione successiva.
6. Selezionare "Would you like anything else?".
7. Immettere "remove mushrooms and add peppers".
    - Accanto a **mushroom**, che deve essere rimosso, è riportato un segno "-". Per "peppers" è invece presente il segno "+" per l'aggiunta ai condimenti.
2. Fare clic su Score Action (Punteggio azione).
    - **peppers** è ora in grassetto perché è un nuovo condimento, mentre la parola **mushrooms** è stata barrata.
8. Fare clic per selezionare "You have $Toppings on your pizza".
6. Selezionare "Would you like anything else?".
7. Immettere "add peas".
    - "peas" è un esempio di condimento non disponibile nelle scorte. La parola viene comunque etichettata come condimento.
2. Fare clic su Score Action (Punteggio azione).
    - Il condimento "peas" risulta incluso in OutOfStock.
    - Per verificare come è stato ottenuto questo risultato, aprire il codice in C:\<\percorsoinstallazione>\src\demos\demoPizzaOrder.ts e osservare il metodo EntityDetectionCallback. Questo metodo viene chiamato dopo ogni condimento per verificarne la disponibilità nelle scorte. Se non è disponibile, viene cancellato dal set di condimenti e viene aggiunto all'entità OutOfStock. La variabile inStock è definita sopra tale metodo e contiene l'elenco dei condimenti presenti nelle scorte.
6. Selezionare "We don't have $OutOfStock".
7. Selezionare "Would you like anything else?".
8. Immettere "no".
9. Fare clic su Score Action (Punteggio azione).
10. Selezionare la chiamata API "FinalizeOrder". 
    - In questo modo verrà chiamata la funzione "FinalizeOrder" definita nel codice, che cancella i condimenti e restituisce "Your order is on its way". 
2. Immettere "order another". Viene avviato un nuovo ordine.
9. Fare clic su Score Action (Punteggio azione).
    - "cheese" e "peppers" sono presenti in memoria come condimenti dell'ultimo ordine.
1. Selezionare "Would you like $LastToppings".
2. Immettere "yes".
3. Fare clic su Score Action (Punteggio azione).
    - Il bot vuole eseguire l'azione UseLastToppings, che è il secondo dei due metodi di callback e copierà i condimenti dell'ultimo ordine nei condimenti e cancellerà gli ultimi condimenti. È così possibile memorizzare l'ultimo ordine e offrire tali condimenti come opzioni se l'utente dichiara di volere un'altra pizza.
2. Fare clic per selezionare "You have $Toppings on your pizza".
3. Selezionare "Would you like anything else?".
8. Immettere "no".
4. Fare clic su Done Teaching (Training completato).

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Demo: Utilità di avvio di app di realtà virtuale](./demo-vr-app-launcher.md)
