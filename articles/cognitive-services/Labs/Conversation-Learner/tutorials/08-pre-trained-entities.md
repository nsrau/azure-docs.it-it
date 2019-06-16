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
ms.openlocfilehash: fb70983c2f9fd20368bb8c6803c9568b27141af7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389268"
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

1. Selezionare **Entities** nel riquadro sinistro, quindi **nuova entità**.
2. Selezionare **Pre-Trained/datetimeV2** per **tipo di entità**.
3. Controllare **multivalore** per abilitare l'entità si accumulano uno o più valori. Si noti che non può essere negabile Pre-Trained entità.
4. Selezionare **Create**.

![](../media/T08_entity_create.png)

1. Selezionare **azioni** nel riquadro sinistro, quindi **nuova azione**.
2. Immettere **la data è $builtin-datetimev2** per **risposta del Bot...** .
3. Selezionare **Create**.

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>Creare la seconda azione

1. Selezionare **azioni** nel riquadro sinistro, quindi **nuova azione**.
2. Immettere **qual è la data?** per **risposta del Bot...** . Le entità con training preliminare non possono essere **le entità necessarie** come vengono riconosciute per impostazione predefinita per tutte le espressioni.
3. Immettere **builtin datetimev2** per **entità qualificato**.
4. Selezionare **Create**.

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>Eseguire il training del modello

1. Selezionare **Train Dialogs** nel riquadro sinistro, quindi **nuova finestra di dialogo Train**.
2. Immettere **hello** per utterance dell'utente nel Pannello di chat a sinistra.
3. Selezionare **assegnare un punteggio azioni**.
4. Selezionare **qual è la data?** nell'elenco di azioni
5. Immettere **oggi stesso** per utterance dell'utente nel Pannello di chat a sinistra.
    - Il **oggi stesso** utterance viene automaticamente riconosciuta da modelli con training preliminare di LUIS.
    - Posizionando il mouse sui valori delle entità con training preliminare vengono visualizzati dati aggiuntivi forniti dal servizio LUIS.

![](../media/T08_training.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Resolver di entità](./09-entity-resolvers.md)
