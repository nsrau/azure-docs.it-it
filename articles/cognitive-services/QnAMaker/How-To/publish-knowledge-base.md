---
title: Pubblicare una knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: La pubblicazione della Knowledge Base è il passaggio finale per rendere disponibile la Knowledge Base come endpoint di domande e risposte. Quando si pubblica una Knowledge Base, il contenuto di domande e risposte della Knowledge Base si sposta dall'indice di test a un indice di produzione in Ricerca di Azure.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: eceb0554d35935f1aee77c4c054cb7e65b327845
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033416"
---
# <a name="publish-a-knowledge-base"></a>Pubblicare una knowledge base

La pubblicazione della Knowledge Base è il passaggio finale per rendere disponibile la Knowledge Base come endpoint di domande e risposte. 

Quando si pubblica una Knowledge Base, il contenuto di domande e risposte della Knowledge Base si sposta dall'indice di test a un indice di produzione in Ricerca di Azure.

![Pubblicare l'indice di test di produzione](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

## <a name="publish-a-knowledge-base"></a>Pubblicare una knowledge base

1. Dopo aver apportato le modifiche alla Knowledge Base, selezionare **Pubblica** nella barra di spostamento in alto. È possibile pubblicare fino al numero di Knowledge Base assegnato per Ricerca di Azure. 

    ![Pubblicare una Knowledge Base](../media/qnamaker-how-to-publish-kb/publish.png)

2. Selezionare di nuovo **Pubblica** per visualizzare i dettagli dell'endpoint che possono essere usati nel codice dell'applicazione o del bot.

    ![Pubblicare una knowledge base](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ottenere analisi sulla Knowledge Base](./get-analytics-knowledge-base.md)
