---
title: 'Knowledge base: QnA Maker'
titleSuffix: Azure Cognitive Services
description: Una knowledge base QnA Maker è costituita da un set di coppie di domanda/risposta (QnA) e metadati facoltativi associati a ogni coppia QnA.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/18/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: d036b93bc043f74dedae24455ca0a43cd808a936
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227115"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Che cos'è una knowledge base di QnA Maker?

Una knowledge base QnA Maker è costituita da un set di coppie di domanda/risposta (QnA) e metadati facoltativi associati a ogni coppia QnA.

## <a name="key-knowledge-base-concepts"></a>Concetti chiave della knowledge base

* **Domande** - Una domanda contiene un testo che meglio rappresenta una query dell'utente. 
* **Risposte** - Una risposta è la risposta restituita quando una query dell'utente viene confrontata con la domanda associata.  
* **Metadati** - I metadati sono tag associati a una coppia QnA e vengono rappresentati come coppie di chiave-valore. I tag di metadati vengono usati per filtrare le coppie di QnA e limitano il set su cui viene eseguita la query corrispondente.

Una singola QnA, rappresentata da un ID numerico di QnA, ha diverse varianti di una domanda ed eseguono il mapping di una sola risposta. Inoltre, ogni coppia di questo tipo può avere più campi di metadati associati.

![Knowledge base di QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Formato di contenuto della Knowledge Base

Quando si inseriscono contenuto avanzato in una knowledge base, QnA Maker prova a convertire il contenuto in un markdown. Leggere [questo](https://aka.ms/qnamaker-docs-markdown-support) blog per comprendere i formati markdown più comprensibili dai client chat.

I campi di metadati sono costituiti da coppie chiave-valore separate da due punti **(Prodotto: Shredder)**. Sia la chiave che il valore deve essere di solo testo. La chiave dei metadati non deve contenere spazi.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ciclo di vita di sviluppo di una knowledge](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Vedere anche 

[Panoramica di QnA Maker](../Overview/overview.md)
