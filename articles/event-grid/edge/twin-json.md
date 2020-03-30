---
title: Modulo Gemello - Azure Event Grid IoT Edge Documenti Microsoft
description: Configurazione tramite il modulo Gemello.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 5c23b9ef280a4a4e3458d279ecf060d2e3d50295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72992145"
---
# <a name="module-twin-json-schema"></a>Schema JSON del modulo gemello

Event Grid su IoT Edge si integra con l'ecosistema IoT Edge e supporta la creazione di argomenti e sottoscrizioni tramite il modulo Gemello. Segnala inoltre lo stato corrente di tutti gli argomenti e le sottoscrizioni di eventi alle proprietà segnalate nel modulo gemello.

> [!WARNING]
> A causa delle limitazioni nell'ecosistema IoT Edge, tutti gli elementi della matrice nell'esempio json seguente sono stati codificati come stringhe json. Vedere `EventSubscription.Filter.EventTypes` `EventSubscription.Filter.AdvancedFilters` e le chiavi nell'esempio seguente.

## <a name="desired-properties-json"></a>Proprietà desiderate JSON

* Il valore di ogni coppia chiave-valore nella sezione degli argomenti ha `Topic.Properties` esattamente lo stesso schema JSON usato nell'API per la creazione di argomenti.
* Il valore di ogni coppia chiave-valore nella sezione **EventSubscriptions** ha esattamente `EventSubscription.Properties` lo stesso schema json usato nell'API durante la creazione di argomenti.
* Per eliminare un argomento, `null` impostarne il valore su nelle proprietà desiderate.
* L'eliminazione di sottoscrizioni di eventi tramite le proprietà desiderate non è supportata.

```json
{
    "topics": {
        "twinTopic1": {},
        "twinTopic2": {
            "inputSchema": "customEventSchema"
        }
    },
    "eventSubscriptions": {
        "twinTopic1WebhookSub": {
            "topic": "twinTopic1",
            "retryPolicy": {
                "eventExpiryInMinutes": 120,
                "maxDeliveryAttempts": 30
            },
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "https://localhost:4438"
                }
            },
            "filter": {
                "subjectBeginsWith": "^",
                "subjectEndsWith": "$",
                "isSubjectCaseSensitive": false,
                "includedEventTypes": "[\"et1\",\"et2\"]",
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"data.b\"},{\"values\":[\"\\\"\",\"c\"],    \"operatorType\":\"StringContains\",\"key\":\"data.s\"}]"
            }
        },
        "twinTopic2EdgeHubSub": {
            "topic": "twinTopic2",
            "deliveryPolicy": {
                "approxBatchSizeInBytes": 200000,
                "maxEventsPerBatch": 25
            },
            "destination": {
                "endpointType": "EdgeHub",
                "properties": {
                    "outputName": "twinTopic2EdgeHubSub"
                }
            },
            "filter": {
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"dAt\\\"A.a\"},{\"values\":[\"\\\"\",    \"c\"],\"operatorType\":\"StringContains\",\"key\":\"dAt\\\"A.a\"}]"
            }
        }
    }
}
```

## <a name="reported-properties-json"></a>Proprietà segnalate JSON

La sezione delle proprietà segnalate del modulo gemello include le seguenti informazioni:

* L'insieme di argomenti e sottoscrizioni presenti nell'archivio del modulo
* Eventuali errori rilevati durante la creazione di sottoscrizioni di argomenti/eventi desiderate
* Eventuali errori di avvio (ad esempio le proprietà desiderate Analisi JSON non riuscita)Any boot up errors (such as desired properties JSON parsing failed)

```json
{
    "topics": {
        "twinTopic1": {},
        "twinTopic2": {
            "inputSchema": "customEventSchema"
        }
    },
    "eventSubscriptions": {
        "twinTopic1WebhookSub": {
            "topic": "twinTopic1",
            "retryPolicy": {
                "eventExpiryInMinutes": 120,
                "maxDeliveryAttempts": 30
            },
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "https://localhost:4438"
                }
            },
            "filter": {
                "subjectBeginsWith": "^",
                "subjectEndsWith": "$",
                "isSubjectCaseSensitive": false,
                "includedEventTypes": "[\"et1\",\"et2\"]",
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"data.b\"},{\"values\":[\"\\\"\",\"c\"],    \"operatorType\":\"StringContains\",\"key\":\"data.s\"}]"
            }
        },
        "twinTopic2EdgeHubSub": {
            "topic": "twinTopic2",
            "deliveryPolicy": {
                "approxBatchSizeInBytes": 200000,
                "maxEventsPerBatch": 25
            },
            "destination": {
                "endpointType": "EdgeHub",
                "properties": {
                    "outputName": "twinTopic2EdgeHubSub"
                }
            },
            "filter": {
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"dAt\\\"A.a\"},{\"values\":[\"\\\"\",    \"c\"],\"operatorType\":\"StringContains\",\"key\":\"dAt\\\"A.a\"}]"
            }
        }
    },
    "errors": {
        "bootupMessage": "",
        "bootupException": "",
        "topics": {},
        "eventSubscriptions": {
            "twinTopic1EventGridUserTopicSub": "HttpStatusCode='BadRequest' ErrorCode='InvalidDestination' Message='EventSubscription.Properties.Destination failed validation. Reason: EndpointUrl must target the /api/events API of Azure Event Grid in the cloud..'"
        }
    }
}
```
