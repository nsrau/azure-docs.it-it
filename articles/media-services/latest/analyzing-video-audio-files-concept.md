---
title: Analizzare i file audio e video
titleSuffix: Azure Media Services
description: Informazioni su come analizzare il contenuto audio e video usando AudioAnalyzerPreset e VideoAnalyzerPreset in servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/21/2019
ms.author: juliako
ms.openlocfilehash: 23d546d6adcdb91b4ef4702b81fe77536fe9f3d3
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186268"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>Analizzare i file audio e video con servizi multimediali di Azure

Servizi multimediali di Azure v3 consente di estrarre informazioni approfondite dai file audio e video con Video Indexer. Questo articolo descrive i set di impostazioni di Media Services V3 Analyzer usati per estrarre tali informazioni. Per ottenere informazioni più dettagliate, usare direttamente Video Indexer. Per informazioni su quando usare Video Indexer e i set di impostazioni di Media Services Analyzer, vedere il [documento di confronto](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Per analizzare i contenuti usando i set di impostazioni di Media Services V3, creare una **trasformazione** e inviare un **processo** che usa uno dei set di impostazioni seguenti: [VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset) o **AudioAnalyzerPreset**. Per un'esercitazione che illustra come usare **VideoAnalyzerPreset**, vedere [analizzare i video con servizi multimediali di Azure](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> Quando si usa un set di impostazioni di analisi video o audio, usare il portale di Azure per impostare l'account in modo che abbia 10 Media Reserved Units S3. Per altre informazioni, vedere [Panoramica del ridimensionamento dell'elaborazione multimediale](media-reserved-units-cli-how-to.md).

## <a name="built-in-presets"></a>Set di impostazioni predefiniti

Attualmente Servizi multimediali supporta i set di impostazioni di analisi predefiniti seguenti:  

|**Nome set di impostazioni**|**Scenario**|**Dettagli**|
|---|---|---|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Analisi dell'audio|Il set di impostazioni applica un set predefinito di operazioni di analisi basate su intelligenza artificiale, tra cui la trascrizione del parlato. Attualmente, il set di impostazioni supporta l'elaborazione del contenuto con una singola traccia audio che contiene il parlato in una sola lingua. È possibile specificare la lingua per il payload audio nell'input usando il formato BCP-47 per 'tag lingua-area'. Le lingue supportate sono inglese ("en-US" e "en-GB"), spagnolo ("es-ES" ed "es-MX"), francese ("fr-FR"), Italiano (it-IT), giapponese ("ja-JP"), portoghese ("PT-BR"), cinese ("zh-CN"), tedesco ("de-DE"), arabo ("ar-EG" e "ar-SY"), russo ("ru-ur"), Hindi (' Hi-IN ') e coreano (' ko-KR ').<br/><br/> Se la lingua non è specificata o è impostata su null, il rilevamento automatico della lingua sceglie il primo linguaggio rilevato e continua con la lingua selezionata per la durata del file. La funzionalità di rilevamento automatico della lingua attualmente supporta inglese, cinese, francese, tedesco, italiano, giapponese, spagnolo, russo e portoghese. Non supporta il cambio dinamico tra le lingue dopo che il primo linguaggio è stato rilevato. La funzionalità di rilevamento automatico della lingua funziona in modo ottimale con registrazioni audio con parlato facilmente comprensibile. Se il rilevamento automatico della lingua non riesce a trovare la lingua, la trascrizione torna all'inglese.|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Analisi di audio e video|Estrae informazioni cognitive dettagliate (metadati avanzati) da audio e video e restituisce un file in formato JSON. È possibile specificare se si vogliono estrarre solo informazioni dettagliate sull'audio durante l'elaborazione di un file video. Per altre informazioni, vedere [Analizzare i video](analyze-videos-tutorial-with-api.md).|
|[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|Rilevamento di visi presenti nel video|Descrive le impostazioni da usare durante l'analisi di un video per rilevare tutti i visi presenti.|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

Il set di impostazioni consente di estrarre informazioni dettagliate sui contenuti audio da un file audio o video. L'output include un file in formato JSON, con tutte le informazioni dettagliate, e un file in formato VTT per la trascrizione dell'audio. Questo set di impostazioni accetta una proprietà che specifica la lingua del file di input sotto forma di stringa [BCP47](https://tools.ietf.org/html/bcp47). Le informazioni dettagliate sui contenuti audio includono:

* **Trascrizione audio**: una trascrizione delle parole pronunciate con timestamp. Sono supportate più lingue.
* **Indicizzazione del relatore**: mapping degli altoparlanti e parole pronunciate corrispondenti.
* **Analisi del sentimento vocale**: output dell'analisi dei sentimenti eseguita sulla trascrizione audio.
* **Parole chiave**: parole chiave estratte dalla trascrizione audio.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

Il set di impostazioni consente di estrarre da un file video più informazioni dettagliate sui contenuti audio e video. L'output include un file in formato JSON, con tutte le informazioni dettagliate, un file in formato VTT per la trascrizione del video e una raccolta di anteprime. Anche questo set di impostazioni accetta come proprietà una stringa [BCP47](https://tools.ietf.org/html/bcp47) che rappresenta la lingua del video. Le informazioni dettagliate sul video includono quelle già indicate per l'audio e gli elementi seguenti:

* **Rilevamento viso**: tempo durante il quale i visi sono presenti nel video. Ogni volto ha un ID viso e una raccolta corrispondente di anteprime.
* **Testo visivo**: il testo rilevato tramite il riconoscimento ottico dei caratteri. Il testo ha un timestamp e viene usato anche per estrarre le parole chiave, oltre alla trascrizione audio.
* **Fotogrammi chiave**: una raccolta di fotogrammi chiave estratti dal video.
* **Moderazione del contenuto visivo**: la parte dei video contrassegnata come adulta o audace per natura.
* **Annotazione**: risultato dell'annotazione dei video in base a un modello a oggetti predefinito

## <a name="insightsjson-elements"></a>Elementi di insights.json

L'output include un file JSON (Insights. Json) con tutte le informazioni dettagliate disponibili nel video o audio. JSON può contenere gli elementi seguenti:

### <a name="transcript"></a>transcript

|Nome|DESCRIZIONE|
|---|---|
|id|ID della riga.|
|text|Testo della trascrizione.|
|Lingua|Lingua della trascrizione. Questo elemento è stato progettato per supportare trascrizioni in cui ogni riga può avere una lingua diversa.|
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

|Nome|DESCRIZIONE|
|---|---|
|id|ID della riga di riconoscimento ottico dei caratteri.|
|text|Testo risultante dal riconoscimento ottico dei caratteri.|
|confidence|Grado di attendibilità del riconoscimento.|
|Lingua|Lingua del riconoscimento ottico dei caratteri.|
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

|Nome|DESCRIZIONE|
|---|---|
|id|ID del volto.|
|Nome|Nome del volto. Può essere "Unknown #0", una celebrità identificata o una persona con training del cliente.|
|confidence|Grado di attendibilità dell'identificazione del volto.|
|Descrizione|Descrizione del personaggio noto. |
|thumbnailId|ID dell'anteprima del volto.|
|knownPersonId|ID interno (se si tratta di una persona nota).|
|referenceId|ID Bing (se si tratta di una celebrità Bing).|
|referenceType|Attualmente solo Bing.|
|title|Il titolo (se si tratta di una celebrità, ad esempio, "CEO di Microsoft").|
|imageUrl|L'URL dell'immagine, se si tratta di una celebrità.|
|instances|Istanze in cui la faccia appare nell'intervallo di tempo specificato. Ogni istanza è associata anche un thumbnailsId. |

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

|Nome|DESCRIZIONE|
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

|Nome|DESCRIZIONE|
|---|---|
|CorrespondenceCount|Numero di corrispondenze nel video.|
|WordCount|Numero di parole per ogni voce.|
|SpeakerNumberOfFragments|Quantità di frammenti della voce in un video.|
|SpeakerLongestMonolog|Monologo più lungo della voce. Se il relatore si trova nel monologo, è incluso. I periodi di silenzio all'inizio e alla fine del monologo vengono rimossi.|
|SpeakerTalkToListenRatio|Il calcolo è basato sul tempo impiegato per il monologo della voce (senza i periodi di silenzio intermedi) diviso per il tempo totale del video. Il tempo viene arrotondato alla terza posizione decimale.|


### <a name="sentiments"></a>sentiments

Le valutazioni vengono aggregate in base al campo sentimentType (positivo/neutro/negativo), ad esempio 0-0.1, 0.1-0.2.

|Nome|DESCRIZIONE|
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

|Nome|DESCRIZIONE|
|---|---|
|id|ID dell'etichetta.|
|Nome|Nome dell'etichetta, ad esempio 'Computer' o 'TV'.|
|Lingua|Lingua del nome dell'etichetta, quando tradotto. BCP-47|
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

|Nome|DESCRIZIONE|
|---|---|
|id|ID della parola chiave.|
|text|Testo della parola chiave.|
|confidence|Grado di attendibilità del riconoscimento della parola chiave.|
|Lingua|Lingua della parola chiave, quando tradotta.|
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

Il blocco visualContentModeration contiene gli intervalli di tempo in cui Video Indexer ha rilevato contenuti potenzialmente per adulti. Se visualContentModeration è vuoto, non è presente alcun contenuto per adulti identificato.

I video in cui vengono rilevati contenuti per adulti o spinti potrebbero essere disponibili solo per la visualizzazione privata. Gli utenti possono inviare una richiesta di revisione umana del contenuto, nel qual caso l'attributo `IsAdult` conterrà il risultato della revisione umana.

|Nome|DESCRIZIONE|
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
