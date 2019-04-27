---
title: Analisi sulla Knowledge Base
titleSuffix: Azure Cognitive Services
description: QnA Maker archivia tutti i log di chat e altri dati di telemetria se è stato attivato Application Insights durante la creazione del servizio QnA Maker. Eseguire le query di esempio per ottenere i log di chat da Application Insights.
services: cognitive-services
author: tulasim88
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/04/2019
ms.author: tulasim
ms.openlocfilehash: 09369e760c654e7e56067e6da02bb772bffa7400
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61372603"
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
        | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
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
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer" 
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
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Traffico degli utenti

```query
    //User Traffic
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
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
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | project timestamp, id, name, resultCode, performanceBucket, KbId
    | summarize count() by performanceBucket, KbId
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Gestire le chiavi](./key-management.md)
