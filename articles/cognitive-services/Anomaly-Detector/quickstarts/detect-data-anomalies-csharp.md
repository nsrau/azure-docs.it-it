---
title: "Avvio rapido: Rilevare le anomalie nei dati delle serie temporali tramite l'API REST Rilevamento anomalie e C#"
titleSuffix: Azure Cognitive Services
description: Questo argomento di avvio rapido illustra come usare le due modalità di rilevamento dell'API Rilevamento anomalie per rilevare le anomalie nei dati delle serie temporali.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 09/03/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: c19f901f4419cca59a3b4e070191c8bb89ce7c34
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319198"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Avvio rapido: Rilevare le anomalie nei dati delle serie temporali tramite l'API REST Rilevamento anomalie e C#

Usare questa guida di avvio rapido per iniziare a usare l'API Rilevamento anomalie per rilevare le anomalie nei dati delle serie temporali. Questa applicazione C# invia le richieste API contenenti i dati delle serie temporali in formato JSON e riceve le risposte.

| Richiesta API                                        | Output dell'applicazione                                                                                                                                         |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Rilevare anomalie come batch                        | Risposta JSON contenente lo stato dell'anomalia (e altri dati) per ogni punto dati nei dati di serie temporali e le posizioni delle anomalie rilevate. |
| Rilevare lo stato delle anomalie del punto dati più recente | Risposta JSON contenente lo stato dell'anomalia (e altri dati) per il punto dati più recente nei dati di serie temporali. |
| Rilevare i punti di modifica che contrassegnano nuove tendenze dei dati | Risposta JSON che contiene i punti di modifica rilevati nei dati delle serie temporali. |

L'applicazione è scritta in C#, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione. Il codice sorgente per questo avvio rapido è disponibile su [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/csharp-detect-anomalies.cs).

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
- Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="creare una risorsa Rilevamento anomalie"  target="_blank">creare una risorsa Rilevamento anomalie <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Attendere che venga distribuita e fare clic sul pulsante **Vai alla risorsa**.
    - La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Rilevamento anomalie. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.
- Qualsiasi edizione di [Visual Studio 2017 o versioni successive](https://visualstudio.microsoft.com/downloads/)
- Il framework [Json.NET](https://www.newtonsoft.com/json), disponibile come pacchetto NuGet. Per installare Newtonsoft.Json come pacchetto NuGet in Visual Studio:

    1. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni**.
    2. Scegliere **Gestisci pacchetti NuGet**.
    3. Cercare *Newtonsoft.Json* e installare il pacchetto.

- Se si usa Linux/MacOS, questa applicazione può essere eseguita tramite [Mono](https://www.mono-project.com/).

- File JSON contenente i punti dati delle serie temporali. I dati di esempio per questa guida di avvio rapido sono disponibili in [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>Creare una nuova applicazione

1. In Visual Studio creare una nuova soluzione console e aggiungere i pacchetti seguenti.

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. Creare variabili per la chiave di sottoscrizione e per l'endpoint. Di seguito sono riportati gli URI che è possibile usare per il rilevamento anomalie. Questi verranno aggiunti all'endpoint di servizio in un secondo momento per creare gli URL delle richieste API.

    | Metodo di rilevamento                   | URI                                              |
    |------------------------------------|--------------------------------------------------|
    | Rilevamento in batch                    | `/anomalydetector/v1.0/timeseries/entire/detect` |
    | Rilevamento nel punto dati più recente | `/anomalydetector/v1.0/timeseries/last/detect`   |
    | Rilevamento dei punti di modifica | `/anomalydetector/v1.0/timeseries/changepoint/detect`   |

    [!code-csharp[initial variables for endpoint, key and data file](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Creare una funzione per inviare richieste

1. Creare una nuova funzione asincrona denominata `Request` che accetta le variabili create in precedenza.

2. Impostare il protocollo di sicurezza del client e le informazioni di intestazione con l'oggetto `HttpClient`. Assicurarsi di aggiungere la chiave di sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key`. Creare quindi un oggetto `StringContent` per la richiesta.

3. Inviare la richiesta con `PostAsync()` e quindi restituire la risposta.

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>Rilevare anomalie come batch

1. Creare una nuova funzione denominata `detectAnomaliesBatch()`. Creare la richiesta e inviarla chiamando la funzione `Request()` con l'endpoint, la chiave di sottoscrizione, l'URL per il rilevamento anomalie in batch e i dati delle serie temporali.

2. Deserializzare l'oggetto JSON e visualizzarlo nella console.

3. Se la risposta contiene il campo `code`, viene visualizzato il codice errore e il messaggio di errore.

4. In caso contrario, trovare le posizioni delle anomalie nel set di dati. Il campo `isAnomaly` della risposta contiene una matrice di valori booleani, ognuno dei quali indica se un punto dati è un'anomalia. Convertirla in una matrice di stringhe con la funzione `ToObject<bool[]>()` dell'oggetto risposta. Scorrere la matrice e stampare l'indice dei valori `true`. Questi valori corrispondono all'indice dei punti dati anomali, se presenti.

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Rilevare lo stato delle anomalie del punto dati più recente

1. Creare una nuova funzione denominata `detectAnomaliesLatest()`. Creare la richiesta e inviarla chiamando la funzione `Request()` con l'endpoint, la chiave di sottoscrizione, l'URL per il rilevamento anomalie nel punto dati più recente e i dati delle serie temporali.

2. Deserializzare l'oggetto JSON e visualizzarlo nella console.

    [!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]

## <a name="detect-change-points-in-the-data"></a>Rilevare i punti di modifica nei dati

1. Creare una nuova funzione denominata `detectChangePoints()`. Creare la richiesta e inviarla chiamando la funzione `Request()` con l'endpoint, l'URL per il rilevamento anomalie in batch, la chiave di sottoscrizione e i dati delle serie temporali.

2. Deserializzare l'oggetto JSON e visualizzarlo nella console.

3. Se la risposta contiene un campo `code`, stampare il codice di errore e il messaggio di errore.

4. In caso contrario, trovare le posizioni delle punti di modifica nel set di dati. Il campo `isChangePoint` della risposta contiene una matrice di valori booleani, ognuno dei quali indica se un punto dati è stato identificato come punto di modifica. Convertirla in una matrice di stringhe con la funzione `ToObject<bool[]>()` dell'oggetto risposta. Scorrere la matrice e stampare l'indice dei valori `true`. Questi valori corrispondono agli indici dei punti di modifica della tendenza, se individuati.

    [!code-csharp[Detect change points](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectChangePoints)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Caricare i dati delle serie temporali e inviare la richiesta

1. Nel metodo main dell'applicazione caricare i dati delle serie temporali JSON con `File.ReadAllText()`.

2. Chiamare le funzioni di rilevamento anomalie create in precedenza. Usare `System.Console.ReadKey()` per mantenere aperta la finestra della console dopo l'esecuzione dell'applicazione.

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>Risposta di esempio

Viene restituita una risposta con esito positivo in formato JSON. Fare clic sui collegamenti seguenti per visualizzare la risposta JSON in GitHub:
* [Esempio di risposta di rilevamento in batch](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Esempio di risposta di rilevamento nel punto dati più recente](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)
* [Esempio di risposta di rilevamento di punti di modifica](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/change-point-sample.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
