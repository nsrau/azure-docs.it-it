---
title: Come usare le chiamate API con un modello di Conversation Learner - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare le chiamate API con un modello di Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 261536932cc82a28ad4ee3ffc3575ea41fe9ec5b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703916"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Come aggiungere le chiamate API a un modello di Conversation Learner

Questa esercitazione illustra come aggiungere chiamate API al modello. Le chiamate API sono funzioni definite e scritte nel bot e che possono essere chiamate da Conversation Learner.

## <a name="video"></a>Video

[![Anteprima dell'esercitazione sulle chiamate API](https://aka.ms/cl_Tutorial_v3_APICalls_Preview)](https://aka.ms/cl_Tutorial_v3_APICalls)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot "tutorialAPICalls.ts" sia in esecuzione.

    npm run tutorial-api-calls

## <a name="details"></a>Dettagli

- Le chiamate API possono leggere e modificare le entità.
- Le chiamate API hanno accesso all'oggetto di gestione della memoria.
- Le chiamate API possono accettare argomenti.

### <a name="open-the-demo"></a>Aprire la demo

Nell'interfaccia utente Web fare clic su "Import tutorials" (Importa esercitazioni) e selezionare il modello denominato "Tutorial-14-APICalls".

### <a name="entities"></a>Entità

È stata definita un'entità nel modello denominata `number`.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>Chiamate API
Il codice per le chiamate API è definito in questo file: `C:\<installedpath>\src\demos\tutorialAPICalls.ts`.

![](../media/tutorial12_apicalls.PNG)

- Il callback `RandomGreeting` restituisce un saluto casuale definito nella matrice `greeting`.
- Il callback `Multiply` moltiplicherà i due numeri passati dall'azione che ha eseguito la chiamata e restituirà un risultato che può essere reso nell'interfaccia utente.
    - Si noti che il gestore della memoria è il primo argomento. 
    - Si noti che i callback di API possono accettare più input, in questo caso `num1string` e `num2string`.
- Il callback `ClearEntities` cancella l'entità "number" per consentire all'utente di immettere un altro numero. 
    - Illustra il modo in cui le chiamate API possono modificare le entità.

### <a name="actions"></a>Azioni
Sono state create quattro azioni. Tre di queste sono azioni di API "senza attesa" e la quarta è un'azione di "testo" che pone all'utente una domanda simile a quella presentata in altre esercitazioni. Per esaminare la modalità di creazione di ogni azione, eseguire le operazioni seguenti:
1. Nel pannello a sinistra fare clic su "Actions" (Azioni) e quindi su una delle quattro azioni elencate nella griglia.
2. Si noti i valori di ogni campo nel modulo visualizzato.
3. Si noti il pulsante `Refresh` accanto al campo della API.
    - Se si dovesse interrompere il bot e apportare modifiche alle API mentre la pagina dell'interfaccia utente è attiva, è possibile fare clic sul pulsante `Refresh` per applicare le modifiche più recenti.

![](../media/tutorial12_actions.PNG)

#### <a name="clearentities-multiply-and-randomgreeting"></a>ClearEntities, Multiply e RandomGreeting
Tutte e tre queste azioni sono di tipo API. Ogni azione si basa sulle funzioni di callback di API per eseguire alcune operazioni e possibilmente restituire un valore da presentare all'utente.

#### <a name="what-number-do-you-want-to-multiply-by-12"></a>"What number do you want to multiply by 12"
Si tratta dell'azione di "testo" e pone semplicemente una domanda dell'utente. Sebbene questa azione non interagisca effettivamente con uno dei callback di API, richiede all'utente di rispondere con un numero che verrà inserito nella memoria di un'entità che potrà essere usata dall'azione "Multiply" che usa uno dei callback di API.


### <a name="train-dialog"></a>Dialogo di training

Verrà ora esaminato un "dialogo di training".

1. Nel riquadro a sinistra fare clic su `Train Dialogs` e quindi sul pulsante `New Train Dialog` (Nuovo dialogo di training).
2. Digitare "hello".
3. Fare clic sul pulsante `Score Actions`.
4. Selezionare `RandomGreeting`. 
    - Verrà eseguita la chiamata API Random Greeting.
    - NON verrà attesa la risposta dell'utente.
5. Selezionare `What number to do you want to multiply by 12?`.
6. Digitare un numero, un numero qualsiasi e solo un numero.
    - Si noti che il numero è stato etichettato automaticamente come entità `number`.
7. Fare clic sul pulsante `Score Actions`.
8. Selezionare l'azione `Multiply`.
    - Si noti il risultato della moltiplicazione per 12.
    - Si noti che la memoria contiene ancora il valore immesso per `number`
9. Selezionare l'azione `ClearEntities`.
    - Si noti che il valore dell'entità per `number` è stato cancellato dalla memoria.
10. Fare clic sul pulsante `Save`.

Si è visto come registrare i callback di API, i relativi modelli comuni e come definire argomenti e associare valori ed entità.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Schede parte 1](./15-cards.md)
