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
ROBOTS: NOINDEX
ms.openlocfilehash: 4edbfc396d4ab32b991b084a7b738f04b5205418
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704115"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Informazioni su come usare le entità negabili con un modello di Conversation Learner

Questa esercitazione illustra la proprietà "Negatable" (Negabile) delle entità.

## <a name="video"></a>Video

[![ Anteprima dell'esercitazione sulle entità negabili](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione.

    npm run tutorial-general

## <a name="details"></a>Dettagli
La proprietà "negabile" di un'entità consente di etichettare sia le istanze normali (positive) che negative dell'entità, insegnare in base ai modelli positivi e negativi e cancellare il valore di un'entità esistente. Le entità con il proprio set di proprietà "Negatable" sono chiamate entità negabili in Conversation Learner.

## <a name="steps"></a>Passaggi

Per iniziare, passare alla home page nell'interfaccia utente Web.

### <a name="create-the-model"></a>Creare il modello

1. Selezionare **nuovo modello**.
2. Immettere **NegatableEntity** per **nome**.
3. Selezionare **Create**.

### <a name="entity-creation"></a>Creazione di entità

1. Selezionare **entità** nel pannello a sinistra e quindi **nuova entità**.
2. Selezionare **personalizzato con training** per il **tipo di entità**.
3. Immettere il **nome** per il **nome dell'entità**.
4. Controllare che sia negabile per consentire agli utenti di fornire un valore di entità o che un elemento *non* sia un valore di entità, eliminando in tal modo il valore dell'entità corrispondente.
5. Selezionare **Create**.

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>Creare la prima azione

1. Selezionare **Actions (azioni** ) nel pannello sinistro, quindi **nuova azione**.
2. Immettere non **conosco il nome.** per **la risposta del bot...**
3. Immettere il **nome** per l' **esclusione dei diritti**.
4. Selezionare **Create**.

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>Creare la seconda azione

1. Selezionare **Actions (azioni** ) nel pannello sinistro, quindi **nuova azione**.
2. Immettere **il nome dell'utente. È $name.** per **la risposta del bot...**
3. Selezionare **Create**.

> [!NOTE]
> L'entità **Name** è stata aggiunta automaticamente come **entità obbligatorie** per riferimento nell'espressione di risposta.

Sono ora disponibili due azioni.

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>Eseguire il training del modello

1. Selezionare **Train Dialogs** nel riquadro sinistro, quindi **New Train Dialog**.
2. Immettere **Hello** per l'espressione dell'utente nel pannello Left chat.
3. Selezionare **azioni Punteggio**.
4. Selezionare non **conosco il nome.** dall'elenco delle azioni. Il percentile è il 100% come unica azione valida in base ai vincoli.
5. Immettere il **nome è Frank** per l'espressione dell'utente nel pannello Left chat.
6. Evidenziare **Frank** , quindi selezionare **+ nome**. Le entità negabili hanno due istanze: (+) più aggiunge o sovrascrive il valore; (-) la sottrazione del valore.
7. Selezionare **azioni Punteggio**. L'entità **Name** ora è definita come **Frank** nella memoria del modello, quindi il **noto il nome. È $Name** azione è disponibile.
8. Selezionare **il nome dell'utente. È $name.** dall'elenco delle azioni.
9. Immettere **il nome non è Frank.** per l'espressione dell'utente nel pannello sinistro della chat.
10. Evidenziare **Frank** , quindi selezionare **-Name** per cancellare il valore dall'entità **Name** .
11. Selezionare **azioni Punteggio**.
12. Selezionare non **conosco il nome.** dall'elenco delle azioni.
13. Immettere **il nome Susan.** per il terzo enunciato dell'utente nel pannello Left chat.
14. Evidenziare **Susan** **+ nome** 

![](../media/T06_training.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Entità multivalore](./07-multi-value-entities.md)
