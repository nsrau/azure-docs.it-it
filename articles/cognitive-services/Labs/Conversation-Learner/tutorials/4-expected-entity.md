---
title: Come usare la proprietà "entità prevista" delle azioni di Conversation Learner - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare la proprietà "entità prevista" in un modello di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 9e013e237a996d722d958920a1310e3aaea36c52
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170909"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Come usare la proprietà "entità prevista" delle azioni

Questa esercitazione illustra il campo delle entità previste delle azioni.

## <a name="video"></a>Video

[![Anteprima esercitazione 4](http://aka.ms/cl-tutorial-04-preview)](http://aka.ms/blis-tutorial-04)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione

    npm run tutorial-general

## <a name="details"></a>Dettagli
Usare il campo delle entità previste di un'azione per comunicare al sistema che nella risposta dell'utente a un'azione è prevista la dichiarazione di un'entità.

In concreto, se il campo delle entità previste di un'azione è impostato su $entity, alla successiva espressione dell'utente il sistema eseguirà queste operazioni:

1. Per prima cosa, come di consueto, il sistema tenterà di trovare le entità usando il modello di estrazione di entità basato sull'apprendimento automatico.
2. Se nel passaggio 1 non vengono trovate entità, l'euristica assegnerà l'intera espressione dell'utente a $entity.
3. Il sistema chiamerà `EntityDetectionCallback` come di consueto e procederà alla selezione dell'azione.

## <a name="steps"></a>Passaggi

### <a name="create-the-model"></a>Creare il modello

1. Nell'interfaccia utente Web fare clic su New Model (Nuovo modello)
2. In Name (Nome) immettere "ExpectedEntities". Fare quindi clic su Create (Crea).

### <a name="create-an-entity"></a>Creare un'entità

1. Fare clic su Entities (Entità) e quindi su New Entity (Nuova entità).
2. In Entity Name (Nome entità) immettere "name".
3. Click Create

> [!NOTE]
> Il tipo di entità è "custom". Questo valore indica che l'entità può essere sottoposta a training.  Sono disponibili anche entità predefinite il cui comportamento non può essere modificato.  Queste entità sono illustrate [nell'esercitazione Entità predefinite](./7-built-in-entities.md).

![](../media/tutorial4_entities.PNG)

### <a name="create-two-actions"></a>Creare due azioni

1. Fare clic su Actions (Azioni) e quindi su New Action (Nuova azione).
2. In Response (Risposta) digitare "What's your name?".
3. In Expected Entities (Entità previste) immettere "$name". Fare clic su Save.
    - Questo valore indica che se viene posta questa domanda e la risposta dell'utente non contiene entità rilevate, il bot supporrà che l'intera risposta dell'utente costituisca l'entità.
2. Fare clic su Actions (Azioni) e quindi su New Action (Nuova azione) per creare una seconda azione.
3. In Response (Risposta) digitare "Hello $name".
    - L'entità viene automaticamente aggiunta come entità non qualificante. 
4. Fare clic su Salva.

Sono ora disponibili due azioni.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-bot"></a>Eseguire il training del bot

1. Fare clic su Train Dialogs (Dialoghi di training) e quindi su New Train Dialog (Nuovo dialogo di training).
2. Digitare "hello".
3. Fare clic su Score Actions (Punteggio azioni) e selezionare "What's your name?".
    - La risposta "Hello $name" non può essere selezionata, perché richiede la definizione dell'entità $name e $name non è presente nella memoria del bot.
2. Immettere "david". 
    - Il nome viene evidenziato come entità, a causa dell'euristica configurata in precedenza per selezionare la risposta come entità.
5. Fare clic su Score Actions (Punteggio azioni).
    - Il valore di name è ora presente nella memoria del bot.
    - "Hello $name" è ora disponibile come risposta. 
6. Selezionare "Hello $name".
7. Fare clic su Done Teaching (Training completato).

Di seguito sono riportati due esempi in cui il modello di estrazione di entità basato sull'apprendimento automatico identifica un nome e non viene quindi attivata l'euristica delle entità previste.

1. Fare clic su New Train Dialog (Nuovo dialogo di training).
2. Immettere "my name is david".
    - Il modello identifica la parola "david" viene identificata come entità name perché è già stata rilevata in precedenza.
2. Fare clic su Score Actions (Punteggio azioni).
3. Selezionare "Hello $name".
4. Immettere "my name is susan".
    - Il modello identifica la parola "susan" viene identificata come nome perché questo modello è già stato rilevato.
2. Fare clic su Score Actions (Punteggio azioni).
2. Selezionare "Hello susan".
3. Fare clic su Done Teaching (Training completato).

Negli esempi seguenti l'euristica "entità previsto" si attiva, ma non è corretta. Quindi, gli esempi illustrano come effettuare una correzione.

1. Digitare "call me jose".
    - Il modello non riconosce il nome come entità.
2. Fare clic su "jose" e selezionare "name".
3. Fare clic su Score Actions (Punteggio azioni).
4. Selezionare "Hello $name".
5. Fare clic su Done Teaching (Training completato).
1. Fare clic su New Train Dialog (Nuovo dialogo di training).
2. Immettere "hello".
3. In risposta a "What's your name" immettere "I am called frank".
    - L'intera frase viene evidenziata. Il modello statistico infatti non ha trovato un nome, di conseguenza l'euristica è stata attivata e ha selezionato l'intera risposta come entità name.
2. Per correggere, fare clic sulla frase evidenziata e quindi sulla X rossa. 
3. Fare clic per selezionare "frank" e quindi fare clic su "name".
2. Fare clic su Score Actions (Punteggio azioni).
3. Selezionare "Hello $name".
4. Fare clic su Done Teaching (Training completato).

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Entità negabili](./5-negatable-entities.md)
