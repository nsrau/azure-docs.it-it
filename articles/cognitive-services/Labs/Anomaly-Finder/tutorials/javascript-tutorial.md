---
title: Rilevamento anomalie in un'app Javascript - Servizi cognitivi Microsoft | Microsoft Docs
description: Esplorare un'app Web Javascript che usa l'API Rilevamento anomalie in Servizi cognitivi Microsoft. Inviare i punti dati originali all'API e ottenere i punti dei valori previsti e delle anomalie.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 42c3941a05efe8b74f818cd99f3606b3073892a9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35375345"
---
# <a name="anomaly-detection-javascript-application"></a>Rilevamento anomalie in un'applicazione Javascript

Si esplorerà un'applicazione Web che usa l'API REST Rilevamento anomalie per rilevare un'anomalia. Nell'esempio vengono inviati i dati di una serie temporale all'API Rilevamento anomalie con la chiave di sottoscrizione dell'utente e si ottengono quindi dall'API tutti i punti di anomalie e il valore previsto per ogni punto dati.

## <a name="prerequisites"></a>prerequisiti

### <a name="platform-requirements"></a>Requisiti di piattaforma

Questa esercitazione è stata sviluppata usando un semplice editor di testo.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Sottoscrivere Rilevamento anomalie e ottenere una chiave di sottoscrizione 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Ottenere e usare l'esempio

Questa esercitazione fornisce due scenari per il rilevamento di anomalie nei dati di una serie temporale. Di seguito sono riportati i requisiti iniziali.

<a name="Step1"></a> 
### <a name="download-the-tutorial-project"></a>Scaricare il progetto dell'esercitazione

Clonare l'[esercitazione di rilevamento anomalie JavaScript di Servizi cognitivi](https://github.com/MicrosoftAnomalyDetection/javascript-sample) o scaricare il file con estensione zip ed estrarre i file in una directory vuota.

<a name="Step2"></a>
### <a name="run-the-example"></a>Eseguire l'esempio

L'esempio può essere provato in due scenari.
1. Inserire la **chiave di sottoscrizione** nel campo subscriptionKey della funzione detect in anomalydetection.html.
2. Inserire l'endpoint API Rilevamento anomalie e accertarsi di usare l'area di sottoscrizione corretta.
3. Aprire il file **anomalydetection.html** in un Web browser.

**Scenario 1 Rilevare dati di serie temporali settimanali**
1. Nel campo Period (Periodo) inserire **7**. 
2. Sostituire i dati di esempio con i propri punti dati della serie temporale settimanale (Json) nel campo Points (Punti), oppure usare direttamente i dati di esempio.
3. Fare clic sul pulsante Anomaly Detection (Rilevamento anomalie) e verificare il risultato nella casella di testo Response (Risposta) a destra.

**Scenario 2 Rilevare i dati della serie temporale senza un periodo**
1. Lasciare vuoto il periodo nel campo Period (Periodo), supponendo che non si conosca il periodo della serie temporale.
2. Usare la stessa serie temporale dello scenario 1.
3. Fare clic sul pulsante Anomaly Detection (Rilevamento anomalie) e verificare il campo Period (Periodo) nella casella di testo Response (Risposta) a destra.

<a name="Step3"></a>
### <a name="read-the-result"></a>Leggere il risultato

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Esame e apprendimento

Si ha ora un'applicazione in esecuzione. Si esaminerà in che modo l'app di esempio si integra con la tecnologia di Servizi cognitivi. Questo passaggio renderà più facile continuare a sviluppare questa applicazione o creare un'applicazione propria usando l'API Rilevamento anomalie di Microsoft.
Quest'app di esempio usa l'endpoint dell'API RESTful Rilevamento anomalie.
Per vedere in che modo l'API RESTful viene usata nell'applicazione di esempio, si esamina ora un frammento di codice nel file anomalydetection.html.
```JavaScript
function anomalyDetection(url, subscriptionKey, points, period) {
    var obj = new Object();
    obj.Points = JSON.parse(points); // this points are read from text box.
    obj.Period = parseInt(period);//period=7 weekly period
    var tsData = JSON.stringify(obj);
    // Perform the REST API call.
    $.ajax({
        url: url, //Anomaly Detection API endpoint
        // Request headers.
        beforeSend: function (xhrObj) {
            xhrObj.setRequestHeader("Content-Type", "application/json");
            xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey); // Replace your subscription key
        },
        type: "POST",
        // Request body.
        data: tsData, // json format
        })
        .done(function (data) {
            // Show formatted JSON on webpage.
            $("#responseTextArea").val(JSON.stringify(data, null, 2));
        })
        .fail(function (jqXHR, textStatus, errorThrown) {
            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" : jQuery.parseJSON(jqXHR.responseText).message;
            $("#responseTextArea").val(errorString);           
        });
}

```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
