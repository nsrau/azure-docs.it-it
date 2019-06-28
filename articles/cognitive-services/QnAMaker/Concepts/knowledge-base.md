---
title: 'Knowledge base: QnA Maker'
titleSuffix: Azure Cognitive Services
description: Una knowledge base QnA Maker è costituita da un set di coppie di domanda/risposta (QnA) e metadati facoltativi associati a ogni coppia QnA.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b9562a1686c4de4f4e2ef57a7d91bbf18dce63ef
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447585"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Che cos'è una knowledge base di QnA Maker?

Una knowledge base QnA Maker è costituita da un set di coppie di domanda/risposta (QnA) e metadati facoltativi associati a ogni coppia QnA.

## <a name="key-knowledge-base-concepts"></a>Concetti chiave della knowledge base

* **Domande** - Una domanda contiene un testo che meglio rappresenta una query dell'utente. 
* **Risposte** - Una risposta è la risposta restituita quando una query dell'utente viene confrontata con la domanda associata.  
* **Metadati** - I metadati sono tag associati a una coppia QnA e vengono rappresentati come coppie di chiave-valore. I tag di metadati vengono usati per filtrare le coppie di QnA e limitano il set su cui viene eseguita la query corrispondente.

Una singola QnA, rappresentata da un ID numerico di QnA, ha diverse varianti di una domanda ed eseguono il mapping di una sola risposta. Inoltre, ogni coppia di questo tipo può avere più campi di metadati associati: una chiave e un valore.

![Knowledge base di QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Formato di contenuto della Knowledge Base

Quando si inseriscono contenuto avanzato in una knowledge base, QnA Maker prova a convertire il contenuto in un markdown. Leggere [questo](https://aka.ms/qnamaker-docs-markdown-support) blog per comprendere i formati markdown più comprensibili dai client chat.

I campi di metadati sono costituiti da coppie chiave-valore separate da due punti **(Prodotto: Shredder)** . Sia la chiave che il valore deve essere di solo testo. La chiave dei metadati non deve contenere spazi. Metadati supportano un solo valore per ogni chiave.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Modalità di elaborazione di una query dell'utente per selezionare la risposta migliore QnA Maker

Il modello con training e [pubblicata](/quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) QnA Maker della knowledge base riceve una query dell'utente, da un robot o un'altra applicazione client, nelle [API GenerateAnswer](/how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api). Il diagramma seguente illustra il processo quando viene ricevuta la query dell'utente.

![Il processo di classificazione per una query dell'utente](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

Il processo è illustrato nella tabella seguente:

|Passaggio|Scopo|
|--|--|
|1|L'applicazione client invia la query dell'utente per il [GenerateAnswer API](/how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api).|
|2|QnA Maker di pre-elaborazione della query utente con il rilevamento della lingua, spellers e word breaker.|
|3|Questa pre-elaborazione viene eseguita per modificare la query dell'utente per ottenere risultati ottimali ricerca.|
|4|Questa query modificata viene inviata all'indice di ricerca di Azure, la ricezione di `top` numero di risultati. Se la risposta corretta non è in questi risultati, aumentare il valore di `top` leggermente. In genere un valore pari a 10 per `top` funziona in 90% delle query.|
|5|QnA Maker applica definizione delle funzionalità avanzate per determinare la correttezza dei risultati della ricerca di Azure recuperati per query dell'utente. |
|6|Il modello con training permettendoti viene utilizzato il punteggio delle funzionalità, nel passaggio 5, per classificare i risultati di ricerca di Azure.|
|7|I nuovi risultati vengono restituiti all'applicazione client in ordine di rango.|
|||

Le funzionalità utilizzate includono ma non sono limitate a word a livello di semantica, termine a livello di importanza in una raccolta e deep i modelli semantici acquisiti per determinare analogie e pertinenza tra due stringhe di testo.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ciclo di vita di sviluppo di una knowledge](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Vedere anche

[Panoramica di QnA Maker](../Overview/overview.md)
