---
title: Come pubblicare una Knowledge Base - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Come pubblicare una Knowledge Base
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: e9dbeacfb0df98c6b8f084c263690c05fe966cdc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376169"
---
# <a name="publish-a-knowledge-base"></a>Pubblicare una knowledge base

La pubblicazione della Knowledge Base è il passaggio finale per rendere disponibile la Knowledge Base come endpoint di domande e risposte. 

Quando si pubblica una Knowledge Base, il contenuto di domande e risposte della Knowledge Base si sposta dall'indice di test a un indice di produzione in Ricerca di Azure.

![Pubblicare l'indice di test di produzione](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

## <a name="publish-a-knowledge-base"></a>Pubblicare una knowledge base

1. Dopo aver apportato le modifiche alla Knowledge Base, selezionare **Pubblica** nella barra di spostamento in alto. È possibile pubblicare fino al numero di Knowledge Base assegnato per Ricerca di Azure. 

    ![Pubblicare una Knowledge Base](../media/qnamaker-how-to-publish-kb/publish.png)

2. Selezionare di nuovo **Pubblica** per visualizzare i dettagli dell'endpoint che possono essere usati nel codice dell'applicazione o del bot.

    ![Pubblicare una Knowledge Base](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ottenere analisi sulla Knowledge Base](./get-analytics-knowledge-base.md)
