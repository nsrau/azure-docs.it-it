---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: f3a1a33b2fe859839deec587191b3b3a319c0cf8
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77495369"
---
Questa guida introduttiva basata su cURL assiste nell'ottenimento di una risposta dalla knowledge base.

## <a name="prerequisites"></a>Prerequisites

* [**cURL**](https://curl.haxx.se/) più recente.
* È necessario avere
    * Un [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md)
    * Una knowledge base con domande e risposte pubblicata e di cui è stato eseguito il training creata nell'argomento di [avvio rapido](../Quickstarts/add-question-metadata-portal.md) precedente configurata con metadati e chit chat.

> [!NOTE]
> Una volta pronti a generare una risposta per una domanda dalla knowledge base, è necessario [eseguire il training](../Quickstarts/create-publish-knowledge-base.md#save-and-train) e [pubblicare](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) la knowledge base. Una volta pubblicata la knowledge base, nella pagina **Pubblica** vengono visualizzate le impostazioni della richiesta HTTP per generare una risposta. La scheda **cURL** mostra le impostazioni necessarie per generare una risposta dallo strumento da riga di comando.

## <a name="use-metadata-to-filter-answer"></a>Usare i metadati per filtrare una risposta

Usare la knowledge base dell'argomento di avvio rapido precedente per eseguire una query per ottenere una risposta in base ai metadati.

1. Nella pagina **Impostazioni** della knowledge base selezionare la scheda **CURL** per visualizzare un esempio di comando cURL usato per generare una risposta dalla knowledge base.
1. Copiare il comando in un ambiente modificabile (come un file di testo) in modo da poterlo modificare. Modificare il valore della domanda come indicato di seguito in modo che i metadati di `service:qna_maker` vengano usati come filtro per i set di domande e risposte.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```

    La domanda è costituita solo da una singola parola, `size`, che può restituire uno dei due set di domande e risposte. La matrice `strictFilters` indica di limitare le risposte a quelle con tag `qna_maker`.

1. La risposta include solo la risposta che soddisfa i criteri di filtro. La risposta cURL seguente è stata formattata per migliorare la leggibilità:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Se è presente un set di domande e risposte che non soddisfa il termine di ricerca ma soddisfa il filtro, non viene restituito. Viene invece restituita la risposta generale `No good match found in KB.`.

## <a name="use-debug-query-property"></a>Usare una proprietà per la query di debug

Le informazioni di debug consentono di capire come è stata determinata la risposta restituita. Sebbene siano utili, non sono necessarie. Per generare una risposta con le informazioni di debug, aggiungere la proprietà `debug`:

```json
Debug: {Enable:true}
```

1. Modificare il comando cURL in modo da includere la proprietà di debug per visualizzare altre informazioni.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'Debug':{'Enable':true}}"
    ```

1. La risposta include le informazioni pertinenti alla risposta. Nel seguente output JSON, alcuni dettagli di debug sono stati sostituiti con i puntini di sospensione per brevità.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) to learn how to collaborate on a knowledge base.",
                "score": 56.07,
                "id": 5,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": {
            "userQuery": {
                "question": "How do I programmatically update my Knowledge Base?",
                "top": 1,
                "userId": null,
                "strictFilters": [],
                "isTest": false,
                "debug": {
                    "enable": true,
                    "recordL1SearchLatency": false,
                    "mockQnaL1Content": null
                },
                "rankerType": 0,
                "context": null,
                "qnaId": 0,
                "scoreThreshold": 0.0
            },
            "rankerInfo": {
                "specialFuzzyQuery": "how do i programmatically~6 update my knowledge base",
                "synonyms": "what s...",
                "rankerLanguage": "English",
                "rankerFileName": "https://qnamakerstore.blob.core.windows.net/qnamakerdata/rankers/ranker-English.ini",
                "rankersDirectory": "D:\\home\\site\\wwwroot\\Data\\QnAMaker\\rd0003ffa60fc45.24.0\\RankerData\\Rankers",
                "allQnAsfeatureValues": {
                    "WordnetSimilarity": {
                        "5": 0.54706300120043716,...
                    },
                    ...
                },
                "rankerVersion": "V2",
                "rankerModelType": "TreeEnsemble",
                "rankerType": 0,
                "indexResultsCount": 25,
                "reRankerResultsCount": 1
            },
            "runtimeVersion": "5.24.0",
            "indexDebugInfo": {
                "indexDefinition": {
                    "name": "064a4112-bd65-42e8-b01d-141c4c9cd09e",
                    "fields": [...
                    ],
                    "scoringProfiles": [],
                    "defaultScoringProfile": null,
                    "corsOptions": null,
                    "suggesters": [],
                    "analyzers": [],
                    "tokenizers": [],
                    "tokenFilters": [],
                    "charFilters": [],
                    "@odata.etag": "\"0x8D7A920EA5EE6FE\""
                },
                "qnaCount": 117,
                "parameters": {},
                "azureSearchResult": {
                    "continuationToken": null,
                    "@odata.count": null,
                    "@search.coverage": null,
                    "@search.facets": null,
                    "@search.nextPageParameters": null,
                    "value": [...],
                    "@odata.nextLink": null
                }
            },
            "l1SearchLatencyInMs": 0,
            "qnaL1Results": {...}
        },
        "activeLearningEnabled": true
    }
    ```

## <a name="use-test-knowledge-base"></a>Usare la knowledge base di test

Se si vuole ottenere una risposta dalla knowledge base di test, usare la proprietà del corpo `isTest`.

La proprietà è un valore booleano.

```json
isTest:true
```

Il comando cURL è simile al seguente:

```bash
curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'IsTest':true}"
```

La risposta JSON usa lo stesso schema della query della knowledge base pubblicata.

> [!NOTE]
> Se la knowledge base pubblicata e quella di test sono esattamente le stesse, potrebbero esserci ancora alcune lievi variazioni perché l'indice di test è condiviso tra tutte le knowledge base della risorsa.

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Usare cURL per eseguire una query per una risposta Chit-chat

1. Nel terminale abilitato per cURL usare una frase dell'utente del bot di fine conversazione, ad esempio `Thank you` come domanda. Non sono presenti altre proprietà da impostare.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'thank you'}"
    ```

1. Eseguire il comando cURL e ricevere la risposta JSON, che include il punteggio e la risposta.

    ```json
    {
      "answers": [
          {
              "questions": [
                  "I thank you",
                  "Oh, thank you",
                  "My sincere thanks",
                  "My humblest thanks to you",
                  "Marvelous, thanks",
                  "Marvelous, thank you kindly",
                  "Marvelous, thank you",
                  "Many thanks to you",
                  "Many thanks",
                  "Kthx",
                  "I'm grateful, thanks",
                  "Ahh, thanks",
                  "I'm grateful for that, thank you",
                  "Perfecto, thanks",
                  "I appreciate you",
                  "I appreciate that",
                  "I appreciate it",
                  "I am very thankful for that",
                  "How kind, thank you",
                  "Great, thanks",
                  "Great, thank you",
                  "Gracias",
                  "Gotcha, thanks",
                  "Gotcha, thank you",
                  "Awesome thanks!",
                  "I'm grateful for that, thank you kindly",
                  "thank you pal",
                  "Wonderful, thank you!",
                  "Wonderful, thank you very much",
                  "Why thank you",
                  "Thx",
                  "Thnx",
                  "That's very kind",
                  "That's great, thanks",
                  "That is lovely, thanks",
                  "That is awesome, thanks!",
                  "Thanks bot",
                  "Thanks a lot",
                  "Okay, thanks!",
                  "Thank you so much",
                  "Perfect, thanks",
                  "Thank you my friend",
                  "Thank you kindly",
                  "Thank you for that",
                  "Thank you bot",
                  "Thank you",
                  "Right on, thanks very much",
                  "Right on, thanks a lot",
                  "Radical, thanks",
                  "Rad, thanks",
                  "Rad thank you",
                  "Wonderful, thanks!",
                  "Thanks"
              ],
              "answer": "You're welcome.",
              "score": 100.0,
              "id": 75,
              "source": "qna_chitchat_Professional.tsv",
              "metadata": [
                  {
                      "name": "editorial",
                      "value": "chitchat"
                  }
              ],
              "context": {
                  "isContextOnly": false,
                  "prompts": []
              }
          }
      ],
      "debugInfo": null,
      "activeLearningEnabled": true
    }
    ```

    Poiché la domanda di `Thank you` corrisponde esattamente a una domanda Chit chat, QnA Maker è assolutamente attendibile con il punteggio di 100. QnA Maker ha restituito anche tutte le domande correlate e la proprietà dei metadati contenente le informazioni dei tag di metadati Chit-chat.

## <a name="use-curl-with-threshold-and-default-answer"></a>Usare cURL con una soglia e una risposta predefinita

È possibile richiedere una soglia minima per la risposta. Se la soglia non viene soddisfatta, viene restituita la risposta predefinita.

1. Usare il comando cURL seguente, sostituendolo con il proprio nome della risorsa, l'ID knowledge base e la chiave dell'endpoint, per richiedere una risposta a `size` con una soglia dell'80% o superiore. La knowledge base non dovrebbe trovare la risposta perché il punteggio della domanda è 71% ma dovrebbe restituire la risposta predefinita fornita al momento della creazione della knowledge base.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':80.00}"
    ```

1. Eseguire il comando cURL e ricevere la risposta JSON, che include il punteggio e la risposta.

    ```json
    {
        "answers": [
            {
                "questions": [],
                "answer": "No good match found in KB.",
                "score": 0.0,
                "id": -1,
                "source": null,
                "metadata": []
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```

    QnA Maker ha restituito il punteggio `0`, che indica nessuna attendibilità, nonché la risposta predefinita.

    ```json
    {
      "answers": [
          {
              "questions": [
                  "I thank you",
                  "Oh, thank you",
                  "My sincere thanks",
                  "My humblest thanks to you",
                  "Marvelous, thanks",
                  "Marvelous, thank you kindly",
                  "Marvelous, thank you",
                  "Many thanks to you",
                  "Many thanks",
                  "Kthx",
                  "I'm grateful, thanks",
                  "Ahh, thanks",
                  "I'm grateful for that, thank you",
                  "Perfecto, thanks",
                  "I appreciate you",
                  "I appreciate that",
                  "I appreciate it",
                  "I am very thankful for that",
                  "How kind, thank you",
                  "Great, thanks",
                  "Great, thank you",
                  "Gracias",
                  "Gotcha, thanks",
                  "Gotcha, thank you",
                  "Awesome thanks!",
                  "I'm grateful for that, thank you kindly",
                  "thank you pal",
                  "Wonderful, thank you!",
                  "Wonderful, thank you very much",
                  "Why thank you",
                  "Thx",
                  "Thnx",
                  "That's very kind",
                  "That's great, thanks",
                  "That is lovely, thanks",
                  "That is awesome, thanks!",
                  "Thanks bot",
                  "Thanks a lot",
                  "Okay, thanks!",
                  "Thank you so much",
                  "Perfect, thanks",
                  "Thank you my friend",
                  "Thank you kindly",
                  "Thank you for that",
                  "Thank you bot",
                  "Thank you",
                  "Right on, thanks very much",
                  "Right on, thanks a lot",
                  "Radical, thanks",
                  "Rad, thanks",
                  "Rad thank you",
                  "Wonderful, thanks!",
                  "Thanks"
              ],
              "answer": "You're welcome.",
              "score": 100.0,
              "id": 75,
              "source": "qna_chitchat_Professional.tsv",
              "metadata": [
                  {
                      "name": "editorial",
                      "value": "chitchat"
                  }
              ],
              "context": {
                  "isContextOnly": false,
                  "prompts": []
              }
          }
      ],
      "debugInfo": null,
      "activeLearningEnabled": true
    }
    ```

    Poiché la domanda di `Thank you` corrisponde esattamente a una domanda Chit chat, QnA Maker è assolutamente attendibile con il punteggio di 100. QnA Maker ha restituito anche tutte le domande correlate e la proprietà dei metadati contenente le informazioni dei tag di metadati Chit-chat.

## <a name="use-curl-with-threshold-and-default-answer"></a>Usare cURL con una soglia e una risposta predefinita

È possibile richiedere una soglia minima per la risposta. Se la soglia non viene soddisfatta, viene restituita la risposta predefinita.

1. Aggiungere la proprietà `threshold` per richiedere una risposta a `size` con una soglia dell'80% o superiore. La knowledge base non dovrebbe trovare tale risposta perché il punteggio della domanda è 71%. Il risultato restituisce la risposta predefinita fornita al momento della creazione della knowledge base.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':80.00}"
    ```

1. Eseguire il comando cURL e ricevere la risposta JSON.

    ```json
    {
        "answers": [
            {
                "questions": [],
                "answer": "No good match found in KB.",
                "score": 0.0,
                "id": -1,
                "source": null,
                "metadata": []
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```

    QnA Maker ha restituito il punteggio `0`, che indica nessuna attendibilità, nonché la risposta predefinita.

1. Modificare il valore di soglia con 60% e richiedere nuovamente la query:

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':60.00}"
    ```

    Il codice JSON restituito ha trovato la risposta.

    ```json
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 71.1,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "server",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```
