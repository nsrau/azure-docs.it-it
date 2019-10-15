---
title: "Guida introduttiva: Riconoscere l'input penna con l'API REST Riconoscimento input penna e Java"
titleSuffix: Azure Cognitive Services
description: Usare l'API Riconoscimento input penna per avviare il riconoscimento di tratti input penna.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: e8cd6a4acbd1492bba1c9e88b523a7c44a44f009
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996856"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Guida introduttiva: Riconoscere l'input penna con l'API REST Riconoscimento input penna e Java

Usare questa guida di avvio rapido per iniziare a usare l'API Riconoscimento input penna su tratti input penna. Questa applicazione Java invia una richiesta API contenenti i dati del tratto input penna in formato JSON e riceve la risposta.

L'applicazione è scritta in Java, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

In genere è necessario chiamare l'API da un'app di input penna. Questa guida di avvio rapido invia i dati del tratto input penna per l'esempio scritto a mano seguente da un file JSON.

![immagine di testo scritto a mano](../media/handwriting-sample.jpg)

Il codice sorgente per questo avvio rapido è disponibile su [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Prerequisiti

- [Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) versione successiva.

- Importare queste librerie dal repository Maven
    - Pacchetto [JSON in Java](https://mvnrepository.com/artifact/org.json/json)
    - Pacchetto [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- I dati di esempio del tratto input penna per questo avvio rapido sono disponibili in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Creare una risorsa di riconoscimento input penna

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Creare una nuova applicazione

1. Creare un nuovo progetto Java nell'ambiente di sviluppo integrato o nell'editor preferito e importare le librerie seguenti.
    
    [!code-java[import statements](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=imports)]

2. Creare variabili per la chiave di sottoscrizione, l'endpoint e il file JSON. L'endpoint verrà poi aggiunto all'URI del riconoscimento input penna.

    [!code-java[initial vars](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Creare una funzione per inviare richieste

1. Creare una nuova funzione denominata `sendRequest()` che accetta le variabili create in precedenza. Quindi, eseguire i passaggi seguenti.

2. Creare un oggetto `CloseableHttpClient` che può inviare richieste all'API. Inviare la richiesta a un oggetto richiesta `HttpPut` combinando l'endpoint e l'URL di riconoscimento input penna.

3. Usare la funzione `setHeader()` della richiesta per impostare l'intestazione `Content-Type` su `application/json` e aggiungere la chiave di sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key`.

4. Usare la funzione `setEntity()` della richiesta per i dati da inviare.   

5. Usare la funzione `execute()` del client per inviare la richiesta e salvarla in un oggetto `CloseableHttpResponse`. 

6. Creare un oggetto `HttpEntity` in cui archiviare il contenuto della risposta. Ottenere il contenuto con `getEntity()`. Viene restituita la risposta se non è vuota.
    
    [!code-java[send a request](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=sendRequest)]

## <a name="send-an-ink-recognition-request"></a>Inviare una richiesta di riconoscimento input penna

Creare un metodo denominato `recognizeInk()` per riconoscere i dati dell'input penna. Chiamare il metodo `sendRequest()` creato in precedenza con l'endpoint, l'URL, la chiave di sottoscrizione e i dati JSON. Ottenere il risultato e visualizzarlo nella console.

[!code-java[recognizeInk](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=recognizeInk)]

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Caricare i dati dell'input penna e inviare la richiesta

1. Nel metodo main dell'applicazione leggere il file JSON che contiene i dati che verranno aggiunti alle richieste.

2. Chiamare la funzione di riconoscimento input penna creata in precedenza.
    
    [!code-java[main method](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Eseguire l'applicazione e visualizzare la risposta

Eseguire l'applicazione. Viene restituita una risposta con esito positivo in formato JSON. È anche possibile trovare la risposta JSON in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Per vedere come funziona l'API Riconoscimento input penna in un'app di input penna, esaminare le applicazioni di esempio seguenti in GitHub:
* [C# e piattaforma UWP (Universal Windows Platform)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e WPF (Windows Presentation Foundation)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [App Javascript per Web browser](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [App Java e per dispositivi mobili Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [App Swift e per dispositivi mobili iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
