---
title: Come testare una knowledge base - QnA Maker
titlesuffix: Azure Cognitive Services
description: Testare la Knowledge Base di QnA Maker è una parte importante di un processo iterativo per migliorare l'accuratezza delle risposte restituite. È possibile testare la Knowledge Base attraverso un'interfaccia di chat avanzata che consente anche di apportare modifiche.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/17/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: ea2381287729579e10d457912c4147f59716bcc4
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875703"
---
# <a name="test-your-knowledge-base-interactively-in-qna-maker"></a>Testare la knowledge base in modo interattivo in QnA Maker

Testare la Knowledge Base di QnA Maker è una parte importante di un processo iterativo per migliorare l'accuratezza delle risposte restituite. È possibile testare la Knowledge Base attraverso un'interfaccia di chat avanzata che consente anche di apportare modifiche.

## <a name="test-answer-matching"></a>Testare la corrispondenza delle risposte

1.  Accedere alla Knowledge Base selezionandone il nome nella pagina **My knowledge bases** (Knowledge Base personali).
2.  Per accedere al pannello a scorrimento Test, selezionare **Test** nel pannello superiore dell'applicazione.

    ![Accedere al pannello Test](../media/qnamaker-how-to-test-kb/access-test.png)

3.  Immettere una query nella casella di testo e premere Invio.

4.  Viene fornita la risposta della Knowledge Base con la migliore corrispondenza.

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

È possibile aggiungere una nuova risposta se una delle risposte esistenti individuate non è corretta o se la risposta non esiste nella Knowledge Base (non è stata trovata una buona corrispondenza nella Knowledge Base). Immettere la nuova risposta alla domanda corrente nella casella di testo e premere Invio per aggiungerla. 

Selezionare **Save and train** (Salva ed esegui training) per salvare questa risposta in modo permanente. Una nuova coppia di domanda/risposta è stata così aggiunta alla Knowledge Base.

![Aggiungere una nuova coppia di domanda e risposta](../media/qnamaker-how-to-test-kb/add-answer.png)

> [!NOTE]
> Tutte le modifiche alla Knowledge Base vengono salvate solo quando si fa clic sul pulsante  **Save and train** (Salva ed esegui training).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Pubblicare una knowledge base](./publish-knowledge-base.md)
