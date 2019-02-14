---
title: Pubblicare una knowledge base
titleSuffix: QnA Maker - Azure Cognitive Services
description: La pubblicazione della Knowledge Base è il passaggio finale per rendere disponibile la Knowledge Base come endpoint di domande e risposte. Quando si pubblica una Knowledge Base, il contenuto di domande e risposte della Knowledge Base si sposta dall'indice di test a un indice di produzione in Ricerca di Azure.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/11/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 585e5f8f955a405b08bef42588ab5caff71c7bba
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55865914"
---
# <a name="publish-a-knowledge-base-using-the-qna-maker-portal"></a>Pubblicare una knowledge base con il portale di QnA Maker

La pubblicazione della propria knowledge base è il passaggio finale per renderla disponibile come endpoint di domande e risposte a un'applicazione client. 

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
