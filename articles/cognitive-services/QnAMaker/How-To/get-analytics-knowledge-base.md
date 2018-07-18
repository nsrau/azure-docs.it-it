---
title: Come ottenere analisi sulla Knowledge Base - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Come ottenere analisi sulla Knowledge Base
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 1588d0c5a8eaf4e161b5319c9f33a772dc56b247
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376228"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Ottenere analisi sulla Knowledge Base

QnA Maker archivia tutti i log di chat e altri dati di telemetria se è stato attivato Application Insights durante la [creazione del servizio QnA Maker](./set-up-qnamaker-service-azure.md). Eseguire le query di esempio per ottenere i log di chat da Application Insights.

1. Passare alla risorsa Application Insights.

    ![Selezionare la risorsa Application Insights](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Selezionare **Analytics**. Si aprirà una nuova finestra in cui è possibile eseguire query sui dati di telemetria di QnA Maker.

    ![Selezionare Analytics](../media/qnamaker-how-to-analytics-kb/analytics.png)

3. Incollare la seguente query ed eseguirla.

    ```query
        requests
        | where url endswith "generateAnswer"
        | project timestamp, id, name, resultCode, duration
        | parse name with *"/knowledgebases/"KbId"/generateAnswer"
        | join kind= inner (
        traces | extend id = operation_ParentId
        ) on id
        | extend question = tostring(customDimensions['Question'])
        | extend answer = tostring(customDimensions['Answer'])
        | project KbId, timestamp, resultCode, duration, question, answer
    ```

    Selezionare **Esegui** per eseguire la query.

    ![Esegui query](../media/qnamaker-how-to-analytics-kb/run-query.png)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Eseguire query per ottenere altre analisi sulla Knowledge Base di QnA Maker

### <a name="total-90-day-traffic"></a>Traffico totale di 90 giorni

```query
    //Total Traffic
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by bin(timestamp, 1d), KbId
``` 

### <a name="total-question-traffic-in-a-given-time-period"></a>Traffico totale relativo alle domande in un determinato periodo di tempo

```query
    //Total Question Traffic in a given time period
    let startDate = todatetime('2018-02-18');
    let endDate = todatetime('2018-03-12');
    requests
    | where timestamp <= endDate and timestamp >=startDate
    | where url endswith "generateAnswer" and name startswith "POST" 
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Traffico degli utenti

```query
    //User Traffic
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration
    | parse name with *"/knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId 
    ) on id
    | extend UserId = tostring(customDimensions['UserId'])
    | summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Distribuzione della latenza delle domande

```query
    //Latency distribution of questions
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | project timestamp, id, name, resultCode, performanceBucket, KbId
    | summarize count() by performanceBucket, KbId
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Gestire le chiavi](./key-management.md)
