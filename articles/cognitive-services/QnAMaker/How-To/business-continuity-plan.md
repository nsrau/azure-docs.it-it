---
title: Creare un piano di continuità aziendale per il servizio QnA Maker - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Come creare un piano di continuità aziendale per il servizio QnA Maker
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: ca6e54b8a8ca8b38e8ef6b1a148f8b2c54bd43da
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376209"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Creare un piano di continuità aziendale per il servizio QnA Maker

L'obiettivo primario del piano di continuità aziendale è creare un endpoint di Knowledge Base resiliente che garantisca l'assenza di tempi di inattività per il bot o l'applicazione che ne fa uso.

![Piano di continuità aziendale QnA Maker](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

L'idea generale è la seguente:

1. Impostare due [servizi QnA Maker](../How-To/set-up-qnamaker-service-azure.md) paralleli in [aree associate di Azure](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions).

2. Mantenere sincronizzati gli indici di ricerca primario e secondario di Azure. Usare [questo](https://github.com/pchoudhari/QnAMakerBackupRestore) esempio GitHub per vedere come eseguire il backup e il ripristino degli indici di Azure.

3. Eseguire il backup di Application Insights usando l'[esportazione continua](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-export-telemetry).

4. Una volta impostati gli stack primario e secondario, usare [Gestione traffico](https://docs.microsoft.com/en-us/azure/traffic-manager/) per configurare i due endpoint e impostare un metodo di routing.

5. È necessario creare un certificato SSL per l'endpoint di Gestione traffico. [Associare il certificato SSL](https://docs.microsoft.com/en-us/azure/app-service/app-service-web-tutorial-custom-ssl) nei servizi app.

6. Usare infine l'endpoint di Gestione traffico nel bot o nell'app.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scegliere la capacità per la distribuzione di QnA Maker](../Tutorials/choosing-capacity-qnamaker-deployment.md)
