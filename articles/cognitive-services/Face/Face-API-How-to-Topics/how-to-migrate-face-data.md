---
title: Eseguire la migrazione dei dati sui visi tra sottoscrizioni - API Viso
titleSuffix: Azure Cognitive Services
description: Questa guida illustra come eseguire la migrazione dei dati sui visi archiviati da una sottoscrizione dell'API Viso a un'altra.
services: cognitive-services
author: lewlu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: lewlu
ms.openlocfilehash: 702aed12860c090e83b997e6b56d56e06b416568
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65913800"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Eseguire la migrazione dei dati sui visi in una sottoscrizione dell'API Viso diversa

Questa guida illustra come spostare i dati di viso, ad esempio un oggetto gruppo di persone salvato con i visi, in un'altra sottoscrizione di API viso servizi cognitivi di Azure. Per spostare i dati, si usa la funzionalità di Snapshot. In questo modo si evita che compilare ed eseguire il training di un oggetto gruppo di persone o FaceList quando si sposta o espandere le operazioni più volte. Ad esempio, forse è creato un oggetto gruppo di persone con una sottoscrizione di valutazione gratuita e a questo punto si desidera eseguirne la migrazione alla sottoscrizione a pagamento. Oppure potrebbe essere necessario sincronizzare i dati di volti tra aree per un'operazione aziendale di grandi dimensioni.

Questa stessa strategia di migrazione si applica anche agli oggetti LargePersonGroup e LargeFaceList. Se non si ha familiarità con i concetti presentati in questa Guida, vedere le relative definizioni nel [affrontare concetti riconoscimento](../concepts/face-recognition.md) Guida. Questa guida usa la libreria client .NET per l'API Viso con C#.

## <a name="prerequisites"></a>Prerequisiti

Sono necessari gli elementi seguenti:

- Due API viso chiavi di sottoscrizione, una con i dati esistenti e l'altro per eseguire la migrazione a. Per sottoscrivere il servizio API viso e ottenere la chiave, seguire le istruzioni in [creare un account servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- La stringa di ID sottoscrizione API viso che corrisponde alla sottoscrizione di destinazione. Per eseguire la ricerca, selezionare **Panoramica** nel portale di Azure. 
- Qualsiasi edizione di [Visual Studio 2015 o 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Creare il progetto di Visual Studio

Questa Guida usa un'app console semplice per eseguire la migrazione dei dati di visi. Per un'implementazione completa, vedere la [esempio di API viso snapshot](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) su GitHub.

1. In Visual Studio, creare un nuovo progetto di .NET Framework di app Console. Denominarlo **FaceApiSnapshotSample**.
1. Ottenere i pacchetti NuGet necessari. Il progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**. Selezionare il **esplorare** scheda e selezionare **Includi versione preliminare**. Trovare e installare il pacchetto seguente:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Creare client dell'API Viso

Nel **Main** metodo nella *Program.cs*, creare due [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) istanze per le sottoscrizioni di origine e di destinazione. Questo esempio Usa una sottoscrizione di volti nell'area Asia orientale come origine e una sottoscrizione di Stati Uniti occidentali come destinazione. In questo esempio viene illustrato come eseguire la migrazione dei dati da un'area di Azure a un altro. Se le sottoscrizioni si trovano in aree diverse, modificare il `Endpoint` stringhe.

```csharp
var FaceClientEastAsia = new FaceClient(new ApiKeyServiceClientCredentials("<East Asia Subscription Key>"))
    {
        Endpoint = "https://southeastasia.api.cognitive.microsoft.com/>"
    };

var FaceClientWestUS = new FaceClient(new ApiKeyServiceClientCredentials("<West US Subscription Key>"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com/"
    };
```

Compilare i valori di chiave di sottoscrizione e l'URL dell'endpoint per le sottoscrizioni di origine e di destinazione.


## <a name="prepare-a-persongroup-for-migration"></a>Preparare un oggetto PersonGroup per la migrazione

È necessario l'ID del gruppo di persone nella propria sottoscrizione di origine per eseguirne la migrazione alla sottoscrizione di destinazione. Usare la [PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) metodo per recuperare un elenco degli oggetti di gruppo di persone. Ottenere quindi i [PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) proprietà. Questo processo ha un aspetto diverso in base quale gruppo di persone di oggetti disponibili. In questa guida è archiviata l'origine ID gruppo di persone `personGroupId`.

> [!NOTE]
> Il [esempi di codice](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) crea ed esegue il training di un nuovo gruppo di persone per eseguire la migrazione. Nella maggior parte dei casi, si dovrebbe già disporre di un gruppo di persone da utilizzare.

## <a name="take-a-snapshot-of-a-persongroup"></a>Creare uno snapshot di un gruppo di persone

Uno snapshot è un archivio remoto temporaneo per determinati tipi di dati di visi. Agisce come una sorta di area appunti per copiare i dati da una sottoscrizione a un'altra. Prima di tutto uno snapshot dei dati nella sottoscrizione di origine. Quindi si applica a un nuovo oggetto di dati nella sottoscrizione di destinazione.

Usare istanza FaceClient della sottoscrizione di origine per creare uno snapshot del gruppo di persone. Uso [TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) con l'ID del gruppo di persone e l'ID della sottoscrizione di destinazione Se si hanno più sottoscrizioni di destinazione, è possibile aggiungerli come le voci della matrice nel terzo parametro.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Il processo di recupero e l'applicazione degli snapshot non interrompere le normali chiamate all'origine o destinazione gruppi di persone o per elenco di visi. Non eseguire chiamate simultanee che modificano l'oggetto di origine, ad esempio [le chiamate di gestione FaceList](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) o il [gruppo di persone Train](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) chiamare, ad esempio. L'operazione snapshot potrebbe essere eseguiti prima o dopo queste operazioni o potrebbe verificarsi degli errori.

## <a name="retrieve-the-snapshot-id"></a>Recuperare l'ID dello snapshot

Il metodo utilizzato per creare snapshot è asincrono, quindi è necessario attendere il completamento. Operazioni di creazione snapshot non possono essere annullate. In questo codice, il `WaitForOperation` metodo consente di monitorare la chiamata asincrona. Controlla lo stato di ogni 100 ms. Al termine dell'operazione, recuperare un ID operazione analizzando il `OperationLocation` campo. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Una tipica `OperationLocation` valore aspetto simile al seguente:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

Il metodo helper `WaitForOperation` è come segue:

```csharp
/// <summary>
/// Waits for the take/apply operation to complete and returns the final operation status.
/// </summary>
/// <returns>The final operation status.</returns>
private static async Task<OperationStatus> WaitForOperation(IFaceClient client, Guid operationId)
{
    OperationStatus operationStatus = null;
    do
    {
        if (operationStatus != null)
        {
            Thread.Sleep(TimeSpan.FromMilliseconds(100));
        }

        // Get the status of the operation.
        operationStatus = await client.Snapshot.GetOperationStatusAsync(operationId);

        Console.WriteLine($"Operation Status: {operationStatus.Status}");
    }
    while (operationStatus.Status != OperationStatusType.Succeeded
            && operationStatus.Status != OperationStatusType.Failed);

    return operationStatus;
}
```

Dopo lo stato dell'operazione viene illustrata `Succeeded`, ottenere l'ID dello snapshot analizzando il `ResourceLocation` campo di istanza OperationStatus restituita.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Una tipica `resourceLocation` valore aspetto simile al seguente:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Applicare uno snapshot per una sottoscrizione di destinazione

Successivamente, creare il nuovo gruppo di persone nella sottoscrizione di destinazione con un ID generato in modo casuale. Usare quindi l'istanza FaceClient della sottoscrizione di destinazione per applicare lo snapshot per questo gruppo di persone. Passare nello snapshot di ID e il nuovo ID di gruppo di persone.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Un oggetto dello Snapshot è valido solo per 48 ore. Accettare solo uno snapshot, se si prevede di usarlo per la migrazione dei dati subito dopo.

Una richiesta di applicazione dello snapshot restituisce un altro ID operazione. Per ottenere questo ID, analizzare il `OperationLocation` campo dell'istanza applySnapshotResult restituito. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Il processo di applicazione dello snapshot è piuttosto asincrono, anche in questo caso usare `WaitForOperation` attenderne il completamento.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Testare la migrazione dei dati

Dopo aver applicato lo snapshot, il nuovo gruppo di persone nella sottoscrizione di destinazione viene popolato con i dati originali viso. Per impostazione predefinita, vengono copiati anche i risultati di training. Il nuovo gruppo di persone è pronto per le chiamate di identificazione viso senza la necessità di ripetizione del training.

Per testare la migrazione dei dati, eseguire le operazioni seguenti e confrontare i risultati che sono stampare sulla console:

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Usare i metodi helper seguenti:

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("Person Group:");
    Console.WriteLine(JsonConvert.SerializeObject(personGroup));

    // List persons.
    var persons = await client.PersonGroupPerson.ListAsync(personGroupId);

    foreach (var person in persons)
    {
        Console.WriteLine(JsonConvert.SerializeObject(person));
    }

    Console.WriteLine();
}
```

```csharp
private static async Task IdentifyInPersonGroup(IFaceClient client, string personGroupId)
{
    using (var fileStream = new FileStream("data\\PersonGroup\\Daughter\\Daughter1.jpg", FileMode.Open, FileAccess.Read))
    {
        var detectedFaces = await client.Face.DetectWithStreamAsync(fileStream);

        var result = await client.Face.IdentifyAsync(detectedFaces.Select(face => face.FaceId.Value).ToList(), personGroupId);
        Console.WriteLine("Test identify against PersonGroup");
        Console.WriteLine(JsonConvert.SerializeObject(result));
        Console.WriteLine();
    }
}
```

A questo punto è possibile usare il nuovo gruppo di persone nella sottoscrizione di destinazione. 

Per aggiornare la destinazione gruppo di persone in futuro, creare un nuovo gruppo di persone per ricevere lo snapshot. A questo scopo, seguire i passaggi descritti in questa Guida. Un singolo oggetto gruppo di persone può avere uno snapshot applicato una sola volta.

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver completato la migrazione di affrontare i dati, eliminare manualmente l'oggetto snapshot.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Passaggi successivi

Successivamente, vedere la relativa documentazione di riferimento API, esplorare un'app di esempio che usa la funzionalità di Snapshot o seguire una Guida dettagliata per iniziare a usare le altre operazioni dell'API indicate di seguito:

- [Documentazione di riferimento sugli snapshot (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Esempio di snapshot API viso](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Aggiungere i visi](how-to-add-faces.md)
- [Rileva i visi in un'immagine](HowtoDetectFacesinImage.md)
- [Identifica i visi in un'immagine](HowtoIdentifyFacesinImage.md)
