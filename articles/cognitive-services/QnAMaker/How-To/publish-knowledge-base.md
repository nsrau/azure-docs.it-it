---
title: Pubblicare una knowledge base
titleSuffix: QnA Maker API - Azure Cognitive Services
description: Pubblicare la knowledge base con il servizio API QnA Maker è il passaggio finale per rendere disponibili come endpoint di rispondere alle domanda la knowledge base. Quando si pubblica una Knowledge Base, il contenuto di domande e risposte della Knowledge Base si sposta dall'indice di test a un indice di produzione in Ricerca di Azure.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/11/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: c65654e7d6b2e66a07116ea9555ed316ace88912
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091954"
---
# <a name="publish-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Pubblicare una knowledge base tramite il portale di servizi API QnA Maker

Pubblicare la knowledge base con il servizio API QnA Maker è il passaggio finale per rendere disponibili come endpoint di rispondere alle domanda la knowledge base. 

Quando si pubblica una knowledge base, il contenuto di domande e risposte della knowledge base passa dall'indice di test a un indice di produzione in Ricerca di Azure.

![Pubblicare l'indice di test di produzione](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="publish-a-knowledge-base"></a>Pubblicare una knowledge base

1. Dopo aver apportato le modifiche alla Knowledge Base, selezionare **Pubblica** nella barra di spostamento in alto. È possibile pubblicare fino al numero di Knowledge Base assegnato per Ricerca di Azure. 

    ![Pubblicare una Knowledge Base](../media/qnamaker-how-to-publish-kb/publish.png)

2. Selezionare di nuovo **Pubblica** per visualizzare i dettagli dell'endpoint che possono essere usati nel codice dell'applicazione o del bot.

    ![Knowledge base pubblicata](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver completato la knowledge base, rimuoverla dal portale di QnA Maker.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ottenere analisi sulla Knowledge Base](./get-analytics-knowledge-base.md)
