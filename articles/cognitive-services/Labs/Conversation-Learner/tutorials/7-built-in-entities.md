---
title: Come aggiungere entità precompilate a un modello Strumento di apprendimento di conversazioni - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Informazioni su come aggiungere entità precompilate a un modello Strumento di apprendimento di conversazioni.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 2dbbf2a47cdc4240e5b0ba38658a4cb8d5307ff8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51260058"
---
# <a name="how-to-add-pre-built-entities"></a>Come aggiungere entità precompilate
Questa esercitazione illustra come aggiungere entità "precompilate" a un modello Strumento di apprendimento di conversazioni.

## <a name="video"></a>Video

[![Anteprima esercitazione 7](https://aka.ms/cl-tutorial-07-preview)](https://aka.ms/blis-tutorial-07)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione

    npm run tutorial-general

## <a name="details"></a>Dettagli

Le entità precompilate riconoscono tipi di entità comuni, ad esempio numeri, date, importi monetari e altri tipi.  A differenza delle entità personalizzate, sono pronte da usare e non richiedono alcun tipo di training.  A differenza delle entità personalizzate, non è possibile modificarne il comportamento.  Per impostazione predefinita, le entità precompilate sono multivalore, vale a dire, la memoria del bot accumulerà ogni istanza identificata dell'entità.

## <a name="steps"></a>Passaggi

### <a name="create-the-model"></a>Creare il modello

1. Nell'interfaccia utente Web fare clic su New Model (Nuovo modello)
2. In Nome immettere BuiltInEntities. Fare quindi clic su Crea.

### <a name="create-an-entity"></a>Creare un'entità

1. Fare clic su Entità, quindi su Nuova entità.
2. Fare clic sul menu a discesa EntityType e selezionare datetimev2.
    - Le opzioni Programmable (Programmabile) e Negatable (Negabile) sono disabilitate, perché non si applicano alle entità precompilate.
3. Fare clic su Crea.

![](../media/tutorial7_entities.PNG)

### <a name="create-two-actions"></a>Creare due azioni

1. Fare clic su Azioni e quindi su Nuova azione
2. In Risposta, digitare 'The date is $luis-datetimev2'.
3. Fare clic su Crea.

![](../media/tutorial7_actions.PNG)

Creare quindi la seconda azione:

1. Fare clic su Azioni e quindi su Nuova azione per creare una seconda azione.
3. In risposta, digitare 'What's the date?'.
4. In Disqualifying Entities (Entità di squalifica), immettere ''luis-datetimev2'.
4. Fare clic su Crea

![](../media/tutorial7_actions2.PNG)

Sono ora disponibili due azioni.

### <a name="train-the-bot"></a>Eseguire il training del bot

1. Fare clic su Train Dialogs (Dialoghi di training) e quindi su New Train Dialog (Nuovo dialogo di training).
2. Digitare 'hello'.
3. Fare clic su Score Actions (Punteggio azioni) e selezionare 'What's the date?'
2. Immettere 'today'. 
    - Si noti che 'today' è contrassegnato e viene indicato nella seconda riga poiché si tratta di un'entità precompilata e non modificabile.
5. Fare clic su Score Actions (Punteggio azioni)
    - Nota: la data viene ora visualizzata nella sezione Entity Memory (Memoria entità). 
    - Se si passa il mouse sulla data, verranno visualizzati i dati aggiuntivi forniti da LUIS, che è possibile usare e modificare ulteriormente nel codice. 
6. Selezionare 'The date is $luis-datetimev2'.
7. Fare clic su Done Teaching (Training completato).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Input alternativi](./8-alternative-inputs.md)
