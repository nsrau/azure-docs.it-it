---
title: Esaminare l'output del Video Indexer prodotto dall'API v2-Azure
titleSuffix: Azure Media Services
description: Questo argomento esamina l'output del Video Indexer di servizi multimediali di Azure prodotto dall'API v2.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/16/2020
ms.author: juliako
ms.openlocfilehash: bf48f873127a12c3cabb28da33d34cedcda2793b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94831567"
---
# <a name="examine-the-video-indexer-output"></a>Esaminare l'output del Video Indexer

Quando un video viene indicizzato, Video Indexer produce il contenuto JSON che contiene i dettagli del video specificato. Le informazioni dettagliate includono: trascrizioni, OCR, visi, argomenti, blocchi e così via. Ogni tipo di informazioni include le istanze degli intervalli temporali che mostrano quando l'Insight viene visualizzato nel video. 

È possibile esaminare visivamente le informazioni dettagliate riepilogate del video premendo il pulsante **Riproduci** sul video nel sito Web [video Indexer](https://www.videoindexer.ai/) . 

È anche possibile usare l'API chiamando il **Get video index** API e lo stato della risposta è OK, si ottiene un output JSON dettagliato come contenuto della risposta.

![Informazioni dettagliate](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Questo articolo esamina l'output del Video Indexer (contenuto JSON). <br/>Per informazioni sulle funzionalità e le informazioni dettagliate disponibili, vedere [video Indexer Insights](video-indexer-overview.md#video-insights).

> [!NOTE]
> La scadenza di tutti i token di accesso in Video Indexer è di un'ora.

## <a name="get-the-insights"></a>Ottieni informazioni dettagliate

### <a name="insightsoutput-produced-in-the-websiteportal"></a>Informazioni dettagliate/output prodotto nel sito Web/portale

1. Passare al sito Web di [Video Indexer](https://www.videoindexer.ai/) ed eseguire l'accesso.
1. Trovare un video dell'output di cui si vuole esaminare l'output.
1. Premere **Riproduci**.
1. Selezionare la scheda **Insights** (informazioni dettagliate riepilogate) o la scheda **sequenza temporale** (consente di filtrare le informazioni rilevanti).
1. Scaricare gli artefatti e gli elementi in essi contenuti.

Per altre informazioni, vedere [Visualizzare e analizzare le informazioni dettagliate per i video](video-indexer-view-edit.md).

## <a name="insightsoutput-produced-by-api"></a>Informazioni dettagliate/output prodotto dall'API

1. Per recuperare il file JSON, chiamare [Get video index API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?)
1. Se si è interessati anche a elementi specifici, chiamare [Get video artefatte URL download API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Artifact-Download-Url?)

    Nella chiamata API specificare il tipo di artefatto richiesto (OCR, visi, fotogrammi chiave e così via)

## <a name="root-elements-of-the-insights"></a>Elementi radice delle informazioni dettagliate

|Nome|Descrizione|
|---|---|
|accountId|ID account Video Indexer della playlist.|
|id|ID della playlist.|
|name|Nome della playlist.|
|description|Descrizione della playlist.|
|userName|Nome dell'utente che ha creato la playlist.|
|created|Ora di creazione della playlist.|
|privacyMode|Modalità di privacy della playlist (privata/pubblica).|
|state|Stato della playlist (caricata, in elaborazione, elaborata, non riuscita, in quarantena).|
|isOwned|Indica se la playlist è stata creata dall'utente corrente.|
|isEditable|Indica se l'utente corrente è autorizzato a modificare la playlist.|
|isBase|Indica se la playlist è una playlist di base (un video) o una playlist composta da altri video (derivata).|
|durationInSeconds|Durata totale della playlist.|
|summarizedInsights|Contiene un elemento [summarizedInsights](#summarizedinsights).
|videos|Elenco di [video](#videos) che costituiscono la playlist.<br/>Se questa playlist è costituta da intervalli di tempo di altri video (derivata), i video in questo elenco conterranno solo i dati relativi agli intervalli di tempo inclusi.|

```json
{
  "accountId": "bca61527-1221-bca6-1527-a90000002000",
  "id": "abc3454321",
  "name": "My first video",
  "description": "I am trying VI",
  "userName": "Some name",
  "created": "2018/2/2 18:00:00.000",
  "privacyMode": "Private",
  "state": "Processed",
  "isOwned": true,
  "isEditable": false,
  "isBase": false,
  "durationInSeconds": 120, 
  "summarizedInsights" : { . . . }
  "videos": [{ . . . }]
}
```

## <a name="summarizedinsights"></a>summarizedInsights

Questa sezione mostra il riepilogo delle informazioni dettagliate.

|Attributo | Descrizione|
|---|---|
|name|Nome del video. Ad esempio, Monitoraggio di Azure.|
|id|ID del video. Ad esempio, 63c6d532ff.|
|privacyMode|La scomposizione può avere una delle modalità seguenti: **Private** o **Public**. **Public**: il video è visibile per tutti gli utenti nell'account e per chiunque disponga di un collegamento al video. **Private**: il video è visibile per tutti gli utenti nell'account.|
|duration|Contiene una durata che definisce il tempo associato a un'informazione dettagliata. La durata è espressa in secondi.|
|thumbnailVideoId|ID del video da cui è stata ottenuta l'anteprima.
|thumbnailId|ID dell'anteprima del video. Per ottenere l'anteprima effettiva, chiamare [Get-thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) e passarla ThumbnailVideoId e thumbnailId.|
|visi/animatedCharacters|Può contenere zero o più volti. Per informazioni più dettagliate, vedere [visi/animatedCharacters](#facesanimatedcharacters).|
|keywords|Può contenere zero o più parole chiave. Per informazioni più dettagliate, vedere [keywords](#keywords).|
|sentiments|Può contenere zero o più valutazioni. Per informazioni più dettagliate, vedere [sentiments](#sentiments).|
|audioEffects| Può contenere zero o più audioEffects. Per informazioni più dettagliate, vedere [audioEffects](#audioeffects).|
|Etichette| Può contenere zero o più etichette. Per informazioni più dettagliate, vedere [labels](#labels).|
|brands| Può contenere zero o più marchi. Per informazioni più dettagliate, vedere [brands](#brands).|
|statistiche | Per informazioni più dettagliate, vedere [statistics](#statistics).|
|emotions| Può contenere zero o più emozioni. Per informazioni più dettagliate, vedere [emotions](#emotions).|
|topics|Può contenere zero o più argomenti. Informazioni dettagliate sugli [argomenti](#topics) .|

## <a name="videos"></a>videos

|Nome|Descrizione|
|---|---|
|accountId|ID account Video Indexer del video.|
|id|ID del video.|
|name|Nome del video.
|state|Stato del video (caricato, in elaborazione, elaborato, non riuscito, in quarantena).|
|processingProgress|Stato di elaborazione durante l'elaborazione (ad esempio, 20%).|
|failureCode|Codice di errore, se l'elaborazione non è riuscita (ad esempio, "UnsupportedFileType").|
|failureMessage|Messaggio di errore, se l'elaborazione non è riuscita.|
|externalId|ID esterno del video (se specificato dall'utente).|
|externalUrl|URL esterno del video (se specificato dall'utente).|
|metadata|Metadati esterni del video (se specificati dall'utente).|
|isAdult|Indica se il video è stato esaminato e identificato manualmente come un video per adulti.|
|insights|Oggetto insights. Per altre informazioni, vedere [insights](#insights).|
|thumbnailId|ID dell'anteprima del video. Per ottenere l'anteprima effettiva, chiamare [Get-thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) e passargli l'ID video e thumbnailId.|
|publishedUrl|URL per lo streaming del video.|
|publishedUrlProxy|URL da cui eseguire lo streaming del video (per i dispositivi Apple).|
|viewToken|Token di visualizzazione di breve durata per lo streaming del video.|
|sourceLanguage|Lingua di origine del video.|
|Linguaggio|Lingua effettiva del video (traduzione).|
|indexingPreset|Set di impostazioni usato per indicizzare il video.|
|streamingPreset|Set di impostazioni usato per pubblicare il video.|
|linguisticModelId|Modello CRIS usato per la trascrizione del video.|
|statistiche | Per ulteriori informazioni, vedere [statistiche](#statistics).|

```json
{
    "videos": [{
        "accountId": "2cbbed36-1972-4506-9bc7-55367912df2d",
        "id": "142a356aa6",
        "state": "Processed",
        "privacyMode": "Private",
        "processingProgress": "100%",
        "failureCode": "General",
        "failureMessage": "",
        "externalId": null,
        "externalUrl": null,
        "metadata": null,
        "insights": {. . . },
        "thumbnailId": "89d7192c-1dab-4377-9872-473eac723845",
        "publishedUrl": "https://videvmediaservices.streaming.mediaservices.windows.net:443/d88a652d-334b-4a66-a294-3826402100cd/Xamarine.ism/manifest",
        "publishedProxyUrl": null,
        "viewToken": "Bearer=<token>",
        "sourceLanguage": "En-US",
        "language": "En-US",
        "indexingPreset": "Default",
        "linguisticModelId": "00000000-0000-0000-0000-000000000000"
    }],
}
```
### <a name="insights"></a>insights

Ogni informazione, ad esempio linee trascrizioni, visi, marchi e così via, contiene un elenco di elementi univoci, ad esempio face1, Face2, face3, e ogni elemento ha i propri metadati e un elenco delle relative istanze (ovvero intervalli di tempo con metadati facoltativi aggiuntivi).

Una faccia potrebbe avere un ID, un nome, un'anteprima, altri metadati e un elenco delle istanze temporali, ad esempio: 00:00:05-00:00:10, 00:01:00-00:02:30 e 00:41:21-00:41:49. Ogni istanza temporale può avere metadati aggiuntivi. ad esempio le coordinate del rettangolo del volto (20,230,60,60).

|Versione|Versione del codice|
|---|---|
|sourceLanguage|Lingua di origine del video (presupponendo una sola lingua principale), nel formato di una stringa [BCP-47](https://tools.ietf.org/html/bcp47).|
|Linguaggio|Lingua delle informazioni dettagliate (tradotte dalla lingua di origine), nel formato di una stringa [BCP-47](https://tools.ietf.org/html/bcp47).|
|transcript|Informazioni dettagliate sulle [trascrizioni](#transcript) .|
|ocr|Informazioni dettagliate sull' [OCR](#ocr) .|
|keywords|[Parole chiave](#keywords) Insight.|
|blocks|Può contenere uno o più [blocchi](#blocks)|
|visi/animatedCharacters|Informazioni dettagliate su [visi/animatedCharacters](#facesanimatedcharacters) .|
|Etichette|Informazioni dettagliate sulle [etichette](#labels) .|
|shots|Informazioni [dettagliate](#shots) .|
|brands|Informazioni dettagliate sui [marchi](#brands) .|
|audioEffects|Informazioni dettagliate su [audioEffects](#audioeffects) .|
|sentiments|Informazioni dettagliate sui [sentimenti](#sentiments) .|
|visualContentModeration|Informazioni dettagliate su [visualContentModeration](#visualcontentmoderation) .|
|textualContentModeration|Informazioni dettagliate su [textualContentModeration](#textualcontentmoderation) .|
|emotions| Le [emozioni](#emotions) approfondite.|
|topics|Informazioni dettagliate sugli [argomenti](#topics) .|
|altoparlanti|Informazioni dettagliate per gli [speaker](#speakers) .|

Esempio:

```json
{
  "version": "0.9.0.0",
  "sourceLanguage": "en-US",
  "language": "es-ES",
  "transcript": ...,
  "ocr": ...,
  "keywords": ...,
  "faces": ...,
  "labels": ...,
  "shots": ...,
  "brands": ...,
  "audioEffects": ...,
  "sentiments": ...,
  "visualContentModeration": ...,
  "textualContentModeration": ...
}
```

#### <a name="blocks"></a>blocks

Attributo | Descrizione
---|---
id|ID del blocco.|
instances|Elenco degli intervalli di tempo di questo blocco.|

#### <a name="transcript"></a>transcript

|Nome|Descrizione|
|---|---|
|id|ID della riga.|
|testo|Testo della trascrizione.|
|confidence|Attendibilità della trascrizione.|
|speakerId|ID del relatore.|
|Linguaggio|Lingua della trascrizione. Questo elemento è stato progettato per supportare trascrizioni in cui ogni riga può avere una lingua diversa.|
|instances|Elenco degli intervalli di tempo in cui è presente la riga. Se l'istanza corrisponde a un'intera trascrizione, è riportata una sola istanza.|

Esempio:

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
{
  "id":2,
  "text":"ignite 2016. Your mission at Microsoft is to empower every",
  "confidence":0.8944,
  "speakerId":2,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:12.81",
    "adjustedEnd":"0:00:17.03",
    "start":"0:00:12.81",
    "end":"0:00:17.03"
     }
  ]
},
```

#### <a name="ocr"></a>ocr

|Nome|Descrizione|
|---|---|
|id|ID della riga di riconoscimento ottico dei caratteri.|
|testo|Testo risultante dal riconoscimento ottico dei caratteri.|
|confidence|Grado di attendibilità del riconoscimento.|
|Linguaggio|Lingua del riconoscimento ottico dei caratteri.|
|instances|Elenco degli intervalli di tempo in cui è presente la riga di riconoscimento ottico dei caratteri. La stessa riga può apparire più volte.|
|altezza|Altezza del rettangolo OCR|
|top|Posizione superiore in px|
|sinistro| Posizione a sinistra in px|
|width|Larghezza del rettangolo OCR|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 675.971,
      "height": 35,
      "language": "en-US",
      "left": 31,
      "top": 97,
      "width": 400,      
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    }
  ],
```

#### <a name="keywords"></a>keywords

|Nome|Descrizione|
|---|---|
|id|ID della parola chiave.|
|testo|Testo della parola chiave.|
|confidence|Grado di attendibilità del riconoscimento della parola chiave.|
|Linguaggio|Lingua della parola chiave, quando tradotta.|
|instances|Elenco degli intervalli di tempo in cui è presente la parola chiave. La stessa parola chiave può apparire più volte.|

```json
{
    id: 0,
    text: "technology",
    confidence: 1,
    language: "en-US",
    instances: [{
            adjustedStart: "0:05:15.782",
            adjustedEnd: "0:05:16.249",
            start: "0:05:15.782",
            end: "0:05:16.249"
    },
    {
            adjustedStart: "0:04:54.761",
            adjustedEnd: "0:04:55.228",
            start: "0:04:54.761",
            end: "0:04:55.228"
    }]
}
```

#### <a name="facesanimatedcharacters"></a>visi/animatedCharacters

`animatedCharacters` l'elemento sostituisce `faces` se il video è stato indicizzato con un modello di caratteri animati. Questa operazione viene eseguita usando un modello personalizzato in Visione personalizzata, Video Indexer la esegue nei fotogrammi chiave.

Se sono presenti visi (caratteri non animati), Video Indexer USA API Viso su tutti i frame del video per rilevare visi e celebrità.

|Nome|Descrizione|
|---|---|
|id|ID del volto.|
|name|Nome del viso. Può essere 'Unknown #0, un personaggio noto identificato o una persona per cui il cliente ha eseguito il training.|
|confidence|Grado di attendibilità dell'identificazione del volto.|
|description|Descrizione del personaggio noto. |
|thumbnailId|ID dell'anteprima del volto.|
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

#### <a name="labels"></a>Etichette

|Nome|Descrizione|
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

#### <a name="scenes"></a>scenes

|Nome|Descrizione|
|---|---|
|id|ID della scena.|
|instances|Elenco di intervalli temporali di questa scena (una scena può avere solo 1 istanza).|

```json
"scenes":[  
    {  
      "id":0,
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },
    {  
      "id":1,
      "instances":[  
          {  
            "start":"0:00:06.34",
            "end":"0:00:47.047",
            "duration":"0:00:40.707"
          }
      ]
    },

]
```

#### <a name="shots"></a>shots

|Nome|Descrizione|
|---|---|
|id|ID dello scatto.|
|keyFrames|Elenco di fotogrammi chiave all'interno dell'immagine, ognuno con un ID e un elenco di intervalli di tempo di istanze. Ogni istanza del fotogramma chiave ha un campo thumbnailId, che contiene l'ID anteprima del fotogramma chiave.|
|instances|Un elenco di intervalli temporali di questo scatto (un Shot può avere solo 1 istanza).|

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

#### <a name="brands"></a>brands

Nomi di marchi di aziende e prodotti rilevati nella trascrizione del riconoscimento vocale e/o nell'OCR del video. Non include il riconoscimento visivo dei marchi o il rilevamento dei logo.

|Nome|Descrizione|
|---|---|
|id|ID del marchio.|
|name|Nome del marchio.|
|referenceId | Suffisso dell'URL di Wikipedia del marchio. Ad esempio, "Target_Corporation" è il suffisso di [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) .
|referenceUrl | URL di Wikipedia del marchio, se presente. ad esempio [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|description|Descrizione del marchio.|
|tags|Elenco di tag predefiniti associati a questo marchio.|
|confidence|Valore di attendibilità della funzionalità di rilevamento dei marchi di Video Indexer (0-1).|
|instances|Elenco degli intervalli di tempo di questo marchio. Ogni istanza ha un brandType, che indica se il marchio è presente nella trascrizione o nell'OCR.|

```json
"brands": [
{
    "id": 0,
    "name": "MicrosoftExcel",
    "referenceId": "Microsoft_Excel",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft_Excel",
    "referenceType": "Wiki",
    "description": "Microsoft Excel is a sprea..",
    "tags": [],
    "confidence": 0.975,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 00: 31.3000000",
        "end": "00: 00: 39.0600000"
    }
    ]
},
{
    "id": 1,
    "name": "Microsoft",
    "referenceId": "Microsoft",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft",
    "description": "Microsoft Corporation is...",
    "tags": [
    "competitors",
    "technology"
    ],
    "confidence": 1.0,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 01: 44",
        "end": "00: 01: 45.3670000"
    },
    {
        "brandType": "Ocr",
        "start": "00: 01: 54",
        "end": "00: 02: 45.3670000"
    }
    ]
}
]
```

#### <a name="statistics"></a>statistiche

|Nome|Descrizione|
|---|---|
|CorrespondenceCount|Numero di corrispondenze nel video.|
|SpeakerWordCount|Numero di parole per ogni voce.|
|SpeakerNumberOfFragments|Quantità di frammenti della voce in un video.|
|SpeakerLongestMonolog|Monologo più lungo della voce. Se la voce comprende periodi di silenzio all'interno del monologo, questi vengono inclusi. I periodi di silenzio all'inizio e alla fine del monologo vengono rimossi.| 
|SpeakerTalkToListenRatio|Il calcolo è basato sul tempo impiegato per il monologo della voce (senza i periodi di silenzio intermedi) diviso per il tempo totale del video. Il tempo viene arrotondato alla terza posizione decimale.|

#### <a name="audioeffects"></a>audioEffects

|Nome|Descrizione|
|---|---|
|id|L'ID dell'effetto audio.|
|tipo|Tipo di effetto audio, ad esempio applausi, voce o silenzio.|
|instances|Elenco degli intervalli di tempo in cui è presente l'effetto audio.|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Clapping",
    "instances": [
    {
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
        "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```

#### <a name="sentiments"></a>sentiments

Le valutazioni vengono aggregate in base al campo sentimentType (positivo/neutro/negativo), ad esempio 0-0.1, 0.1-0.2.

|Nome|Descrizione|
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

#### <a name="visualcontentmoderation"></a>visualContentModeration

Il blocco visualContentModeration contiene gli intervalli di tempo in cui Video Indexer ha rilevato contenuti potenzialmente per adulti. Se visualContentModeration è vuoto, non sono stati identificati contenuti per adulti.

I video in cui vengono rilevati contenuti per adulti o spinti potrebbero essere disponibili solo per la visualizzazione privata. Gli utenti hanno la possibilità di inviare una richiesta per una revisione umana del contenuto, nel quale caso l'attributo IsAdult conterrà il risultato della revisione umana.

|Nome|Descrizione|
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

#### <a name="textualcontentmoderation"></a>textualContentModeration 

|Nome|Descrizione|
|---|---|
|id|ID della moderazione dei contenuti di testo.|
|bannedWordsCount |Numero di parole escluse.|
|bannedWordsRatio |Rapporto rispetto al numero totale di parole.|

#### <a name="emotions"></a>emotions

Video Indexer identifica le emozioni in base ai suggerimenti vocali e audio. L'emozione identificata potrebbe essere: gioia, tristezza, rabbia o timore.

|Nome|Descrizione|
|---|---|
|id|ID dell'emozione.|
|tipo|Il momento in cui l'emozione è stata identificata in base ai suggerimenti vocali e audio. L'emozione potrebbe essere: gioia, tristezza, rabbia o timore.|
|instances|Elenco degli intervalli di tempo in cui è comparsa l'emozione.|

```json
"emotions": [{
    "id": 0,
    "type": "Fear",
    "instances": [{
      "adjustedStart": "0:00:39.47",
      "adjustedEnd": "0:00:45.56",
      "start": "0:00:39.47",
      "end": "0:00:45.56"
    },
    {
      "adjustedStart": "0:07:19.57",
      "adjustedEnd": "0:07:23.25",
      "start": "0:07:19.57",
      "end": "0:07:23.25"
    }]
  },
  {
    "id": 1,
    "type": "Anger",
    "instances": [{
      "adjustedStart": "0:03:55.99",
      "adjustedEnd": "0:04:05.06",
      "start": "0:03:55.99",
      "end": "0:04:05.06"
    },
    {
      "adjustedStart": "0:04:56.5",
      "adjustedEnd": "0:05:04.35",
      "start": "0:04:56.5",
      "end": "0:05:04.35"
    }]
  },
  {
    "id": 2,
    "type": "Joy",
    "instances": [{
      "adjustedStart": "0:12:23.68",
      "adjustedEnd": "0:12:34.76",
      "start": "0:12:23.68",
      "end": "0:12:34.76"
    },
    {
      "adjustedStart": "0:12:46.73",
      "adjustedEnd": "0:12:52.8",
      "start": "0:12:46.73",
      "end": "0:12:52.8"
    },
    {
      "adjustedStart": "0:30:11.29",
      "adjustedEnd": "0:30:16.43",
      "start": "0:30:11.29",
      "end": "0:30:16.43"
    },
    {
      "adjustedStart": "0:41:37.23",
      "adjustedEnd": "0:41:39.85",
      "start": "0:41:37.23",
      "end": "0:41:39.85"
    }]
  },
  {
    "id": 3,
    "type": "Sad",
    "instances": [{
      "adjustedStart": "0:13:38.67",
      "adjustedEnd": "0:13:41.3",
      "start": "0:13:38.67",
      "end": "0:13:41.3"
    },
    {
      "adjustedStart": "0:28:08.88",
      "adjustedEnd": "0:28:18.16",
      "start": "0:28:08.88",
      "end": "0:28:18.16"
    }]
  }
],
```

#### <a name="topics"></a>topics

Video Indexer deduce gli argomenti principali dalle trascrizioni. Quando possibile, viene inclusa la tassonomia [IPTC](https://iptc.org/standards/media-topics/) di secondo livello. 

|Nome|Descrizione|
|---|---|
|id|ID dell'argomento.|
|name|Nome dell'argomento, ad esempio "Prodotti farmaceutici".|
|referenceId|Percorsi di navigazione che riflettono la gerarchia di argomenti. Ad esempio: "Salute e benessere / Medicina e sanità / Prodotti farmaceutici".|
|confidence|Punteggio di attendibilità nell'intervallo [0,1]. A un punteggio maggiore corrisponde una maggiore attendibilità.|
|Linguaggio|Lingua usata nell'argomento.|
|iptcName|Nome del codice per i media IPTC, se rilevato.|
|instances |Video Indexer attualmente non esegue l'indicizzazione di un argomento in base a intervalli di tempo, quindi l'intero video viene usato come intervallo.|

```json
"topics": [{
    "id": 0,
    "name": "INTERNATIONAL RELATIONS",
    "referenceId": "POLITICS AND GOVERNMENT/FOREIGN POLICY/INTERNATIONAL RELATIONS",
    "referenceType": "VideoIndexer",
    "confidence": 1,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}, {
    "id": 1,
    "name": "Politics and Government",
    "referenceType": "VideoIndexer",
    "iptcName": "Politics",
    "confidence": 0.9041,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}]
. . .
```

#### <a name="speakers"></a>altoparlanti

|Nome|Descrizione|
|---|---|
|id|ID altoparlante.|
|name|Il nome del relatore sotto forma di "Speaker # *<number>* ", ad esempio: "speaker #1".|
|instances |Elenco di intervalli di tempo in cui è stato visualizzato questo altoparlante.|

```json
"speakers":[
{
  "id":1,
  "name":"Speaker #1",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
{
  "id":2,
  "name":"Speaker #2",
  "instances":[
     {
    "adjustedStart":"0:00:12.81",
    "adjustedEnd":"0:00:17.03",
    "start":"0:00:12.81",
    "end":"0:00:17.03"
     }
  ]
},
` ` `
```
## <a name="next-steps"></a>Passaggi successivi

[Portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai)

Per informazioni su come incorporare i widget nell'applicazione, vedere [Embed Video Indexer widgets into your applications](video-indexer-embed-widgets.md) (Incorporare i widget di Video Indexer nelle applicazioni). 

