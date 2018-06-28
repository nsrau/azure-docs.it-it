---
title: Usare i metadati nella knowledge base con l'API GenerateAnswer | Microsoft Docs
description: Uso dei metadati con l'API GenerateAnswer
services: cognitive-services
author: pchoudhari
manager: rsrikan
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/18/2018
ms.author: pchoudh
ms.openlocfilehash: 94e3632884d7033971ff1c45b455afb9a09ee798
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "35378776"
---
# <a name="using-metadata-and-the-generateanswer-api"></a>Uso dei metadati e dell'API GenerateAnswer

QnA Maker consente di aggiungere metadati, sotto forma di coppie chiave/valore, per i set di domande/risposte. Queste informazioni possono essere usate in diversi modi, ad esempio per filtrare i risultati per le query utente, aumentare la priorità di alcuni risultati e archiviare informazioni aggiuntive che possono essere usate nelle conversazioni successive. Per altre informazioni, vedere [Knowledge base](../Concepts/knowledge-base.md).

## <a name="qna-entity"></a>Entità di tipo Domande e risposte

Prima di tutto, è importante comprendere come vengono archiviati da QnA Maker i dati di domanda/risposta. La figura seguente mostra un'entità di tipo Domande e risposte:

![Entità di tipo Domande e risposte](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Ogni entità di tipo Domande e risposte ha un ID univoco e persistente. L'ID può essere usato per aggiornare un'entità di tipo Domande e risposte specifica.

## <a name="generateanswer-api"></a>API GenerateAnswer

Usare l'API GenerateAnswer nel bot o nell'applicazione per eseguire una query sulla knowledge base con una domanda di un utente, per ottenere la migliore corrispondenza dal set di domande/risposte.

### <a name="generateanswer-endpoint"></a>Endpoint GenerateAnswer

Una volta pubblicata la knowledge base, dal [portale di QnA Maker](https://www.qnamaker.ai) o usando l'[API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff), è possibile ottenere i dettagli dell'endpoint GenerateAnswer.

Per ottenere i dettagli dell'endpoint:
1. Accedere a [https://www.qnamaker.ai](https://www.qnamaker.ai).
2. In **My knowledge bases** (Knowledge base personali) fare clic su **View Code (Visualizza codice)** per la knowledge base.
![My knowledge bases](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png) (Knowledge base personali)
3. Ottenere i dettagli dell'endpoint GenerateAnswer.

![Dettagli dell'endpoint](../media/qnamaker-how-to-metadata-usage/view-code.png)

È anche possibile ottenere i dettagli dell'endpoint dalla scheda **Settings** (Impostazioni) della knowledge base.

### <a name="generateanswer-request"></a>Richiesta GenerateAnswer

È possibile chiamare GenerateAnswer con una richiesta HTTP POST. Per il codice di esempio che illustra come chiamare GenerateAnswer, vedere le [guide introduttive](../quickstarts/csharp.md).

- **URL della richiesta**: https://{QnA Maker endpoint}/knowledgebases/{knowledge base ID}/generateAnswer

- **Parametri della richiesta**: 
    - **Knowledge base ID** (stringa): GUID relativo alla knowledge base.
    - **QnAMaker endpoint** (stringa): nome host dell'endpoint distribuito nella sottoscrizione di Azure.
- **Intestazioni della richiesta**
    - **Content-Type** (stringa): tipo multimediale del corpo inviato all'API.
    - **Authorization** (stringa): chiave dell'endpoint.
- **Corpo della richiesta**
    - **question** (stringa): domanda dell'utente in base a cui eseguire la query sulla knowledge base.
    - **top** (facoltativo, numero intero): numero di risultati classificati da includere nell'output. Il valore predefinito è 1.
    - **userId** (facoltativo, stringa): ID univoco per identificare l'utente. Questo ID verrà registrato nei log di chat.
    - **strictFilters** (facoltativo, stringa): se specificato, indica a QnA Maker di restituire solo le risposte contenenti i metadati specificati. Per altre informazioni, vedere sotto.
    ```json
    {
        "question": "qna maker and luis",
        "top": 6,
        "strictFilters": [
        {
            "name": "category",
            "value": "api"
        }],
        "userId": "sd53lsY="
    }
    ```

### <a name="generateanswer-response"></a>Risposta GenerateAnswer

- **Risposta 200**: una chiamata riuscita restituisce il risultato della domanda. La risposta contiene i campi seguenti:
    - **answers**: elenco di risposte per la query utente, in ordine decrescente in base al punteggio di classificazione.
        - **score**: punteggio di classificazione, compreso tra 0 e 100.
        - **questions**: domande fornite dall'utente.
        - **source**: nome dell'origine da cui la risposta è stata estratta o salvata nella knowledge base.
        - **metadata**: metadati associati alla risposta.
            - name: nome dei metadati. (stringa, lunghezza massima: 100, obbligatorio)
            - value: valore dei metadati. (stringa, lunghezza massima: 100, obbligatorio)
        - **Id**: ID univoco assegnato alla risposta.
    ```json
    {
        "answers": [
            {
                "score": 28.54820341616869,
                "Id": 20,
                "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
                "source": "Custom Editorial",
                "questions": [
                    "How can I integrate LUIS with QnA Maker?"
                ],
                "metadata": [
                    {
                        "name": "category",
                        "value": "api"
                    }
                ]
            }
        ]
    }
    ```

## <a name="metadata-example"></a>Esempio di metadati

Prendere in considerazione i dati relativi alle domande frequenti seguenti per i ristoranti di Hyderabad. Aggiungere i metadati alla knowledge base facendo clic sull'icona a forma di ingranaggio.

![Aggiungere metadati](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

### <a name="filter-results-with-strictfilters"></a>Filtrare i risultati con strictFilters

Considerare la domanda utente "When does this hotel close?" per informazioni su quando chiude l'hotel, dove la finalità è implicitamente riferita al ristorante "Paradise".

Poiché sono richiesti i risultati solo per il ristorante "Paradise", è possibile impostare un filtro nella chiamata a GenerateAnswer sui metadati relativi al nome del ristorante, come indicato di seguito.

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

### <a name="keep-context"></a>Mantenere il contesto
La risposta a GenerateAnswer contiene le informazioni sui metadati corrispondenti del set di domande/risposte corrispondente, come indicato di seguito.

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

Queste informazioni possono essere usate per registrare il contesto della conversazione precedente da usare nelle conversazioni successive. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare una knowledge base](./create-knowledge-base.md)
