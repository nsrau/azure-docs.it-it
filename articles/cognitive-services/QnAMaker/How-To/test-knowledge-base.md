---
title: Come testare una knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: Testare la Knowledge Base di QnA Maker è una parte importante di un processo iterativo per migliorare l'accuratezza delle risposte restituite. È possibile testare la Knowledge Base attraverso un'interfaccia di chat avanzata che consente anche di apportare modifiche.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/08/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 6a512098d5dfda47b7755e24b286aabf83aa7e69
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563077"
---
# <a name="test-your-knowledge-base-interactively-in-qna-maker"></a>Testare la knowledge base in modo interattivo in QnA Maker

Testare la Knowledge Base di QnA Maker è una parte importante di un processo iterativo per migliorare l'accuratezza delle risposte restituite. È possibile testare la Knowledge Base attraverso un'interfaccia di chat avanzata che consente anche di apportare modifiche.

## <a name="test-answer-matching"></a>Testare la corrispondenza delle risposte

1. Accedere alla Knowledge Base selezionandone il nome nella pagina **My knowledge bases** (Knowledge Base personali).
1. Per accedere al pannello a scorrimento Test, selezionare **Test** nel pannello superiore dell'applicazione.
1. Immettere una query nella casella di testo e premere Invio.
1. Viene fornita la risposta della Knowledge Base con la migliore corrispondenza.

## <a name="clear-test-panel"></a>Cancellare il pannello Test

Per cancellare tutte le query di test inserite e i relativi risultati dalla console di test, selezionare **Ricomincia** nell'angolo in alto a sinistra del pannello Test.

## <a name="close-test-panel"></a>Chiudere il pannello Test

Per chiudere il pannello Test, selezionare nuovamente il pulsante **Test**. Quando il pannello Test è aperto, non è possibile modificare i contenuti della Knowledge Base.

## <a name="inspect-score"></a>Ispezionare il punteggio

È possibile ispezionare i dettagli dei risultati del test nel pannello Inspect (Ispeziona).

1.  Con il pannello a scorrimento Test aperto, selezionare **Inspect** (Ispeziona) per visualizzare altri dettagli sulla risposta.

    ![Ispezionare le risposte](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Verrà visualizzato il pannello Inspection (Ispeziona). Il pannello include la finalità con il punteggio più alto ed eventuali entità identificate. Il pannello mostra il risultato dell'espressione selezionata.

## <a name="correct-the-top-scoring-answer"></a>Correggere la risposta con il punteggio più alto

Se la risposta con il punteggio più alto non è corretta, selezionare la risposta corretta dall'elenco e selezionare **Save and Train** (Salva ed esegui training).

![Correggere la risposta con il punteggio più alto](../media/qnamaker-how-to-test-kb/choose-answer.png)

## <a name="add-alternate-questions"></a>Aggiungere domande alternative

È possibile aggiungere forme alternative di una domanda a una determinata risposta. Digitare le risposte alternative nella casella di testo e premere Invio per aggiungerle. Selezionare **Save and Train** (Salva ed esegui training) per archiviare gli aggiornamenti.

![Aggiungere domande alternative](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

## <a name="add-a-new-answer"></a>Aggiungere una nuova risposta

È possibile aggiungere una nuova risposta se una delle risposte esistenti individuate non è corretta o se la risposta non esiste nella Knowledge Base (non è stata trovata una buona corrispondenza nella Knowledge Base). 

Nella parte inferiore dell'elenco risposte utilizzare la casella di testo per immettere una nuova risposta e premere INVIO per aggiungerla. 

Selezionare **Save and train** (Salva ed esegui training) per salvare questa risposta in modo permanente. Una nuova coppia di domanda/risposta è stata così aggiunta alla Knowledge Base. 

> [!NOTE]
> Tutte le modifiche alla Knowledge Base vengono salvate solo quando si fa clic sul pulsante  **Save and train** (Salva ed esegui training).

## <a name="test-the-published-knowledge-base"></a>Testare la Knowledge base pubblicata

È possibile testare la versione pubblicata della Knowledge base nel riquadro test. Dopo aver pubblicato la Knowledge base, selezionare la casella **KB pubblicati** e inviare una query per ottenere risultati dalla KB pubblicata.

![Eseguire test su una KB pubblicata](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Pubblicare una knowledge base](./publish-knowledge-base.md)
