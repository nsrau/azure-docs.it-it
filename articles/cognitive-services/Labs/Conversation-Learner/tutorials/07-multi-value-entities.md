---
title: Come usare le entità multivalore con un modello di Conversation Learner - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare le entità multivalore con un modello di Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7911dd561da0f1e7b62b1b457ae5b059d5d54767
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704094"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Come usare le entità multivalore con un modello di Conversation Learner
Questa esercitazione illustra la proprietà "multivalore" dell'entità.

## <a name="video"></a>Video

[![Esercitazione sulle entità multivalore-anteprima](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione.

    npm run tutorial-general

## <a name="details"></a>Dettagli
Le entità multivalore accumulano i valori in un elenco, anziché archiviare un valore singolo.  Queste entità sono utili quando gli utenti possono specificare più valori. Ad esempio i condimenti su una pizza.

Per le entità contrassegnate come multivalore ogni istanza riconosciuta dell'entità sarà accodata a un elenco nella memoria del bot. I riconoscimenti successivi vengono accodati al valore dell'entità anziché sovrascritti.

## <a name="steps"></a>Passaggi

Per iniziare, passare alla home page nell'interfaccia utente Web.

### <a name="create-the-model"></a>Creare il modello

1. Selezionare **nuovo modello**.
2. Immettere **MultiValueEntities** per **nome**.
3. Selezionare **Create**.

### <a name="entity-creation"></a>Creazione di entità

1. Selezionare **entità** nel pannello a sinistra e quindi **nuova entità**.
2. Selezionare **personalizzato con training** per il **tipo di entità**.
3. Immettere i toping per **nome entità**.
4. Controllare **multivalore** per consentire all'entità di accumulare uno o più valori.
5. Controllareche sia negabile.
6. Selezionare **Create**.

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>Creare la prima azione

1. Selezionare **Actions (azioni** ) nel pannello sinistro, quindi **nuova azione**.
2. Immettere **qui i ping: $toppings** per la **risposta del bot...** . Il simbolo del dollaro principale indica un riferimento a un'entità.
3. Selezionare **Create**.

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>Creare la seconda azione

1. Selezionare **Actions (azioni** ) nel pannello sinistro, quindi **nuova azione**.
2. Immettere **i** risultati desiderati per **la risposta del bot...** .
3. Immettere i sovraping per i **diritti di esclusione**.
4. Selezionare **Create**.

Sono ora disponibili due azioni.

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>Eseguire il training del modello

1. Selezionare **Train Dialogs** nel riquadro sinistro, quindi **New Train Dialog**.
2. Immettere **Hi** per l'espressione dell'utente nel pannello Left chat.
3. Selezionare **azioni Punteggio**.
4. Selezionare i dati **che si** desidera eseguire nel ping dall'elenco delle azioni. Il percentile è il 100% come unica azione valida in base ai vincoli.
5. Immettere **Cheese e i funghi** per l'espressione dell'utente nel pannello sinistro della chat.
6. Evidenziare **Cheese** , quindi selezionare **+** overpings.
7. Evidenziare i **funghi** e quindi selezionare **+ proping**.
8. Selezionare **azioni Punteggio**.
9. Selezionare **qui i ping: $toppings** dall'elenco delle azioni.
10. Immettere **Aggiungi pepe** per l'espressione successiva dell'utente nel pannello sinistro della chat.
11. Evidenziare **pepe** e quindi selezionare **+ toping**.
12. Selezionare **azioni Punteggio**.
13. Selezionare **qui i ping: $toppings** dall'elenco delle azioni.
14. Immettere **Remove Cheese** per il terzo enunciato dell'utente nel pannello Left chat.
15. Evidenziare **Cheese** , quindi selezionare **-** topings.
16. Selezionare **azioni Punteggio**.
17. Selezionare **qui i ping: $toppings** dall'elenco delle azioni.

![](../media/T07_training.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Entità con training preliminare](./08-pre-trained-entities.md)
