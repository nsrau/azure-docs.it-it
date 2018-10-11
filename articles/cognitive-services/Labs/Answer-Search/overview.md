---
title: Informazioni su Project Answer Search
titlesuffix: Azure Cognitive Services
description: Introduzione a Project Answer Search.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-answer-search
ms.topic: overview
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 5658054b3cc77db20edd64f6c560ee5d4a58eb46
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2018
ms.locfileid: "48883721"
---
# <a name="what-is-project-answer-search"></a>Informazioni su Project Answer Search
L'API Project Answer Search usa l'endpoint Bing v7 per ottenere risposte alle query interrogative. Una domanda come ad esempio "Qual è la circonferenza della terra?" restituisce una risposta con informazioni fattuali.  Una query per una persona, un luogo o un oggetto restituisce informazioni sull'entità identificata dalla query. Questi scenari possono essere utili nelle applicazioni, ad esempio bot di conversazione, app di messaggistica, lettori e così via.  

Le query restituiscono risposte che dipendono dallo scenario delle query: le pagine Web vengono sempre restituite, mentre i [fatti](fact-queries.md) e/o le [entità](entity-queries.md) vengono restituiti se pertinenti.

## <a name="endpoint"></a>Endpoint
Per ottenere risposte a una domanda o informazioni su una persona, un luogo o un oggetto, inviare una richiesta all'endpoint dell'API Answer Search. Usare le intestazioni e i parametri URL per le varie specifiche.  Includere l'intestazione *Ocp-Apim-Subscription-Key* con un token valido.  Il parametro del mercato è obbligatorio. Attualmente è supportato solo il mercato `en-us`.

La query seguente ottiene risposte alla domanda: "Qual è la circonferenza della terra?"

GET:
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=what+is+circumference+of+the=earth?&mkt=en-us

````

Il parametro URL `q=` è necessario per specificare l'oggetto della ricerca.

## <a name="response-object"></a>Oggetto della risposta

La risposta include intestazioni HTTP, pagine Web, fatti e/o entità.

````
BingAPIs-TraceId: AB2E75C998614ADB8EBF5110DF648298
X-MSEdge-ClientID: 1E48FC4F7B8768C80B14F7997A106906
BingAPIs-SessionId: 0504DDD6DAE84861A4842306F8DA7A58
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: AB2E75C998614ADB8EBF5110DF648298 Ref B: CO1EDGE0322 Ref C: 2018-04-19T19:57:13Z

JSON Response:

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "what is the circumference of earth"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q\u003dwhat+is+the+circumference+of+earth",
    "totalEstimatedMatches": 217000,
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.0",
        "name": "Circumference of the Earth - Universe Today",
        "url": "https://www.universetoday.com/26461/circumference-of-the-earth/",
        "isFamilyFriendly": true,
        "displayUrl": "https://www.universetoday.com/26461/circumference-of-the-earth",
        "snippet": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "deepLinks": [
          {
            "name": "About Earth",
            "url": "https://www.universetoday.com/14382/10-interesting-facts-about-planet-earth/"
          }
        ],
        "dateLastCrawled": "2018-04-12T14:13:00.0000000Z",
        "language": "en"
      },
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.1",
        "name": "Earth - Wikipedia",
        "url": "https://en.wikipedia.org/wiki/Earth",
        "about": [
          {
            "name": "Earth"
          },
          {
            "name": "Earth"
          }
        ],
        "isFamilyFriendly": true,
        "displayUrl": "https://en.wikipedia.org/wiki/Earth",
        "snippet": "Circumference: 40 075.017 km equatorial (24 901.461 mi) ... Earth is the third planet from the Sun and the only object in the Universe known to harbor life.",
        "deepLinks": [
          {
            "name": "Moon",
            "url": "https://en.wikipedia.org/wiki/Moon"
          },
          {
            "name": "Planet",
            "url": "https://en.wikipedia.org/wiki/Planet"
          },
          {
            "name": "Quasi-Satellites",
            "url": "https://en.wikipedia.org/wiki/Quasi-satellite"
          },
          {
            "name": "World Population",
            "url": "https://en.wikipedia.org/wiki/World_population"
          },
   . . .

    ]
  },
  "entities": {
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#Entities.0",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
              "name": "CC-BY-SA",
              "url": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "http://en.wikipedia.org/wiki/Earth"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://en.wikipedia.org/wiki/Earth"
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q\u003dEarth\u0026filters\u003dsid:%226ddb3372-4801-5567-321e-e8a53bd774a4%22\u0026elv\u003dAXXfrEiqqD9r3GuelwApulpmymQx!ODfuQu*veOQHkvP0!Zbvi5F5tVcMSDJvDEWiQWwrdueYTtIszgj03oFQHykYYLYgq3q5!Sf00QxXGIS",
        "name": "Earth",
        "image": {
          "name": "Earth",
          "thumbnailUrl": "https://www.bing.com/th?id\u003dA3ab623665ab412f386c162bd29f0683a\u0026w\u003d110\u0026h\u003d110\u0026c\u003d7\u0026rs\u003d1\u0026qlt\u003d80\u0026cdv\u003d1\u0026pid\u003d16.1",
          "provider": [
            {
              "_type": "Organization",
              "url": "http://en.wikipedia.org/wiki/Earth"
            }
          ],
          "hostPageUrl": "http://upload.wikimedia.org/wikipedia/commons/9/97/The_Earth_seen_from_Apollo_17.jpg",
          "width": 110,
          "height": 110,
          "sourceWidth": 799,
          "sourceHeight": 800
        },
        "description": "Earth is the third planet from the Sun and the only object in the Universe known to harbor life. According to radiometric dating and other sources of evidence, Earth formed over 4.5 billion years ago. Earth\u0027s gravity interacts with other objects in space, especially the Sun and the Moon, Earth\u0027s only natural satellite. Earth revolves around the Sun in 365.26 days, a period known as an Earth year. During this time, Earth rotates about its axis about 366.26 times.",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Generic"
          ]
        },
        "bingId": "6ddb3372-4801-5567-321e-e8a53bd774a4"
      }
    ]
  },
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "www.universetoday.com/26461/circumference-of-the-earth/",
        "url": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "www.universetoday.com/26461/circumference-of-the-earth/",
        "seeMoreUrl": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "value": [
      {
        "description": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "subjectName": ""
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "Facts",
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Facts"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.0"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.1"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 2,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.2"
          }
        },
        
        . . . 
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        }
      ]
    }
  }
}

````

## <a name="terms-of-use"></a>Condizioni per l'utilizzo
Project Answer Search e Project Video Trends sono soggetti ai [requisiti di visualizzazione e utilizzo di Ricerca Bing](use-display-requirements.md).

L'utente, o una terza parte per conto dell'utente, non può usare, conservare, archiviare, memorizzare nella cache, condividere o distribuire alcun dato dall'API URL Preview allo scopo di testare, sviluppare, eseguire il training, distribuire o rendere disponibile un servizio o una funzionalità non Microsoft. 

## <a name="throttling-requests"></a>Limitazione delle richieste

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="data-attribution"></a>Attribuzione di dati  

Le risposte di Project Answer Search contengono informazioni di proprietà di terze parti. L'utente è tenuto a garantire un uso appropriato, ad esempio con la conformità a qualsiasi licenza Creative Commons su ci potrebbe basarsi l'esperienza utente.  
  
Se una risposta o un risultato include i campi `contractualRules`, `attributions` o `provider`, è necessario attribuire i dati. Se la risposta non include uno di questi campi, non è necessaria alcuna attribuzione. Se la risposta include il campo `contractualRules` e i campi `attributions` e/o `provider`, è necessario usare le regole contrattuali per attribuire i dati.  
  
L'esempio seguente illustra un'entità che include una regola contrattuale MediaAttribution e un'immagine che include un campo `provider`. La regola MediaAttribution identifica l'immagine come destinazione della regola, pertanto è possibile ignorare il campo `provider` dell'immagine e usare la regola MediaAttribution per fornire l'attribuzione.  
  
```  
        "value" : [{
            "contractualRules" : [
                . . .
                {
                    "_type" : "ContractualRules\/MediaAttribution",
                    "targetPropertyName" : "image",
                    "mustBeCloseToContent" : true,
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }
            ],
            . . .
            "image" : {
                "name" : "Space Needle",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A46378861201...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }],
                "hostPageUrl" : "http:\/\/www.citydictionary.com\/Uploaded...",
                "width" : 110,
                "height" : 110
            },
            . . .
        }]
```  
  
Se una regola contrattuale include il campo `targetPropertyName`, la regola si applica solo al campo di destinazione. In caso contrario, la regola si applica all'oggetto padre che contiene il campo `contractualRules`.  
  
  
Nell'esempio seguente la regola `LinkAttribution` include il campo `targetPropertyName`, quindi la regola si applica al campo `description`. Per le regole che si applicano a campi specifici, è necessario includere una riga subito dopo i dati di destinazione contenenti un collegamento ipertestuale al sito Web del provider. Ad esempio, per attribuire la descrizione, includere una riga subito dopo il testo della descrizione contenente un collegamento ipertestuale ai dati sul sito Web del provider, in questo caso creare un collegamento a en.wikipedia.org.  
  
```  
"entities" : {  
    "value" : [{  
            . . .  
            "description" : "Peyton Williams Manning is a former American....",  
            . . .  
            "contractualRules" : [{  
                    "_type" : "ContractualRules\/LinkAttribution",  
                    "targetPropertyName" : "description",  
                    "mustBeCloseToContent" : true,  
                    "text" : "en.wikipedia.org",  
                    "url" : "http:\/\/www.bing.com\/cr?IG=B8AD73..."  
                 },  
            . . .  
  
```  

### <a name="license-attribution"></a>Attribuzione di licenze  

Se l'elenco di regole contrattuali include una regola [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution), è necessario visualizzare l'avviso sulla riga immediatamente successiva al contenuto a cui si applica la licenza. La regola `LicenseAttribution` usa il campo `targetPropertyName` per identificare la proprietà a cui si applica la licenza.  
  
Di seguito è riportato un esempio che include una regola `LicenseAttribution`.  
  
![Attribuzione di licenze](./media/licenseattribution.png)  
  
L'avviso di licenza visualizzato deve includere un collegamento ipertestuale al sito Web che contiene informazioni sulla licenza. In genere l'utente rende il nome della licenza un collegamento ipertestuale. Ad esempio, se l'avviso è **Text under CC-BY-SA license** (Testo nella licenza CC-BY-SA) e CC-BY-SA è il nome della licenza, rendere CC-BY-SA come collegamento ipertestuale.  
  
### <a name="link-and-text-attribution"></a>Attribuzione di collegamento e testo  

Le regole [LinkAttribution](reference.md#linkattribution) e [TextAttribution](reference.md#textattribution) vengono in genere usate per identificare il provider dei dati. Il campo `targetPropertyName` identifica il campo a cui si applica la regola.  
  
Per attribuire i provider, includere una riga subito dopo il contenuto a cui si applicano le attribuzioni (ad esempio, il campo di destinazione). La riga deve essere chiaramente etichettata per indicare che i provider sono l'origine dei dati. Ad esempio, "Data from: en.wikipedia.org". Per le regole `LinkAttribution` è necessario creare un collegamento ipertestuale al sito Web del provider.  
  
Di seguito è riportato un esempio che include le regole `LinkAttribution` e `TextAttribution`.  
  
![Attribuzione del testo di un collegamento](./media/linktextattribution.png)  

### <a name="media-attribution"></a>Attribuzione di file multimediali  

Se l'entità include un'immagine e viene visualizzata, è necessario fornire un collegamento click-through al sito Web del provider. Se l'entità include una regola [MediaAttribution](reference.md#mediaattribution), usare l'URL della regola per creare il collegamento click-through. In caso contrario, usare l'URL incluso nel campo `provider` dell'immagine per creare il collegamento click-through.  
  
Di seguito è riportato un esempio che include il campo `provider` e le regole contrattuali di un'immagine. Poiché l'esempio include la regola contrattuale, verrà ignorato il campo `provider` dell'immagine e verrà applicata la regola `MediaAttribution`.  
  
![Attribuzione di file multimediali](./media/mediaattribution.png)  

## <a name="next-steps"></a>Passaggi successivi
- [Guida introduttiva in C#](c-sharp-quickstart.md)
- [Guida introduttiva in Java](java-quickstart.md)
- [Guida introduttiva in Node](node-quickstart.md)
- [Guida introduttiva in Python](python-quickstart.md)
