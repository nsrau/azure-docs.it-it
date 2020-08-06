---
title: 'Avvio rapido: Libreria client di QnA Maker per .NET'
description: Questo argomento di avvio rapido illustra come usare la libreria client di QnA Maker per .NET. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.  QnA Maker consente di sviluppare un servizio di domande e risposte a partire dal contenuto semistrutturato, ad esempio documenti con domande frequenti, URL e manuali di prodotti.
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: 0dfabfa1357543e4011412c0a9f63061e8856019
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87507427"
---
Usare la libreria client di QnA Maker per .NET per:

 * Creare una knowledge base
 * Aggiornare una knowledge base
 * Pubblicare una knowledge base
 * Ottenere la chiave dell'endpoint di runtime di previsione
 * Attendere un'attività a esecuzione prolungata
 * Scaricare una knowledge base
 * Ottenere la risposta
 * Eliminare una knowledge base

[Documentazione di riferimento](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/) | [Esempi C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* [IDE di Visual Studio](https://visualstudio.microsoft.com/vs/) o la versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Dopo aver creato la sottoscrizione di Azure, creare una [risorsa di QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) nel portale di Azure per ottenere la chiave di creazione e il nome della risorsa. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e il nome della risorsa creata sono necessari per connettere l'applicazione all'API QnA Maker. La chiave e il nome della risorsa verranno incollati nel codice seguente più avanti in questo argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

#### <a name="visual-studio-ide"></a>[IDE di Visual Studio](#tab/visual-studio)

Usando Visual Studio creare un'applicazione .NET Core e installare la libreria client facendo clic con il pulsante destro del mouse sulla soluzione in **Esplora soluzioni** e scegliendo **Gestisci pacchetti NuGet**. Nella finestra di dialogo Gestione pacchetti visualizzata selezionare **Sfoglia**, **Includi versione preliminare** e cercare `Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker`. Selezionare la versione `2.0.0-preview.1`, quindi **Installa**.

#### <a name="cli"></a>[CLI](#tab/cli)

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `qna-maker-quickstart`. Questo comando crea un semplice progetto C# "Hello World" con un unico file di origine: *program.cs*.

```console
dotnet new console -n qna-maker-quickstart
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

Nella directory dell'applicazione installare la libreria client di QnA Maker per .NET con il comando seguente:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 2.0.0-preview.1
```


---

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs), che contiene gli esempi di codice di questo argomento.

Dalla directory del progetto aprire il file *program.cs* e aggiungere le direttive `using` seguenti:

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies&highlight=1-2)]

Nel metodo `Main` dell'applicazione aggiungere le variabili e il codice, come illustrato nella sezione seguente, per usare le attività comuni di questo argomento di avvio rapido.

> [!IMPORTANT]
> Passare al portale di Azure e individuare la chiave e l'endpoint per la risorsa QnA Maker creata nei prerequisiti. Si trovano nella pagina **Chiave ed endpoint** della risorsa, in **Gestione risorse**.
> Per creare la knowledge base, è necessaria l'intera chiave. È necessario solo il nome della risorsa dall'endpoint. Il formato è `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Al termine, ricordarsi di rimuovere la chiave dal codice e non renderlo mai pubblico. Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Ad esempio, [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) fornisce una risorsa di archiviazione sicura per le chiavi.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]


## <a name="object-models"></a>Modelli a oggetti

QnA Maker usa due modelli a oggetti diversi:
* **[QnAMakerClient](#qnamakerclient-object-model)** è l'oggetto per creare, gestire, pubblicare e scaricare la knowledge base.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** è l'oggetto per eseguire una query sulla knowledge base con l'API GenerateAnswer e inviare nuove domande suggerite usando l'API Train (come parte dell'[apprendimento attivo](../concepts/active-learning-suggestions.md)).

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Modello a oggetti QnAMakerClient

Il client di creazione di QnA Maker è un oggetto [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) che esegue l'autenticazione ad Azure con Microsoft.Rest.ServiceClientCredentials, che contiene la chiave.

Dopo la creazione del client, usare la proprietà [Knowledgebase](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) per creare, gestire e pubblicare la knowledge base.

Gestire la knowledge base inviando un oggetto JSON. Per le operazioni immediate, un metodo restituisce in genere un oggetto JSON che indica lo stato. Per le operazioni a esecuzione prolungata, la risposta è l'ID operazione. Chiamare il metodo [client.Operations.GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) con l'ID operazione per determinare lo [stato della richiesta](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet).

### <a name="qnamakerruntimeclient-object-model"></a>Modello a oggetti QnAMakerRuntimeClient

Il client di previsione di QnA Maker è un oggetto [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) che esegue l'autenticazione ad Azure con Microsoft.Rest.ServiceClientCredentials, che contiene la chiave di runtime di previsione, restituita dalla chiamata del client di creazione `client.EndpointKeys.GetKeys` dopo la pubblicazione della knowledge base.

Usare il metodo [GenerateAnswer](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions) per ottenere una risposta dal runtime di query.

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le operazioni seguenti con la libreria client di QnA Maker per .NET:

* [Autenticare il client di creazione](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Creare una knowledge base](#create-a-knowledge-base)
* [Aggiornare una knowledge base](#update-a-knowledge-base)
* [Scaricare una knowledge base](#download-a-knowledge-base)
* [Pubblicare una knowledge base](#publish-a-knowledge-base)
* [Eliminare una knowledge base](#delete-a-knowledge-base)
* [Ottenere una chiave di runtime di query](#get-query-runtime-key)
* [Ottenere lo stato di un'operazione](#get-status-of-an-operation)
* [Autenticare il client di runtime di query](#authenticate-the-runtime-for-generating-an-answer)
* [Generare una risposta dalla knowledge base](#generate-an-answer-from-the-knowledge-base)



## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autenticare il client per la creazione della knowledge base

Creare un'istanza di un oggetto client con la chiave e usarla con la risorsa per creare l'endpoint e quindi creare un oggetto [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) con l'endpoint e la chiave. Creare un oggetto [ServicePrincipalCredentials](https://docs.microsoft.com/dotnet/api/microsoft.rest.serviceclientcredentials?view=azure-dotnet).

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>Creare una knowledge base

In una knowledge base vengono archiviate le coppie di domande e risposte per l'oggetto [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) da tre origini:

* Per **contenuto editoriale**, usare l'oggetto [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet).
    * Per usare i metadati e le richieste di completamento, usare il contesto editoriale, perché questi dati vengono aggiunti a livello di singola coppia di domande e risposte.
* Per i **file**, usare l' oggetto [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet). FileDTO include il nome file e l'URL pubblico per raggiungere il file.
* Per **URL**, usare un elenco di stringhe per rappresentare gli URL disponibili pubblicamente.

Il passaggio di creazione include anche le proprietà della knowledge base:
* `defaultAnswerUsedForExtraction`: il risultato restituito quando non si trova nessuna risposta
* `enableHierarchicalExtraction`: crea automaticamente relazioni di richieste tra coppie di domande e risposte estratte
* `language`: quando si crea la prima knowledge base di una risorsa, imposta la lingua da usare nell'indice di Ricerca di Azure.

Chiamare il metodo [CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet), quindi passare l'ID operazione restituito al metodo [MonitorOperation](#get-status-of-an-operation) per eseguire il polling dello stato.

La riga finale del codice seguente restituisce l'ID knowledge base dalla risposta di MonitorOperation.

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod&highlight=31)]

Per creare correttamente una knowledge base, assicurarsi di includere la funzione [`MonitorOperation`](#get-status-of-an-operation), a cui si fa riferimento nel codice precedente.

## <a name="update-a-knowledge-base"></a>Aggiornare una knowledge base

È possibile aggiornare una knowledge base passando l'ID knowledge base e un [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) contenente gli oggetti DTO per l'[aggiunta](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), l'[aggiornamento](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet) e l'[eliminazione](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) al metodo [UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet). Usare il metodo [MonitorOperation](#get-status-of-an-operation) per determinare se l'aggiornamento è riuscito.

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod&highlight=8)]

Per aggiornare correttamente una knowledge base, assicurarsi di includere la funzione [`MonitorOperation`](#get-status-of-an-operation), a cui si fa riferimento nel codice precedente.

## <a name="download-a-knowledge-base"></a>Scaricare una knowledge base

Usare il metodo [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) per scaricare il database come un elenco di [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). Questa operazione _non_ equivale all'esportazione dalla pagina **Settings** (Impostazioni) del portale di QnA Maker, perché il risultato di questo metodo non è un file.

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB&highlight=3)]

## <a name="publish-a-knowledge-base"></a>Pubblicare una knowledge base

Pubblicare la knowledge base con il metodo [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet). In questo modo, il modello corrente salvato e sottoposto a training a cui fa riferimento l'ID knowledge base viene pubblicato in un endpoint. Questo passaggio è necessario per eseguire query sulla knowledge base.

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB&highlight=3)]



## <a name="get-query-runtime-key"></a>Ottenere una chiave di runtime di query

Dopo la pubblicazione di una knowledge base, è necessaria la chiave di runtime di query per eseguire una query sul runtime. Non è la stessa chiave usata per creare l'oggetto client originale.

Usare il metodo [EndpointKeys](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) per ottenere la classe [EndpointKeysDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto?view=azure-dotnet).

Usare una delle proprietà chiave restituite nell'oggetto per eseguire una query sulla knowledge base.

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey&highlight=3)]

Una chiave di runtime è necessaria per eseguire query sulla knowledge base.

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autenticare il runtime per la generazione di una risposta

Creare un oggetto [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) per eseguire una query sulla knowledge base e generare una risposta o eseguire il training da apprendimento attivo.

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

Usare QnAMakerRuntimeClient per:
* Ottenere una risposta dalla knowledge base
* Inviare nuove domande suggerite alla knowledge base per l'[apprendimento attivo](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Generare una risposta dalla knowledge base

Generare una risposta da una knowledge base pubblicata usando il metodo [RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase).[GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet). Questo metodo accetta l'ID knowledge base e [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet). Accedere ad altre proprietà di QueryDTO, ad esempio [Top](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) e [Context](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet) da usare nel chatbot.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer&highlight=3)]

Si tratta di un semplice esempio di esecuzione di query sulla knowledge base. Per informazioni sugli scenari di query avanzati, vedere [altri esempi di query](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Eliminare una knowledge base

Eliminare la knowledge base usando il metodo [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) con un parametro dell'ID knowledge base.

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB&highlight=3)]

## <a name="get-status-of-an-operation"></a>Ottenere lo stato di un'operazione

Alcuni metodi, ad esempio per la creazione e l'aggiornamento, possono richiedere una quantità di tempo tale che invece di attendere il completamento del processo viene restituita un'[operazione](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet). Usare il relativo [ID operazione](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) per eseguire il polling (con logica di ripetizione dei tentativi) e determinare lo stato del metodo originale.

Il _ciclo_ e _Task.Delay_ nel blocco di codice seguente vengono usati per simulare la logica di ripetizione dei tentativi. Dovranno essere sostituiti con la propria logica di ripetizione dei tentativi.

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `dotnet run` dalla directory dell'applicazione.

```dotnetcli
dotnet run
```

Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart).
