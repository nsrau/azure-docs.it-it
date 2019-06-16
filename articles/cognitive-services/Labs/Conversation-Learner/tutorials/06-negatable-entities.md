---
title: Come usare le entità negabili con un modello di Conversation Learner - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare le entità negabili con un modello di Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 2c758d1dd5d4d1e7ab25faccd5280963211181d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388796"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Informazioni su come usare le entità negabili con un modello di Conversation Learner

Questa esercitazione illustra la proprietà "Negatable" (Negabile) delle entità.

## <a name="video"></a>Video

[![ Anteprima dell'esercitazione sulle entità negabili](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione.

    npm run tutorial-general

## <a name="details"></a>Dettagli
La proprietà "Negatable" di un'entità consente di etichettare entrambi normale (positivo) e negativi istanze dell'entità, insegnare basate sui modelli positivi e negativi e cancellare il valore di un'entità esistente. Le entità con il proprio set di proprietà "Negatable" sono chiamate entità negabili in Conversation Learner.

## <a name="steps"></a>Passaggi

Per iniziare, passare alla home page nell'interfaccia utente Web.

### <a name="create-the-model"></a>Creare il modello

1. Selezionare **nuovo modello**.
2. Immettere **NegatableEntity** per **nome**.
3. Selezionare **Create**.

### <a name="entity-creation"></a>Creazione di entità

1. Selezionare **Entities** nel riquadro sinistro, quindi **nuova entità**.
2. Selezionare **Custom sottoposto a training** per **tipo di entità**.
3. Immettere **name** per **nome entità**.
4. Controllare **Negatable** per consentire agli utenti di fornire un valore di entità, o si supponga che un elemento sia *non* un'entità valore eliminando il valore di entità corrispondente.
5. Selezionare **Create**.

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>Creare la prima azione

1. Selezionare **azioni** nel riquadro sinistro, quindi **nuova azione**.
2. Immettere **non si è certi del nome.** per **risposta del Bot...** .
3. Immettere **name** per **qualificato dà diritto**.
4. Selezionare **Create**.

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>Creare la seconda azione

1. Selezionare **azioni** nel riquadro sinistro, quindi **nuova azione**.
2. Immettere **conosce il nome. È $name.** per **risposta del Bot...** .
3. Selezionare **Create**.

> [!NOTE]
> Il **name** entità è stato automaticamente aggiunto come una **entità necessarie** per riferimento in utterance la risposta.

Sono ora disponibili due azioni.

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>Eseguire il training del modello

1. Selezionare **Train Dialogs** nel riquadro sinistro, quindi **nuova finestra di dialogo Train**.
2. Immettere **hello** per utterance dell'utente nel Pannello di chat a sinistra.
3. Selezionare **assegnare un punteggio azioni**.
4. Selezionare **non si è certi del nome.** Nell'elenco di azioni. Il percentile è 100% come l'unica azione valida in base ai vincoli.
5. Immettere **il mio nome è Frank** per utterance dell'utente nel Pannello di chat a sinistra.
6. Evidenziare **Frank** quindi selezionare **+ nome**. Le entità negabile dispongono di due istanze: (+) e aggiunge o sovrascrive il valore. (-) meno rimuove il valore.
7. Selezionare **assegnare un punteggio azioni**. Il **name** entità viene definita come **Frank** nella memoria del modello, pertanto la **conosce il nome. Si tratta $name** azione è disponibile.
8. Selezionare **conosce il nome. È $name.** Nell'elenco di azioni.
9. Immettere **il mio nome non è Frank.** per utterance dell'utente nel Pannello di chat a sinistra.
10. Evidenziare **Frank** quindi selezionare **-name** per cancellare il valore dal **nome** entità.
11. Selezionare **assegnare un punteggio azioni**.
12. Selezionare **non si è certi del nome.** Nell'elenco di azioni.
13. Immettere **il mio nome è Susan.** per utterance terza dell'utente nel Pannello di chat a sinistra.
14. Evidenziare **Susan** quindi **+ nome** 

![](../media/T06_training.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Entità multivalore](./07-multi-value-entities.md)
