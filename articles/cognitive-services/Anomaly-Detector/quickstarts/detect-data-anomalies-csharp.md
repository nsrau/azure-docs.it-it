---
title: "Guida introduttiva: Rilevare le anomalie nei dati delle serie temporali tramite l'API REST Rilevamento anomalie e C#"
titleSuffix: Azure Cognitive Services
description: Usare l'API Rilevamento anomalie per rilevare le anomalie nelle serie di dati sia come batch che nei dati in streaming.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: aahi
ms.openlocfilehash: 76308e2167cbedae9572f1fb5037dfb394ce4b17
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483419"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Guida introduttiva: Rilevare le anomalie nei dati delle serie temporali tramite l'API REST Rilevamento anomalie e C# 

Usare questo avvio rapido per iniziare a usare le due modalità di rilevamento dell'API Rilevamento anomalie per rilevare le anomalie nei dati delle serie temporali. Questa applicazione C# invia due richieste API contenenti i dati delle serie temporali in formato JSON e riceve le risposte.

| Richiesta API                                        | Output dell'applicazione                                                                                                                                         |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Rilevare anomalie come batch                        | Risposta JSON contenente lo stato dell'anomalia (e altri dati) per ogni punto dati nei dati di serie temporali e le posizioni delle anomalie rilevate. |
| Rilevare lo stato delle anomalie del punto dati più recente | Risposta JSON contenente lo stato dell'anomalia (e altri dati) per il punto dati più recente nei dati di serie temporali.                                        |

 L'applicazione è scritta in C#, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione. Il codice sorgente per questo avvio rapido è disponibile su [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/csharp-detect-anomalies.cs).

## <a name="prerequisites"></a>Prerequisiti

- Qualsiasi edizione di [Visual Studio 2017 o versioni successive](https://visualstudio.microsoft.com/downloads/)
- Chiave di Rilevamento anomalie ed endpoint
- Il framework [Json.NET](https://www.newtonsoft.com/json), disponibile come pacchetto NuGet. Per installare Newtonsoft.Json come pacchetto NuGet in Visual Studio:
    
    1. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni**.
    2. Scegliere **Gestisci pacchetti NuGet**.
    3. Cercare *Newtonsoft.Json* e installare il pacchetto.

- Se si usa Linux/MacOS, questa applicazione può essere eseguita tramite [Mono](https://www.mono-project.com/).

- File JSON contenente i punti dati delle serie temporali. I dati di esempio per questa guida di avvio rapido sono disponibili in [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Creare una risorsa di Rilevamento anomalie

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Creare una nuova applicazione

1. In Visual Studio creare una nuova soluzione console e aggiungere i pacchetti seguenti. 

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. Creare variabili per la chiave di sottoscrizione e per l'endpoint. Di seguito sono riportati gli URI che è possibile usare per il rilevamento anomalie. Questi verranno aggiunti all'endpoint di servizio in un secondo momento per creare gli URL delle richieste API.

    | Metodo di rilevamento                   | URI                                              |
    |------------------------------------|--------------------------------------------------|
    | Rilevamento in batch                    | `/anomalydetector/v1.0/timeseries/entire/detect` |
    | Rilevamento nel punto dati più recente | `/anomalydetector/v1.0/timeseries/last/detect`   |
        
    [!code-csharp[initial variables for endpoint, key and data file](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Creare una funzione per inviare richieste

1. Creare una nuova funzione asincrona denominata `Request` che accetta le variabili create in precedenza.

2. Impostare il protocollo di sicurezza del client e le informazioni di intestazione con l'oggetto `HttpClient`. Assicurarsi di aggiungere la chiave di sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key`. Creare quindi un oggetto `StringContent` per la richiesta.

3. Inviare la richiesta con `PostAsync()` e quindi restituire la risposta.

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>Rilevare anomalie come batch

1. Creare una nuova funzione denominata `detectAnomaliesBatch()`. Creare la richiesta e inviarla chiamando la funzione `Request()` con l'endpoint, la chiave di sottoscrizione, l'URL per il rilevamento anomalie in batch e i dati delle serie temporali.

2. Deserializzare l'oggetto JSON e visualizzarlo nella console.

3. Se la risposta contiene il campo `code`, viene visualizzato il codice di errore e il messaggio di errore. 

4. In caso contrario, trovare le posizioni delle anomalie nel set di dati. Il campo `isAnomaly` della risposta contiene una matrice di valori booleani, ognuno dei quali indica se un punto dati è un'anomalia. Convertirla in una matrice di stringhe con la funzione `ToObject<bool[]>()` dell'oggetto risposta. Scorrere la matrice e stampare l'indice dei valori `true`. Questi valori corrispondono all'indice dei punti dati anomali, se presenti.

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Rilevare lo stato delle anomalie del punto dati più recente

1. Creare una nuova funzione denominata `detectAnomaliesLatest()`. Creare la richiesta e inviarla chiamando la funzione `Request()` con l'endpoint, la chiave di sottoscrizione, l'URL per il rilevamento anomalie nel punto dati più recente e i dati delle serie temporali.

2. Deserializzare l'oggetto JSON e visualizzarlo nella console.

    [!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]
 
## <a name="load-your-time-series-data-and-send-the-request"></a>Caricare i dati delle serie temporali e inviare la richiesta

1. Nel metodo main dell'applicazione caricare i dati delle serie temporali JSON con `File.ReadAllText()`. 

2. Chiamare le funzioni di rilevamento anomalie create in precedenza. Usare `System.Console.ReadKey()` per mantenere aperta la finestra della console dopo l'esecuzione dell'applicazione.

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>Risposta di esempio

Viene restituita una risposta con esito positivo in formato JSON. Fare clic sui collegamenti seguenti per visualizzare la risposta JSON in GitHub:
* [Esempio di risposta di rilevamento in batch](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Esempio di risposta di rilevamento nel punto dati più recente](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
