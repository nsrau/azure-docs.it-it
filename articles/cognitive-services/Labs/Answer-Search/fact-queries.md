---
title: 'Guida introduttiva: Query di fatti con Project Answer Search'
titlesuffix: Azure Cognitive Services
description: Query per i fatti con Project Answer Search
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/16/2018
ms.author: rosh
ms.openlocfilehash: fca459291fbf25c5404427fdcfc96947c52a1e74
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544308"
---
# <a name="quickstart-query-for-facts"></a>Guida introduttiva: Query per i fatti

Se la query è per un fatto, ad esempio una data o informazioni personali, la risposta può contenere risposte `facts`. Le risposte di fatti contengono i risultati pertinenti estratti da paragrafi di documenti Web.  Queste query restituiscono sempre pagine Web, e i [fatti](fact-queries.md) e/o le [entità](entity-queries.md) dipendono dalla query.

Le query come San Valentino+2016 o quando+è+il+ramadan vengono considerate query correlate a date. Se Bing determina che la query è correlata a date, la risposta sarà una risposta `facts`.

L'esempio seguente contiene una risposta `facts` correlata a date.

**Query:**
```
https://labsportalppe.azure-api.net/answerSearch/v7.0/search?q=valentines+2016

```

**Risposta:** il campo `subjectName` contiene una versione visualizzata della query dell'utente che è possibile usare come etichetta quando si visualizza il fatto. Se la stringa di query è San Valentino+2016, Bing può modificarla con il giorno di San Valentino 2016. Il campo descrizione contiene il fatto.

```
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "valentines 2016"
    },
    "facts" : {
        "id" : "https:\/\/www.bingapis.com\/api\/v7\/#Facts",
        "value" : [{
            "description" : "Valentine's Day was on Sunday, February 14, 2016.",
            "subjectName" : "Valentine's Day 2016"
        }]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Facts",
                "value" : {
                    "id" : "https:\/\/www.bingapis.com\/api\/v7\/knowledge\/#Facts"
                }
            }]
        }
    }
}

```

La query "Why is the sky blue?" restituisce un esempio di una risposta correlata a informazioni.

**Query:**

```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=why+is+the+sky+blue

```

**Risposta:** il campo `value/description` contiene le informazioni richieste dalla query.

```
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "en.wikipedia.org/wiki/Diffuse_sky_radiation",
        "url": "https://en.wikipedia.org/wiki/Diffuse_sky_radiation"
      },
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "spaceplace.nasa.gov/blue-sky/en/",
        "url": "https://spaceplace.nasa.gov/blue-sky/en/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "en.wikipedia.org/wiki/Diffuse_sky_radiation",
        "seeMoreUrl": "https://en.wikipedia.org/wiki/Diffuse_sky_radiation"
      },
      {
        "providerDisplayName": "spaceplace.nasa.gov/blue-sky/en/",
        "seeMoreUrl": "https://spaceplace.nasa.gov/blue-sky/en/"
      }
    ],
    "value": [
      {
        "image": {
          "webSearchUrl": "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&q=&id=B632272C4E934D7F0B18790300B2DE34E7676C7A&simid=608045681196075791&iss=eqna",
          "name": "Why is the sky blue in the sky?",
          "thumbnailUrl": "https://www.bing.com/th?id=ODE.858093005&w=140&h=140&c=8&rs=1&qlt=100&pid=3.1",
          "isFamilyFriendly": true
        },
        "description": "When sunlight reaches Earth's atmosphere, it is scattered in all directions by all the gases and particles in the air. Blue is scattered by air molecules more than other colors because it travels as shorter, smaller waves. This is why we see a blue sky most of the time.\n\nCloser to the horizon, the sky fades to a lighter blue or white. The sunlight reaching us from low in the sky has passed through even more air than the sunlight reaching us from overhead. As the sunlight has passed through all this air, the air molecules have scattered and rescattered the blue light many times in many directions. Also, the surface of Earth has reflected and scattered the light. All this scattering mixes the colors together again so we see more white and less blue.",
        "subjectName": "Why is the sky blue in the sky?",
        "primaryData": [
          "The atmosphere scatters more blue light"
        ]
      }
    ]
  },

```

## <a name="tabular-data"></a>Dati tabulari
In alcuni casi, i fatti possono essere restituiti come `_type: StructuredValue/TabularData`. La query seguente ottiene i dati tabulari con informazioni contrastanti su caffè e tè.

```
https://labsportalppe.azure-api.net/answerSearch/v7.0/search?q=coffee+vs+tea&mkt=en-us

```
I risultati `facts` includono le righe e celle seguenti:
```
    "value": [
      {
        "subjectName": "Coffee vs. Tea",
        "richCaption": {
          "_type": "StructuredValue/TabularData",
          "header": [
            "",
            "Coffee",
            "Tea"
          ],
          "rows": [
            {
              "cells": [
                {
                  "text": "Part of plant used"
                },
                {
                  "text": "Bean"
                },
                {
                  "text": "Leaf"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Caffeine Content"
                },
                {
                  "text": "80-185 mg per 8 ounce cup depending upon the brew and the..."
                },
                {
                  "text": "15 - 70 mg per cup"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Types of Consumption"
                },
                {
                  "text": "Drip Coffee"
                },
                {
                  "text": "White Tea"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Additions"
                },
                {
                  "text": "Sugar"
                },
                {
                  "text": "Milk"
                }
              ]
            }
          ],
          "seeMoreUrl": {
            "text": "8 more rows",
            "url": "https://www.diffen.com/difference/Coffee_vs_Tea"
          }
        }
      }
    ]
  },

```

## <a name="next-steps"></a>Passaggi successivi
- [Guida introduttiva in C#](c-sharp-quickstart.md)
- [Guida introduttiva in Java](java-quickstart.md)
- [Guida introduttiva in Node](node-quickstart.md)
- [Guida introduttiva in Python](python-quickstart.md)
