---
title: Eseguire l'aggiornamento alla versione 3.0 dell'API Visione artificiale
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
ms.openlocfilehash: 6e695fcfacac19ca82273d84d049bdb2afe14b54
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214192"
---
# <a name="upgrade-to-computer-vision-v30-read-api-from-v20v21"></a>Eseguire l'aggiornamento all'API di lettura di Visione artificiale versione 3.0 dalla versione 2.0/v 2.1

Questa guida illustra come aggiornare il codice dell'API REST di Visione artificiale versione 2.0 o 2.1 esistente alle operazioni di lettura versione 3.0. 

## <a name="upgrade-batch-read-file-to-read"></a>Aggiornare `Batch Read File` in `Read`


1. Modificare il percorso dell'API per `Batch Read File` versione 2.x come illustrato di seguito:


    |Read 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0/read/core/asyncBatchAnalyze**     |https://{endpoint}/vision/**v3.0/read/analyze**[?language]|
    
    È disponibile un nuovo parametro facoltativo _language_. Se non si conosce la lingua del documento o se il documento è multilingue, non includere questo parametro. 

2. Modificare il percorso dell'API per `Get Read Results` nella versione 2.x come illustrato di seguito:

    |Read 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0**/read/**operations**/{operationId}     |https://{endpoint}/vision/**v3.0**/read/**analyzeResults**/{operationId}|

3. Modificare il codice per il controllo dei risultati JSON da `Get Read Operation Result`. Quando la chiamata a `Get Read Operation Result` ha esito positivo, restituisce un campo di stringa di stato nel corpo JSON. I valori seguenti della versione 2.0 sono stati modificati per un migliore allineamento con le altre API REST di Servizi cognitivi. 
 
    |Read 2.x |Read 3.0  |
    |----------|-----------|
    |`"NotStarted"` |   `"notStarted"`|
    |`"Running"` | `"running"`|
    |`"Failed"` | `"failed"`|
    |`"Succeeded"` | `"succeeded"`|
    
4. Modificare il codice per interpretare il codice JSON del risultato del riconoscimento finale da `Get Read Operation Result`. 

    Osservare le modifiche seguenti in JSON:
    
    - Nella versione 2.x `"Get Read Operation Result"` restituisce il codice JSON di riconoscimento ottico dei caratteri (OCR) quando lo stato è `"Succeeded"`. Nella versione 3.0 questo campo è `"succeeded"`.
    - Per ottenere la radice per la matrice di pagine, modificare la gerarchia JSON da `"recognitionResults"` a `"analyzeResult"`/`"readResults"`. La gerarchia JSON di righe e parole per pagina rimane invariata, quindi non sono necessarie modifiche al codice.
    -   L'angolo della pagina `"clockwiseOrientation"` è stato rinominato in `"angle"` e l'intervallo è stato modificato da un valore compreso tra 0 e 360 gradi a un valore compreso tra -180 e 180 gradi. A seconda del codice, può essere necessario o meno apportare modifiche, in quanto la maggior parte delle funzioni matematiche è in grado di gestire entrambi gli intervalli.
    -   L'API versione 3.0 introduce inoltre i miglioramenti seguenti, che è possibile sfruttare: sono stati aggiunti gli elementi `"createdDateTime"` e `"lastUpdatedDateTime"` per tenere traccia della durata dell'elaborazione. Per altri dettagli, vedere la documentazione. 
        - `"version"` indica la versione dell'API usata per generare i risultati
        - È stato aggiunto un valore `"confidence"` per parola. Questo valore è calibrato in modo che un valore di 0,95 significhi che c'è una probabilità del 95% che il riconoscimento sia corretto. Il punteggio di attendibilità può essere usato per selezionare il testo da inviare per la revisione umana. 
    
    
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

## <a name="upgrade-from-recognize-text-to-read"></a>Aggiornamento da `Recognize Text` a `Read`
`Recognize Text` è un'operazione di *anteprima* che sarà *deprecata in tutte le versioni dell'API Visione artificiale*. È necessario eseguire la migrazione da `Recognize Text` a `Read` (versione 3.0) o `Batch Read File` (versioni 2.0 e 2.1). È consigliata la versione 3.0 di `Read`, perché include modelli più recenti e migliori per il riconoscimento del testo e funzionalità aggiuntive. Per eseguire l'aggiornamento da `Recognize Text` a `Read`:

1. Modificare il percorso dell'API per `Recognize Text` versione 2.x come illustrato di seguito:


    |Recognize Text 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0/recognizeText[?mode]**|https://{endpoint}/vision/**v3.0/read/analyze**[?language]|
    
    Il parametro _mode_ non è supportato in `Read`. Sia il testo stampato che quello scritto a mano saranno supportati automaticamente.
    
    Nella versione 3.0 è disponibile un nuovo parametro facoltativo _language_. Se non si conosce la lingua del documento o se il documento è multilingue, non includere questo parametro. 

2. Modificare il percorso dell'API per `Get Recognize Text Operation Result` versione 2.x come illustrato di seguito:

    |Recognize Text 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0/textOperations/** {operationId}|https://{endpoint}/vision/**v3.0/read/analyzeResults**/{operationId}|

3. Modificare il codice per il controllo dei risultati JSON da `Get Recognize Text Operation Result`. Quando la chiamata a `Get Read Operation Result` ha esito positivo, restituisce un campo di stringa di stato nel corpo JSON. 
 
    |Recognize Text 2.x |Read 3.0  |
    |----------|-----------|
    |`"NotStarted"` |   `"notStarted"`|
    |`"Running"` | `"running"`|
    |`"Failed"` | `"failed"`|
    |`"Succeeded"` | `"succeeded"`|


4. Modificare il codice per interpretare il codice JSON del risultato del riconoscimento finale da `Get Recognize Text Operation Result` per supportare `Get Read Operation Result`.

    Osservare le modifiche seguenti in JSON:    

    - Nella versione 2.x `"Get Read Operation Result"` restituisce il codice JSON di riconoscimento ottico dei caratteri (OCR) quando lo stato è `"Succeeded"`. Nella versione 3.0 questo campo è `"succeeded"`.
    - Per ottenere la radice per la matrice di pagine, modificare la gerarchia JSON da `"recognitionResult"` a `"analyzeResult"`/`"readResults"`. La gerarchia JSON di righe e parole per pagina rimane invariata, quindi non sono necessarie modifiche al codice.
    -   L'API versione 3.0 introduce inoltre i miglioramenti seguenti, che è possibile sfruttare. Per informazioni dettagliate, vedere le informazioni di riferimento sulle API: sono stati aggiunti gli elementi `"createdDateTime"` e `"lastUpdatedDateTime"` per tenere traccia della durata dell'elaborazione. Per altri dettagli, vedere la documentazione. 
        - `"version"` indica la versione dell'API usata per generare i risultati
        - È stato aggiunto un valore `"confidence"` per parola. Questo valore è calibrato in modo che un valore di 0,95 significhi che c'è una probabilità del 95% che il riconoscimento sia corretto. Il punteggio di attendibilità può essere usato per selezionare il testo da inviare per la revisione umana. 
        - `"angle"` indica l'orientamento generale del testo in senso orario, misurato in gradi tra -180 e 180.
        -  `"width"` e `"height"` forniscono le dimensioni del documento e `"unit"` fornisce l'unità di tali dimensioni (pixel o pollici, a seconda del tipo di documento).
        - `"page"` indica che sono supportati i documenti con più pagine
        - `"language"` indica la lingua di input del documento (dal parametro facoltativo _language_).


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
    
## <a name="all-other-operations"></a>Tutte le altre operazioni

Non sono state apportate altre modifiche che causano un'interruzione tra la versione 2.x e la versione 3.0 dell'API Visione artificiale. È sufficiente modificare il percorso dell'API per sostituire `v2.0` con `v3.0`.
