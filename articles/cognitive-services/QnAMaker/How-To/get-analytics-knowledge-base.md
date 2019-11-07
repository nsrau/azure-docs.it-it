---
title: Analytics su Knowledge base-QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker archivia tutti i log di chat e altri dati di telemetria se è stato attivato Application Insights durante la creazione del servizio QnA Maker. Eseguire le query di esempio per ottenere i log di chat da Application Insights.
services: cognitive-services
author: diberry
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: 72d2598c1ff17f80fc264e6d547a799ab74a163f
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621969"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Ottenere analisi sulla Knowledge Base

QnA Maker archivia tutti i log di chat e altri dati di telemetria se è stato attivato Application Insights durante la [creazione del servizio QnA Maker](./set-up-qnamaker-service-azure.md). Eseguire le query di esempio per ottenere i log di chat da Application Insights.

1. Passare alla risorsa Application Insights.

    ![Selezionare la risorsa Application Insights](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Selezionare **Log Analytics**. Si aprirà una nuova finestra in cui è possibile eseguire query sui dati di telemetria di QnA Maker.

3. Incollare la seguente query ed eseguirla.

    ```kusto
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, url, resultCode, duration, performanceBucket
    | parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId
    ) on id
    | extend question = tostring(customDimensions['Question'])
    | extend answer = tostring(customDimensions['Answer'])
    | extend score = tostring(customDimensions['Score'])
    | project timestamp, resultCode, duration, id, question, answer, score, performanceBucket,KbId
    ```

    Selezionare **Esegui** per eseguire la query.

    [![eseguire query per determinare domande, risposte e Punteggio dagli utenti](../media/qnamaker-how-to-analytics-kb/run-query.png)](../media/qnamaker-how-to-analytics-kb/run-query.png#lightbox)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Eseguire query per ottenere altre analisi sulla Knowledge Base di QnA Maker

### <a name="total-90-day-traffic"></a>Traffico totale di 90 giorni

```kusto
//Total Traffic
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer" 
| summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>Traffico totale relativo alle domande in un determinato periodo di tempo

```kusto
//Total Question Traffic in a given time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
requests
| where timestamp <= endDate and timestamp >=startDate
| where url endswith "generateAnswer" and name startswith "POST" 
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer" 
| summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Traffico degli utenti

```kusto
//User Traffic
requests
| where url endswith "generateAnswer"
| project timestamp, id, url, resultCode, duration
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId 
) on id
| extend UserId = tostring(customDimensions['UserId'])
| summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Distribuzione della latenza delle domande

```kusto
//Latency distribution of questions
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| project timestamp, id, name, resultCode, performanceBucket, KbId
| summarize count() by performanceBucket, KbId
```

### <a name="unanswered-questions"></a>Domande senza risposta

```kusto
// Unanswered questions
requests
| where url endswith "generateAnswer"
| project timestamp, id, url
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend question = tostring(customDimensions['Question'])
| extend answer = tostring(customDimensions['Answer'])
| extend score = tostring(customDimensions['Score'])
| where  score  == "0"
| project timestamp, KbId, question, answer, score 
| order  by timestamp  desc
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scegliere capactiy](../tutorials/choosing-capacity-qnamaker-deployment.md)
