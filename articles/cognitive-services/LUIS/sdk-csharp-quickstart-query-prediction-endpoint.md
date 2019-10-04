---
title: "Guida introduttiva: Endpoint di previsione con l'SDK per C# - LUIS"
titleSuffix: Azure Cognitive Services
description: Usare l'SDK per C# per inviare un'espressione dell'utente a LUIS e ricevere una previsione.
author: diberry
manager: nitinme
ms.service: cognitive-services
services: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: c0b534848232d60929722e2036f69f4b6e670a4a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563159"
---
# <a name="quickstart-query-prediction-endpoint-with-c-net-sdk"></a>Guida introduttiva: Eseguire query sull'endpoint di previsione con .NET SDK per C#

Usare .NET SDK, disponibile in [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/), per inviare un'espressione dell'utente a Language Understanding (LUIS) e ricevere una previsione dell'intenzione dell'utente. 

Questa guida introduttiva consente di inviare un'espressione dell'utente, ad esempio `turn on the bedroom light`, a un'applicazione Language Understanding pubblica e quindi ricevere la previsione e visualizzare la finalità con punteggio più alto `HomeAutomation.TurnOn` e l'entità `HomeAutomation.Room` trovata all'interno dell'espressione. 

## <a name="prerequisites"></a>Prerequisiti

* [Visual Studio Community 2017 edition](https://visualstudio.microsoft.com/vs/community/)
* Linguaggio di programmazione C# (incluso in VS Community 2017)
* ID app pubblica: df67dcdb-c37d-46af-88e1-8b97951ca1c2

> [!Note]
> La soluzione completa è disponibile nel repository GitHub [cognitive-services-language-understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/sdk-quickstarts/c%23/UsePredictionRuntime).

Per altre informazioni, vedere:

 * [Documentazione di riferimento sull'SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)


## <a name="get-cognitive-services-or-language-understanding-key"></a>Ottenere una chiave di Servizi cognitivi o Language Understanding

Per usare l'app pubblica per la domotica, è necessaria una chiave valida per le previsioni dell'endpoint. È possibile usare una chiave di Servizi cognitivi, creata di seguito con l'interfaccia della riga di comando di Azure e valida per molti servizi cognitivi, oppure una chiave di `Language Understanding`. 

Usare questo [comando dell'interfaccia della riga di comando di Azure per creare una chiave di Servizi cognitivi](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create):

```azurecli-interactive
az cognitiveservices account create \
    -n my-cog-serv-resource \
    -g my-cog-serv-resource-group \
    --kind CognitiveServices \
    --sku S0 \
    -l WestEurope \ 
    --yes
```

## <a name="create-net-core-project"></a>Creare un progetto .NET Core

Creare un progetto console .NET Core in Visual Studio Community 2017.

1. Aprire Visual Studio Community 2017.
1. Creare un nuovo progetto. Nella sezione **Visual C#**  scegliere **Console App (.NET Core)** (App console - .NET Core).
1. Immettere `QueryPrediction` come nome del progetto, lasciare gli altri valori predefiniti e selezionare **OK**.
    Viene così creato un progetto semplice il cui file di codice primario è denominato **Program.cs**.

## <a name="add-sdk-with-nuget"></a>Aggiungere l'SDK con NuGet

1. In **Esplora soluzioni** selezionare il progetto denominato **QueryPrediction** nella visualizzazione albero e quindi fare clic con il pulsante destro del mouse. Nel menu selezionare **Gestisci pacchetti NuGet**.
1. Selezionare **Sfoglia** e quindi immettere `Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime`. Quando vengono visualizzate le informazioni sul pacchetto, selezionare **Installa** per installare il pacchetto nel progetto. 
1. Aggiungere le istruzioni _using_ seguenti all'inizio di **Program.cs**. Non rimuovere l'istruzione _using_ esistente per `System`. 

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
```

## <a name="create-a-new-method-for-the-prediction"></a>Creare un nuovo metodo per la previsione

Creare un nuovo metodo `GetPrediction` per inviare la query all'endpoint di previsione di query. Il metodo creerà e configurerà tutti gli oggetti necessari e quindi restituirà `Task` con i risultati della previsione [`LuisResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.luisresult?view=azure-dotnet). 

```csharp
static async  Task<LuisResult> GetPrediction() {
}
```

## <a name="create-credentials-object"></a>Creare l'oggetto credenziali

Aggiungere il codice seguente al metodo `GetPrediction` per creare le credenziali client con la chiave di Servizi cognitivi.

Sostituire `<REPLACE-WITH-YOUR-KEY>` con l'area della chiave di Servizi cognitivi. La chiave è riportata nel [portale di Azure](https://portal.azure.com) nella pagina Chiavi per la risorsa.

```csharp
// Use Language Understanding or Cognitive Services key
// to create authentication credentials
var endpointPredictionkey = "<REPLACE-WITH-YOUR-KEY>";
var credentials = new ApiKeyServiceClientCredentials(endpointPredictionkey);
```

## <a name="create-language-understanding-client"></a>Creare il client Language Understanding

Nel metodo `GetPrediction` aggiungere il codice seguente dopo quello precedente per usare le nuove credenziali creando un oggetto client [`LUISRuntimeClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient.-ctor?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Runtime_LUISRuntimeClient__ctor_Microsoft_Rest_ServiceClientCredentials_System_Net_Http_DelegatingHandler___). 

Sostituire `<REPLACE-WITH-YOUR-KEY-REGION>` con l'area della chiave, ad esempio `westus`. L'area della chiave è riportata nel [portale di Azure](https://portal.azure.com) nella pagina Panoramica per la risorsa.

```csharp
// Create Luis client and set endpoint
// region of endpoint must match key's region, for example `westus`
var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { });
luisClient.Endpoint = "https://<REPLACE-WITH-YOUR-KEY-REGION>.api.cognitive.microsoft.com";
```

## <a name="set-query-parameters"></a>Impostare i parametri di query

Nel metodo `GetPrediction` aggiungere il codice seguente dopo quello precedente per impostare i parametri di query.

```csharp
// public Language Understanding Home Automation app
var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

// query specific to home automation app
var query = "turn on the bedroom light";

// common settings for remaining parameters
Double? timezoneOffset = null;
var verbose = true;
var staging = false;
var spellCheck = false;
String bingSpellCheckKey = null;
var log = false;
```

## <a name="query-prediction-endpoint"></a>Eseguire query sull'endpoint di stima

Nel metodo `GetPrediction` aggiungere il codice seguente dopo quello precedente per impostare i parametri di query:

```csharp
// Create prediction client
var prediction = new Prediction(luisClient);

// get prediction
return await prediction.ResolveAsync(appId, query, timezoneOffset, verbose, staging, spellCheck, bingSpellCheckKey, log, CancellationToken.None);
```

## <a name="display-prediction-results"></a>Visualizzare i risultati della previsione

Modificare il metodo **Main** in modo da chiamare il nuovo metodo `GetPrediction` e restituire i risultati della previsione:

```csharp
static void Main(string[] args)
{

    var luisResult = GetPrediction().Result;

    // Display query
    Console.WriteLine("Query:'{0}'", luisResult.Query);

    // Display most common properties of query result
    Console.WriteLine("Top intent is '{0}' with score {1}", luisResult.TopScoringIntent.Intent,luisResult.TopScoringIntent.Score);

    // Display all entities detected in query utterance
    foreach (var entity in luisResult.Entities)
    {
        Console.WriteLine("{0}:'{1}' begins at position {2} and ends at position {3}", entity.Type, entity.Entity, entity.StartIndex, entity.EndIndex);
    }

    Console.Write("done");

}
```

## <a name="run-the-project"></a>Eseguire il progetto

Compilare il progetto in Studio ed eseguirlo per visualizzare l'output della query:

```console
Query:'turn on the bedroom light'
Top intent is 'HomeAutomation.TurnOn' with score 0.809439957
HomeAutomation.Room:'bedroom' begins at position 12 and ends at position 18
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) e [documentazione di riferimento su .NET](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet). 

> [!div class="nextstepaction"] 
> [Esercitazione: Creare un'app LUIS per determinare le intenzioni dell'utente](luis-quickstart-intents-only.md) 