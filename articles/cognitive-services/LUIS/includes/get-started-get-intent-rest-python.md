---
title: Ottenere la finalità con una chiamata REST in Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/03/2020
ms.openlocfilehash: bc266cba20e72edea54a71028dc98b75dbd77cd9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91544933"
---
[Documentazione di riferimento](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08) | [Esempio](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/python-predict-with-rest/predict.py)

## <a name="prerequisites"></a>Prerequisiti

* [Python 3.6](https://www.python.org/downloads/) o versione successiva.
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-pizza-app"></a>Creare l’app Pizza

[!INCLUDE [Create pizza app](get-started-get-intent-create-pizza-app.md)]

## <a name="get-intent-from-the-prediction-endpoint"></a>Ottenere una finalità dall'endpoint di previsione

Usare Python per eseguire query sull'[endpoint di previsione](https://aka.ms/luis-apim-v3-prediction) per ottenere un risultato della previsione.

1. Copiare questo frammento di codice nel file denominato `predict.py`:

    [!code-python[Code snippet](~/cognitive-services-quickstart-code/python/LUIS/python-predict-with-rest/predict.py)]

1. Sostituire i valori che iniziano con `YOUR-` con i propri valori.

    |Informazioni|Scopo|
    |--|--|
    |`YOUR-APP-ID`|L'ID app. Disponibile nel portale LUIS, pagina Impostazioni applicazione per l'app.
    |`YOUR-PREDICTION-KEY`|La chiave di previsione di 32 caratteri. Disponibile nel portale LUIS, pagina delle risorse di Azure per l'app.
    |`YOUR-PREDICTION-ENDPOINT`| L'endpoint dell'URL di previsione. Disponibile nel portale LUIS, pagina delle risorse di Azure per l'app.<br>Ad esempio: `https://westus.api.cognitive.microsoft.com/`.|

1. Installare la dipendenza `requests`. La libreria `requests` viene usata per creare richieste HTTP:

    ```console
    pip install requests
    ```

1. Eseguire lo script con questo comando della console:

    ```console
    python predict.py
    ```

1. Esaminare la risposta di previsione restituita in formato JSON:

    ```console
    {'query': 'I want two large pepperoni pizzas on thin crust please', 'prediction': {'topIntent': 'ModifyOrder', 'intents': {'ModifyOrder': {'score': 1.0}, 'None': {'score': 8.55e-09}, 'Greetings': {'score': 1.82222226e-09}, 'CancelOrder': {'score': 1.47272727e-09}, 'Confirmation': {'score': 9.8125e-10}}, 'entities': {'Order': [{'FullPizzaWithModifiers': [{'PizzaType': ['pepperoni pizzas'], 'Size': [['Large']], 'Quantity': [2], 'Crust': [['Thin']], '$instance': {'PizzaType': [{'type': 'PizzaType', 'text': 'pepperoni pizzas', 'startIndex': 17, 'length': 16, 'score': 0.9978157, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}], 'Size': [{'type': 'SizeList', 'text': 'large', 'startIndex': 11, 'length': 5, 'score': 0.9984481, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}], 'Quantity': [{'type': 'builtin.number', 'text': 'two', 'startIndex': 7, 'length': 3, 'score': 0.999770939, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}], 'Crust': [{'type': 'CrustList', 'text': 'thin crust', 'startIndex': 37, 'length': 10, 'score': 0.933985531, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}]}}], '$instance': {'FullPizzaWithModifiers': [{'type': 'FullPizzaWithModifiers', 'text': 'two large pepperoni pizzas on thin crust', 'startIndex': 7, 'length': 40, 'score': 0.90681237, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}]}}], 'ToppingList': [['Pepperoni']], '$instance': {'Order': [{'type': 'Order', 'text': 'two large pepperoni pizzas on thin crust', 'startIndex': 7, 'length': 40, 'score': 0.9047088, 'modelTypeId': 1, 'modelType': 'Entity Extractor', 'recognitionSources': ['model']}], 'ToppingList': [{'type': 'ToppingList', 'text': 'pepperoni', 'startIndex': 17, 'length': 9, 'modelTypeId': 5, 'modelType': 'List Entity Extractor', 'recognitionSources': ['model']}]}}}}
    ```

    Risposta JSON formattata per migliorare la leggibilità:

    ```JSON
    {
      'query': 'I want two large pepperoni pizzas on thin crust please',
      'prediction': {
        'topIntent': 'ModifyOrder',
        'intents': {
          'ModifyOrder': {
            'score': 1.0
          },
          'None': {
            'score': 8.55e-9
          },
          'Greetings': {
            'score': 1.82222226e-9
          },
          'CancelOrder': {
            'score': 1.47272727e-9
          },
          'Confirmation': {
            'score': 9.8125e-10
          }
        },
        'entities': {
          'Order': [
            {
              'FullPizzaWithModifiers': [
                {
                  'PizzaType': [
                    'pepperoni pizzas'
                  ],
                  'Size': [
                    [
                      'Large'
                    ]
                  ],
                  'Quantity': [
                    2
                  ],
                  'Crust': [
                    [
                      'Thin'
                    ]
                  ],
                  '$instance': {
                    'PizzaType': [
                      {
                        'type': 'PizzaType',
                        'text': 'pepperoni pizzas',
                        'startIndex': 17,
                        'length': 16,
                        'score': 0.9978157,
                        'modelTypeId': 1,
                        'modelType': 'Entity Extractor',
                        'recognitionSources': [
                          'model'
                        ]
                      }
                    ],
                    'Size': [
                      {
                        'type': 'SizeList',
                        'text': 'large',
                        'startIndex': 11,
                        'length': 5,
                        'score': 0.9984481,
                        'modelTypeId': 1,
                        'modelType': 'Entity Extractor',
                        'recognitionSources': [
                          'model'
                        ]
                      }
                    ],
                    'Quantity': [
                      {
                        'type': 'builtin.number',
                        'text': 'two',
                        'startIndex': 7,
                        'length': 3,
                        'score': 0.999770939,
                        'modelTypeId': 1,
                        'modelType': 'Entity Extractor',
                        'recognitionSources': [
                          'model'
                        ]
                      }
                    ],
                    'Crust': [
                      {
                        'type': 'CrustList',
                        'text': 'thin crust',
                        'startIndex': 37,
                        'length': 10,
                        'score': 0.933985531,
                        'modelTypeId': 1,
                        'modelType': 'Entity Extractor',
                        'recognitionSources': [
                          'model'
                        ]
                      }
                    ]
                  }
                }
              ],
              '$instance': {
                'FullPizzaWithModifiers': [
                  {
                    'type': 'FullPizzaWithModifiers',
                    'text': 'two large pepperoni pizzas on thin crust',
                    'startIndex': 7,
                    'length': 40,
                    'score': 0.90681237,
                    'modelTypeId': 1,
                    'modelType': 'Entity Extractor',
                    'recognitionSources': [
                      'model'
                    ]
                  }
                ]
              }
            }
          ],
          'ToppingList': [
            [
              'Pepperoni'
            ]
          ],
          '$instance': {
            'Order': [
              {
                'type': 'Order',
                'text': 'two large pepperoni pizzas on thin crust',
                'startIndex': 7,
                'length': 40,
                'score': 0.9047088,
                'modelTypeId': 1,
                'modelType': 'Entity Extractor',
                'recognitionSources': [
                  'model'
                ]
              }
            ],
            'ToppingList': [
              {
                'type': 'ToppingList',
                'text': 'pepperoni',
                'startIndex': 17,
                'length': 9,
                'modelTypeId': 5,
                'modelType': 'List Entity Extractor',
                'recognitionSources': [
                  'model'
                ]
              }
            ]
          }
        }
      }
    }
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questo argomento di avvio rapido, eliminare il file dal file system.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere espressioni ed eseguire il training](../get-started-get-model-rest-apis.md)