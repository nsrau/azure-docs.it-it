---
title: Riconoscimento ottico di caratteri (OCR)-Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti relativi al riconoscimento ottico dei caratteri (OCR) di immagini e documenti con testo stampato e scritto a mano usando il API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e2226f70ed3318bb370f0afee003fd9f91153a45
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167870"
---
# <a name="optical-character-recognition-ocr"></a>Riconoscimento ottico dei caratteri (OCR)

API Visione artificiale di Azure include funzionalità di riconoscimento ottico dei caratteri (OCR) che estraggono testo stampato o scritto a mano dalle immagini. È possibile estrarre il testo dalle immagini, ad esempio le foto delle targhe o dei contenitori con i numeri di serie, nonché da documenti-fatture, fatture, rapporti finanziari, articoli e altro ancora. 

## <a name="read-api"></a>API di lettura 

L' [API visione artificiale Read](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) è la tecnologia OCR più recente di Azure che estrae il testo stampato (in diversi linguaggi), il testo scritto a mano (solo in inglese), le cifre e i simboli di valuta da immagini e documenti PDF a più pagine. È ottimizzato per estrarre testo da immagini con un elevato numero di testo e documenti PDF a più pagine con lingue miste. Supporta il rilevamento di testo stampato e scritto a mano nella stessa immagine o documento.

![Come OCR converte immagini e documenti in un output strutturato con testo Estratto](./Images/how-ocr-works.svg)

L'API Read fornisce funzionalità OCR tramite due operazioni: **lettura** e lettura **dei risultati**.

## <a name="the-read-operation"></a>Operazione di lettura

L' [operazione di lettura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) accetta un'immagine o un documento PDF come input ed estrae il testo in modo asincrono. La chiamata restituisce con un campo di intestazione della risposta denominato `Operation-Location` . Il `Operation-Location` valore è un URL che contiene l'ID operazione da usare nel passaggio successivo.

|Intestazione risposta| URL risultato |
|:-----|:----|
|Operation-Location | https://cognitiveservice/vision/v3.0-preview/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f |

## <a name="the-get-read-results-operation"></a>Operazione Get Read Results

Il secondo passaggio consiste nel chiamare l'operazione [Get Read results](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) . Questa operazione accetta come input l'ID operazione creato dall'operazione di lettura. Restituisce una risposta JSON che contiene un campo di **stato** con i valori possibili seguenti. Questa operazione viene chiamata in modo iterativo fino a quando non viene restituita con il valore **succeeded** . Utilizzare un intervallo da 1 a 2 secondi per evitare il superamento della frequenza di richieste al secondo (RPS).

|Campo| Type | Valori possibili |
|:-----|:----:|:----|
|status | string | notStarted: l'operazione non è stata avviata. |
| |  | Running: l'operazione è in corso di elaborazione. |
| |  | non riuscito: l'operazione non è riuscita. |
| |  | Succeeded: l'operazione è riuscita. |

> [!NOTE]
> Il livello gratuito limita la frequenza delle richieste a 20 chiamate al minuto. Il livello a pagamento consente 10 richieste al secondo (RPS) che possono essere aumentate su richiesta. Usare il canale del supporto di Azure o il team dell'account per richiedere una tariffa di richiesta al secondo (RPS) superiore.

Quando il campo **stato** ha il valore **succeeded** , la risposta JSON contiene il contenuto di testo estratto dall'immagine o dal documento. La risposta JSON gestisce i raggruppamenti di righe originali delle parole riconosciute. Sono incluse le righe di testo estratte e le relative coordinate del rettangolo di delimitazione. Ogni riga di testo include tutte le parole estratte con le relative coordinate e punteggi di confidenza.

### <a name="sample-json-output"></a>Output JSON di esempio

Vedere l'esempio seguente di una risposta JSON riuscita:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              }
            ]
          }
        ]
      }
    ]
  }
}
```

Seguire la Guida introduttiva alla [stampa e al testo scritto a mano](./QuickStarts/CSharp-hand-text.md) per implementare OCR con C# e l'API REST.

## <a name="input-requirements"></a>Requisiti di input

Per le immagini e i documenti di input sono necessari i requisiti seguenti:
* Formati di file supportati: JPEG, PNG, BMP, PDF e TIFF
* Per PDF e TIFF, vengono elaborate fino a 2000 pagine. Per i sottoscrittori del livello gratuito, vengono elaborate solo le prime due pagine.
* Le dimensioni del file devono essere inferiori a 50 MB e dimensioni di almeno 50 x 50 pixel e al massimo 10000 x 10000 pixel.
* Le dimensioni PDF devono essere al massimo 17 x 17 pollici, corrispondenti a dimensioni della carta note o a3 e inferiori.

> [!NOTE]
> **Input della lingua** 
>
> L' [operazione di lettura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) ha un parametro di richiesta facoltativo per la lingua. Si tratta del codice della lingua BCP-47 del testo nel documento. Read supporta l'identificazione automatica della lingua e i documenti multilingue, in modo da fornire solo un codice di lingua se si vuole forzare l'elaborazione del documento come linguaggio specifico.

## <a name="language-support"></a>Lingue supportate

### <a name="printed-text"></a>Testo stampato
L' [API Read 3,0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) supporta l'estrazione di testo stampato in lingua inglese, spagnolo, tedesco, francese, italiano, portoghese e olandese. 

L' [anteprima pubblica dell'API Read 3,1](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) aggiunge il supporto per il cinese semplificato. Se lo scenario richiede il supporto di più lingue, vedere la sezione [API OCR](#ocr-api) . 

Vedere le [lingue supportate](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) per l'elenco completo dei linguaggi supportati dall'OCR.

### <a name="handwritten-text"></a>Testo scritto a mano
L'operazione di lettura supporta attualmente l'estrazione del testo scritto a mano esclusivamente in inglese.

## <a name="integration-options"></a>Opzioni di integrazione

### <a name="use-the-rest-api-or-client-sdk"></a>Usare l'API REST o l'SDK client
L' [API REST Read 3. x](./QuickStarts/CSharp-hand-text.md) è l'opzione consigliata per la maggior parte dei clienti grazie alla facilità di integrazione e alla produttività rapida. Azure e il servizio Visione artificiale gestiscono la scalabilità, le prestazioni, la sicurezza dei dati e le esigenze di conformità pur concentrandosi sulla soddisfazione delle esigenze dei clienti.

### <a name="use-containers-for-on-premise-deployment"></a>Usare i contenitori per la distribuzione locale
Il [contenitore Docker Read 2,0 (anteprima)](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers) consente di distribuire le nuove funzionalità OCR nell'ambiente locale. I contenitori sono ottimi per requisiti specifici di sicurezza e governance dei dati.

## <a name="read-ocr-examples"></a>Leggi esempi di OCR

### <a name="text-from-images"></a>Testo dalle immagini

L'output dell'API Read seguente mostra il testo Estratto da un'immagine con diversi angoli di testo, colori e tipi di carattere.

![Immagine di diverse parole con colori e angoli diversi, con il testo estratto elencato](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Testo da documenti

L'API Read può anche richiedere documenti PDF come input.

![Un documento di fattura, con il testo estratto elencato](./Images/text-from-pdf-example.png)

### <a name="handwritten-text"></a>Testo scritto a mano

L'operazione di lettura estrae il testo scritto a mano dalle immagini (attualmente solo in inglese).

![Immagine di una nota scritta a mano, con testo estratto elencato](./Images/handwritten-example.png)

### <a name="printed-text"></a>Testo stampato

L'operazione Read consente di estrarre il testo stampato in diversi linguaggi.

![Immagine di un libro di testo spagnolo, con il testo estratto elencato](./Images/supported-languages-example.png)

### <a name="mixed-language-documents"></a>Documenti in linguaggio misto

L'API Read supporta immagini e documenti che contengono più linguaggi diversi, comunemente noti come documenti in linguaggio misto. Funziona classificando ogni riga di testo nel documento nella lingua rilevata prima di estrarre il contenuto di testo.

![Immagine di frasi in diverse lingue, con testo estratto elencato](./Images/mixed-language-example.png)

## <a name="ocr-api"></a>API OCR

L' [API OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) usa un modello di riconoscimento precedente, supporta solo le immagini e viene eseguito in modo sincrono, restituendo immediatamente il testo rilevato. Vedere le [lingue supportate dall'OCR](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) , quindi leggere l'API.

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Come per tutti i servizi cognitivi, gli sviluppatori che usano i servizi di lettura/OCR dovrebbero essere a conoscenza dei criteri Microsoft sui dati dei clienti. Per altre informazioni, vedere la pagina Servizi cognitivi nel [Centro protezione Microsoft](https://www.microsoft.com/trust-center/product-overview) .

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sull' [API REST di lettura 3,0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005).
- Informazioni sull' [API REST Read 3,1 Public Preview](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) con aggiunta del supporto per il cinese semplificato.
- Seguire la Guida introduttiva all' [estrazione del testo](./QuickStarts/CSharp-hand-text.md) per implementare OCR con C#, Java, JavaScript o Python insieme all'API REST.
