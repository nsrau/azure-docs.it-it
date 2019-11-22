---
title: 'Guida introduttiva: Libreria client di Visione artificiale per .NET | Microsoft Docs'
description: Introduzione alla libreria client di Visione artificiale per .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: pafarley
ms.openlocfilehash: 45cf376d54ef9f2e77acb2ece9529af640938853
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931767"
---
# <a name="quickstart-computer-vision-client-library-for-net"></a>Guida introduttiva: Libreria client di Visione artificiale per .NET

Introduzione alla libreria client di Visione artificiale per .NET. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. Il servizio Visione artificiale consente di accedere ad algoritmi avanzati per l'elaborazione delle immagini e la restituzione di informazioni.

Usare la libreria client di Visione artificiale per .NET per:

* Analizzare un'immagine per trovare tag, descrizioni di testo, visi, contenuto per adulti e altro ancora.
* Riconoscere il testo stampato e scritto a mano con l'API di lettura batch.

[Documentazione di riferimento](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [Esempi](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* Versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>Configurazione

### <a name="create-a-computer-vision-azure-resource"></a>Creare una risorsa di Visione artificiale in Azure

I Servizi cognitivi di Azure sono rappresentati dalle risorse di Azure a cui si effettua la sottoscrizione. Creare una risorsa per Visione artificiale usando il [portale di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) nel computer locale. È anche possibile:

* Ottenere un [codice di valutazione gratuito](https://azure.microsoft.com/try/cognitive-services/#decision) valido per 7 giorni. Dopo aver eseguito l'iscrizione, sarà disponibile sul [sito Web di Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visualizzare questa risorsa nel [portale di Azure](https://portal.azure.com/).

Dopo aver ottenuto una chiave dalla sottoscrizione di valutazione o dalla risorsa, [creare le variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave e per l'URL dell'endpoint, denominate rispettivamente `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`.

### <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

Creare una nuova applicazione .NET Core nell'ambiente di sviluppo integrato o nell'editor preferito. 

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `computer-vision-quickstart`. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: *Program.cs*.

```console
dotnet new console -n computer-vision-quickstart
```

Spostarsi nella cartella dell'app appena creata. È possibile compilare l'applicazione con il comando seguente:

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

Dalla directory del progetto aprire il file *Program.cs* nell'ambiente di sviluppo integrato o nell'editor preferito. Aggiungere le direttive `using` seguenti:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_using)]

Nella classe **Program** dell'applicazione creare le variabili per l'endpoint e per la chiave di Azure della risorsa.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_vars)]

### <a name="install-the-client-library"></a>Installare la libreria client

Nella directory dell'applicazione installare la libreria client di Visione artificiale per .NET con il comando seguente:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 5.0.0
```

Se si usa l'ambiente di sviluppo integrato di Visual Studio, la libreria client è disponibile come pacchetto NuGet scaricabile.

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità dell'SDK Visione artificiale per .NET.

|NOME|DESCRIZIONE|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) | Questa classe è necessaria per tutte le funzionalità di Visione artificiale. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per eseguire la maggior parte delle operazioni con le immagini.|
|[ComputerVisionClientExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions?view=azure-dotnet)| Questa classe contiene metodi aggiuntivi per **ComputerVisionClient.**|
|[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)| Questa enumerazione definisce i diversi tipi di analisi delle immagini che è possibile eseguire in un'operazione di analisi standard. Specificare un set di valori di VisualFeatureTypes a seconda delle esigenze. |

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Visione artificiale per .NET:

* [Autenticare il client](#authenticate-the-client)
* [Analizzare un'immagine](#analyze-an-image)
* [Leggere il testo stampato e scritto a mano](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticare il client

> [!NOTE]
> Questa guida di avvio rapido presuppone che siano state [create variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave e l'endpoint del servizio Visione artificiale, denominate rispettivamente `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`.

In un nuovo metodo creare un'istanza di un client con l'endpoint e la chiave. Creare un oggetto [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) con la chiave e usarlo con l'endpoint per creare un oggetto [ContentModeratorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_auth)]

È possibile chiamare questo metodo nel metodo `Main`.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_client)]

## <a name="analyze-an-image"></a>Analizzare un'immagine

Il codice seguente definisce un metodo, `AnalyzeImageUrl`, che usa l'oggetto client per analizzare un'immagine remota e stampare i risultati. Il metodo restituisce una descrizione di testo, una categorizzazione, un elenco di tag, i visi rilevati, i flag di contenuto per adulti, i colori principali e il tipo di immagine.

Aggiungere la chiamata al metodo nel metodo `Main`.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyzeinmain)]

### <a name="set-up-test-image"></a>Configurare l'immagine di test

Nella classe **Program** salvare un riferimento all'URL dell'immagine da analizzare.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyze_url)]

> [!NOTE]
> È anche possibile analizzare un'immagine locale. Per gli scenari con immagini locali, vedere il codice di esempio in [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs).

### <a name="specify-visual-features"></a>Specificare le caratteristiche visive

Definire il nuovo metodo per l'analisi delle immagini. Aggiungere il codice seguente, che specifica le caratteristiche visive da estrarre nell'analisi. Per un elenco completo, vedere l'enumerazione [VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_visualfeatures)]

### <a name="analyze"></a>Analizzare

Il metodo **AnalyzeImageAsync** restituisce un oggetto **ImageAnalysis** che contiene tutte le informazioni estratte.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyze_call)]

Le sezioni seguenti illustrano come analizzare queste informazioni in modo dettagliato.

### <a name="get-image-description"></a>Ottenere la descrizione dell'immagine

Il codice seguente ottiene l'elenco di didascalie generate per l'immagine. Per altre informazioni, vedere [Descrivere le immagini](../concept-describing-images.md).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_describe)]

### <a name="get-image-category"></a>Ottenere la categoria dell'immagine

Il codice seguente ottiene la categoria rilevata dell'immagine. Per altre informazioni, vedere [Categorizzare le immagini](../concept-categorizing-images.md).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>Ottenere i tag delle immagini

Il codice seguente ottiene il set di tag rilevati nell'immagine. Per altre informazioni, vedere [Tag di contenuto](../concept-tagging-images.md).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_tags)]

### <a name="detect-objects"></a>Rilevare oggetti

Il codice seguente rileva gli oggetti comuni nell'immagine e li stampa nella console. Per altre informazioni, vedere [Rilevamento di oggetti](../concept-object-detection.md).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_objects)]

### <a name="detect-brands"></a>Rilevare marchi

Il codice seguente rileva i marchi e i logo aziendali nell'immagine e li stampa nella console. Per altre informazioni, vedere [Rilevamento di marchi](../concept-brand-detection.md).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_brands)]

### <a name="detect-faces"></a>Rilevare visi

Il codice seguente restituisce i visi rilevati nell'immagine con le rispettive coordinate del rettangolo e determinati attributi del viso. Per altre informazioni, vedere [Rilevamento viso](../concept-detecting-faces.md).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Rilevare contenuto per adulti, spinto o cruento

Il codice seguente stampa la presenza rilevata di contenuto per adulti nell'immagine. Per altre informazioni, vedere [Contenuto per adulti, spinto o cruento](../concept-detecting-adult-content.md).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Ottenere la combinazione di colori dell'immagine

Il codice seguente stampa gli attributi di colore rilevati nell'immagine, ad esempio i colori dominanti e il colore principale. Per altre informazioni, vedere [Combinazioni di colori](../concept-detecting-color-schemes.md).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Ottenere contenuto specifico del dominio

Visione artificiale può usare modelli speciali per eseguire un'ulteriore analisi delle immagini. Per altre informazioni, vedere [Contenuto specifico del dominio](../concept-detecting-domain-content.md). 

Il codice seguente analizza i dati sulle celebrità rilevate nell'immagine.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_celebs)]

Il codice seguente analizza i dati sui luoghi di interesse rilevati nell'immagine.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Ottenere il tipo di immagine

Il codice seguente stampa informazioni sul tipo di immagine,&mdash;ad esempio se si tratta di ClipArt o di un disegno.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Leggere il testo stampato e scritto a mano

Visione artificiale può leggere il testo visibile di un'immagine e convertirlo in un flusso di caratteri. Il codice di questa sezione definisce un metodo, `ExtractTextUrl`, che usa l'oggetto client per rilevare ed estrarre il testo stampato o scritto a mano nell'immagine.

Aggiungere la chiamata al metodo nel metodo `Main`.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extracttextinmain)]

### <a name="set-up-test-image"></a>Configurare l'immagine di test

Nella classe **Program** salvare un riferimento all'URL dell'immagine da cui estrarre il testo.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extracttext_url)]

> [!NOTE]
> È anche possibile estrarre il testo da un'immagine locale. Per gli scenari con immagini locali, vedere il codice di esempio in [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs).

### <a name="call-the-read-api"></a>Chiamare l'API di lettura

Definire il nuovo metodo per la lettura del testo. Aggiungere il codice seguente, che chiama il metodo **BatchReadFileAsync** per l'immagine specificata. Viene restituito un ID operazione e viene avviato un processo asincrono per leggere il contenuto dell'immagine.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_call)]

### <a name="get-read-results"></a>Ottenere risultati della lettura

Ottenere quindi l'ID operazione restituito dalla chiamata a **BatchReadFileAsync** e usarlo per eseguire una query sul servizio per ottenere i risultati dell'operazione. Il codice seguente controlla l'operazione a intervalli di un secondo fino a quando non vengono restituiti i risultati. Stampa quindi i dati di testo estratti nella console.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_response)]

### <a name="display-read-results"></a>Visualizzare i risultati della lettura

Aggiungere il codice seguente per analizzare e visualizzare i dati di testo recuperati e terminare la definizione del metodo.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_display)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione dalla directory dell'applicazione con il comando `dotnet run`.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[Informazioni di riferimento per l'API Visione artificiale (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)

* [Che cos'è l'API Visione artificiale?](../Home.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs).
