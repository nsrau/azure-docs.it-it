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
ms.openlocfilehash: dd44d9cb01ff072d89afeb4efc4a59071c621315
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758864"
---
Questa guida introduttiva basata su Postman assiste nell'ottenimento di una risposta da una knowledge base.

## <a name="prerequisites"></a>Prerequisiti

* Ultima versione di [**Postman**](https://www.getpostman.com/).
* È necessario avere
    * Un [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md)
    * Una [knowledge base con domande e risposte](../Quickstarts/add-question-metadata-portal.md) pubblicata e di cui è stato eseguito il training creata nell'argomento di avvio rapido configurata con metadati e chit chat.

> [!NOTE]
> Una volta pronti a generare una risposta per una domanda dalla knowledge base, è necessario [eseguire il training](../Quickstarts/create-publish-knowledge-base.md#save-and-train) e [pubblicare](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) la knowledge base. Una volta pubblicata la knowledge base, nella pagina **Pubblica** vengono visualizzate le impostazioni della richiesta HTTP per generare una risposta. La scheda **Postman** mostra le impostazioni necessarie per generare una risposta.

## <a name="set-up-postman-for-requests"></a>Configurare Postman per le richieste

Questo argomento di avvio rapido usa le stesse impostazioni della richiesta **POST** Postman, quindi configura il codice JSON del corpo della richiesta POST inviato al servizio in base a ciò per cui si sta provando a eseguire una query.

Usare questa procedura per configurare Postman, quindi leggere ogni sezione successiva per configurare il codice JSON del corpo della richiesta POST.

1. Nella pagina **Impostazioni** della knowledge base selezionare la scheda **Postman** per visualizzare la configurazione usata per generare una risposta dalla knowledge base. Copiare le informazioni seguenti da usare in Postman.

    |Nome|Impostazione|Scopo e valore|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Si tratta del metodo HTTP e della route per l'URL.|
    |`Host`|`https://diberry-qna-s0-s.azurewebsites.net/qnamaker`|Si tratta dell'host dell'URL. Concatenare i valori Host e Post per ottenere l'URL completo di generateAnswer.|
    |`Authorization`|`EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|Valore dell'intestazione per autorizzare la richiesta ad Azure. |
    |`Content-type`|`application/json`|Valore dell'intestazione per il contenuto.|
    ||`{"question":"<Your question>"}`|Corpo della richiesta POST come oggetto JSON. Questo valore verrà modificato in ogni sezione a seconda dello scopo della query.|

1. Aprire Postman e creare una nuova richiesta **POST** di base con le impostazioni della knowledge base pubblicata. Nelle sezioni seguenti modificare il codice JSON del corpo della richiesta POST per modificare la query nella knowledge base.

## <a name="use-metadata-to-filter-answer"></a>Usare i metadati per filtrare una risposta

In un argomento di avvio rapido precedente sono stati aggiunti i metadati a due coppie di domande e risposte per distinguere tra due domande diverse. Aggiungere i metadati alla query per limitare il filtro alla sola coppia di domande e risposte pertinente.

1. In Postman modificare solo la query JSON aggiungendo la proprietà `strictFilters` con la coppia nome/valore `service:qna_maker`. Il codice JSON del corpo dovrebbe essere:

    ```json
    {
        'question':'size',
        'strictFilters': [
            {
                'name':'service','value':'qna_maker'
            }
        ]
    }
    ```

    La domanda è costituita solo da una singola parola, `size`, che può restituire uno dei due set di domande e risposte. La matrice `strictFilters` indica di limitare le risposte a quelle con tag `qna_maker`.

1. La risposta include solo la risposta che soddisfa i criteri di filtro.

    La risposta seguente è stata formattata per migliorare la leggibilità:

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

1. In Postman modificare solo il codice JSON del corpo aggiungendo la proprietà `debug`. Il codice JSON dovrebbe essere:

    ```json
    {
        'question':'size',
        'Debug': {
            'Enable':true
        }

    }
    ```

1. La risposta include le informazioni pertinenti alla risposta. Nel seguente output JSON, alcuni dettagli di debug sono stati sostituiti con i puntini di sospensione.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) how to collaborate on a knowledge base.",
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

In Postman modificare solo il codice JSON del corpo aggiungendo la proprietà `isTest`. Il codice JSON dovrebbe essere:

```json
{
    'question':'size',
    'isTest': true
}
```

La risposta JSON usa lo stesso schema della query della knowledge base pubblicata.

> [!NOTE]
> Se la knowledge base pubblicata e quella di test sono esattamente le stesse, potrebbero esserci ancora alcune lievi variazioni perché l'indice di test è condiviso tra tutte le knowledge base della risorsa.

## <a name="query-for-a-chit-chat-answer"></a>Eseguire una query per ottenere una risposta chit-chat

1. In Postman modificare solo il codice JSON del corpo in una frase di fine conversazione dell'utente. Il codice JSON dovrebbe essere:

    ```json
    {
        'question':'thank you'
    }
    ```

1. La risposta include il punteggio e la risposta.

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

## <a name="use-threshold-and-default-answer"></a>Usare una soglia e una risposta predefinita

È possibile richiedere una soglia minima per la risposta. Se la soglia non viene soddisfatta, viene restituita la risposta predefinita.

1. In Postman modificare solo il codice JSON del corpo in una frase di fine conversazione dell'utente. Il codice JSON dovrebbe essere:

    ```json
    {
        'question':'size',
        'scoreThreshold':80.00
    }
    ```

    La knowledge base non dovrebbe trovare la risposta perché il punteggio della domanda è 71% ma dovrebbe restituire la risposta predefinita fornita al momento della creazione della knowledge base.

    La risposta JSON restituita, inclusi il punteggio e la risposta è:

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

    ```json
    {
        'question':'size',
        'scoreThreshold':60.00
    }
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