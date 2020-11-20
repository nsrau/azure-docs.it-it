---
title: 'Avvio rapido: Libreria client del servizio Viso per .NET'
description: Usare la libreria client di Viso per .NET per rilevare visi, trovare volti simili (ricerca di volti per immagine), identificare i visi (ricerca basata su riconoscimento facciale) ed eseguire la migrazione dei dati sui visi.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: 1299cbf1b837315a1a95c8a2ec2e4ed0706d959c
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94816778"
---
Introduzione al riconoscimento facciale con la libreria client di Viso per .NET. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. Il servizio Viso fornisce l'accesso ad algoritmi avanzati per il rilevamento e il riconoscimento dei visi umani nelle immagini.

Usare la libreria client dell'API Viso per .NET per:

* [Rilevare i visi in un'immagine](#detect-faces-in-an-image)
* [Individuare visi simili](#find-similar-faces)
* [Creare un gruppo di persone](#create-a-person-group)
* [Identificare un viso](#identify-a-face)

[Documentazione di riferimento](/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1) | [Esempi](/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Prerequisiti


* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* [IDE di Visual Studio](https://visualstudio.microsoft.com/vs/) o la versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="creare una risorsa Viso"  target="_blank">creare una risorsa Viso <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Viso. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

#### <a name="visual-studio-ide"></a>[IDE di Visual Studio](#tab/visual-studio)

Creare un'applicazione .NET Core con Visual Studio. 

### <a name="install-the-client-library"></a>Installare la libreria client 

Dopo aver creato un nuovo progetto, installare la libreria client facendo clic con il pulsante destro del mouse sulla soluzione del progetto in **Esplora soluzioni** e scegliendo **Gestisci pacchetti NuGet**. Nella finestra di dialogo Gestione pacchetti visualizzata selezionare **Sfoglia**, **Includi versione preliminare** e cercare `Microsoft.Azure.CognitiveServices.Vision.Face`. Selezionare la versione `2.6.0-preview.1`, quindi **Installa**. 

#### <a name="cli"></a>[CLI](#tab/cli)

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `face-quickstart`. Questo comando crea un semplice progetto C# "Hello World" con un unico file di origine: *program.cs*. 

```console
dotnet new console -n face-quickstart
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

### <a name="install-the-client-library"></a>Installare la libreria client 

Nella directory dell'applicazione installare la libreria client dell'API Viso per .NET con il comando seguente:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.6.0-preview.1
```

---

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs), che contiene gli esempi di codice di questo argomento.


Dalla directory del progetto aprire il file *program.cs* e aggiungere le direttive `using` seguenti:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

Nella classe **Program** dell'applicazione creare le variabili per l'endpoint e la chiave della risorsa.


> [!IMPORTANT]
> Accedere al portale di Azure. Se la risorsa [Nome prodotto] creata nella sezione **Prerequisiti** è stata distribuita correttamente, fare clic sul pulsante **Vai alla risorsa** in **Passaggi successivi**. La chiave e l'endpoint saranno disponibili nella pagina **Chiavi ed endpoint** della risorsa in **Gestione risorse**. 
>
> Al termine, ricordarsi di rimuovere la chiave dal codice e non renderlo mai pubblico. Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Per altre informazioni, vedere l'articolo sulla [sicurezza](../../../cognitive-services-security.md) di Servizi cognitivi.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_creds)]

Nel metodo **Main** dell'applicazione aggiungere le chiamate per i metodi usati in questa guida di avvio rapido. Queste verranno implementate in un secondo momento.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_maincalls)]

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client dell'API Viso per .NET:

|Nome|Descrizione|
|---|---|
|[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | Questa classe rappresenta l'autorizzazione per l'utilizzo del servizio Viso ed è necessaria per tutte le funzionalità del servizio. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per produrre istanze di altre classi. |
|[FaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|Questa classe gestisce le attività di rilevamento e riconoscimento di base che è possibile eseguire con i visi umani. |
|[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|Questa classe rappresenta tutti i dati rilevati da un singolo viso in un'immagine. È possibile usarla per recuperare informazioni dettagliate sul viso.|
|[FaceListOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|Questa classe gestisce i costrutti **FaceList** archiviati nel cloud, che archiviano un set assortito di visi. |
|[PersonGroupPersonExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| Questa classe gestisce i costrutti **Person** archiviati nel cloud, che archiviano un set di visi che appartengono a una singola persona.|
|[PersonGroupOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| Questa classe gestisce i costrutti **PersonGroup** archiviati nel cloud, che archiviano un set di oggetti **Person** assortiti. |

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client dell'API Viso per .NET:

* [Autenticare il client](#authenticate-the-client)
* [Rilevare i visi in un'immagine](#detect-faces-in-an-image)
* [Individuare visi simili](#find-similar-faces)
* [Creare un gruppo di persone](#create-a-person-group)
* [Identificare un viso](#identify-a-face)

## <a name="authenticate-the-client"></a>Autenticare il client

In un nuovo metodo creare un'istanza di un client con l'endpoint e la chiave. Creare un oggetto **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials?view=azure-dotnet)** con la chiave e usarlo con l'endpoint per creare un oggetto **[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

### <a name="declare-helper-fields"></a>Dichiarare i campi dell'helper

I campi seguenti sono necessari per diverse operazioni di Viso che verranno aggiunte in un secondo momento. Nella radice della classe **Program** definire la stringa di URL seguente. Questo URL punta a una cartella di immagini di esempio.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

Nel metodo **Main** definire le stringhe in modo che puntino ai diversi tipi di modelli di riconoscimento. In un secondo momento, sarà possibile specificare il modello di riconoscimento da usare per il rilevamento volto. Per informazioni su queste opzioni, vedere [Specificare un modello di riconoscimento volto](../../Face-API-How-to-Topics/specify-recognition-model.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>Rilevare i visi in un'immagine

### <a name="get-detected-face-objects"></a>Ottenere gli oggetti viso rilevati

Creare un nuovo metodo per rilevare i visi. Il metodo `DetectFaceExtract` elabora tre delle immagini all'URL specificato e crea un elenco di oggetti **[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** nella memoria del programma. L'elenco di valori **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** specifica quali caratteristiche estrarre. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

> [!TIP]
> È anche possibile rilevare i visi in un'immagine locale. Vedere i metodi [FaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ifaceoperations?view=azure-dotnet) come **DetectWithStreamAsync**.

### <a name="display-detected-face-data"></a>Visualizzare i dati dei visi rilevati

Il resto del metodo `DetectFaceExtract` analizza e stampa i dati di attributo per ogni viso rilevato. Ogni attributo deve essere specificato separatamente nella chiamata all'API di rilevamento viso originale (nell'elenco **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** ). Il codice seguente elabora ogni attributo, ma è probabile che sia necessario usarne solo uno o alcuni.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Individuazione di visi simili

Il codice seguente confronta un singolo viso rilevato (origine) con un set di altri visi (destinazione) per trovare corrispondenze (ricerca di volti per immagine). Quando trova una corrispondenza, visualizza l'ID del viso corrispondente nella console.

### <a name="detect-faces-for-comparison"></a>Rilevare visi per il confronto

Prima di tutto, definire un secondo metodo di rilevamento volto. Per poter confrontare i visi occorre prima rilevarli nelle immagini e questo metodo di rilevamento è ottimizzato per le operazioni di confronto. Non estrae gli attributi dettagliati dei visi come nella sezione precedente e usa un modello di riconoscimento diverso.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Trovare le corrispondenze

Il metodo seguente rileva i visi in un set di immagini di destinazione e in un'unica immagine di origine, quindi li confronta e trova tutte le immagini di destinazione simili all'immagine di origine.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Visualizzare le corrispondenze

Il codice seguente visualizza i dettagli delle corrispondenze nella console:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar_print)]

## <a name="identify-a-face"></a>Identificare un viso

L'operazione di identificazione acquisisce un'immagine di una persona (o di più persone) e cerca di individuare l'identità di ogni viso nell'immagine (ricerca basata su riconoscimento facciale). Confronta ogni viso rilevato con un **PersonGroup**, un database di oggetti **Person** diversi le cui caratteristiche del viso sono note. Per eseguire l'operazione di identificazione, è prima di tutto necessario creare ed eseguire il training di un oggetto **PersonGroup**

### <a name="create-a-person-group"></a>Creare un gruppo di persone

Il codice seguente crea un **PersonGroup** con sei diversi oggetti **Person**. Associa ogni oggetto **Person** a un set di immagini di esempio e quindi esegue il training per riconoscere ogni persona in base alle caratteristiche del volto. Gli oggetti **Person** e **PersonGroup** vengono usati nelle operazioni di verifica, identificazione e raggruppamento.

Dichiarare una variabile di stringa alla radice della classe per rappresentare l'ID dell'oggetto **PersonGroup** che verrà creato.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

Aggiungere il codice seguente in un nuovo metodo. Questo metodo effettuerà l'operazione di identificazione. Il primo blocco di codice associa i nomi delle persone alle immagini di esempio.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

Si noti che questo codice definisce una variabile `sourceImageFileName`. Questa variabile corrisponde all'immagine di origine, ossia l'immagine che contiene le persone da identificare.

Aggiungere quindi il codice seguente per creare un oggetto **Person** per ogni persona nel dizionario e aggiungere i dati sul viso dalle immagini appropriate. Ogni oggetto **Person** è associato allo stesso **PersonGroup** tramite la propria stringa ID univoca. Ricordare di passare le variabili `client`, `url` e `RECOGNITION_MODEL1` nel metodo.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

> [!TIP]
> È anche possibile creare un **PersonGroup** dalle immagini locali. Vedere i metodi [IPersonGroupPerson](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ipersongroupperson?view=azure-dotnet), ad esempio **AddFaceFromStreamAsync**.

### <a name="train-the-persongroup"></a>Eseguire il training del PersonGroup

Dopo aver estratto i dati sui visi dalle immagini e averli ordinati in oggetti **Person** diversi, è necessario eseguire il training di **PersonGroup** per identificare le caratteristiche visive associate a ogni oggetto **Person**. Il codice seguente chiama il metodo **train** asincrono ed esegue il polling dei risultati, visualizzando lo stato nella console.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

> [!TIP]
> L'API Viso esegue una serie di modelli predefiniti che sono per natura statici (le prestazioni del modello non regrediscono né migliorano durante l'esecuzione del servizio). I risultati generati dal modello potrebbero cambiare se Microsoft ne aggiorna il back-end senza eseguire la migrazione a una versione interamente nuova. Per trarre vantaggio da una versione più recente di un modello, è possibile ripetere il training di **PersonGroup** specificando il nuovo modello come parametro con le stesse immagini di registrazione.

Questo gruppo **Person** e gli oggetti **Person** associati sono ora pronti per essere usati nelle operazioni di verifica, identificazione o raggruppamento.

### <a name="identify-faces"></a>Identificare visi

Il codice seguente crea un elenco di tutti i visi rilevati nell'immagine di origine. Sono i visi che verranno identificati in base al **PersonGroup**.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

Il frammento di codice successivo chiama l'operazione **IdentifyAsync** e visualizza i risultati nella console. In questo caso, il servizio tenta di trovare una corrispondenza tra ogni viso nell'immagine di origine e un oggetto **Person** nel **PersonGroup**. In questo modo si chiude il metodo di identificazione.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

## <a name="run-the-application"></a>Eseguire l'applicazione

#### <a name="visual-studio-ide"></a>[IDE di Visual Studio](#tab/visual-studio)

Eseguire l'applicazione facendo clic sul pulsante **Debug** nella parte superiore della finestra dell'ambiente di sviluppo integrato.

#### <a name="cli"></a>[CLI](#tab/cli)

Eseguire l'applicazione dalla directory dell'applicazione con il comando `dotnet run`.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Se è stato creato un oggetto **PersonGroup** in questa guida di avvio rapido e si vuole eliminarlo, eseguire il codice seguente nel programma:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

Definire il metodo di eliminazione con il codice seguente:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come usare la libreria client di Viso per .NET per eseguire attività di riconoscimento facciale di base. Successivamente, esplorare la documentazione di riferimento per altre informazioni sulla libreria.

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API Viso (.NET)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [Che cos'è il servizio Viso?](../../overview.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs).