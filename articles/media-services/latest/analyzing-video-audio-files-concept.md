---
title: Analisi di file video e audio con Servizi multimediali di Azure | Microsoft Docs
description: Con Servizi multimediali di Azure è possibile analizzare contenuti audio e video usando AudioAnalyzerPreset e VideoAnalyzerPreset.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 11/17/2018
ms.author: juliako
ms.openlocfilehash: 95d3f0aac4acdfbd70dcadd8db5c13456e83a7e7
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344313"
---
# <a name="analyzing-video-and-audio-files"></a>Analisi di file video e audio

Servizi multimediali di Azure v3 consente anche di estrarre informazioni dettagliate da file audio e video con Video Indexer tramite i set di impostazioni di analisi di Servizi multimediali di Azure v3 descritti in questo articolo. Per ottenere informazioni più dettagliate, usare direttamente Video Indexer. Per comprendere quando è opportuno usare Video Indexer invece dei set di impostazioni di analisi di Servizi multimediali, vedere il [documento di confronto](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Per eseguire l'analisi con Servizi multimediali di Azure v3, si crea un oggetto **Transform** e si invia un oggetto **Job** che usa uno dei due set di impostazioni: **AudioAnalyzerPreset** oppure **VideoAnalyzerPreset**. L'articolo seguente illustra come usare **VideoAnalyzerPreset**: [Esercitazione: Analizzare i video con Servizi multimediali di Azure](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> Quando si usa un set di impostazioni di analisi video o audio, usare il portale di Azure per impostare l'account in modo che abbia 10 Media Reserved Units S3. Per altre informazioni, vedere [Panoramica del ridimensionamento dell'elaborazione multimediale](../previous/media-services-scale-media-processing-overview.md).

## <a name="built-in-presets"></a>Set di impostazioni predefiniti

Attualmente Servizi multimediali supporta i set di impostazioni di analisi predefiniti seguenti:  

|**Nome set di impostazioni**|**Scenario**|**Dettagli**|
|---|---|---|
|**AudioAnalyzerPreset**|Analisi dell'audio|Il set di impostazioni applica un set predefinito di operazioni di analisi basate su intelligenza artificiale, tra cui la trascrizione del parlato. Attualmente il set di impostazioni supporta l'elaborazione del contenuto con una singola traccia audio. È possibile specificare la lingua per il payload audio nell'input usando il formato BCP-47 per 'tag lingua-area'. Sono supportate le lingua: inglese ('en-US' e 'en-GB'), spagnolo ('es-ES' ed 'es-MX'), francese ('fr-FR'), italiano ('it-IT'), giapponese ('ja-JP'), portoghese ('pt-BR'), cinese ('zh-CN'), tedesco ('de-DE'), arabo ('ar-EG'), russo ('ru-RU'), hindi ('hi-IN') e coreano ('ko-KR').<br/><br/> Se la lingua non è specificata o è impostata su Null, verrà usato il rilevamento automatico della lingua. La funzionalità di rilevamento automatico della lingua attualmente supporta inglese, cinese, francese, tedesco, italiano, giapponese, spagnolo, russo e portoghese. La funzionalità di rilevamento automatico della lingua funziona in modo ottimale con registrazioni audio con parlato chiaro e percepibile. Se il rilevamento automatico della lingua non riesce a individuare la lingua, la trascrizione tornerà all'inglese.|
|**VideoAnalyzerPreset**|Analisi di audio e video|Estrae informazioni cognitive dettagliate (metadati avanzati) da audio e video e restituisce un file in formato JSON. È possibile specificare se si vogliono estrarre solo informazioni dettagliate sull'audio durante l'elaborazione di un file video. Per altre informazioni, vedere [Analizzare i video](analyze-videos-tutorial-with-api.md).|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

Il set di impostazioni consente di estrarre informazioni dettagliate sui contenuti audio da un file audio o video. L'output include un file in formato JSON, con tutte le informazioni dettagliate, e un file in formato VTT per la trascrizione dell'audio. Questo set di impostazioni accetta una proprietà che specifica la lingua del file di input sotto forma di stringa [BCP47](https://tools.ietf.org/html/bcp47). Le informazioni dettagliate sui contenuti audio includono:

* Trascrizione dell'audio: una trascrizione del parlato con timestamp. Sono supportate più lingue.
* Indicizzazione dei parlanti: un mapping dei parlanti e delle parole pronunciate corrispondenti.
* Analisi del sentiment vocale: l'output dell'analisi del sentiment eseguita sulla trascrizione dell'audio.
* Parole chiave: le parole chiave estratte dalla trascrizione dell'audio.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

Il set di impostazioni consente di estrarre da un file video più informazioni dettagliate sui contenuti audio e video. L'output include un file in formato JSON, con tutte le informazioni dettagliate, un file in formato VTT per la trascrizione del video e una raccolta di anteprime. Anche questo set di impostazioni accetta come proprietà una stringa [BCP47](https://tools.ietf.org/html/bcp47) che rappresenta la lingua del video. Le informazioni dettagliate sul video includono quelle già indicate per l'audio e gli elementi seguenti:

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

### <a name="faces"></a>faces

|NOME|DESCRIZIONE|
|---|---|
|id|ID del volto.|
|name|Nome del volto. Può essere 'Unknown #0', un personaggio noto identificato o una persona per cui il cliente ha eseguito il training.|
|confidence|Grado di attendibilità dell'identificazione del volto.|
|description|Descrizione del personaggio noto. |
|thumbnalId|ID dell'anteprima del volto.|
|knownPersonId|Nel caso di una persona nota, il relativo ID interno.|
|referenceId|Nel caso di un personaggio noto di Bing, il relativo ID Bing.|
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

### <a name="shots"></a>shots

|NOME|DESCRIZIONE|
|---|---|
|id|ID dello scatto.|
|keyFrames|Elenco dei fotogrammi chiave inclusi nello scatto, ciascuno con un ID e un elenco degli intervalli di tempo delle istanze. Le istanze dei fotogrammi chiave hanno un campo thumbnailId con l'ID anteprima del fotogramma chiave.|
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
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
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
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
      "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="statistics"></a>statistics

|NOME|DESCRIZIONE|
|---|---|
|CorrespondenceCount|Numero di corrispondenze nel video.|
|WordCount|Numero di parole per ogni voce.|
|SpeakerNumberOfFragments|Quantità di frammenti della voce in un video.|
|SpeakerLongestMonolog|Monologo più lungo della voce. Se la voce comprende periodi di silenzio all'interno del monologo, questi vengono inclusi. I periodi di silenzio all'inizio e alla fine del monologo vengono rimossi.| 
|SpeakerTalkToListenRatio|Il calcolo è basato sul tempo impiegato per il monologo della voce (senza i periodi di silenzio intermedi) diviso per il tempo totale del video. Il tempo viene arrotondato alla terza posizione decimale.|


### <a name="sentiments"></a>sentiments

Le valutazioni vengono aggregate in base al campo sentimentType (positivo/neutro/negativo), ad esempio 0-0.1, 0.1-0.2.

|NOME|DESCRIZIONE|
|---|---|
|id|ID della valutazione.|
|averageScore |Media di tutti i punteggi di tutte le istanze di un determinato tipo di valutazione: positivo/neutro/negativo.|
|instances|Elenco degli intervalli di tempo in cui è presente la valutazione.|
|sentimentType |Il tipo può essere "Positive", "Neutral" o "Negative".|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
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

#### <a name="visualcontentmoderation"></a>visualContentModeration

Il blocco visualContentModeration contiene gli intervalli di tempo in cui Video Indexer ha rilevato contenuti potenzialmente per adulti. Se visualContentModeration è vuoto, non sono stati identificati contenuti per adulti.

I video in cui vengono rilevati contenuti per adulti o spinti potrebbero essere disponibili solo per la visualizzazione privata. Gli utenti hanno la possibilità di inviare una richiesta per una revisione umana del contenuto, nel quale caso l'attributo IsAdult conterrà il risultato della revisione umana.

|NOME|DESCRIZIONE|
|---|---|
|id|ID della moderazione dei contenuti visivi.|
|adultScore|Punteggio contenuti per adulti (dalla moderazione del contenuto).|
|racyScore|Punteggio contenuti spinti (dalla moderazione del contenuto).|
|instances|Elenco degli intervalli di tempo in cui è presente questa moderazione dei contenuti visivi.|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```
## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: Analizzare i video con Servizi multimediali di Azure](analyze-videos-tutorial-with-api.md)
