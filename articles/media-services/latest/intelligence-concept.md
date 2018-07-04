---
title: Intelligenza dei contenuti multimediali di Azure | Microsoft Docs
description: Con Servizi multimediali di Azure è possibile analizzare contenuti audio e video usando AudioAnalyzerPreset e VideoAnalyzerPreset.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/24/2018
ms.author: juliako
ms.openlocfilehash: c488060b9db0ba482d12eee2394e5149b918950e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "36331521"
---
# <a name="media-intelligence"></a>Intelligenza dei contenuti multimediali

L'API REST v3 di Servizi multimediali di Azure consente di analizzare contenuti audio e video. Per eseguire l'analisi, si crea un oggetto **Transform** e si invia un oggetto **Job** che usa uno dei due set di impostazioni **AudioAnalyzerPreset** e **VideoAnalyzerPreset** . 

## <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

**AudioAnalyzerPreset** consente di estrarre informazioni dettagliate sui contenuti audio da un file audio o video. L'output include un file in formato JSON, con tutte le informazioni dettagliate, e un file in formato VTT per la trascrizione dell'audio. Questo set di impostazioni accetta una proprietà che specifica la lingua del file di input sotto forma di stringa [BCP47](https://tools.ietf.org/html/bcp47). Le informazioni dettagliate sui contenuti audio includono:

* Trascrizione dell'audio: una trascrizione del parlato con timestamp. Sono supportate più lingue.
* Indicizzazione dei parlanti: un mapping dei parlanti e delle parole pronunciate corrispondenti.
* Analisi del sentiment vocale: l'output dell'analisi del sentiment eseguita sulla trascrizione dell'audio.
* Parole chiave: le parole chiave estratte dalla trascrizione dell'audio.

## <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

**VideoAnalyzerPreset** consente di estrarre da un file video più informazioni dettagliate sui contenuti audio e video. L'output include un file in formato JSON, con tutte le informazioni dettagliate, un file in formato VTT per la trascrizione del video e una raccolta di anteprime. Anche questo set di impostazioni accetta come proprietà una stringa [BCP47](https://tools.ietf.org/html/bcp47) che rappresenta la lingua del video. Le informazioni dettagliate sul video includono quelle già indicate per l'audio e gli elementi seguenti:

* Rilevamento volti: il tempo durante il quale sono presenti dei volti nel video. Ogni volto ha un id e una raccolta di anteprime corrispondente.
* Testo visivo: il testo rilevato tramite riconoscimento ottico dei caratteri. Il testo è associato a un timestamp e viene usato anche per estrarre parole chiave, in aggiunta alla trascrizione dell'audio.
* Fotogrammi chiave: una raccolta di fotogrammi chiave estratti dal video.
* Moderazione dei contenuti visivi: la parte di video che è stata contrassegnata come contenuti pornografici o per adulti.
* Annotazione: il risultato dell'annotazione dei video in base a un modello a oggetti predefinito.

##  <a name="insightsjson-elements"></a>Elementi di insights.json

L'output include un file in formato JSON (insights.json) con tutte le informazioni dettagliate che sono state rilevate nel video o nell'audio. Il codice json può includere gli elementi seguenti:

### <a name="transcript"></a>transcript

|NOME|DESCRIZIONE|
|---|---|
|id|ID della riga.|
|text|Testo della trascrizione.|
|Linguaggio|Lingua della trascrizione. Questo elemento è stato progettato per supportare trascrizioni in cui ogni riga può avere una lingua diversa.|
|instances|Elenco degli intervalli di tempo in cui è presente la riga. Se l'istanza corrisponde a un'intera trascrizione, è riportata una sola istanza.|

Esempio:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>ocr

|NOME|DESCRIZIONE|
|---|---|
|id|ID della riga di riconoscimento ottico dei caratteri.|
|text|Testo risultante dal riconoscimento ottico dei caratteri.|
|confidence|Grado di attendibilità del riconoscimento.|
|Linguaggio|Lingua del riconoscimento ottico dei caratteri.|
|instances|Elenco degli intervalli di tempo in cui è presente la riga di riconoscimento ottico dei caratteri. La stessa riga può apparire più volte.|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="keywords"></a>keywords

|NOME|DESCRIZIONE|
|---|---|
|id|ID della parola chiave.|
|text|Testo della parola chiave.|
|confidence|Grado di attendibilità del riconoscimento della parola chiave.|
|Linguaggio|Lingua della parola chiave, quando tradotta.|
|instances|Elenco degli intervalli di tempo in cui è presente la parola chiave. La stessa parola chiave può apparire più volte.|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 

```

### <a name="faces"></a>faces

|NOME|DESCRIZIONE|
|---|---|
|id|ID del volto.|
|name|Nome del volto. Può essere 'Unknown #0', un personaggio noto identificato o una persona per cui il cliente ha eseguito il training.|
|confidence|Grado di attendibilità dell'identificazione del volto.|
|description|Nel caso di un personaggio noto, la relativa descrizione. |
|thumbnalId|ID dell'anteprima del volto.|
|knownPersonId|Nel caso di una persona nota, il relativo ID interno.|
|referenceId|Nel caso di un personaggio di Bing, il relativo ID Bing.|
|referenceType|Attualmente solo Bing.|
|title|Nel caso di un personaggio noto, la qualifica, ad esempio CEO Microsoft.|
|imageUrl|Nel caso di un personaggio noto, l'URL della relativa immagine.|
|instances|Istanze in cui è presente il volto nell'intervallo di tempo specificato. Ogni istanza è associata anche un thumbnailsId. |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

### <a name="labels"></a>Etichette

|NOME|DESCRIZIONE|
|---|---|
|id|ID dell'etichetta.|
|name|Nome dell'etichetta, ad esempio 'Computer' o 'TV'.|
|Linguaggio|Lingua del nome dell'etichetta, quando tradotto. BCP-47|
|instances|Elenco degli intervalli di tempo in cui è presente l'etichetta. La stessa etichetta può apparire più volte. Ogni istanza ha un campo che indica il grado di attendibilità. |


```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

### <a name="shots"></a>shots

|NOME|DESCRIZIONE|
|---|---|
|id|ID dello scatto.|
|keyFrames|Elenco dei fotogrammi chiave inclusi nello scatto, ciascuno con un ID e un elenco degli intervalli di tempo delle istanze.|
|instances|Elenco degli intervalli di tempo dello scatto. Per gli scatti è prevista una sola istanza.|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```


### <a name="sentiments"></a>sentiments

Le valutazioni vengono aggregate in base al campo sentimentType (positivo/neutro/negativo), ad esempio 0-0.1, 0.1-0.2.

|NOME|DESCRIZIONE|
|---|---|
|id|ID della valutazione.|
|averageScore |Media di tutti i punteggi di tutte le istanze di un determinato tipo di valutazione: positivo/neutro/negativo.|
|instances|Elenco degli intervalli di tempo in cui è presente la valutazione.|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Analizzare i video con Servizi multimediali di Azure](analyze-videos-tutorial-with-api.md)
