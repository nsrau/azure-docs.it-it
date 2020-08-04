---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 4bf86c616420bb049e1d7a82ad0e942e6eb7b36f
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87369264"
---
Usare la libreria client di previsione di LUIS (Language Understanding) per .NET per:

* Ottenere previsioni per slot
* Previsioni per versione

[Documentazione di riferimento](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) | [Pacchetto di runtime di previsioni (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [Esempi in C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x)

## <a name="prerequisites"></a>Prerequisiti

* Account del portale di Language Understanding (LUIS) - [Creare un account gratuito](https://www.luis.ai)
* Versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Un ID app LUIS: usare l'ID app IoT pubblica `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. La query utente usata nel codice di avvio rapido è specifica di tale app.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

Creare una nuova applicazione .NET Core nell'ambiente di sviluppo integrato o nell'editor preferito.

1. Nella finestra di una console (ad esempio cmd, PowerShell o Bash) usare il comando dotnet `new` per creare una nuova app console con il nome `language-understanding-quickstart`. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: `Program.cs`.

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Spostarsi nella cartella dell'app appena creata.

1. È possibile compilare l'applicazione con il comando seguente:

    ```dotnetcli
    dotnet build
    ```

    L'output di compilazione non deve contenere alcun avviso o errore.

    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```

### <a name="install-the-sdk"></a>Installare l'SDK

Nella directory dell'applicazione installare la libreria client del runtime di previsioni di LUIS (Language Understanding) per .NET con il comando seguente:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

Se si usa l'ambiente di sviluppo integrato di Visual Studio, la libreria client è disponibile come pacchetto NuGet scaricabile.

## <a name="object-model"></a>Modello a oggetti

Il client del runtime di previsioni di LUIS (Language Understanding) è un oggetto [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) che esegue l'autenticazione in Azure e contiene la chiave della risorsa.

Al termine della creazione del client, usare il client per accedere alle funzionalità, tra cui:

* Previsioni per [staging o slot del prodotto](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)
* Previsioni per [versione](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)


## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le operazioni seguenti con la libreria client del runtime di previsioni di LUIS (Language Understanding) per .NET:

* [Previsioni per slot](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Aggiungere le dipendenze

Dalla directory del progetto aprire il file *Program.cs* nell'ambiente di sviluppo integrato o nell'editor preferito. Sostituire il codice `using` esistente con le direttive `using` seguenti:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Autenticare il client

1. Creare variabili per la chiave, il nome della risorsa, l'ID app e lo slot di pubblicazione. Impostare l'ID app sull'app IoT pubblica:

    **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`**

    [!code-csharp[Create variables](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_variables)]

1. Creare un oggetto [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) con la chiave e usarlo con l'endpoint per creare un oggetto [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet).

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_create_client)]

## <a name="get-prediction-from-runtime"></a>Ottenere previsioni dal runtime

Aggiungere il metodo seguente per creare la richiesta al runtime di previsioni.

L'espressione utente fa parte dell'oggetto [PredictionRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet).

Per soddisfare la richiesta, il metodo **GetSlotPredictionAsync** richiede diversi parametri, ad esempio l'ID app, il nome dello slot e l'oggetto richiesta di previsioni. Le altre opzioni, ad esempio per l'output dettagliato, la visualizzazione di tutte le finalità e il log, sono facoltative.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_maintask)]

## <a name="main-code-for-the-prediction"></a>Codice main per le previsioni

Usare il metodo main seguente per collegare variabili e metodi tra loro e ottenere le previsioni.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_main)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `dotnet run` dalla directory dell'applicazione.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Pulire le risorse

Una volta completate le operazioni con le previsioni, pulire le risorse usate in questo argomento di avvio rapido eliminando il file program.cs e le relative sottodirectory.
