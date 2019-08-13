---
title: 'Knowledge base: QnA Maker'
titleSuffix: Azure Cognitive Services
description: Una knowledge base QnA Maker è costituita da un set di coppie di domanda/risposta (QnA) e metadati facoltativi associati a ogni coppia QnA.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 022b16669791b9b9cce066b3dd17c70b33569cc0
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955246"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Che cos'è una knowledge base di QnA Maker?

Una knowledge base QnA Maker è costituita da un set di coppie di domanda/risposta (QnA) e metadati facoltativi associati a ogni coppia QnA.

## <a name="key-knowledge-base-concepts"></a>Concetti chiave della knowledge base

* **Domande** - Una domanda contiene un testo che meglio rappresenta una query dell'utente. 
* **Risposte** - Una risposta è la risposta restituita quando una query dell'utente viene confrontata con la domanda associata.  
* **Metadati** - I metadati sono tag associati a una coppia QnA e vengono rappresentati come coppie di chiave-valore. I tag di metadati vengono usati per filtrare le coppie di QnA e limitano il set su cui viene eseguita la query corrispondente.

Una singola QnA, rappresentata da un ID numerico di QnA, ha diverse varianti di una domanda ed eseguono il mapping di una sola risposta. Ogni coppia di questo tipo può inoltre avere più campi di metadati associati, ovvero una chiave e un valore.

![Knowledge base di QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Formato di contenuto della Knowledge Base

Quando si inseriscono contenuto avanzato in una knowledge base, QnA Maker prova a convertire il contenuto in un markdown. Leggere [questo](https://aka.ms/qnamaker-docs-markdown-support) blog per comprendere i formati markdown più comprensibili dai client chat.

I campi di metadati sono costituiti da coppie chiave-valore separate da due punti **(Prodotto: Shredder)** . Sia la chiave che il valore deve essere di solo testo. La chiave dei metadati non deve contenere spazi. I metadati supportano solo un valore per chiave.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Come QnA Maker elabora una query utente per selezionare la risposta migliore

La Knowledge base con formazione e [pubblicazione](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker riceve una query utente, da un bot o da un'altra applicazione client, nell' [API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). Il diagramma seguente illustra il processo di ricezione della query utente.

![Processo di classificazione per una query utente](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

Il processo è illustrato nella tabella seguente:

|Passaggio|Scopo|
|--|--|
|1|L'applicazione client invia la query utente all' [API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|QnA Maker pre-elaborazione della query utente con rilevamento della lingua, ortografia e Word breaker.|
|3|Questa pre-elaborazione viene eseguita per modificare la query utente per ottenere risultati di ricerca ottimali.|
|4|Questa query modificata viene inviata all'indice di ricerca di Azure, `top` ricevendo il numero di risultati. Se la risposta corretta non è presente in questi risultati, aumentare leggermente `top` il valore di. In genere il valore 10 per `top` funziona nel 90% delle query.|
|5|QnA Maker applica conteggi avanzati per determinare la correttezza dei risultati recuperati di ricerca di Azure per la query utente. |
|6|Il modello di classificazione con training usa il punteggio della funzionalità, dal passaggio 5, per classificare i risultati di ricerca di Azure.|
|7|I nuovi risultati vengono restituiti all'applicazione client in ordine di rango.|
|||

Le funzionalità usate includono ma non sono limitate alla semantica a livello di parola, alla priorità a livello di termine in un corpus e ai modelli semantici appresi in modo approfondito per determinare la somiglianza e la pertinenza tra due stringhe di testo.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ciclo di vita di sviluppo di una knowledge](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Vedere anche

[Panoramica di QnA Maker](../Overview/overview.md)
