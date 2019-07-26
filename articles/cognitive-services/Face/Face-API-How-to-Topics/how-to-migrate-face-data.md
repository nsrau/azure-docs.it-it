---
title: Eseguire la migrazione dei dati sui visi tra sottoscrizioni - API Viso
titleSuffix: Azure Cognitive Services
description: Questa guida illustra come eseguire la migrazione dei dati sui visi archiviati da una sottoscrizione dell'API Viso a un'altra.
services: cognitive-services
author: lewlu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: lewlu
ms.openlocfilehash: 886e0ff353ab270bb823629d2068508531c14fc2
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516870"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Eseguire la migrazione dei dati sui visi in una sottoscrizione dell'API Viso diversa

Questa guida illustra come spostare i dati facciali, ad esempio un oggetto gruppo salvato con visi, in un'altra sottoscrizione di servizi cognitivi di Azure API Viso. Per spostare i dati, utilizzare la funzionalità snapshot. In questo modo si evita di dover compilare ripetutamente e eseguire il training di un oggetto gruppo o facet quando si spostano o si espandono le operazioni. Ad esempio, è possibile che sia stato creato un oggetto gruppo usando una sottoscrizione di valutazione gratuita e che ora si voglia eseguirne la migrazione alla sottoscrizione a pagamento. In alternativa, potrebbe essere necessario sincronizzare i dati volti tra le aree per un'operazione aziendale di grandi dimensioni.

Questa stessa strategia di migrazione si applica anche agli oggetti LargePersonGroup e LargeFaceList. Se non si ha familiarità con i concetti illustrati in questa guida, vedere le relative definizioni nella Guida ai [concetti di riconoscimento delle facce](../concepts/face-recognition.md) . Questa guida usa la libreria client .NET per l'API Viso con C#.

## <a name="prerequisites"></a>Prerequisiti

Sono necessari gli elementi seguenti:

- Due API Viso chiavi di sottoscrizione, una con i dati esistenti e l'altra per la migrazione. Per sottoscrivere il servizio API Viso e ottenere la chiave, seguire le istruzioni in [creare un account servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- Stringa dell'ID di sottoscrizione API Viso corrispondente alla sottoscrizione di destinazione. Per individuarlo, selezionare **Panoramica** nella portale di Azure. 
- Qualsiasi edizione di [Visual Studio 2015 o 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Creare il progetto di Visual Studio

Questa guida usa una semplice app console per eseguire la migrazione dei dati per i volti. Per un'implementazione completa, vedere l' [esempio API viso snapshot](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) su GitHub.

1. In Visual Studio creare una nuova app console .NET Framework progetto. Denominarlo **FaceApiSnapshotSample**.
1. Ottenere i pacchetti NuGet necessari. Fare clic con il pulsante destro del mouse sul progetto nella Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**. Selezionare la scheda **Sfoglia** e selezionare **Includi versione preliminare**. Trovare e installare il pacchetto seguente:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Creare client dell'API Viso

Nel metodo **Main** in *Program.cs*creare due istanze di [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) per le sottoscrizioni di origine e di destinazione. In questo esempio viene usata una sottoscrizione viso nell'area Asia orientale come origine e una sottoscrizione degli Stati Uniti occidentali come destinazione. Questo esempio illustra come eseguire la migrazione dei dati da un'area di Azure a un'altra. Se le sottoscrizioni si trovano in aree diverse, `Endpoint` modificare le stringhe.

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

Immettere i valori delle chiavi di sottoscrizione e gli URL degli endpoint per le sottoscrizioni di origine e di destinazione.


## <a name="prepare-a-persongroup-for-migration"></a>Preparare un oggetto PersonGroup per la migrazione

Per eseguire la migrazione alla sottoscrizione di destinazione, è necessario l'ID di gruppo nella sottoscrizione di origine. Usare il metodo [PersonGroupOperationsExtensions. ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) per recuperare un elenco di oggetti gruppo. Ottenere quindi la proprietà [gruppo. PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) . Questo processo ha un aspetto diverso a seconda degli oggetti gruppo. In questa guida, l'ID gruppo di origine viene archiviato `personGroupId`in.

> [!NOTE]
> Il [codice di esempio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) crea ed esegue il training di un nuovo gruppo per la migrazione. Nella maggior parte dei casi, è necessario avere già un gruppo da usare.

## <a name="take-a-snapshot-of-a-persongroup"></a>Eseguire uno snapshot di un gruppo

Uno snapshot è un'archiviazione remota temporanea per determinati tipi di dati visi. Agisce come una sorta di area appunti per copiare i dati da una sottoscrizione a un'altra. Prima di tutto, è necessario creare uno snapshot dei dati nella sottoscrizione di origine. Quindi applicarla a un nuovo oggetto dati nella sottoscrizione di destinazione.

Usare l'istanza di FaceClient della sottoscrizione di origine per creare uno snapshot di gruppo. Usare [TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) con l'ID gruppo e l'ID della sottoscrizione di destinazione. Se si dispone di più sottoscrizioni di destinazione, aggiungerle come voci di matrice nel terzo parametro.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Il processo di acquisizione e applicazione degli snapshot non interrompe alcuna chiamata normale all'origine o alla destinazione persone o visi. Non eseguire chiamate simultanee che modificano l'oggetto di origine, ad esempio le [chiamate di gestione facet](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) o la chiamata di [Training gruppo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) . L'operazione di snapshot può essere eseguita prima o dopo queste operazioni o potrebbe verificarsi errori.

## <a name="retrieve-the-snapshot-id"></a>Recuperare l'ID dello snapshot

Il metodo utilizzato per creare gli snapshot è asincrono, pertanto è necessario attenderne il completamento. Impossibile annullare le operazioni di snapshot. In questo codice, il `WaitForOperation` metodo monitora la chiamata asincrona. Controlla lo stato ogni 100 ms. Al termine dell'operazione, recuperare un ID operazione analizzando il `OperationLocation` campo. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Un valore `OperationLocation` tipico è simile al seguente:

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

Una volta visualizzato `Succeeded`lo stato dell'operazione, ottenere l'ID dello snapshot analizzando il `ResourceLocation` campo dell'istanza di OperationStatus restituita.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Un valore `resourceLocation` tipico è simile al seguente:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Applicare uno snapshot a una sottoscrizione di destinazione

Successivamente, creare il nuovo gruppo nella sottoscrizione di destinazione usando un ID generato in modo casuale. Usare quindi l'istanza di FaceClient della sottoscrizione di destinazione per applicare lo snapshot a questo gruppo. Passare l'ID dello snapshot e il nuovo ID gruppo.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Un oggetto snapshot è valido solo per 48 ore. Creare uno snapshot solo se si intende usarlo per la migrazione dei dati subito dopo.

Una richiesta di applicazione snapshot restituisce un altro ID operazione. Per ottenere questo ID, analizzare il `OperationLocation` campo dell'istanza di applySnapshotResult restituita. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Il processo dell'applicazione snapshot è anche asincrono, quindi è `WaitForOperation` necessario usarlo di nuovo per attendere il completamento.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Testare la migrazione dei dati

Dopo aver applicato lo snapshot, la nuova gruppo nella sottoscrizione di destinazione viene popolata con i dati del viso originali. Per impostazione predefinita, vengono copiati anche i risultati di training. Il nuovo gruppo è pronto per le chiamate di identificazione facciali senza dover eseguire la ripetizione del training.

Per testare la migrazione dei dati, eseguire le operazioni seguenti e confrontare i risultati stampati nella console:

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

A questo punto è possibile usare il nuovo gruppo nella sottoscrizione di destinazione. 

Per aggiornare il gruppo di destinazione in futuro, creare un nuovo gruppo per ricevere lo snapshot. A tale scopo, attenersi alla procedura descritta in questa guida. A un singolo oggetto gruppo può essere applicato uno snapshot solo una volta.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine della migrazione dei dati volti, eliminare manualmente l'oggetto snapshot.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Passaggi successivi

Vedere quindi la documentazione di riferimento per le API pertinente, esplorare un'app di esempio che usa la funzionalità snapshot oppure seguire una guida dettagliata per iniziare a usare le altre operazioni API indicate di seguito:

- [Documentazione di riferimento sugli snapshot (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Esempio di API Viso snapshot](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Aggiungere visi](how-to-add-faces.md)
- [Rilevare i visi in un'immagine](HowtoDetectFacesinImage.md)
- [Identificare i visi in un'immagine](HowtoIdentifyFacesinImage.md)
