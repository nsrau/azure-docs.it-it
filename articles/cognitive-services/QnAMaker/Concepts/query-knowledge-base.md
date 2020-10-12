---
title: Eseguire una query sulla Knowledge base-QnA Maker
description: È necessario pubblicare una Knowledge base. Una volta pubblicato, la Knowledge base viene sottoposta a query nell'endpoint di stima di runtime tramite l'API generateAnswer.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: e903714aab35de40c1179045505e1520c65b3ebc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776919"
---
# <a name="query-the-knowledge-base-for-answers"></a>Eseguire una query sulla Knowledge base per le risposte

È necessario pubblicare una Knowledge base. Una volta pubblicato, la Knowledge base viene sottoposta a query nell'endpoint di stima di runtime tramite l'API generateAnswer. La query include il testo della domanda e altre impostazioni per consentire QnA Maker di selezionare la migliore corrispondenza possibile a una risposta.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Come QnA Maker elabora una query utente per selezionare la risposta migliore

La Knowledge base con formazione e [pubblicazione](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker riceve una query utente, da un bot o da un'altra applicazione client, nell' [API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). Il diagramma seguente illustra il processo di ricezione della query utente.

![Processo del modello di classificazione per una query utente](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Processo di classificazione

Il processo è illustrato nella tabella seguente.

|Passaggio|Scopo|
|--|--|
|1|L'applicazione client invia la query utente all' [API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|QnA Maker pre-elabora la query utente con rilevamento della lingua, ortografia e Word breaker.|
|3|Questa pre-elaborazione viene eseguita per modificare la query utente per ottenere risultati di ricerca migliori.|
|4|Questa query modificata viene inviata a un indice di ricerca cognitiva di Azure, che riceve il `top` numero di risultati. Se la risposta corretta non è presente in questi risultati, aumentare leggermente il valore di `top` . In genere, il valore 10 per `top` funziona nel 90% delle query.|
|5|QnA Maker USA conteggi sintattico e semantico per determinare la somiglianza tra la query utente e i risultati QnA recuperati.|
|6|Il modello di classificazione Machine-learned usa le diverse funzionalità, dal passaggio 5, per determinare i punteggi di confidenza e il nuovo ordine di rango.|
|7|I nuovi risultati vengono restituiti all'applicazione client in ordine di rango.|
|||

Le funzionalità usate includono ma non sono limitate alla semantica a livello di parola, alla priorità a livello di termine in un corpus e ai modelli semantici appresi in modo approfondito per determinare la somiglianza e la pertinenza tra due stringhe di testo.

## <a name="http-request-and-response-with-endpoint"></a>Richiesta e risposta HTTP con endpoint
Quando si pubblica la Knowledge base, il servizio crea un endpoint HTTP basato su REST che può essere integrato nell'applicazione, in genere un bot di chat.

### <a name="the-user-query-request-to-generate-an-answer"></a>Richiesta di query utente per generare una risposta

Una query utente è la domanda che l'utente finale chiede alla Knowledge base, ad esempio `How do I add a collaborator to my app?` . La query è spesso in un formato di linguaggio naturale o alcune parole chiave che rappresentano la domanda, ad esempio `help with collaborators` . La query viene inviata alla Knowledge base da una richiesta HTTP nell'applicazione client.

```json
{
    "question": "How do I add a collaborator to my app?",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "QuestionType",
        "value": "Support"
    }],
    "userId": "sd53lsY="
}
```

Per controllare la risposta, impostare proprietà quali [scoreThreshold](./confidence-score.md#choose-a-score-threshold), [Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)e [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags).

Usare il [contesto di conversazione](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) con la funzionalità a più [turni](../how-to/multiturn-conversation.md) per continuare la conversazione per ridefinire le domande e le risposte, per trovare la risposta corretta e finale.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>Risposta da una chiamata per generare una risposta

La risposta HTTP è la risposta recuperata dalla Knowledge base, in base alla corrispondenza migliore per una query utente specificata. La risposta include la risposta e il Punteggio di stima. Se è stata richiesta più di una risposta top con la `top` proprietà, si ottiene più di una risposta top, ognuno con un punteggio.

```json
{
    "answers": [
        {
            "questions": [
                "How do I add a collaborator to my app?",
                "What access control is provided for the app?",
                "How do I find user management and security?"
            ],
            "answer": "Use the Azure portal to add a collaborator using Access Control (IAM)",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "QuestionType",
                    "value": "Support"
                },
                {
                    "name": "ToolDependency",
                    "value": "Azure Portal"
                }
            ]
        }
    ]
}
```


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Punteggio di attendibilità](./confidence-score.md)
