---
title: Eseguire una query sulla Knowledge Base - QnA Maker
description: È necessario pubblicare una Knowledge Base. Una volta pubblicata, la Knowledge Base viene eseguita una query nell'endpoint di stima di runtime utilizzando l'API generateAnswer.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb777aa16fada50811cce1bbf49f28662c62b49b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220721"
---
# <a name="query-the-knowledge-base-for-answers"></a>Eseguire una query sulla Knowledge Base per ottenere risposte

È necessario pubblicare una Knowledge Base. Una volta pubblicata, la Knowledge Base viene eseguita una query nell'endpoint di stima di runtime utilizzando l'API generateAnswer. La query include il testo della domanda e altre impostazioni per consentire a QnA Maker di selezionare la migliore corrispondenza possibile con una risposta.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Come QnA Maker elabora una query utente per selezionare la risposta migliore

La Knowledge Base QnA Maker pubblicata e [pubblicata](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) riceve una query utente, da un bot o da un'altra applicazione client, [nell'API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). Il diagramma seguente illustra il processo di ricezione della query utente.

![Processo del modello di classificazione per una query utente](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Processo di Ranker

Il processo è illustrato nella tabella seguente.

|Passaggio|Scopo|
|--|--|
|1|L'applicazione client invia la query utente [all'API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|QnA Maker pre-elabora la query dell'utente con il rilevamento della lingua, i correttori ortografici e i word breaker.|
|3|Questa pre-elaborazione viene eseguita per modificare la query utente per ottenere i migliori risultati di ricerca.|
|4|Questa query modificata viene inviata a un indice `top` di ricerca cognitivo di Azure, che riceve il numero di risultati. Se la risposta corretta non è in questi `top` risultati, aumentare il valore di leggermente. In genere, un valore `top` pari a 10 per le query nel 90% delle query.|
|5|QnA Maker utilizza la fattibilizzazione sintattica e semantica per determinare la somiglianza tra la query utente e i risultati QnA recuperati.|
|6|Il modello di classificazione appresa dalla macchina utilizza le diverse funzionalità, dal passaggio 5, per determinare i punteggi di confidenza e il nuovo ordine di classificazione.|
|7|I nuovi risultati vengono restituiti all'applicazione client in ordine di classificazione.|
|||

Le funzionalità utilizzate includono, ma non sono limitate alla semantica a livello di parola, all'importanza a livello di termine in un corpo e ai modelli semantici appresi approfonditi per determinare la somiglianza e la pertinenza tra due stringhe di testo.

## <a name="http-request-and-response-with-endpoint"></a>Richiesta e risposta HTTP con endpoint
Quando si pubblica la Knowledge Base, il servizio crea un endpoint HTTP basato su REST che può essere integrato nell'applicazione, in genere un bot di chat.

### <a name="the-user-query-request-to-generate-an-answer"></a>Richiesta di query utente per generare una risposta

Una query utente è la domanda che l'utente `How do I add a collaborator to my app?`finale chiede alla Knowledge Base, ad esempio . La query è spesso in un formato di linguaggio naturale `help with collaborators`o in alcune parole chiave che rappresentano la domanda, ad esempio . La query viene inviata alla Knowledge Base da una richiesta HTTP nell'applicazione client.

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

È possibile controllare la risposta impostando proprietà quali [scoreThreshold](./confidence-score.md#choose-a-score-threshold), [top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)e [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags).

Usa il [contesto](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) della conversazione con [funzionalità a più turni](../how-to/multiturn-conversation.md) per mantenere la conversazione in attiva operare per perfezionare le domande e le risposte, per trovare la risposta corretta e finale.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>La risposta da una chiamata per generare una risposta

La risposta HTTP è la risposta recuperata dalla Knowledge Base, in base alla corrispondenza migliore per una determinata query utente. La risposta include la risposta e il punteggio di stima. Se hai chiesto più di una `top` risposta top con la proprietà, ottieni più di una risposta top, ognuna con un punteggio.

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
> [Punteggio di confidenza](./confidence-score.md)
