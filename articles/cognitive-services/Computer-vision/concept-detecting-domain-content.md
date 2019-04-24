---
title: Rilevare contenuti specifici di dominio - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Informazioni su come specificare un dominio di classificazione delle immagini per restituire informazioni più dettagliate su un'immagine.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e4b64e00f71768a8821c83a73b019f77089e1b3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60368077"
---
# <a name="detect-domain-specific-content"></a>Rilevare contenuti specifici di dominio

Oltre all'assegnazione di tag e alla classificazione di primo livello, Visione artificiale supporta anche un'ulteriore analisi specifica di dominio tramite modelli di cui è stato effettuato il training su dati specializzati.

È possibile usare i modelli specifici di dominio in due modi: autonomamente (analisi con ambito) o come miglioramento per la funzionalità di categorizzazione.

### <a name="scoped-analysis"></a>Analisi con ambito

È possibile analizzare un'immagine usando solo il modello specifico di dominio scelto chiamando l'API [Models/\<modello\>/Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200).

Di seguito è riportata una risposta JSON di esempio restituita dall'API **models/celebrities/analyze** per l'immagine specificata:

![Satya Nadella permanente, sorridenti](./images/satya.jpeg)

```json
{
  "result": {
    "celebrities": [{
      "faceRectangle": {
        "top": 391,
        "left": 318,
        "width": 184,
        "height": 184
      },
      "name": "Satya Nadella",
      "confidence": 0.99999856948852539
    }]
  },
  "requestId": "8217262a-1a90-4498-a242-68376a4b956b",
  "metadata": {
    "width": 800,
    "height": 1200,
    "format": "Jpeg"
  }
}
```

### <a name="enhanced-categorization-analysis"></a>Analisi avanzata della categorizzazione

È anche possibile usare i modelli specifici di dominio per integrare l'analisi generale delle immagini. È possibile eseguire questa operazione come parte della [categorizzazione di alto livello](concept-categorizing-images.md) indicando modelli specifici di dominio nel parametro *details* della chiamata API [Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa).

In questo caso, viene chiamato per primo lo strumento di classificazione della tassonomia di 86 categorie. Se per una delle categorie è presente un modello specifico di dominio corrispondente, l'immagine viene passata anche attraverso il modello e vengono aggiunti i risultati.

La risposta JSON seguente mostra come includere l'analisi specifica di dominio come nodo `detail` in un'analisi della categorizzazione più ampia.

```json
"categories":[
  {
    "name":"abstract_",
    "score":0.00390625
  },
  {
    "name":"people_",
    "score":0.83984375,
    "detail":{
      "celebrities":[
        {
          "name":"Satya Nadella",
          "faceRectangle":{
            "left":597,
            "top":162,
            "width":248,
            "height":248
          },
          "confidence":0.999028444
        }
      ],
      "landmarks":[
        {
          "name":"Forbidden City",
          "confidence":0.9978346
        }
      ]
    }
  }
]
```

## <a name="list-the-domain-specific-models"></a>Elencare i modelli specifici di dominio

Visione artificiale supporta attualmente i modelli specifici di dominio seguenti:

| Name | DESCRIZIONE |
|------|-------------|
| celebrities | Riconoscimento di personaggi famosi, supportato per le immagini classificate nella categoria `people_` |
| landmarks | Riconoscimento di luoghi di interesse, supportato per le immagini classificate nella categoria `outdoor_` o `building_` |

La chiamata dell'API [Models](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) restituisce queste informazioni insieme alle categorie cui può essere applicato ogni modello:

```json
{
  "models":[
    {
      "name":"celebrities",
      "categories":[
        "people_",
        "人_",
        "pessoas_",
        "gente_"
      ]
    },
    {
      "name":"landmarks",
      "categories":[
        "outdoor_",
        "户外_",
        "屋外_",
        "aoarlivre_",
        "alairelibre_",
        "building_",
        "建筑_",
        "建物_",
        "edifício_"
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Concetti relativi alla [classificazione delle immagini](concept-categorizing-images.md).
