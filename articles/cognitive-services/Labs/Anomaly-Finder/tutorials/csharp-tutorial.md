---
title: 'Esercitazione: Rilevamento anomalie, C#'
titlesuffix: Azure Cognitive Services
description: Esplorare un'app C# che usa l'API Rilevamento anomalie. Inviare i punti dati originali all'API e ottenere i punti dei valori previsti e delle anomalie.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: f99ce765c1d9417fd5ca88b49214eca8a3b0bf49
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887650"
---
# <a name="tutorial-anomaly-detection-with-c-application"></a>Esercitazione: Rilevamento anomalie con un'applicazione C#

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Esplorare un'applicazione Windows di base che usa l'API Rilevamento anomalie per rilevare le anomalie nell'input. Nell'esempio vengono inviati i dati di una serie temporale all'API Rilevamento anomalie con la chiave di sottoscrizione dell'utente e si ottengono quindi dall'API tutti i punti di anomalie e il valore previsto per ogni punto dati.

## <a name="prerequisites"></a>Prerequisiti

### <a name="platform-requirements"></a>Requisiti di piattaforma

L'esempio è stato sviluppato per .NET Framework usando [Visual Studio 2017, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs). 

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Sottoscrivere Rilevamento anomalie e ottenere una chiave di sottoscrizione 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Ottenere e usare l'esempio

È possibile clonare l'applicazione di esempio AnomalyDetection nel computer in uso da [Github](https://github.com/MicrosoftAnomalyDetection/csharp-sample.git). 
<a name="Step1"></a>
### <a name="install-the-example"></a>Installare l'esempio

In GitHub Desktop aprire Sample\AnomalyDetectionSample.sln.

<a name="Step2"></a>
### <a name="build-the-example"></a>Compilare l'esempio

Premere CTRL+MAIUSC+B oppure fare clic su Build (Compila) nel menu della barra multifunzione e quindi selezionare Build Solution (Compila soluzione).

<a name="Step3"></a>
### <a name="run-the-example"></a>Eseguire l'esempio

1. Al termine della compilazione, premere **F5** o fare clic su **Start** (Avvia) nel menu della barra multifunzione per eseguire l'esempio.
2. Individuare la finestra dell'interfaccia utente di Rilevamento anomalie con la casella di modifica testo "{chiave_sottoscrizione}".
3. Sostituire il file request.json, che contiene i dati di esempio, con i propri dati, quindi fare clic sul pulsante "Send" (Invia). Microsoft riceve i dati caricati e li usa per rilevare eventuali punti di anomalie presenti. I dati caricati non saranno salvati in modo permanente nel server Microsoft. Per rilevare nuovamente i punti di anomalie, è necessario caricare nuovamente i dati.
4. Se i dati sono validi, il risultato del rilevamento delle anomalie si troverà nel campo "Response" (Risposta). Altrimenti, se si verifica un errore, lo stesso campo conterrà informazioni sull'errore.

<a name="Review"></a>
### <a name="read-the-result"></a>Leggere il risultato

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Esame e apprendimento

Con l'applicazione in esecuzione, è ora possibile esaminare in che modo l'applicazione di esempio si integra con la tecnologia di Servizi cognitivi. Questo passaggio renderà più facile continuare a sviluppare questa applicazione o creare un'applicazione propria usando l'API Rilevamento anomalie di Microsoft.

Quest'app di esempio usa l'endpoint dell'API RESTful Rilevamento anomalie.

Per vedere in che modo l'API RESTful viene usata nell'applicazione di esempio, si esamina ora un frammento di codice nel file **AnomalyDetectionClient.cs**. Il file contiene commenti al codice che indicano "KEY SAMPLE CODE STARTS HERE" e "KEY SAMPLE CODE ENDS HERE" che aiutano a individuare i frammenti di codice riportati di seguito.

```csharp
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

```
### <a name="request"></a>**Richiesta**
Il frammento di codice riportato sotto mostra come usare HttpClient per inviare la chiave di sottoscrizione e i punti dati all'endpoint dell'API Rilevamento anomalie.

```csharp
    public async Task<string> Request(string baseAddress, string endpoint, string subscriptionKey, string requestData)
    {
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(baseAddress) })
        {
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Build the request content
            // ---------------------------------------------------------------------- 
            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Send the request content with POST method.
            // ---------------------------------------------------------------------- 
            var res = await client.PostAsync(endpoint, content);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------
            if (res.IsSuccessStatusCode)
            {
                return await res.Content.ReadAsStringAsync();
            }
            else
            {
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
