---
title: 'Guida introduttiva: Libreria client di Content Moderator per .NET | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Introduzione alla libreria client di Content Moderator per .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: pafarley
ms.openlocfilehash: cb812a0432e5fdb2828054751ef35f3de82226e7
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539027"
---
# <a name="quickstart-content-moderator-client-library-for-net"></a>Guida introduttiva: Libreria client di Content Moderator per .NET

Introduzione alla libreria client di Content Moderator per .NET. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. Content Moderator è un servizio cognitivo che verifica la presenza di materiale potenzialmente offensivo, rischioso o altrimenti indesiderato in contenuti di testo, immagini e video. Quando tale materiale viene trovato, il servizio applica al contenuto apposite etichette (flag). L'app può quindi gestire il contenuto contrassegnato per garantire la conformità alle normative o gestire un ambiente con le caratteristiche previste per gli utenti.

Usare la libreria client di Content Moderator per .NET per:

* [Moderare il testo](#moderate-text)
* [Moderare le immagini](#moderate-images)
* [Creare una revisione](#create-a-review)

[Documentazione di riferimento](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) | [Esempi](https://docs.microsoft.com/azure/cognitive-services/content-moderator/samples-dotnet)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* Versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>Configurazione

### <a name="create-a-content-moderator-azure-resource"></a>Creare una risorsa di Azure per Content Moderator

I Servizi cognitivi di Azure sono rappresentati dalle risorse di Azure a cui si effettua la sottoscrizione. Creare una risorsa per Content Moderator usando il [portale di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) nel computer locale. È anche possibile:

* Ottenere un [codice di valutazione gratuito](https://azure.microsoft.com/try/cognitive-services/#decision) valido per 7 giorni. Dopo aver eseguito l'iscrizione, sarà disponibile sul [sito Web di Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visualizzare questa risorsa nel [portale di Azure](https://portal.azure.com/)

Dopo aver ottenuto una chiave dalla sottoscrizione di valutazione o dalla risorsa, [creare le variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave e per l'URL dell'endpoint, denominate rispettivamente `CONTENT_MODERATOR_SUBSCRIPTION_KEY` e `CONTENT_MODERATOR_ENDPOINT`.

### <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

Creare una nuova applicazione .NET Core nell'ambiente di sviluppo integrato o nell'editor di testo preferito. 

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `content-moderator-quickstart`. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: *Program.cs*.

```console
dotnet new console -n content-moderator-quickstart
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

Dalla directory del progetto aprire il file *Program.cs* nell'ambiente di sviluppo integrato o nell'editor preferito. Aggiungere le istruzioni `using` seguenti:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_using)]

Nella classe **Program** creare le variabili per la località dell'endpoint della risorsa e la chiave come variabile di ambiente.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_creds)]

> [!NOTE]
> Se le variabili di ambiente sono state create dopo aver avviato l'applicazione, per accedervi sarà necessario chiudere e riaprire l'editor, l'IDE o la shell che la eseguono.

### <a name="install-the-client-library"></a>Installare la libreria client

All'interno della directory dell'applicazione, installare la libreria client di Content Moderator per .NET con il comando seguente:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

Se si usa l'ambiente di sviluppo integrato di Visual Studio, la libreria client è disponibile come pacchetto NuGet scaricabile.

## <a name="object-model"></a>Modello a oggetti

Le classi seguenti gestiscono alcune delle principali funzionalità di Content Moderator .NET SDK.

|NOME|DESCRIZIONE|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|Questa classe è necessaria per tutte le funzionalità di Content Moderator. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per produrre istanze di altre classi.|
|[ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|Questa classe fornisce la funzionalità per analizzare le immagini e individuare contenuti per adulti, informazioni personali o visi umani.|
|[TextModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|Questa classe fornisce la funzionalità per analizzare il testo e individuare lingua, volgarità, errori e informazioni personali.|
|[Revisioni](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|Questa classe fornisce la funzionalità delle API di revisione, inclusi i metodi per la creazione di processi, flussi di lavoro personalizzati e revisioni umane.|

## <a name="code-examples"></a>Esempi di codice


Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Content Moderator per .NET:

* [Autenticare il client](#authenticate-the-client)
* [Moderare il testo](#moderate-text)
* [Moderare le immagini](#moderate-images)
* [Creare una revisione](#create-a-review)

## <a name="authenticate-the-client"></a>Autenticare il client

In un nuovo metodo creare un'istanza di oggetti client con l'endpoint e la chiave. Non è necessario un client diverso per ogni scenario, ma può contribuire a mantenere il codice organizzato.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_client)]

## <a name="moderate-text"></a>Moderare il testo

Il codice seguente usa un client di Content Moderator per analizzare un corpo di testo e stampare i risultati nella console. Nella radice della classe **Program** definire i file di input e di output:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_text_vars)]

Nella radice del progetto aggiungere un file *TextFile.txt*. Aggiungere un testo personalizzato in questo file oppure usare il testo di esempio seguente:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

Aggiungere la chiamata seguente al metodo `Main`:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod_call)]

Definire quindi il metodo di moderazione del testo in un punto qualsiasi della classe **Program**:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Moderare le immagini

Il codice seguente usa un client di Content Moderator, insieme a un oggetto [ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet), per analizzare le immagini remote e verificare la presenza di contenuti per adulti e spinti.

> [!NOTE]
> È anche possibile analizzare il contenuto di un'immagine locale. Vedere la [documentazione di riferimento](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) per i metodi e le operazioni che funzionano con le immagini locali.

### <a name="get-sample-images"></a>Recupera immagini di esempio

Definire i file di input e output:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_image_vars)]

Creare quindi il file di input, *ImageFiles.txt* nella radice del progetto. In questo file aggiungere gli URL delle immagini da analizzare, inserendo un URL in ogni riga. È possibile usare le immagini di esempio seguenti:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

Passare i file di input e di output nella chiamata seguente al metodo `Main`. Questo metodo verrà definito in un passaggio successivo.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod_call)]

### <a name="define-helper-class"></a>Definire la classe helper

Aggiungere la definizione di classe seguente all'interno della classe **Program**. Questa classe interna gestirà i risultati della moderazione delle immagini.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>Definire il metodo di moderazione delle immagini

Il metodo seguente consente di scorrere gli URL delle immagini in un file di testo, di creare un'istanza di **EvaluationData** e di analizzare l'immagine per rilevare la presenza di contenuto per adulti o spinti, testo e visi umani. Aggiunge quindi l'istanza finale di **EvaluationData** a un elenco e scrive l'elenco completo dei dati restituiti nella console.

#### <a name="iterate-through-image-urls"></a>Scorrere gli URL delle immagini

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Analizzare il contenuto

Per altre informazioni sugli attributi dell'immagine verificati da Content Moderator, vedere la [guida concettuale alla moderazione di immagini](./image-moderation-api.md).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Scrivere i risultati della moderazione in un file

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Creare una revisione

È possibile usare Content Moderator .NET SDK per inserire contenuti nello [strumento di revisione](https://contentmoderator.cognitive.microsoft.com), in modo che i moderatori possano esaminarlo. Per altre informazioni sullo strumento di revisione, vedere la [Guida concettuale dello strumento di revisione](./review-tool-user-guide/human-in-the-loop.md).

Il metodo di questa sezione usa la classe [Reviews](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet) per creare una revisione, recuperare il relativo ID e controllarne i dettagli dopo aver ricevuto input degli utenti tramite il portale Web dello strumento di revisione. Registra tutte queste informazioni in un file di testo di output. Chiamare il metodo dal metodo `Main`:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_call)]

### <a name="get-sample-images"></a>Recupera immagini di esempio

Dichiarare la matrice seguente nella radice della classe **Program**. Questa variabile fa riferimento a un'immagine di esempio da usare per creare la revisione.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Ottenere le credenziali per la revisione

Accedere allo [strumento di revisione](https://contentmoderator.cognitive.microsoft.com) e recuperare il nome del proprio team. Quindi assegnarlo alla variabile appropriata nella classe **Program**. Facoltativamente, è possibile configurare un endpoint di callback per ricevere aggiornamenti sull'attività della revisione.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Definire la classe helper

Aggiungere la definizione di classe seguente all'interno della classe **Program**. Questa classe verrà usata per rappresentare una singola istanza di revisione inviata allo strumento di revisione.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Definire il metodo helper

Aggiungere il metodo seguente alla classe **Program**. Questo metodo scriverà i risultati delle query di revisione nel file di testo di output.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>Definire il metodo di creazione della revisione

A questo punto si è pronti per definire il metodo che gestirà la creazione della revisione e l'esecuzione di query. Aggiungere un nuovo metodo, **CreateReviews** e definire le variabili locali seguenti.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Inviare le revisioni allo strumento di revisione

Aggiungere quindi il codice seguente per scorrere le immagini di esempio specificate, aggiungere metadati e inviarle allo strumento di revisione in un singolo batch. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_create)]

L'oggetto restituito dalla chiamata API conterrà valori ID univoci per ogni immagine caricata. Il codice seguente analizza questi ID e li usa per eseguire query in Content Moderator per lo stato di ogni immagine nel batch.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Ottenere i dettagli della revisione

Il codice seguente fa in modo che il programma attenda l'input dell'utente. Quando si arriva a questo passaggio in fase di esecuzione, è possibile passare allo [strumento di revisione](https://contentmoderator.cognitive.microsoft.com) manualmente, verificare che l'immagine di esempio sia stata caricata e interagire con essa. Per informazioni su come interagire con una revisione, vedere la [Guida pratica per le revisioni](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images). Al termine, è possibile premere un tasto qualsiasi per continuare con il programma e recuperare i risultati del processo di revisione.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_results)]

Se in questo scenario è stato usato un endpoint di callback, si dovrebbe ricevere un evento nel formato seguente:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione dalla directory dell'applicazione con il comando `dotnet run`.

```dotnet
dotnet run 
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi


In questo argomento di avvio rapido si è appreso come usare la libreria .NET di Content Moderator per eseguire attività di moderazione. Per altre informazioni sulla moderazione di immagini o di altri contenuti multimediali, è possibile leggere una guida concettuale.

> [!div class="nextstepaction"]
> [Concetti di moderazione delle immagini](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Che cos'è Azure Content Moderator?](./overview.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ContentModerator/Program.cs).
