---
title: Come testare una knowledge base - QnA Maker
description: Testare la Knowledge Base di QnA Maker è una parte importante di un processo iterativo per migliorare l'accuratezza delle risposte restituite. È possibile testare la Knowledge Base attraverso un'interfaccia di chat avanzata che consente anche di apportare modifiche.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: da4988ced0b077952ce64e6227d16e58d40ae329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927266"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Testare la Knowledge Base in QnA Maker

Testare la Knowledge Base di QnA Maker è una parte importante di un processo iterativo per migliorare l'accuratezza delle risposte restituite. È possibile testare la Knowledge Base attraverso un'interfaccia di chat avanzata che consente anche di apportare modifiche.

## <a name="interactively-test-in-qna-maker-portal"></a>Test interattivo nel portale QnA Maker

1. Accedere alla Knowledge Base selezionandone il nome nella pagina **My knowledge bases** (Knowledge Base personali).
1. Per accedere al pannello a scorrimento Test, selezionare **Test** nel pannello superiore dell'applicazione.
1. Immettere una query nella casella di testo e premere Invio.
1. Viene fornita la risposta della Knowledge Base con la migliore corrispondenza.

### <a name="clear-test-panel"></a>Cancellare il pannello Test

Per cancellare tutte le query di test inserite e i relativi risultati dalla console di test, selezionare **Ricomincia** nell'angolo in alto a sinistra del pannello Test.

### <a name="close-test-panel"></a>Chiudere il pannello Test

Per chiudere il pannello Test, selezionare nuovamente il pulsante **Test**. Quando il pannello Test è aperto, non è possibile modificare i contenuti della Knowledge Base.

### <a name="inspect-score"></a>Ispezionare il punteggio

È possibile ispezionare i dettagli dei risultati del test nel pannello Inspect (Ispeziona).

1.  Con il pannello a scorrimento Test aperto, selezionare **Inspect** (Ispeziona) per visualizzare altri dettagli sulla risposta.

    ![Ispezionare le risposte](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Verrà visualizzato il pannello Inspection (Ispeziona). Il pannello include la finalità con il punteggio più alto ed eventuali entità identificate. Il pannello mostra il risultato dell'espressione selezionata.

### <a name="correct-the-top-scoring-answer"></a>Correggere la risposta con il punteggio più alto

Se la risposta con il punteggio più alto non è corretta, selezionare la risposta corretta dall'elenco e selezionare **Save and Train** (Salva ed esegui training).

![Correggere la risposta con il punteggio più alto](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>Aggiungere domande alternative

È possibile aggiungere forme alternative di una domanda a una determinata risposta. Digitare le risposte alternative nella casella di testo e premere Invio per aggiungerle. Selezionare **Save and Train** (Salva ed esegui training) per archiviare gli aggiornamenti.

![Aggiungere domande alternative](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Aggiungere una nuova risposta

È possibile aggiungere una nuova risposta se una delle risposte esistenti individuate non è corretta o se la risposta non esiste nella Knowledge Base (non è stata trovata una buona corrispondenza nella Knowledge Base).

Nella parte inferiore dell'elenco delle risposte, utilizzare la casella di testo per immettere una nuova risposta e premere INVIO per aggiungerla.

Selezionare **Save and train** (Salva ed esegui training) per salvare questa risposta in modo permanente. Una nuova coppia di domanda/risposta è stata così aggiunta alla Knowledge Base.

> [!NOTE]
> Tutte le modifiche alla Knowledge Base vengono salvate solo quando si fa clic sul pulsante ** Save and train** (Salva ed esegui training).

### <a name="test-the-published-knowledge-base"></a>Testare la Knowledge Base pubblicata

È possibile testare la versione pubblicata della Knowledge Base nel riquadro dei test. Dopo aver pubblicato la KB, selezionare la casella **PUBLISHED KB** e inviare una query per ottenere i risultati dalla KB pubblicata.

![Test against a published KB](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>Test batch con strumento

Utilizzare lo strumento di test batch quando si desidera:

* determinare la risposta superiore e il punteggio per una serie di domande
* convalidare la risposta prevista per una serie di domande

Leggere [l'esercitazione](../Quickstarts/batch-testing.md) sui test batch per istruzioni dettagliate.

Il test batch viene fornito con lo strumento di test batch. Questo strumento è disponibile come [file eseguibile compresso](https://aka.ms/qnamakerbatchtestingtool) per il download o come [codice sorgente di C.](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting)

[La documentazione di riferimento relativa allo strumento](../reference-tsv-format-batch-testing.md) include:

* l'esempio della riga di comando dello strumento
* il formato per i file di input e file outfile TSV

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Pubblicare una knowledge base](./publish-knowledge-base.md)
