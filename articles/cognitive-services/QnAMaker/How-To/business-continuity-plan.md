---
title: Piano di continuità aziendale - QnA Maker
titleSuffix: Azure Cognitive Services
description: L'obiettivo primario del piano di continuità aziendale è creare un endpoint di Knowledge Base resiliente che garantisca l'assenza di tempi di inattività per il bot o l'applicazione che ne fa uso.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 10d3809de590a79b6efa86e3d55fbbe535ea13b6
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413116"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Creare un piano di continuità aziendale per il servizio QnA Maker

L'obiettivo primario del piano di continuità aziendale è creare un endpoint di Knowledge Base resiliente che garantisca l'assenza di tempi di inattività per il bot o l'applicazione che ne fa uso.

![Piano di continuità aziendale QnA Maker](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

L'idea generale è la seguente:

1. Impostare due [servizi QnA Maker](../How-To/set-up-qnamaker-service-azure.md) paralleli in [aree associate di Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Mantenere sincronizzati gli indici di ricerca primario e secondario di Azure. Usare [questo](https://github.com/pchoudhari/QnAMakerBackupRestore) esempio di GitHub per vedere come eseguire il backup e il ripristino degli indici di Azure.

3. Eseguire il backup di Application Insights usando l'[esportazione continua](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. Una volta impostati gli stack primario e secondario, usare [Gestione traffico](https://docs.microsoft.com/azure/traffic-manager/) per configurare i due endpoint e impostare un metodo di routing.

5. È necessario creare un certificato SSL per l'endpoint di Gestione traffico. [Associare il certificato SSL](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl) nei servizi app.

6. Usare infine l'endpoint di Gestione traffico nel bot o nell'app.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scegliere la capacità per la distribuzione di QnA Maker](../Tutorials/choosing-capacity-qnamaker-deployment.md)
