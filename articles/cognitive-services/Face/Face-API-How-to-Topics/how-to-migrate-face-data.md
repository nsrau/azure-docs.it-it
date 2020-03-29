---
title: Eseguire la migrazione dei dati facciali tra sottoscrizioni - Face
titleSuffix: Azure Cognitive Services
description: Questa guida illustra come eseguire la migrazione dei dati facciali archiviati da una sottoscrizione Face a un'altra.
services: cognitive-services
author: lewlu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: lewlu
ms.openlocfilehash: e5ca51da7322e4eab4ea364ec5da086a1068fa9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169818"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Eseguire la migrazione dei dati sui visi in una sottoscrizione dell'API Viso diversa

Questa guida illustra come spostare i dati dei volti, ad esempio un oggetto PersonGroup salvato con volti, in un'altra sottoscrizione di Servizi cognitivi di Azure.This guide shows you how to move face data, such as a saved PersonGroup object with faces, to a different Azure Cognitive Services Face subscription. Per spostare i dati, utilizzare la funzione Istantanea. In questo modo si evita di dover compilare e addestrare ripetutamente un oggetto PersonGroup o FaceList quando si spostano o si espandono le operazioni. Ad esempio, è possibile che sia stato creato un oggetto PersonGroup usando una sottoscrizione di valutazione gratuita e che ora si desideri eseguirne la migrazione alla sottoscrizione a pagamento. In alternativa, potrebbe essere necessario sincronizzare i dati volti tra sottoscrizioni in aree diverse per un'operazione aziendale di grandi dimensioni.

Questa stessa strategia di migrazione si applica anche agli oggetti LargePersonGroup e LargeFaceList. Se non si ha familiarità con i concetti illustrati in questa guida, vedere le relative definizioni nella guida Concetti relativi al [riconoscimento](../concepts/face-recognition.md) facciale. In questa guida viene utilizzata la libreria client Face .NET con C.

## <a name="prerequisites"></a>Prerequisiti

Sono necessari i seguenti elementi:

- Due chiavi di sottoscrizione Face, una con i dati esistenti e una a cui eseguire la migrazione. Per sottoscrivere il servizio Face e ottenere la chiave, seguire le istruzioni in [Creare un account Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- Stringa dell'ID sottoscrizione Face che corrisponde alla sottoscrizione di destinazione. Per trovarlo, selezionare **Panoramica** nel portale di Azure.To find it, select Overview in the Azure portal. 
- Qualsiasi edizione di [Visual Studio 2015 o 2017.](https://www.visualstudio.com/downloads/)

## <a name="create-the-visual-studio-project"></a>Creare il progetto di Visual Studio

Questa guida usa una semplice app console per eseguire la migrazione dei dati facciali. Per un'implementazione completa, vedere [l'esempio di snapshot Face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) in GitHub.For a full implementation, see the Face snapshot sample on GitHub.

1. In Visual Studio creare un nuovo progetto .NET Framework per l'app console. Denominarlo **FaceApiSnapshotSample**.
1. Ottenere i pacchetti NuGet necessari. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e selezionare **Gestisci pacchetti NuGet**. Selezionare la scheda **Sfoglia** e selezionare **Includi versione non definitiva**. Trovare e installare il pacchetto seguente:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Creare client dell'API Viso

Nel metodo **Main** in *Program.cs*, creare due istanze di [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) per le sottoscrizioni di origine e di destinazione. In questo esempio viene usata una sottoscrizione Face nell'area dell'Asia orientale come origine e una sottoscrizione West US come destinazione. In questo esempio viene illustrato come eseguire la migrazione dei dati da un'area di Azure a un'altra. 

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

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

Compilare i valori della chiave di sottoscrizione e gli URL degli endpoint per le sottoscrizioni di origine e di destinazione.


## <a name="prepare-a-persongroup-for-migration"></a>Preparare un oggetto PersonGroup per la migrazione

È necessario l'ID del PersonGroup nella sottoscrizione di origine per eseguirne la migrazione nella sottoscrizione di destinazione. Utilizzare il [metodo PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) per recuperare un elenco di oggetti PersonGroup. Ottenere quindi la proprietà [PersonGroup.PersonGroupId.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) Questo processo ha un aspetto diverso a seconda degli oggetti PersonGroup di cui si dispone. In questa guida, l'ID PersonGroup di origine viene archiviato in `personGroupId`.

> [!NOTE]
> Il [codice di esempio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) crea ed esegue il training di un nuovo PersonGroup di cui eseguire la migrazione. Nella maggior parte dei casi, è necessario avere già un PersonGroup da utilizzare.

## <a name="take-a-snapshot-of-a-persongroup"></a>Creare uno snapshot di un PersonGroup

Uno snapshot è un archivio remoto temporaneo per determinati tipi di dati Face. Agisce come una sorta di area appunti per copiare i dati da una sottoscrizione a un'altra. Innanzitutto, si crea uno snapshot dei dati nella sottoscrizione di origine. Quindi applicarlo a un nuovo oggetto dati nella sottoscrizione di destinazione.

Usare l'istanza FaceClient della sottoscrizione di origine per creare uno snapshot di PersonGroup. Usare [TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) con l'ID PersonGroup e l'ID della sottoscrizione di destinazione. Se si dispone di più sottoscrizioni di destinazione, aggiungerle come voci di matrice nel terzo parametro.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Il processo di acquisizione e applicazione di snapshot non interrompe le chiamate regolari a PersonGroups o FaceList di origine o di destinazione. Non effettuare chiamate simultanee che modificano l'oggetto di origine, ad esempio le chiamate di [gestione FaceList](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) o la chiamata [PersonGroup Train.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) L'operazione snapshot potrebbe essere eseguita prima o dopo tali operazioni o potrebbero verificarsi errori.

## <a name="retrieve-the-snapshot-id"></a>Recuperare l'ID snapshot

Il metodo utilizzato per scattare snapshot è asincrono, pertanto è necessario attendere il completamento. Le operazioni snapshot non possono essere annullate. In questo codice, il `WaitForOperation` metodo monitora la chiamata asincrona. Controlla lo stato ogni 100 ms. Al termine dell'operazione, recuperare un ID operazione `OperationLocation` analizzando il campo. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Un `OperationLocation` valore tipico è simile al seguente:A typical value looks like this:

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

Dopo la visualizzazione `Succeeded`dello stato dell'operazione , `ResourceLocation` ottenere l'ID snapshot analizzando il campo dell'istanza OperationStatus restituita.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Un `resourceLocation` valore tipico è simile al seguente:A typical value looks like this:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Applicare uno snapshot a una sottoscrizione di destinazioneApply a snapshot to a target subscription

Successivamente, creare il nuovo PersonGroup nella sottoscrizione di destinazione utilizzando un ID generato casualmente. Usare quindi l'istanza FaceClient della sottoscrizione di destinazione per applicare lo snapshot a questo PersonGroup.Then use the target subscription's FaceClient instance to apply the snapshot to this PersonGroup. Passare l'ID snapshot e il nuovo ID PersonGroup.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Un oggetto Snapshot è valido solo per 48 ore. Creare uno snapshot solo se si intende utilizzarlo per la migrazione dei dati subito dopo.

Una richiesta di applicazione snapshot restituisce un altro ID operazione. Per ottenere questo ID, analizzare il `OperationLocation` campo dell'istanza applySnapshotResult restituita. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Anche il processo dell'applicazione snapshot `WaitForOperation` è asincrono, pertanto è nuovamente possibile attendere il completamento.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Testare la migrazione dei dati

Dopo aver applicato lo snapshot, il nuovo PersonGroup nella sottoscrizione di destinazione popola con i dati del volto originale. Per impostazione predefinita, vengono copiati anche i risultati dell'allenamento. Il nuovo PersonGroup è pronto per le chiamate di identificazione faccia senza bisogno di riqualificazione.

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

A questo punto è possibile usare il nuovo PersonGroup nella sottoscrizione di destinazione. 

Per aggiornare nuovamente il PersonGroup di destinazione in futuro, creare un nuovo PersonGroup per ricevere lo snapshot. A tale scopo, seguire i passaggi in questa guida. A un singolo oggetto PersonGroup può essere applicato uno snapshot una sola volta.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine della migrazione dei dati della faccia, eliminare manualmente l'oggetto snapshot.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Passaggi successivi

Vedere quindi la documentazione di riferimento per le API pertinente, esplorare un'app di esempio che usa la funzionalità Snapshot o seguire una guida alle procedure per iniziare a usare le altre operazioni API menzionate di seguito:Next, see the relevant API reference documentation, explore a sample app that uses the Snapshot feature, or follow a how-to guide to start using the other API operations mentioned here:

- [Documentazione di riferimento sugli snapshot (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Esempio di istantanea del volto](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Aggiungere visi](how-to-add-faces.md)
- [Rilevare i volti in un'immagine](HowtoDetectFacesinImage.md)
- [Identificare i visi in un'immagine](HowtoIdentifyFacesinImage.md)
