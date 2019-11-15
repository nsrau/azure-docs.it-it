---
title: "Guida introduttiva: Libreria client dell'API Viso per .NET | Microsoft Docs"
description: Introduzione alla libreria client dell'API Viso per .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/20/2019
ms.author: pafarley
ms.openlocfilehash: 1848f7230ed189f139a223020f08db150295132d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647478"
---
# <a name="quickstart-face-client-library-for-net"></a>Guida introduttiva: Libreria client dell'API Viso per .NET

Introduzione alla libreria client dell'API Viso per .NET. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. Il servizio API Viso fornisce l'accesso ad algoritmi avanzati per il rilevamento e il riconoscimento dei visi umani nelle immagini.

Usare la libreria client dell'API Viso per .NET per:

* [Autenticare il client](#authenticate-the-client)
* [Rilevare i visi in un'immagine](#detect-faces-in-an-image)
* [Individuare visi simili](#find-similar-faces)
* [Creare ed eseguire il training di un gruppo di persone](#create-and-train-a-person-group)
* [Identificare un viso](#identify-a-face)
* [Creare uno snapshot per la migrazione dei dati](#take-a-snapshot-for-data-migration)

[Documentazione di riferimento](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1) | [Esempi](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* Versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>Configurazione

### <a name="create-a-face-azure-resource"></a>Creare una risorsa di Azure Viso

I Servizi cognitivi di Azure sono rappresentati dalle risorse di Azure a cui si effettua la sottoscrizione. Creare una risorsa Viso usando il [portale di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) nel computer locale. È anche possibile:

* Ottenere un [codice di valutazione gratuito](https://azure.microsoft.com/try/cognitive-services/#decision) valido per 7 giorni. Dopo aver eseguito l'iscrizione, sarà disponibile sul [sito Web di Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visualizzare questa risorsa nel [portale di Azure](https://portal.azure.com/).

Dopo aver ottenuto una chiave dalla sottoscrizione di valutazione o dalla risorsa, [creare una variabile di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave e per l'URL dell'endpoint, denominate rispettivamente `FACE_SUBSCRIPTION_KEY` e `FACE_ENDPOINT`.

### <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

Creare una nuova applicazione .NET Core nell'ambiente di sviluppo integrato o nell'editor preferito. 

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `face-quickstart`. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: *Program.cs*. 

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

Dalla directory del progetto aprire il file *Program.cs* nell'ambiente di sviluppo integrato o nell'editor preferito. Aggiungere le direttive `using` seguenti:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_using)]

Nel metodo `Main` dell'applicazione creare variabili per l'endpoint e la chiave di Azure della risorsa.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>Installare la libreria client

Nella directory dell'applicazione installare la libreria client dell'API Viso per .NET con il comando seguente:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.5.0-preview.1
```

Se si usa l'ambiente di sviluppo integrato di Visual Studio, la libreria client è disponibile come pacchetto NuGet scaricabile.

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità di Face .NET SDK:

|NOME|DESCRIZIONE|
|---|---|
|[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | Questa classe rappresenta l'autorizzazione per l'utilizzo del servizio Viso ed è necessaria per tutte le funzionalità del servizio. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per produrre istanze di altre classi. |
|[FaceOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|Questa classe gestisce le attività di rilevamento e riconoscimento di base che è possibile eseguire con i visi umani. |
|[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|Questa classe rappresenta tutti i dati rilevati da un singolo viso in un'immagine. È possibile usarla per recuperare informazioni dettagliate sul viso.|
|[FaceListOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|Questa classe gestisce i costrutti **FaceList** archiviati nel cloud, che archiviano un set assortito di visi. |
|[PersonGroupPersonExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| Questa classe gestisce i costrutti **Person** archiviati nel cloud, che archiviano un set di visi che appartengono a una singola persona.|
|[PersonGroupOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| Questa classe gestisce i costrutti **PersonGroup** archiviati nel cloud, che archiviano un set di oggetti **Person** assortiti. |
|[ShapshotOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|Questa classe gestisce la funzionalità Snapshot, che può essere usata per salvare temporaneamente tutti i dati sui visi basati sul cloud ed eseguirne la migrazione in una nuova sottoscrizione di Azure. |

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client dell'API Viso per .NET:

* [Autenticare il client](#authenticate-the-client)
* [Rilevare i visi in un'immagine](#detect-faces-in-an-image)
* [Individuare visi simili](#find-similar-faces)
* [Creare ed eseguire il training di un gruppo di persone](#create-and-train-a-person-group)
* [Identificare un viso](#identify-a-face)
* [Creare uno snapshot per la migrazione dei dati](#take-a-snapshot-for-data-migration)


## <a name="authenticate-the-client"></a>Autenticare il client

> [!NOTE]
> Questo argomento di avvio rapido presuppone che siano state [create variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave e l'endpoint del servizio Viso, denominate rispettivamente `FACE_SUBSCRIPTION_KEY` e `FACE_ENDPOINT`.

In un nuovo metodo creare un'istanza di un client con l'endpoint e la chiave. Creare un oggetto [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) con la chiave e usarlo con l'endpoint per creare un oggetto [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_auth)]

È possibile chiamare questo metodo nel metodo `Main`.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_client)]

## <a name="detect-faces-in-an-image"></a>Rilevare i visi in un'immagine

Alla radice della classe, definire la stringa di URL seguente. Questo URL punta a un set di immagini di esempio.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

Facoltativamente, è possibile scegliere il modello di intelligenza artificiale da usare per estrarre i dati dai visi rilevati. Per informazioni su queste opzioni, vedere [Specificare un modello di riconoscimento volto](../Face-API-How-to-Topics/specify-recognition-model.md).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

L'operazione di rilevamento finale acquisirà un oggetto [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet), un URL di immagine e un modello di riconoscimento.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>Ottenere gli oggetti viso rilevati

Nel prossimo blocco di codice il metodo `DetectFaceExtract` rileva i visi in tre delle immagini all'URL specificato e crea un elenco di oggetti [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) nella memoria del programma. L'elenco di valori [FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) specifica le funzionalità da estrarre. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Visualizzare i dati dei visi rilevati

Il resto del metodo `DetectFaceExtract` analizza e stampa i dati di attributo per ogni viso rilevato. Ogni attributo deve essere specificato separatamente nella chiamata all'API di rilevamento viso originale (nell'elenco [FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)). Il codice seguente elabora ogni attributo, ma è probabile che sia necessario usarne solo uno o alcuni.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Individuazione di visi simili

Il codice seguente cerca le corrispondenze di un singolo viso rilevato (origine) in un set di altri visi (destinazione). Quando trova una corrispondenza, visualizza l'ID del viso corrispondente nella console.

### <a name="detect-faces-for-comparison"></a>Rilevare visi per il confronto

Prima di tutto, definire un secondo metodo di rilevamento volto. Per poter confrontare i visi occorre prima rilevarli nelle immagini e questo metodo di rilevamento è ottimizzato per le operazioni di confronto. Non estrae gli attributi dettagliati dei visi come nella sezione precedente e usa un modello di riconoscimento diverso.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Trovare le corrispondenze

Il metodo seguente rileva i visi in un set di immagini di destinazione e in un'unica immagine di origine, quindi li confronta e trova tutte le immagini di destinazione simili all'immagine di origine.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Visualizzare le corrispondenze

Il codice seguente visualizza i dettagli delle corrispondenze nella console.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar_print)]

## <a name="create-and-train-a-person-group"></a>Creare ed eseguire il training di un gruppo di persone

Il codice seguente crea un **PersonGroup** con sei diversi oggetti **Person**. Associa ogni oggetto **Person** a un set di immagini di esempio e quindi esegue il training per riconoscere ogni persona in base alle caratteristiche del volto. Gli oggetti **Person** e **PersonGroup** vengono usati nelle operazioni di verifica, identificazione e raggruppamento.

Se non è già stato fatto, definire la stringa di URL seguente alla radice della classe. Questo URL punta a un set di immagini di esempio.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

Il codice riportato più avanti in questa sezione specifica un modello di riconoscimento per estrarre i dati dai visi e il frammento di codice seguente crea riferimenti ai modelli disponibili. Per informazioni sui modelli di riconoscimento, vedere [Specificare un modello di riconoscimento volto](../Face-API-How-to-Topics/specify-recognition-model.md).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

### <a name="create-persongroup"></a>Creare l'oggetto PersonGroup

Dichiarare una variabile di stringa alla radice della classe per rappresentare l'ID dell'oggetto **PersonGroup** che verrà creato.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_declare)]

Aggiungere il codice seguente in un nuovo metodo. Questo codice associa i nomi delle persone alle immagini di esempio.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_files)]

Aggiungere quindi il codice seguente per creare un oggetto **Person** per ogni persona nel dizionario e aggiungere i dati sul viso dalle immagini appropriate. Ogni oggetto **Person** è associato allo stesso **PersonGroup** tramite la propria stringa ID univoca. Ricordare di passare le variabili `client`, `url` e `RECOGNITION_MODEL1` nel metodo.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_create)]

### <a name="train-persongroup"></a>Eseguire il training di PersonGroup

Dopo aver estratto i dati sui visi dalle immagini e averli ordinati in oggetti **Person** diversi, è necessario eseguire il training di **PersonGroup** per identificare le caratteristiche visive associate a ogni oggetto **Person**. Il codice seguente chiama il metodo **train** asincrono ed esegue il polling dei risultati, visualizzando lo stato nella console.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_train)]

Questo gruppo **Person** e gli oggetti **Person** associati sono ora pronti per essere usati nelle operazioni di verifica, identificazione o raggruppamento.

## <a name="identify-a-face"></a>Identificare un viso

L'operazione di identificazione acquisisce un'immagine di una persona (o di più persone) e cerca di individuare l'identità di ogni viso nell'immagine. Confronta ogni viso rilevato con un **PersonGroup**, un database di oggetti **Person** diversi le cui caratteristiche del viso sono note.

> [!IMPORTANT]
> Prima di eseguire questo esempio occorre eseguire il codice in [Creare ed eseguire il training di un gruppo di persone](#create-and-train-a-person-group). Le variabili usate in quella sezione, ossia `client`, `url` e `RECOGNITION_MODEL1`, devono essere disponibili anche qui.

### <a name="get-a-test-image"></a>Ottenere un'immagine di test

Si noti che il codice nella sezione [Creare ed eseguire il training di un gruppo di persone](#create-and-train-a-person-group) definisce una variabile `sourceImageFileName`. Questa variabile corrisponde all'immagine di origine, ossia l'immagine che contiene le persone da identificare.

### <a name="identify-faces"></a>Identificare visi

Il codice seguente crea un elenco di tutti i visi rilevati nell'immagine di origine. Sono i visi che verranno identificati in base al **PersonGroup**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify_sources)]

Il frammento di codice successivo chiama l'operazione di identificazione e visualizza i risultati nella console. In questo caso, il servizio tenta di trovare una corrispondenza tra ogni viso nell'immagine di origine e un oggetto **Person** nel **PersonGroup**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>Creare uno snapshot per la migrazione dei dati

La funzionalità Snapshot consente di spostare i dati sui visi salvati, ad esempio un **PersonGroup** sottoposto a training, in una sottoscrizione diversa dell'API Viso di Servizi cognitivi di Azure. È consigliabile usare questa funzionalità se, ad esempio, è stato creato un oggetto **PersonGroup** usando una sottoscrizione di valutazione gratuita e si vuole eseguirne la migrazione a una sottoscrizione a pagamento. Per una panoramica della funzionalità Snapshot, vedere [Eseguire la migrazione dei dati sui visi in una sottoscrizione dell'API Viso diversa](../Face-API-How-to-Topics/how-to-migrate-face-data.md).

In questo esempio si eseguirà la migrazione del **PersonGroup** creato in [Creare ed eseguire il training di un gruppo di persone](#create-and-train-a-person-group). È possibile completare prima quella sezione o creare costrutti personalizzati di dati sui visi di cui eseguire la migrazione.

### <a name="set-up-target-subscription"></a>Configurare la sottoscrizione di destinazione

Prima di tutto è necessario avere una seconda sottoscrizione di Azure con una risorsa Viso. A tale scopo, seguire la procedura descritta nella sezione [Configurazione](#setting-up). 

Definire quindi le variabili seguenti nel metodo `Main` del programma. Sarà necessario creare nuove variabili di ambiente per l'ID sottoscrizione dell'account Azure, oltre alla chiave, all'endpoint e all'ID sottoscrizione del nuovo account (di destinazione). 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

Per questo esempio, dichiarare una variabile per l'ID del **PersonGroup** di destinazione, ossia l'oggetto che appartiene alla nuova sottoscrizione in cui si copieranno i dati.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>Autenticare il client di destinazione

Aggiungere quindi il codice per autenticare la sottoscrizione dell'API Viso secondaria.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>Usare uno snapshot

Il resto delle operazioni snapshot deve essere eseguito all'interno di un metodo asincrono. 

1. Il primo passaggio consiste nel **creare** lo snapshot, che salva i dati relativi ai visi della sottoscrizione originale in una posizione cloud temporanea. Questo metodo restituisce un ID da usare per eseguire query sullo stato dell'operazione.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take)]

1. Successivamente, eseguire query sull'ID fino al completamento dell'operazione.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take_wait)]

1. Usare quindi l'operazione **apply** per scrivere i dati relativi ai visi nella sottoscrizione di destinazione. Questo metodo restituisce anche un valore di ID.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Eseguire di nuovo query sull'ID fino al completamento dell'operazione.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Infine, completare il blocco TRY/CATCH e terminare il metodo.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_trycatch)]

A questo punto, il nuovo oggetto **PersonGroup** dovrebbe avere gli stessi dati di quello originale e dovrebbe essere accessibile dalla nuova sottoscrizione dell'API Viso di Azure (di destinazione).

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione dalla directory dell'applicazione con il comando `dotnet run`.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Se è stato creato un oggetto **PersonGroup** in questa guida di avvio rapido e si vuole eliminarlo, eseguire il codice seguente nel programma:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_delete)]

Definire il metodo di eliminazione con il codice seguente:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_deletepersongroup)]

Inoltre, se è stata eseguita la migrazione dei dati usando la funzionalità Snapshot in questa guida di avvio rapido, sarà anche necessario eliminare il **PersonGroup** salvato nella sottoscrizione di destinazione.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come usare la libreria dell'API Viso per .NET per eseguire attività di base. Successivamente, esplorare la documentazione di riferimento per altre informazioni sulla libreria.

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API Viso (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [Informazioni sull'API Viso](../overview.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/Face/Program.cs).