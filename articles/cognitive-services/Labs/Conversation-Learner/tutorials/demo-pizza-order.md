---
title: 'Modello demo di Conversation Learner: ordinazione pizza - Servizi cognitivi Microsoft| Microsoft Docs'
titleSuffix: Azure
description: Informazioni su come creare un modello dimostrativo di apprendimento della conversazione.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 010245480d8e1f59d5c1b92a9e717f73b5ba7f4c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389149"
---
# <a name="demo-pizza-order"></a>Demo: Ordinazione pizza
Questa demo illustra un bot per l'ordinazione di pizze che supporta l'ordinazione di pizze singole mediante:

- riconoscimento dei condimenti per la pizza nelle espressioni dell'utente
- gestione dell'inventario condimenti e risposta appropriata
- memorizzazione degli ordini precedenti e velocizzazione delle nuove ordinazioni di pizze identiche

## <a name="video"></a>Video

[![Anteprima Demo Pizza](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot per l'ordinazione di pizza sia in esecuzione.

    npm run demo-pizza

### <a name="open-the-demo"></a>Aprire la demo

Nell'elenco modelli dell'interfaccia utente Web fare clic su TutorialDemo Pizza Order. 

## <a name="entities"></a>Entità

Il modello contiene tre entità:

- "Toppings" riunisce i condimenti specificati dell'utente, se disponibili.
- "OutofStock" segnala all'utente che il condimento selezionato è esaurito
- "LastToppings" contiene lo storico dei condimenti degli ordini precedenti

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Azioni

Il modello contiene un set di azioni che chiede all'utente la selezione del condimento, i condimenti accumulati e altro ancora.

Sono fornite anche due chiamate API:

- "FinalizeOrder" gestisce l'evasione degli ordini
- "UseLastToppings" elabora le informazioni dello storico dei condimenti

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Dialoghi di training

Nel modello sono presenti molti dialoghi di training.

![](../media/tutorial_pizza_dialogs.PNG)

Ora si eseguirà altro training mediante la creazione di un altro dialogo di training.

1. Nel riquadro a sinistra fare clic su "Train Dialogs" (Dialoghi di training), quindi sul pulsante "New Train Dialog" (Nuovo dialogo di training).
2. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "Order a pizza with cheese"
    - La parola "cheese" è stata estratta dall'estrattore di entità.
3. Fare clic su "Score Actions" (Punteggio azioni).
4. Selezionare la risposta "You have cheese on your pizza" (Il formaggio è stato aggiunto alla pizza).
5. Selezionare la risposta "Would you like anything else?" (Desideri altro?)
6. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "add mushrooms and peppers"
7. Fare clic su "Score Actions" (Punteggio azioni).
8. Selezionare la risposta "You have cheese, mushrooms and peppers on your pizza" (Formaggio, funghi e peperoni sono stati aggiunti alla pizza).
9. Selezionare la risposta "Would you like anything else?" (Desideri altro?)
10. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "remove peppers and add sausage"
11. Fare clic su "Score Actions" (Punteggio azioni).
12. Selezionare la risposta "You have cheese, mushrooms and sausage on your pizza" (Formaggio, funghi e salsiccia sono stati aggiunti alla pizza).
13. Selezionare la risposta "Would you like anything else?" (Desideri altro?)
14. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "add yam"
15. Fare clic su "Score Actions" (Punteggio azioni).
    - Il valore "yam" è stato aggiunto ad "OutofStock" dal codice di callback di rilevamento delle entità in quanto il testo non corrisponde a nessun ingrediente supportato.
16. Selezionare la risposta "OutOfStock"
17. Selezionare la risposta "Would you like anything else?" (Desideri altro?)
18. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "no"
    - Il "no" non è contrassegnato come tipo di finalità. Si sceglierà invece l'azione pertinente in base al contesto corrente.
19. Fare clic su "Score Actions" (Punteggio azioni).
20. Selezionare la risposta "FinalizeOrder"
    - Come conseguenza della selezione di questa azione, i condimenti attuali del cliente sono stati salvati nell'entità "LastToppings" e l'entità "Toppings" è stata eliminata dal codice di callback FinalizeOrder.
21. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "order another"
22. Fare clic su "Score Actions" (Punteggio azioni).
23. Selezionare la risposta "Would you like cheese, mushrooms and sausage?" (Vuoi formaggio, funghi e salsiccia?)
    - Questa azione è ora disponibile grazie al fatto che è stata impostata l'entità "LastToppings".
24. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "yes"
25. Fare clic su "Score Actions" (Punteggio azioni).
26. Selezionare la risposta "UseLastToppings"
27. Selezionare la risposta "You have cheese, mushrooms and sausage on your pizza" (Formaggio, funghi e salsiccia sono stati aggiunti alla pizza).
28. Selezionare la risposta "Would you like anything else?" (Desideri altro?)

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Distribuzione di un bot di Conversation Learner](../deploy-to-bf.md)
