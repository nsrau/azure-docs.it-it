---
title: 'Esercitazione: API Visione artificiale con JavaScript'
titlesuffix: Azure Cognitive Services
description: In questa esercitazione si esamina un'app JavaScript di base che usa l'API Visione artificiale in Servizi cognitivi di Azure. Si effettua il riconoscimento ottico dei caratteri (OCR), si creano anteprime e si usano le caratteristiche visive in un'immagine.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: kefre
ms.openlocfilehash: c024e517eb59c7d3b61408e477c94004ccb01a54
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341311"
---
# <a name="tutorial-computer-vision-api-javascript"></a>Esercitazione: API Visione artificiale con JavaScript

Questa esercitazione illustra le funzionalità dell'API REST Visione artificiale di Servizi cognitivi di Azure.

Si esamina un'applicazione JavaScript che usa l'API REST Visione artificiale per effettuare il riconoscimento ottico dei caratteri (OCR), creare immagini di anteprima con ritaglio intelligente, nonché rilevare, classificare, contrassegnare con tag e descrivere le caratteristiche visive, inclusi i visi, in un'immagine. L'esempio consente di inviare un URL di un'immagine per l'analisi o l'elaborazione. È possibile usare questo esempio open source come modello per la creazione di un'app JavaScript personalizzata per l'uso dell'API REST Visione artificiale.

L'applicazione JavaScript è già stata scritta, ma non include la funzionalità di Visione artificiale. In questa esercitazione si aggiunge codice specifico dell'API REST Visione artificiale per completare le funzionalità dell'applicazione.

## <a name="prerequisites"></a>Prerequisiti

### <a name="platform-requirements"></a>Requisiti di piattaforma

Questa esercitazione è stata sviluppata usando un semplice editor di testo.

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Effettuare la sottoscrizione dell'API Visione artificiale e ottenere una chiave di sottoscrizione 

Prima di creare l'esempio, è necessario effettuare la sottoscrizione dell'API Visione artificiale, che fa parte di Servizi cognitivi di Azure. Per informazioni dettagliate sulla sottoscrizione e sulla gestione delle chiavi, vedere [Sottoscrizioni](https://azure.microsoft.com/try/cognitive-services/). In questa esercitazione è possibile usare sia la chiave primaria sia quella secondaria. 

## <a name="acquire-the-incomplete-tutorial-project"></a>Acquisire il progetto dell'esercitazione incompleto

### <a name="download-the-tutorial-project"></a>Scaricare il progetto dell'esercitazione

Clonare l'[esercitazione JavaScript per Visione artificiale di Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-javascript-computer-vision-tutorial) o scaricare il file ZIP ed estrarlo in una directory vuota.

Se si preferisce usare l'esercitazione completata con tutto il codice dell'esercitazione aggiunto, è possibile usare i file nella cartella **Completed**.

## <a name="add-the-tutorial-code-to-the-project"></a>Aggiungere il codice dell'esercitazione al progetto

L'applicazione JavaScript è configurata con sei file HTML, uno per ogni funzionalità. Ogni file illustra una funzione diversa di Visione artificiale (analisi, OCR e così via). Le sei sezioni dell'esercitazione non hanno interdipendenze, quindi è possibile aggiungere il codice dell'esercitazione a un file, a tutti e sei i file o solo a un paio di file. È possibile aggiungere il codice dell'esercitazione ai file in qualsiasi ordine.

Di seguito sono riportati i requisiti iniziali.

### <a name="analyze-an-image"></a>Analizzare un'immagine

La funzionalità di analisi di Visione artificiale analizza un'immagine cercando più di 2.000 oggetti, esseri viventi, panorami e azioni riconoscibili. Una volta completata l'analisi, la funzionalità restituisce un oggetto JSON che descrive l'immagine con tag descrittivi, analisi del colore, didascalie e molto altro.

Per completare la funzionalità di analisi dell'applicazione dell'esercitazione, procedere come segue:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Aggiungere il codice del gestore dell'evento per il pulsante del modulo

Aprire il file **analyze.html** in un editor di testo e individuare la funzione **analyzeButtonClick** nella parte inferiore del file.

La funzione del gestore dell'evento **analyzeButtonClick** cancella il modulo, visualizza l'immagine specificata nell'URL e quindi chiama la funzione **AnalyzeImage** per analizzare l'immagine.

Copiare e incollare il codice seguente nella funzione **analyzeButtonClick**.

```javascript
function analyzeButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    AnalyzeImage(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Aggiungere il wrapper per la chiamata API REST

La funzione **AnalyzeImage** esegue il wrapping della chiamata API REST per analizzare un'immagine. Dopo il completamento, l'analisi JSON formattata viene visualizzata nell'area di testo specificata e la didascalia viene visualizzata nello spazio specificato.

Copiare e incollare il codice della funzione **AnalyzeImage** subito sotto la funzione **analyzeButtonClick**.

```javascript
/* Analyze the image at the specified URL by using Microsoft Cognitive Services Analyze Image API.
 * @param {string} sourceImageUrl - The URL to the image to analyze.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function AnalyzeImage(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "visualFeatures": "Categories,Description,Color",
        "details": "",
        "language": "en",
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseAnalyze +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.description && data.description.captions) {
            var caption = data.description.captions[0];
            
            if (caption.text && caption.confidence) {
                captionSpan.text("Caption: " + caption.text +
                    " (confidence: " + caption.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-application"></a>Eseguire l'applicazione

Salvare il file **analyze.html** e aprirlo in un Web browser. Inserire la chiave di sottoscrizione nel campo **Chiave sottoscrizione** e verificare di usare l'area corretta in **Subscription Region** (Area sottoscrizione). Immettere un URL di un'immagine da analizzare, quindi fare clic sul pulsante **Analyze Image** (Analizza immagine) per analizzare un'immagine e visualizzare il risultato.

### <a name="recognize-a-landmark"></a>Riconoscere un punto di riferimento

La funzionalità relativa ai punti di riferimento di Visione artificiale analizza un'immagine per individuare punti di riferimento naturali e artificiali, ad esempio montagne o edifici famosi. Una volta completata l'analisi, questa funzionalità restituisce un oggetto JSON che identifica i punti di riferimento trovati nell'immagine.

Per completare la funzionalità relativa ai punti di riferimento dell'applicazione dell'esercitazione, procedere come segue:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Aggiungere il codice del gestore dell'evento per il pulsante del modulo

Aprire il file **landmark.html** in un editor di testo e individuare la funzione **landmarkButtonClick** nella parte inferiore del file.

La funzione del gestore dell'evento **landmarkButtonClick** cancella il modulo, visualizza l'immagine specificata nell'URL e quindi chiama la funzione **IdentifyLandmarks** per analizzare l'immagine.

Copiare e incollare il codice seguente nella funzione **landmarkButtonClick**.

```javascript
function landmarkButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyLandmarks(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Aggiungere il wrapper per la chiamata API REST

La funzione **IdentifyLandmarks** esegue il wrapping della chiamata API REST per analizzare un'immagine. Dopo il completamento, l'analisi JSON formattata viene visualizzata nell'area di testo specificata e la didascalia viene visualizzata nello spazio specificato.

Copiare e incollare il codice della funzione **IdentifyLandmarks** subito sotto la funzione **landmarkButtonClick**.

```javascript
/* Identify landmarks in the image at the specified URL by using Microsoft Cognitive Services 
 * Landmarks API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for landmarks.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyLandmarks(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "landmarks"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseLandmark +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.landmarks) {
            var landmark = data.result.landmarks[0];
            
            if (landmark.name && landmark.confidence) {
                captionSpan.text("Landmark: " + landmark.name +
                    " (confidence: " + landmark.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-application"></a>Eseguire l'applicazione

Salvare il file **landmark.html** e aprirlo in un Web browser. Inserire la chiave di sottoscrizione nel campo **Chiave sottoscrizione** e verificare di usare l'area corretta in **Subscription Region** (Area sottoscrizione). Immettere un URL di un'immagine da analizzare, quindi fare clic sul pulsante **Analyze Image** (Analizza immagine) per analizzare un'immagine e visualizzare il risultato.

### <a name="recognize-celebrities"></a>Riconoscere le celebrità

La funzionalità relativa alle celebrità di Visione artificiale analizza un'immagine per individuare personaggi famosi. Una volta completata l'analisi, questa funzionalità restituisce un oggetto JSON che identifica le celebrità trovate nell'immagine.

Per completare la funzionalità relativa alle celebrità dell'applicazione dell'esercitazione, procedere come segue:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Aggiungere il codice del gestore dell'evento per il pulsante del modulo

Aprire il file **celebrities.html** in un editor di testo e individuare la funzione **celebritiesButtonClick** nella parte inferiore del file.

La funzione del gestore dell'evento **celebritiesButtonClick** cancella il modulo, visualizza l'immagine specificata nell'URL e quindi chiama la funzione **IdentifyCelebrities** per analizzare l'immagine.

Copiare e incollare il codice seguente nella funzione **celebritiesButtonClick**.

```javascript
function celebritiesButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyCelebrities(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Aggiungere il wrapper per la chiamata API REST

```javascript
/* Identify celebrities in the image at the specified URL by using Microsoft Cognitive Services 
 * Celebrities API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for celebrities.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyCelebrities(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "celebrities"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseCelebrities +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.celebrities) {
            var celebrity = data.result.celebrities[0];
            
            if (celebrity.name && celebrity.confidence) {
                captionSpan.text("Celebrity name: " + celebrity.name +
                    " (confidence: " + celebrity.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-application"></a>Eseguire l'applicazione

Salvare il file **celebrities.html** e aprirlo in un Web browser. Inserire la chiave di sottoscrizione nel campo **Chiave sottoscrizione** e verificare di usare l'area corretta in **Subscription Region** (Area sottoscrizione). Immettere un URL di un'immagine da analizzare, quindi fare clic sul pulsante **Analyze Image** (Analizza immagine) per analizzare un'immagine e visualizzare il risultato.

### <a name="intelligently-generate-a-thumbnail"></a>Generare un'anteprima intelligente

La funzionalità di anteprima di Visione artificiale genera un'anteprima da un'immagine. Usando la funzionalità **Smart Crop** (Ritaglio intelligente), la funzionalità di anteprima identifica l'area di interesse in un'immagine e centra l'anteprima su quest'area, per generare immagini di anteprima più gradevoli.

Per completare la funzionalità di anteprima dell'applicazione dell'esercitazione, procedere come segue:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Aggiungere il codice del gestore dell'evento per il pulsante del modulo

Aprire il file **thumbnail.html** in un editor di testo e individuare la funzione **thumbnailButtonClick** nella parte inferiore del file.

La funzione del gestore dell'evento **thumbnailButtonClick** cancella il modulo, visualizza l'immagine specificata nell'URL e quindi chiama la funzione **getThumbnail** due volte per creare due anteprime, una con il ritaglio intelligente e l'altra senza.

Copiare e incollare il codice seguente nella funzione **thumbnailButtonClick**.

```javascript
function thumbnailButtonClick() {

    // Clear the display fields.
    document.getElementById("sourceImage").src = "#";
    document.getElementById("thumbnailImageSmartCrop").src = "#";
    document.getElementById("thumbnailImageNonSmartCrop").src = "#";
    document.getElementById("responseTextArea").value = "";
    document.getElementById("captionSpan").text = "";
    
    // Display the image.
    var sourceImageUrl = document.getElementById("inputImage").value;
    document.getElementById("sourceImage").src = sourceImageUrl;
    
    // Get a smart cropped thumbnail.
    getThumbnail (sourceImageUrl, true, document.getElementById("thumbnailImageSmartCrop"), 
        document.getElementById("responseTextArea"));
    
    // Get a non-smart-cropped thumbnail.
    getThumbnail (sourceImageUrl, false, document.getElementById("thumbnailImageNonSmartCrop"),
        document.getElementById("responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Aggiungere il wrapper per la chiamata API REST

La funzione **getThumbnail** esegue il wrapping della chiamata API REST per analizzare un'immagine. Dopo il completamento, l'anteprima viene visualizzata nell'elemento img specificato.

Copiare e incollare la funzione **getThumbnail** seguente subito sotto la funzione **thumbnailButtonClick**.

```javascript
/* Get a thumbnail of the image at the specified URL by using Microsoft Cognitive Services
 * Thumbnail API.
 * @param {string} sourceImageUrl URL to image.
 * @param {boolean} smartCropping Set to true to use the smart cropping feature which crops to the
 *                                more interesting area of an image; false to crop for the center
 *                                of the image.
 * @param {<img> element} imageElement The img element in the DOM which will display the thumnail image.
 * @param {<textarea> element} responseTextArea - The text area to display the Response Headers returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function getThumbnail (sourceImageUrl, smartCropping, imageElement, responseTextArea) {
    // Create the HTTP Request object.
    var xhr = new XMLHttpRequest();
    
    // Request parameters.
    var params = "width=100&height=150&smartCropping=" + smartCropping.toString();

    // Build the full URI.
    var fullUri = common.uriBasePreRegion + 
                  document.getElementById("subscriptionRegionSelect").value + 
                  common.uriBasePostRegion + 
                  common.uriBaseThumbnail +
                  "?" + 
                  params;
    
    // Identify the request as a POST, with the URI and parameters.
    xhr.open("POST", fullUri);
    
    // Add the request headers.
    xhr.setRequestHeader("Content-Type","application/json");
    xhr.setRequestHeader("Ocp-Apim-Subscription-Key", 
        encodeURIComponent(document.getElementById("subscriptionKeyInput").value));
    
    // Set the response type to "blob" for the thumbnail image data.
    xhr.responseType = "blob";
    
    // Process the result of the REST API call.
    xhr.onreadystatechange = function(e) {
        if(xhr.readyState === XMLHttpRequest.DONE) {
            
            // Thumbnail successfully created.
            if (xhr.status === 200) {
                // Show response headers.
                var s = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                responseTextArea.value = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                
                // Show thumbnail image.
                var urlCreator = window.URL || window.webkitURL;
                var imageUrl = urlCreator.createObjectURL(this.response);
                imageElement.src = imageUrl;
            } else {
                // Display the error message. The error message is the response body as a JSON string. 
                // The code in this code block extracts the JSON string from the blob response.
                var reader = new FileReader();
                
                // This event fires after the blob has been read.
                reader.addEventListener('loadend', (e) => {
                    responseTextArea.value = JSON.stringify(JSON.parse(e.srcElement.result), null, 2);
                });
                
                // Start reading the blob as text.
                reader.readAsText(xhr.response);
            }
        }
    }
    
    // Execute the REST API call.
    xhr.send('{"url": ' + '"' + sourceImageUrl + '"}');
}
```

#### <a name="run-the-application"></a>Eseguire l'applicazione

Salvare il file **thumbnail.html** e aprirlo in un Web browser. Inserire la chiave di sottoscrizione nel campo **Chiave sottoscrizione** e verificare di usare l'area corretta in **Subscription Region** (Area sottoscrizione). Immettere un URL di un'immagine da analizzare, quindi fare clic sul pulsante **Generate Thumbnails** (Genera anteprime) per analizzare un'immagine e visualizzare il risultato.

### <a name="read-printed-text-ocr"></a>Leggere testo stampato (OCR)

La funzionalità di riconoscimento ottico dei caratteri (OCR) di Visione artificiale analizza un'immagine di testo stampato. Dopo il completamento dell'analisi, la funzionalità OCR restituisce un oggetto JSON che contiene il testo e la posizione del testo nell'immagine.

Per completare la funzionalità OCR dell'applicazione dell'esercitazione, procedere come segue:

### <a name="ocr-step-1-add-the-event-handler-code-for-the-form-button"></a>OCR, passaggio 1: aggiungere il codice del gestore dell'evento per il pulsante del modulo

Aprire il file **ocr.html** in un editor di testo e individuare la funzione **ocrButtonClick** nella parte inferiore del file.

La funzione del gestore dell'evento **ocrButtonClick** cancella il modulo, visualizza l'immagine specificata nell'URL e quindi chiama la funzione **ReadOcrImage** per analizzare l'immagine.

Copiare e incollare il codice seguente nella funzione **ocrButtonClick**.

```javascript
function ocrButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadOcrImage(sourceImageUrl, $("#responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Aggiungere il wrapper per la chiamata API REST

La funzione **ReadOcrImage** esegue il wrapping della chiamata API REST per analizzare un'immagine. Dopo il completamento, il codice JSON formattato che descrive il testo e la relativa posizione viene visualizzato nell'area di testo specificata.

Copiare e incollare la funzione **ReadOcrImage** seguente subito sotto la funzione **ocrButtonClick**.

```javascript
/* Recognize and read printed text in an image at the specified URL by using Microsoft Cognitive 
 * Services OCR API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for printed text.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadOcrImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "language": "unk",
        "detectOrientation ": "true",
    };

    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseOcr +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

#### <a name="run-the-application"></a>Eseguire l'applicazione

Salvare il file **ocr.html** e aprirlo in un Web browser. Inserire la chiave di sottoscrizione nel campo **Chiave sottoscrizione** e verificare di usare l'area corretta in **Subscription Region** (Area sottoscrizione). Immettere un URL di un'immagine di testo da leggere, quindi fare clic sul pulsante **Read Image** (Leggi immagine) per analizzare un'immagine e visualizzare il risultato.

### <a name="read-handwritten-text-handwriting-recognition"></a>Leggere testo scritto a mano (riconoscimento della grafia)

La funzionalità di riconoscimento della grafia di Visione artificiale analizza un'immagine di testo scritto a mano. Dopo il completamento dell'analisi, la funzionalità di riconoscimento della grafia restituisce un oggetto JSON che contiene il testo e la posizione del testo nell'immagine.

Per completare la funzionalità di riconoscimento della grafia dell'applicazione dell'esercitazione, procedere come segue:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Aggiungere il codice del gestore dell'evento per il pulsante del modulo

Aprire il file **handwriting.html** in un editor di testo e individuare la funzione **handwritingButtonClick** nella parte inferiore del file.

La funzione del gestore dell'evento **handwritingButtonClick** cancella il modulo, visualizza l'immagine specificata nell'URL e quindi chiama la funzione **HandwritingImage** per analizzare l'immagine.

Copiare e incollare il codice seguente nella funzione **handwritingButtonClick**.

```javascript
function handwritingButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadHandwrittenImage(sourceImageUrl, $("#responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Aggiungere il wrapper per la chiamata API REST

La funzione **ReadHandwrittenImage** esegue il wrapping delle due chiamate API REST necessarie per analizzare un'immagine. Poiché il riconoscimento della grafia richiede tempo, viene usato un processo in due fasi. La prima chiamata invia l'immagine per l'elaborazione, mentre la seconda recupera il testo rilevato al termine dell'elaborazione.

Dopo che il testo è stato recuperato, il codice JSON formattato che descrive il testo e la relativa posizione viene visualizzato nell'area di testo specificata.

Copiare e incollare la funzione **ReadHandwrittenImage** seguente subito sotto la funzione **handwritingButtonClick**.

```javascript
/* Recognize and read text from an image of handwriting at the specified URL by using Microsoft 
 * Cognitive Services Recognize Handwritten Text API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for handwriting.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadHandwrittenImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "handwriting": "true",
    };

    // This operation requrires two REST API calls. One to submit the image for processing,
    // the other to retrieve the text found in the image. 
    //
    // Perform the first REST API call to submit the image for processing.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseHandwriting +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data, textStatus, jqXHR) {
        // Show progress.
        responseTextArea.val("Handwritten image submitted.");
        
        // Note: The response may not be immediately available. Handwriting Recognition is an
        // async operation that can take a variable amount of time depending on the length
        // of the text you want to recognize. You may need to wait or retry this GET operation.
        //
        // Try once per second for up to ten seconds to receive the result.
        var tries = 10;
        var waitTime = 100;
        var taskCompleted = false;
        
        var timeoutID = setInterval(function () { 
            // Limit the number of calls.
            if (--tries <= 0) {
                window.clearTimeout(timeoutID);
                responseTextArea.val("The response was not available in the time allowed.");
                return;
            }

            // The "Operation-Location" in the response contains the URI to retrieve the recognized text.
            var operationLocation = jqXHR.getResponseHeader("Operation-Location");
            
            // Perform the second REST API call and get the response.
            $.ajax({
                url: operationLocation,
                
                // Request headers.
                beforeSend: function(jqXHR){
                    jqXHR.setRequestHeader("Content-Type","application/json");
                    jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key",
                        encodeURIComponent($("#subscriptionKeyInput").val()));
                },
                
                type: "GET",
            })
            
            .done(function(data) {
                // If the result is not yet available, return.
                if (data.status && (data.status === "NotStarted" || data.status === "Running")) {
                    return;
                }
                
                // Show formatted JSON on webpage.
                responseTextArea.val(JSON.stringify(data, null, 2));
                
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
            })
            
            .fail(function(jqXHR, textStatus, errorThrown) {
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
                
                // Display error message.
                var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
                errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
                    jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
                alert(errorString);
            });
        }, waitTime);
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

#### <a name="run-the-application"></a>Eseguire l'applicazione

Salvare il file **handwriting.html** e aprirlo in un Web browser. Inserire la chiave di sottoscrizione nel campo **Chiave sottoscrizione** e verificare di usare l'area corretta in **Subscription Region** (Area sottoscrizione). Immettere un URL di un'immagine di testo da leggere, quindi fare clic sul pulsante **Read Image** (Leggi immagine) per analizzare un'immagine e visualizzare il risultato.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione in C&#35; dell'API Visione artificiale](CSharpTutorial.md)
- [Esercitazione per Python dell'API Visione artificiale](PythonTutorial.md)
