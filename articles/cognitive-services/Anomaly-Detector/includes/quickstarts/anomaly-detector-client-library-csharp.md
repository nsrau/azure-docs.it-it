---
title: Avvio rapido per la libreria client di Rilevamento anomalie per .NET
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/04/2020
ms.author: aahi
ms.openlocfilehash: 6f1c016efb300dea2cdef91c84bb901cffd09fa0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "91024989"
---
Questo argomento include informazioni introduttive sulla libreria client di Rilevamento anomalie per .NET. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. Il servizio Rilevamento anomalie consente di trovare le anomalie nei dati delle serie temporali applicando automaticamente i modelli di mapping più appropriati, indipendentemente dal settore, dallo scenario o dal volume di dati.

Usare la libreria client di Rilevamento anomalie per .NET per:

* Rilevare anomalie nell'intero set di dati della serie temporale come richiesta batch
* Rilevare lo stato di anomalia del punto dati più recente nella serie temporale
* Rilevare i punti di modifica di una tendenza nel set di dati.

[Documentazione di riferimento della libreria](https://aka.ms/anomaly-detector-dotnet-ref) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Azure.AI.AnomalyDetector/3.0.0-preview.2) | [Trovare il codice in GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* Versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="creare una risorsa Rilevamento anomalie"  target="_blank">creare una risorsa Rilevamento anomalie <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Attendere che venga distribuita e fare clic sul pulsante **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Rilevamento anomalie. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-net-core-application"></a>Creare una nuova applicazione .NET Core

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `anomaly-detector-quickstart`. Questo comando crea un semplice progetto "Hello World" con un singolo file di origine C#: *Program.cs*.

```dotnetcli
dotnet new console -n anomaly-detector-quickstart
```

Spostarsi nella cartella dell'app appena creata. È possibile compilare l'applicazione con il comando seguente:

```dotnetcli
dotnet build
```

L'output di compilazione non deve contenere alcun avviso o errore.

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installare la libreria client

All'interno della directory dell'applicazione, installare la libreria client di Rilevamento anomalie per .NET con il comando seguente:

```dotnetcli
dotnet add package Azure.AI.AnomalyDetector --version 3.0.0-preview.2
```

Dalla directory del progetto aprire il file *program.cs* e aggiungere quanto segue usando `directives`:

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

Nel metodo `main()` dell'applicazione creare variabili per la località della risorsa di Azure e la propria chiave come variabile di ambiente. Se la variabile di ambiente è stata creata dopo l'avvio dell'applicazione, l'editor, la shell o l'ambiente di sviluppo integrato che esegue tale variabile dovrà essere chiuso e ricaricato per accedervi.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>Modello a oggetti

Il client di Rilevamento anomalie è un oggetto [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) che esegue l'autenticazione ad Azure tramite l'oggetto [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials) contenente la chiave. Il client può eseguire il rilevamento anomalie in un intero set di dati usando [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync) oppure nel punto dati più recente usando [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync). Il metodo [ChangePointDetectAsync](https://aka.ms/anomaly-detector-dotnet-ref) rileva i punti che contrassegnano le modifiche in una tendenza.

I dati delle serie temporali vengono inviati come serie di oggetti [Point](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) in un oggetto [Request](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request). L'oggetto `Request` contiene le proprietà per la descrizione dei dati (ad esempio, [Granularity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity)) e i parametri per il rilevamento delle anomalie.

La risposta del servizio Rilevamento anomalie è un oggetto [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse), [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) o [changePointDetectResponse](https://aka.ms/anomaly-detector-dotnet-ref), a seconda del metodo usato.

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le operazioni seguenti con la libreria client di Rilevamento anomalie per .NET:

* [Autenticare il client](#authenticate-the-client)
* [Caricare il set di dati di una serie temporale da un file](#load-time-series-data-from-a-file)
* [Rilevare le anomalie nell'intero set di dati](#detect-anomalies-in-the-entire-data-set)
* [Rilevare lo stato di anomalia del punto dati più recente](#detect-the-anomaly-status-of-the-latest-data-point)
* [Rilevare i punti di modifica nel set di dati](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>Autenticare il client

In un nuovo metodo creare un'istanza di un client con l'endpoint e la chiave. Creare un oggetto [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) con la propria chiave e usarlo con l'endpoint per creare un oggetto [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview).

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]

## <a name="load-time-series-data-from-a-file"></a>Caricare i dati di una serie temporale da un file

Scaricare i dati di esempio per questo avvio rapido da [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. Nel browser fare clic con il pulsante destro del mouse su **Raw** (Non elaborato).
2. Fare clic su **Salva link con nome**.
3. Salvare il file nella directory dell'applicazione come file con estensione csv.

I dati della serie temporale vengono formattati come file con estensione csv e vengono inviati all'API Rilevamento anomalie.

Creare un nuovo metodo per la lettura dei dati della serie temporale e aggiungerlo a un oggetto [Request](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview). Chiamare `File.ReadAllLines()` con il percorso del file, creare un elenco di oggetti [Point](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) e rimuovere eventuali caratteri di nuova riga. Estrarre i valori e separare il datestamp dal rispettivo valore numerico, quindi aggiungerli a un nuovo oggetto `Point`.

Creare un oggetto `Request` con la serie di punti e con `Granularity.Daily` per definire il valore di [Granularity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (o periodicità) dei punti dati.

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Rilevare le anomalie nell'intero set di dati

Creare un metodo per chiamare il metodo [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) del client con l'oggetto `Request` e attendere la risposta come oggetto [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview). Se la serie temporale contiene anomalie, eseguire l'iterazione attraverso i valori [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) della risposta e stampare quelli che sono `true`. Questi valori corrispondono all'indice dei punti dati anomali, se presenti.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Rilevare lo stato delle anomalie del punto dati più recente

Creare un metodo per chiamare il metodo [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) del client con l'oggetto `Request` e attendere la risposta come oggetto [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview). Controllare l'attributo [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) della risposta per determinare se il punto dati più recente inviato era un'anomalia.

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="detect-change-points-in-the-data-set"></a>Rilevare i punti di modifica nel set di dati

Creare un metodo per chiamare il metodo [DetectChangePointAsync](https://aka.ms/anomaly-detector-dotnet-ref) del client con l'oggetto `Request` e attendere la risposta come oggetto [ChangePointDetectResponse](https://aka.ms/anomaly-detector-dotnet-ref). Controllare i valori IsChangePoint della risposta e stampare quelli che restituiscono `true`. Questi valori corrispondono ai punti di modifica della tendenza, se individuati.

[!code-csharp[DetectChangePoint() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=changePointExample)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `dotnet run` dalla directory dell'applicazione.

```dotnetcli
dotnet run
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
