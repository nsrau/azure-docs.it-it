---
title: 'Guida introduttiva: Libreria client di Riconoscimento modulo per .NET | Microsoft Docs'
description: Iniziare a usare la libreria client di Riconoscimento modulo per .NET per eseguire il training, estrarre, analizzare e generare l'output di dati strutturati.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 018371f4c188d05d92a5fd3fd81511d941a359a6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978612"
---
# <a name="quickstart-form-recognizer-client-library-for-net"></a>Guida introduttiva: Libreria client di Riconoscimento modulo per .NET

Introduzione alla libreria client di Riconoscimento modulo per .NET. Riconoscimento modulo è un servizio cognitivo che usa la tecnologia di Machine Learning per identificare ed estrarre coppie chiave-valore e dati di tabella da documenti modulo. Restituisce quindi dati strutturati che includono le relazioni nel file originale. Seguire questi passaggi per installare il pacchetto SDK e provare il codice di esempio per le attività di base.

Usare la libreria client di Riconoscimento modulo per .NET per:

* [Eseguire il training di un modello di Riconoscimento modulo personalizzato](#train-a-custom-model)
* [Ottenere un elenco di chiavi estratte](#get-a-list-of-extracted-keys)
* [Analizzare i moduli con un modello personalizzato](#analyze-forms-with-a-custom-model)
* [Ottenere un elenco di modelli personalizzati](#get-a-list-of-custom-models)
* [Eliminare un modello personalizzato](#delete-a-custom-model)

[Documentazione di riferimento](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/formrecognizer?view=azure-dotnet-preview) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.FormRecognizer) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.FormRecognizer/)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/).
* Accesso all'anteprima dell'API di riconoscimento modulo ad accesso limitato. Per avere accesso all'anteprima, completare e inviare il modulo di [richiesta di accesso al riconoscimento modulo](https://aka.ms/FormRecognizerRequestAccess).
* Un BLOB di Archiviazione di Azure contenente un set di dati di training. Consultare [Compilare un training set per un modello personalizzato](../build-training-data-set.md) per suggerimenti e opzioni per la creazione di dati di training. Per questo argomento di avvio rapido, è possibile usare i file inclusi nella cartella **Train** del [set di dati di esempio](https://go.microsoft.com/fwlink/?linkid=2090451).
* Versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>Configurazione

### <a name="create-a-form-recognizer-azure-resource"></a>Creare una risorsa di Azure per Riconoscimento modulo

[!INCLUDE [create resource](../includes/create-resource.md)]

Dopo aver ottenuto una chiave dalla sottoscrizione di valutazione o dalla risorsa, [creare le variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave e l'endpoint, denominate rispettivamente `FORM_RECOGNIZER_KEY` e `FORM_RECOGNIZER_ENDPOINT`.

### <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `formrecognizer-quickstart`. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: _Program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Spostarsi nella cartella dell'app appena creata. Quindi compilare l'applicazione con il comando seguente:

```console
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

Dalla directory del progetto aprire il file _Program.cs_ nell'ambiente di sviluppo integrato o nell'editor preferito. Aggiungere le istruzioni `using` seguenti:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_using)]

Aggiungere quindi il codice seguente nel metodo **Main** dell'applicazione. Questa attività asincrona verrà definita in un secondo momento.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>Installare la libreria client

Nella directory dell'applicazione installare la libreria client di Riconoscimento modulo per .NET con il comando seguente:

```console
dotnet add package Microsoft.Azure.CognitiveServices.FormRecognizer --version 0.8.0-preview
```

Se si usa l'ambiente di sviluppo integrato di Visual Studio, la libreria client è disponibile come pacchetto NuGet scaricabile.

## <a name="object-model"></a>Modello a oggetti

Le classi seguenti gestiscono la funzionalità principale dell'SDK Riconoscimento modulo.

|NOME|DESCRIZIONE|
|---|---|
|[FormRecognizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.formrecognizerclient?view=azure-dotnet-preview)|Questa classe è necessaria per tutte le funzionalità di Riconoscimento modulo. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per produrre istanze di altre classi.|
|[TrainRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainrequest?view=azure-dotnet-preview)| Usare questa classe per eseguire il training di un modello di Riconoscimento modulo personalizzato usando i propri dati di input del training. |
|[TrainResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainresult?view=azure-dotnet-preview)| Questa classe fornisce i risultati di un'operazione di training del modello personalizzato, incluso l'ID del modello, che è quindi possibile usare per analizzare i moduli. |
|[AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)| Questa classe fornisce i risultati di un'operazione di analisi del modello personalizzato. Include un elenco di istanze di **ExtractedPage**. |
|[ExtractedPage](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.extractedpage?view=azure-dotnet-preview)| Questa classe rappresenta tutti i dati estratti da un singolo documento modulo.|

## <a name="code-examples"></a>Esempi di codice

<!--
    Include code snippets and short descriptions for each task you list in the the bulleted list. Briefly explain each operation, but include enough clarity to explain complex or otherwise tricky operations.

    Include links to the service's reference content when introducing a class for the first time
-->

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Riconoscimento modulo per .NET:

* [Autenticare il client](#authenticate-the-client)
* [Eseguire il training di un modello di Riconoscimento modulo personalizzato](#train-a-custom-model)
* [Ottenere un elenco di chiavi estratte](#get-a-list-of-extracted-keys)
* [Analizzare i moduli con un modello personalizzato](#analyze-forms-with-a-custom-model)
* [Ottenere un elenco di modelli personalizzati](#get-a-list-of-custom-models)
* [Eliminare un modello personalizzato](#delete-a-custom-model)

## <a name="define-variables"></a>Definire le variabili

Prima di definire i metodi, aggiungere le definizioni delle variabili seguenti all'inizio della classe **Program**. Sarà necessario inserire manualmente alcune variabili. 

* Per recuperare l'URL di firma di accesso condiviso per i dati di training, aprire Microsoft Azure Storage Explorer, fare clic con il pulsante destro del mouse sul contenitore e scegliere **Ottieni firma di accesso condiviso**. Assicurarsi che le autorizzazioni **Lettura** ed **Elenco** siano selezionate e fare clic su **Crea**. A questo punto, copiare il valore dalla sezione **URL**. Dovrebbe essere in questo formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Se è necessario un modulo di esempio da analizzare, è possibile usare uno dei file includi nella cartella **Test** del [set di dati di esempio](https://go.microsoft.com/fwlink/?linkid=2090451). Questa guida usa moduli PDF.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_variables)]

## <a name="authenticate-the-client"></a>Autenticare il client

Sotto il metodo `Main` definire l'attività a cui si fa riferimento in `Main`. In questo caso si eseguirà l'autenticazione dell'oggetto client usando le variabili di sottoscrizione definite in precedenza. Gli altri metodi verranno definiti in un secondo momento.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_maintask)]

## <a name="train-a-custom-model"></a>Eseguire il training di un modello personalizzato

Il metodo seguente usa l'oggetto client di Riconoscimento modulo per eseguire il training di un nuovo modello di riconoscimento nei documenti archiviati nel contenitore BLOB di Azure. Usa un metodo helper per visualizzare informazioni sul modello appena sottoposto a training (rappresentato da un oggetto [ModelResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelresult?view=azure-dotnet-preview)) e restituisce l'ID del modello.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_train)]

Il metodo helper seguente visualizza informazioni su un modello di Riconoscimento modulo.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displaymodel)]

## <a name="get-a-list-of-extracted-keys"></a>Ottenere un elenco di chiavi estratte

Al termine del training, il modello personalizzato manterrà un elenco di chiavi estratte dai documenti di training. Prevede che i documenti modulo futuri contengano queste chiavi ed estrarrà i valori corrispondenti nell'operazione di analisi. Usare il metodo seguente per recuperare l'elenco di chiavi estratte e stamparlo nella console. Si tratta di un metodo valido per verificare che il processo di training sia stato efficace.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getkeys)]

## <a name="analyze-forms-with-a-custom-model"></a>Analizzare i moduli con un modello personalizzato

Questo metodo usa il client di Riconoscimento modulo e un ID modello per analizzare un documento modulo PDF ed estrarre i dati delle coppie chiave-valore. Usa un metodo helper per visualizzare i risultati (rappresentati da un oggetto [AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)).

> [!NOTE]
> Il metodo seguente analizza un modulo PDF. Per metodi simili che analizzano i moduli JPEG e PNG, vedere il codice di esempio completo in [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_analyzepdf)]

Il metodo helper seguente visualizza informazioni su un'operazione di analisi.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displayanalyze)]

## <a name="get-a-list-of-custom-models"></a>Ottenere un elenco di modelli personalizzati

È possibile restituire un elenco di tutti i modelli sottoposti a training che appartengono all'account, nonché recuperare informazioni relative alla data di creazione. L'elenco di modelli è rappresentato da un oggetto [ModelsResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelsresult?view=azure-dotnet-preview).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getmodellist)]

## <a name="delete-a-custom-model"></a>Eliminare un modello personalizzato

Se si vuole eliminare un modello personalizzato dall'account, usare il metodo seguente:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_deletemodel)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione chiamando il comando `dotnet run` dalla directory dell'applicazione.

```console
dotnet run
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Inoltre, se è stato eseguito il training di un modello personalizzato che si vuole eliminare dall'account, eseguire il metodo illustrato in [Eliminare un modello personalizzato](#delete-a-custom-model).

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata usata la libreria client di Riconoscimento modulo per .NET per eseguire il training di un modello personalizzato e analizzare i moduli. In seguito, è possibile ottenere suggerimenti per creare un set di dati di training migliore e produrre modelli più accurati.

> [!div class="nextstepaction"]
>[Creare un set di dati di training](../build-training-data-set.md)

* [Informazioni su Riconoscimento modulo](../overview.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).
