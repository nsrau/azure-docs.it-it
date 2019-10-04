---
title: Come aggiungere entità con training preliminare a un modello di Conversation Learner - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Informazioni su come aggiungere entità con training preliminare a un modello di Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: fceca459b80725e1a9c8b7dbc6a4387ea98dbb7b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704131"
---
# <a name="how-to-add-pre-trained-entities"></a>Come aggiungere entità con training preliminare
Questa esercitazione illustra come aggiungere entità con training preliminare a un modello di Conversation Learner.

## <a name="video"></a>Video

[![Anteprima dell'esercitazione sulle entità con training preliminare](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione.

    npm run tutorial-general

## <a name="details"></a>Dettagli

Le entità con training preliminare riconoscono tipi comuni di entità, ad esempio numeri, date, importi monetari e altri tipi.  A differenza delle entità personalizzate, sono pronte da usare, non richiedono alcun tipo di training e il loro comportamento non può essere cambiato.  Per impostazione predefinita, le entità con training preliminare sono multivalore, vale a dire, accumulano ogni istanza identificata dell'entità.

## <a name="steps"></a>Passaggi

Per iniziare, passare alla home page nell'interfaccia utente Web.

### <a name="create-the-model"></a>Creare il modello

1. Selezionare **nuovo modello**.
2. Immettere **PretrainedEntities** per **nome**.
3. Selezionare **Create**.

### <a name="entity-creation"></a>Creazione di entità

1. Selezionare **entità** nel pannello a sinistra e quindi **nuova entità**.
2. Selezionare **pre-training/datetimeV2** per **tipo di entità**.
3. Controllare **multivalore** per consentire all'entità di accumulare uno o più valori. Si noti che le entità con training preliminare non possono essere negabili.
4. Selezionare **Create**.

![](../media/T08_entity_create.png)

1. Selezionare **Actions (azioni** ) nel pannello sinistro, quindi **nuova azione**.
2. Immettere **la data $builtin-datetimev2** per la **risposta del bot...** .
3. Selezionare **Create**.

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>Creare la seconda azione

1. Selezionare **Actions (azioni** ) nel pannello sinistro, quindi **nuova azione**.
2. Immettere **la data? per la** **risposta del bot...** . Le entità con training preliminare non possono essere **entità obbligatorie** perché sono riconosciute per impostazione predefinita per tutte le espressioni.
3. Immettere **Builtin-datetimev2** per la disattivazione delle **entità**.
4. Selezionare **Create**.

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>Eseguire il training del modello

1. Selezionare **Train Dialogs** nel riquadro sinistro, quindi **New Train Dialog**.
2. Immettere **Hello** per l'espressione dell'utente nel pannello Left chat.
3. Selezionare **azioni Punteggio**.
4. Selezionare **la data?** dall'elenco delle azioni
5. Immettere **Today** per l'espressione dell'utente nel pannello Left chat.
    - L' espressione odierna viene riconosciuta automaticamente da modelli con training preliminare in Luis.
    - Posizionando il mouse sui valori delle entità con training preliminare vengono visualizzati dati aggiuntivi forniti dal servizio LUIS.

![](../media/T08_training.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Resolver di entità](./09-entity-resolvers.md)
