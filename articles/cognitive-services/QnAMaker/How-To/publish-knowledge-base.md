---
title: Pubblicare una knowledge base
titleSuffix: QnA Maker - Azure Cognitive Services
description: La pubblicazione della propria knowledge base è il passaggio finale per renderla disponibile come endpoint di domande e risposte. Quando si pubblica una knowledge base, il contenuto di domande e risposte della knowledge base passa dall'indice di test a un indice di produzione in Ricerca di Azure.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 12/11/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: deeebd99425bc1ec1ed2ae76ef4852119a9c10ea
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53342443"
---
# <a name="publish-a-knowledge-base-using-the-qna-maker-portal"></a>Pubblicare una knowledge base con il portale di QnA Maker

La pubblicazione della propria knowledge base è il passaggio finale per renderla disponibile come endpoint di domande e risposte a un'applicazione client. 

Quando si pubblica una knowledge base, il contenuto di domande e risposte della knowledge base passa dall'indice di test a un indice di produzione in Ricerca di Azure.

![Pubblicare l'indice di test di produzione](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="publish-a-knowledge-base"></a>Pubblicare una knowledge base

1. Dopo aver apportato le modifiche alla knowledge base, selezionare **Pubblica** nella barra di spostamento in alto. È possibile pubblicare fino al numero di knowledge base assegnato per Ricerca di Azure. 

    ![Pubblicare una knowledge base](../media/qnamaker-how-to-publish-kb/publish.png)

2. Selezionare di nuovo **Pubblica** per visualizzare i dettagli dell'endpoint che possono essere usati nel codice dell'applicazione o del bot.

    ![Knowledge base pubblicata](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver completato la knowledge base, rimuoverla dal portale di QnA Maker.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ottenere analisi sulla Knowledge Base](./get-analytics-knowledge-base.md)
