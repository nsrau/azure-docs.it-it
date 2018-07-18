---
title: Come usare le chiamate API con un'applicazione di Conversation Learner - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare le chiamate API con un'applicazione di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: ec752cbadfac7a47e08ed7b0ffe8bb475969fac5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376276"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-application"></a>Come aggiungere le chiamate API a un'applicazione di Conversation Learner

Questa esercitazione illustra come aggiungere chiamate API all'applicazione. Le chiamate API sono funzioni definite e scritte nel bot e che possono essere chiamate da Conversation Learner.

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot "tutorialAPICalls.ts" sia in esecuzione.

    npm run tutorial-api-calls

## <a name="details"></a>Dettagli

- Le chiamate API possono leggere e modificare le entità.
- Le chiamate API hanno accesso all'oggetto di gestione della memoria.
- Le chiamate API possono anche accettare argomenti e questo consente di riutilizzare la stessa chiamata API per scopi diversi.

### <a name="open-the-demo"></a>Aprire la demo

Nell'elenco delle app dell'interfaccia utente Web fare clic su Tutorial-12-APICalls. 

### <a name="entities"></a>Entità

È stata definita un'entità nell'applicazione denominata number.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>CHIAMATE API
Il codice per le chiamate API è definito in questo file: C:\<percorsoinstallazione\>\src\demos\tutorialAPICalls.ts.

![](../media/tutorial12_apicalls.PNG)

- Il primo callback API è RandomGreeting. Restituisce un saluto casuale definito nella variabile greeting.
- Il callback API Multiply moltiplicherà due numeri forniti dall'utente. Restituisce quindi il risultato del prodotto dei due numeri. Ciò dimostra che i callback API possono accettare input. Si noti che il gestore della memoria è il primo argomento. 
- Il callback API ClearEntities cancella l'entità number per consentire all'utente di immettere il numero successivo. Ciò illustra il modo in cui le chiamate API possono modificare le entità.

### <a name="actions"></a>Azioni

Sono state create quattro azioni. 

![](../media/tutorial12_actions.PNG)

- Oltre all'azione 'What number do you want to multiply by 12?' (Quale numero si vuole moltiplicare per 12?) che è un'azione comunicativa, sono disponibili tre diverse chiamate API che illustrano i tipici modelli di chiamata API.

- RandomGreeting è un'azione senza attesa. Per configurare questa azione, nella finestra di dialogo Crea azione è stato selezionato il tipo di azione API_LOCAL e quindi RandomGreeting. 

![](../media/tutorial12_setupapicall.PNG)

Il pulsante di aggiornamento accanto all'API viene usato nel caso dovesse risultare necessario arrestare il bot e apportare modifiche alle API. Facendo clic sul pulsante di aggiornamento verrebbero recuperate le modifiche più recenti.

Ecco come è stata creata l'azione Multiply: dopo aver selezionato API_Local e API, è stata immessa un'entità ($number) per il primo valore di input (num1string) e un valore (12) per il secondo valore di input (num2string). Ciò consente un livello di indirizzamento indiretto tra il bot e le chiamate API, in modo che lo stesso callback possa essere mappato a un numero limitato di azioni nel sistema e per ottenere una differenziazione in base alla modalità di assegnazione.

![](../media/tutorial12_actionmultiply.PNG)

### <a name="train-dialog"></a>Dialogo di training

Verrà ora esaminato un dialogo di training.

1. Fare clic su Train Dialogs (Dialoghi di training) e quindi su New Train Dialog (Nuovo dialogo di training).
1. Immettere 'hi'.
2. Fare clic su Score Action (Punteggio azione).
3. Fare clic per selezionare RandomGreeting. Verrà eseguita la chiamata API Random Greeting.
3. Fare clic per selezionare 'What number to do you want to multiply by 12?'
4. Immettere '8'. Quindi fare clic su Score Actions (Punteggio azioni).
4. Selezionare 'Multiply $number 12'. Si noti il risultato della moltiplicazione.
5. Selezionare 'Clear Entities'.
    - Si noti che il valore dell'entità number è stato cancellato.
3. Fare clic per selezionare 'What number to do you want to multiply by 12?'
4. Fare clic su Done Testing (Test completato).

![](../media/tutorial12_dialog.PNG)

Si è visto come registrare i callback di API, i relativi modelli comuni e come definire argomenti e associare valori ed entità.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Schede parte 1](./13-cards-1.md)
