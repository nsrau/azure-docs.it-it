---
title: 'Guida introduttiva: Libreria client di creazione di LUIS (Language Understanding) 3.x per .NET'
titleSuffix: Azure Cognitive Services
description: Introduzione alla libreria client LUIS 3.x per .NET. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 12/03/2019
ms.author: diberry
ms.openlocfilehash: c0a6fd3921f301b269adad6c264b6b687462260e
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74808020"
---
# <a name="quickstart-language-understanding-luis-client-library-3x-for-net"></a>Guida introduttiva: Libreria client di LUIS (Language Understanding) 3.x per .NET

Introduzione alla libreria client di LUIS (Language Understanding) 3.x per .NET. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.  Language Understanding (LUIS) consente di applicare l'intelligence dell'apprendimento automatico personalizzato al testo in linguaggio naturale parlato di un utente per prevedere il significato generale ed estrarre informazioni pertinenti e dettagliate.

Usare la libreria client di LUIS (Language Understanding) 3.x per .NET per:

* Eseguire query su un endpoint pubblicato

[Documentazione di riferimento](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) | [Pacchetto di creazione (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) | [Esempi C#](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs)

## <a name="prerequisites"></a>Prerequisiti

* Account del portale di Language Understanding (LUIS) - [Creare un account gratuito](https://www.luis.ai)
* Versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)


## <a name="setting-up"></a>Configurazione

### <a name="get-your-language-understanding-luis-starter-key"></a>Ottenere la chiave di avvio di Language Understanding (LUIS)

Ottenere la [chiave di avvio](luis-how-to-azure-subscription.md#starter-key) creando una risorsa di creazione LUIS. Conservare la chiave e l'area della chiave per il passaggio successivo.

### <a name="create-an-environment-variable"></a>Creare una variabile di ambiente

Usando la chiave e l'area della chiave, creare due variabili di ambiente per l'autenticazione:

* `COGNITIVESERVICE_AUTHORING_KEY`: la chiave della risorsa per l'autenticazione delle richieste.
* `COGNITIVESERVICE_REGION`: area associata alla chiave. Ad esempio, `westus`.

Usare le istruzioni per il sistema operativo in uso.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx COGNITIVESERVICE_AUTHORING_KEY <replace-with-your-authoring-key>
setx COGNITIVESERVICE_REGION <replace-with-your-authoring-region>
```

Dopo aver aggiunto la variabile di ambiente, riavviare la finestra della console.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export COGNITIVESERVICE_AUTHORING_KEY=<replace-with-your-authoring-key>
export COGNITIVESERVICE_REGION=<replace-with-your-authoring-region>
```

Dopo avere aggiunto la variabile di ambiente, eseguire `source ~/.bashrc` dalla finestra della console per rendere effettive le modifiche.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Modificare `.bash_profile` e aggiungere la variabile di ambiente:

```bash
export COGNITIVESERVICE_AUTHORING_KEY=<replace-with-your-authoring-key>
export COGNITIVESERVICE_REGION=<replace-with-your-authoring-region>
```

Dopo avere aggiunto la variabile di ambiente, eseguire `source .bash_profile` dalla finestra della console per rendere effettive le modifiche.
***

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

Nella directory dell'applicazione installare la libreria client di creazione di Language Understanding (LUIS) per .NET con il comando seguente:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.0.0
```

Se si usa l'ambiente di sviluppo integrato di Visual Studio, la libreria client è disponibile come pacchetto NuGet scaricabile.


## <a name="object-model"></a>Modello a oggetti

Il client di creazione di Language Understanding (LUIS) è un oggetto [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) contenente la chiave di creazione che esegue l'autenticazione in Azure.

Al termine della creazione del client, usare il client per accedere alle funzionalità, tra cui:

* App: [creazione](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet), [eliminazione](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet), [pubblicazione](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* Espressioni di esempio: [aggiunta in base al batch](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet), [eliminazione in base all'ID](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet)
* Funzionalità: gestione di [elenchi di frasi](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet)
* Modello: gestione di [finalità](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) ed entità
* Criterio: gestione di [criteri](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet)
* Training: [training](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet) dell'app e polling per lo [stato del training](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet)
* [Versioni](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet): gestione con clonazione, esportazione ed eliminazione


## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le operazioni seguenti con la libreria client di creazione di Language Understanding (LUIS) per .NET:

* [Creare un'app](#create-a-luis-app)
* [Aggiungere entità](#create-entities-for-the-app)
* [Aggiungere tipi](#create-intent-for-the-app)
* [Aggiungi espressioni di esempio](#add-example-utterance-to-intent)
* [Eseguire il training dell'app](#train-the-app)
* [Pubblicare l'app](#publish-a-language-understanding-app)

## <a name="add-the-dependencies"></a>Aggiungere le dipendenze

Dalla directory del progetto aprire il file *Program.cs* nell'ambiente di sviluppo integrato o nell'editor preferito. Sostituire il codice `using` esistente con le direttive `using` seguenti:

[!code-csharp[Using statements](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>Autenticare il client

1. Creare una variabile per gestire la chiave di creazione derivata da una variabile di ambiente denominata `COGNITIVESERVICES_AUTHORING_KEY`. Se la variabile di ambiente è stata creata dopo l'avvio dell'applicazione, per accedere alla variabile sarà necessario chiudere e ricaricare l'editor, la shell o l'ambiente di sviluppo integrato in cui è in esecuzione. I metodi verranno creati in un secondo momento.

1. Creare le variabili per mantenere l'endpoint e l'area di creazione. L'area della chiave di creazione dipende dalla località in cui si esegue la creazione. Le [tre aree di creazione](luis-reference-regions.md) disponibili sono:

    * Australia - `australiaeast`
    * Europa - `westeurope`
    * Stati Uniti e altre aree - `westus` (valore predefinito)

    [!code-csharp[Authorization to resource key](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Variables)]

1. Creare un oggetto [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) con la chiave e usarlo con l'endpoint per creare un oggetto [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet).

    [!code-csharp[Create LUIS client object](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Creare un'app LUIS

1. Creare un'app LUIS contenente il modello di elaborazione del linguaggio naturale (NLP) che include finalità, entità ed espressioni di esempio.

1. Creare un oggetto [ApplicationCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet). Il nome e le impostazioni cultura della lingua sono proprietà obbligatorie.

1. Chiamare il metodo [Apps.AddAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet). La risposta corrisponde all'ID app.

    [!code-csharp[Create a LUIS app](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Creare la finalità per l'app
L'oggetto primario in un modello di app LUIS è la finalità. La finalità è allineata a un raggruppamento di _intenzioni_ delle espressioni dell'utente. Un utente può porre una domanda o fare un'affermazione per una risposta _con finalità_ specifica da un bot o da un'altra applicazione client. Alcuni esempi di intenzioni sono la prenotazione di un volo, la richiesta di informazioni meteo per una città di destinazione e la richiesta di informazioni di contatto per il servizio clienti.

Creare un oggetto [ModelCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet) con il nome della finalità univoca, quindi passare l'ID app, l'ID versione e l'oggetto ModelCreateObject al metodo [Model.AddIntentAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet). La risposta corrisponde all'ID finalità.

[!code-csharp[Create intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddIntents)]

## <a name="create-entities-for-the-app"></a>Creare le entità per l'app

Sebbene le entità non siano obbligatorie, sono in genere presenti nella maggior parte delle app. L'entità estrae le informazioni necessarie per soddisfare l'intenzione dell'utente dall'espressione dell'utente. Sono disponibili numerosi tipi di entità [predefinite](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet) e personalizzate, ognuna con i propri modelli DTO (Data Transformation Object).  Le entità predefinite comuni da aggiungere all'app includono [number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), [geographyV2](luis-reference-prebuilt-geographyv2.md), [ordinal](luis-reference-prebuilt-ordinal.md).

Questo metodo **AddEntities** ha creato un'entità semplice `Location` con due ruoli, un'entità semplice `Class`, un'entità composita `Flight` e aggiunge numerose entità predefinite.

È importante tenere presente che le entità non sono contrassegnate con una finalità. Possono in genere essere applicate a molte finalità. Solo le espressioni utente di esempio sono contrassegnate per un'unica finalità specifica.

I metodi di creazione per le entità sono parte della classe [Model](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet). Ogni tipo di entità dispone di un modello DTO (Data Transformation Object) specifico, in genere contenente la parola `model` nello spazio dei nomi [Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet).

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddEntities)]

## <a name="add-example-utterance-to-intent"></a>Aggiungere un'espressione di esempio alla finalità

Per determinare l'intenzione di un'espressione ed estrarre le entità, l'app necessita di esempi di espressioni. Gli esempi devono fare riferimento a un'unica finalità specifica e contrassegnare tutte le entità personalizzate. Non è necessario contrassegnare le entità predefinite.

Aggiungere le espressioni di esempio creando un elenco di oggetti [ExampleLabelObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet), un oggetto per ogni espressione di esempio. Ogni esempio deve contrassegnare tutte le entità con un dizionario di coppie nome/valore con nome dell'entità e valore dell'entità. Il valore dell'entità deve corrispondere esattamente a quanto visualizzato nel testo dell'espressione di esempio.

Chiamare [Examples.BatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ExamplesExtensions_BatchAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_IExamples_System_Guid_System_String_System_Collections_Generic_IList_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_Models_ExampleLabelObject__System_Threading_CancellationToken_) con l'ID app, l'ID versione e l'elenco di esempi. La chiamata risponde con un elenco di risultati. È necessario controllare il risultato di ogni esempio per assicurarsi che sia stato aggiunto correttamente al modello.

[!code-csharp[Add example utterances to a specific intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringBatchAddUtterancesForIntent)]

I metodi **CreateUtterance** e **CreateLabel** sono metodi di utilità che consentono di creare gli oggetti.

## <a name="train-the-app"></a>Eseguire il training dell'app

Dopo aver creato il modello, è necessario eseguire il training dell'app LUIS per questa versione del modello. Un modello sottoposto a training può essere usatoin un [contenitore](luis-container-howto.md) o [pubblicato](luis-how-to-publish-app.md) negli slot di staging o di produzione.

Il metodo [Train.TrainVersionAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) richiede l'ID app e l'ID versione.

Il training di un modello di dimensioni ridotte, come quello mostrato in questa esercitazione di avvio rapido, verrà eseguito molto rapidamente. Per le applicazioni a livello di produzione, il training dell'app deve includere una chiamata di polling al metodo [GetStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) per determinare quando o se il training ha avuto esito positivo. La risposta è un elenco di oggetti [ModelTrainingInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) con uno stato separato per ogni oggetto. Affinché il training venga considerato completato, è necessario che tutti gli oggetti abbiano esito positivo.

[!code-csharp[Train the app's version](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringTrainVersion)]

## <a name="publish-a-language-understanding-app"></a>Pubblicare un'app Language Understanding

Pubblicare l'app LUIS con il metodo [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet). Questo metodo consente di pubblicare la versione con training corrente nello slot specificato nell'endpoint. L'applicazione client usa questo endpoint per inviare espressioni utente per la previsione della finalità e l'estrazione di entità.

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringPublishVersionAndSlot)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `dotnet run` dalla directory dell'applicazione.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per eseguire la pulizia, eliminare l'app LUIS. Per eliminare l'app, usare il metodo [Apps.DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet). È anche possibile eliminare l'app dal [portale di LUIS](https://www.luis.ai).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[Usare .Net SDK per eseguire query sull'endpoint previsioni](sdk-csharp-quickstart-query-prediction-endpoint.md)

* [Che cos'è l'API Language Understanding (LUIS)?](what-is-luis.md)
* [Novità](whats-new.md)
* [Finalità](luis-concept-intent.md), [entità](luis-concept-entity-types.md), [espressioni di esempio](luis-concept-utterance.md) ed [entità predefinite](luis-reference-prebuilt-entities.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs).
