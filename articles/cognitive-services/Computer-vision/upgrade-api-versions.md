---
title: Eseguire l'aggiornamento a Read v3.0 dell'API Visione artificiale
titleSuffix: Azure Cognitive Services
description: Informazioni su come eseguire l'aggiornamento all'API di lettura di Visione artificiale versione 3.0 dalla versione 2.0/v 2.1.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: c3394156b073df54d6582dc43571137b21df29cd
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91968940"
---
# <a name="upgrade-from-read-v2x-to-read-v3x"></a>Eseguire l'aggiornamento da Read v2.x a Read v3.x

Questa guida illustra come eseguire l'aggiornamento del contenitore esistente o del codice dell'API cloud da Read v2.x a Read v3.0 e all'anteprima di v3.1.

## <a name="determine-your-api-path"></a>Determinare il percorso dell'API
Usare la tabella seguente per determinare la **stringa di versione** nel percorso dell'API in base alla versione di Read 3.x a cui si esegue la migrazione.

|Tipo di prodotto| Versione | Stringa di versione nel percorso dell'API 3.x |
|:-----|:----|:----|
|Servizio | Read 3.0 | **v3.0** |
|Contenitore | Anteprima di Read 3.0 | **v3.0** |
|Servizio/Contenitore | Anteprima di Read 3.1 | **v3.1-preview.2** |

Usare quindi le sezioni seguenti per limitare le operazioni e sostituire la **stringa di versione** nel percorso dell'API con il valore dalla tabella. Per le versioni cloud e contenitore dell'**anteprima di Read v3.1**, ad esempio, aggiornare il percorso dell'API a **https://{endpoint}/vision/v3.1-preview.2/read/analyze[?language]** .

## <a name="servicecontainer"></a>Servizio/Contenitore

### `Batch Read File`

|Read 2.x |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/core/asyncBatchAnalyze**     |https://{endpoint}/vision/<**stringa di versione**>/read/analyze[?language]|
    
È disponibile un nuovo parametro facoltativo _language_. Se non si conosce la lingua del documento o se il documento è multilingue, non includere questo parametro. 

### `Get Read Results`

|Read 2.x |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/operations**/{operationId}     |https://{endpoint}/vision/<**stringa di versione**>/read/analyzeResults/{operationId}|

### <a name="get-read-operation-result-status-flag"></a>Flag di stato `Get Read Operation Result`

Quando la chiamata a `Get Read Operation Result` ha esito positivo, restituisce un campo di stringa di stato nel corpo JSON.
 
|Read 2.x |Read 3.x  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|
    
### <a name="api-response-json"></a>Risposta API (JSON) 

Osservare le modifiche seguenti in JSON:
* Nella versione 2.x `Get Read Operation Result` restituisce il codice JSON di riconoscimento ottico dei caratteri (OCR) quando lo stato è `Succeeded"`. Nella versione 3.0 questo campo è `succeeded`.
* Per ottenere la radice per la matrice di pagine, modificare la gerarchia JSON da `recognitionResults` a `analyzeResult`/`readResults`. La gerarchia JSON di righe e parole per pagina rimane invariata, quindi non sono necessarie modifiche al codice.
* L'angolo della pagina `clockwiseOrientation` è stato rinominato in `angle` e l'intervallo è stato modificato da un valore compreso tra 0 e 360 gradi a un valore compreso tra -180 e 180 gradi. A seconda del codice, può essere necessario o meno apportare modifiche, in quanto la maggior parte delle funzioni matematiche è in grado di gestire entrambi gli intervalli.

L'API v3.0 introduce inoltre i miglioramenti seguenti, che è possibile sfruttare:
* Aggiunta di `createdDateTime` e `lastUpdatedDateTime` per consentire di tenere traccia della durata dell'elaborazione. Per altri dettagli, vedere la documentazione. 
* `version` indica la versione dell'API usata per generare i risultati
* È stato aggiunto un valore `confidence` per parola. Questo valore è calibrato in modo che un valore di 0,95 significhi che c'è una probabilità del 95% che il riconoscimento sia corretto. Il punteggio di attendibilità può essere usato per selezionare il testo da inviare per la revisione umana. 
    
    
Nella versione 2.x il formato di output è il seguente: 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResults": [
                    {
                    "page": 1,
                    "language": "en",
                    "clockwiseOrientation": 349.59,
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
                            },
            // The rest of result is omitted for brevity 
            
}
```
    
Nella versione 3.0 è stato modificato:
    
```json
    {
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
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="service-only"></a>Solo servizio

### `Recognize Text`
`Recognize Text` è un'operazione di *anteprima* che sarà *deprecata in tutte le versioni dell'API Visione artificiale*. È necessario eseguire la migrazione da `Recognize Text` a `Read` (versione 3.0) o `Batch Read File` (versioni 2.0 e 2.1). È consigliata la versione 3.0 di `Read`, perché include modelli più recenti e migliori per il riconoscimento del testo e funzionalità aggiuntive. Per eseguire l'aggiornamento da `Recognize Text` a `Read`:

|Recognize Text 2.x |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/recognizeText[?mode]**|https://{endpoint}/vision/<**stringa di versione**>/read/analyze[?language]|
    
Il parametro _mode_ non è supportato in `Read`. Sia il testo stampato che quello scritto a mano saranno supportati automaticamente.
    
Nella versione 3.0 è disponibile un nuovo parametro facoltativo _language_. Se non si conosce la lingua del documento o se il documento è multilingue, non includere questo parametro. 

### `Get Recognize Text Operation Result`

|Recognize Text 2.x |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/textOperations/** {operationId}|https://{endpoint}/vision/<**stringa di versione**>/read/analyzeResults/{operationId}|

### <a name="get-recognize-text-operation-result-status-flags"></a>Flag di stato `Get Recognize Text Operation Result`
Quando la chiamata a `Get Recognize Text Operation Result` ha esito positivo, restituisce un campo di stringa di stato nel corpo JSON. 
 
|Recognize Text 2.x |Read 3.x  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|

### <a name="api-response-json"></a>Risposta API (JSON)

Osservare le modifiche seguenti in JSON:    
* Nella versione 2.x `Get Read Operation Result` restituisce il codice JSON di riconoscimento ottico dei caratteri (OCR) quando lo stato è `Succeeded`. In v3.x questo campo è `succeeded`.
* Per ottenere la radice per la matrice di pagine, modificare la gerarchia JSON da `recognitionResult` a `analyzeResult`/`readResults`. La gerarchia JSON di righe e parole per pagina rimane invariata, quindi non sono necessarie modifiche al codice.

L'API versione 3.0 introduce inoltre i miglioramenti seguenti, che è possibile sfruttare. Per altri dettagli, vedere le informazioni di riferimento sull'API:
* Aggiunta di `createdDateTime` e `lastUpdatedDateTime` per consentire di tenere traccia della durata dell'elaborazione. Per altri dettagli, vedere la documentazione. 
* `version` indica la versione dell'API usata per generare i risultati
* È stato aggiunto un valore `confidence` per parola. Questo valore è calibrato in modo che un valore di 0,95 significhi che c'è una probabilità del 95% che il riconoscimento sia corretto. Il punteggio di attendibilità può essere usato per selezionare il testo da inviare per la revisione umana. 
* `angle` indica l'orientamento generale del testo in senso orario, misurato in gradi tra -180 e 180.
* `width` e `"height"` forniscono le dimensioni del documento e `"unit"` fornisce l'unità di tali dimensioni (pixel o pollici, a seconda del tipo di documento).
* `page` indica che sono supportati i documenti con più pagine
* `language` indica la lingua di input del documento (dal parametro facoltativo _language_).


Nella versione 2.x il formato di output è il seguente: 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResult": [
                    {
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
                            },
            // The rest of result is omitted for brevity 
            
    }
```
    
In v3.x è stata apportata una modifica:
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
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
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="container-only"></a>Solo contenitore

### `Synchronous Read`

|Read 2.0 |Read 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/core/Analyze**     |https://{endpoint}/vision/<**stringa di versione**>/read/syncAnalyze[?language]|
