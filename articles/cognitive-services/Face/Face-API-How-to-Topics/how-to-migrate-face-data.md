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
ms.openlocfilehash: 30ceb0e396597530071c70c4448761d914acb4ac
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548405"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Eseguire la migrazione dei dati sui visi in una sottoscrizione dell'API Viso diversa

Questa guida illustra come spostare i dati sui visi (ad esempio un **PersonGroup** di visi salvato) in un'altra sottoscrizione delll'API Viso tramite la funzionalità per la creazione di snapshot. In questo modo è possibile evitare di dover creare ripetutamente **PersonGroup** o **FaceList** e sottoporli a training in caso di trasferimento o ampliamento delle attività. Ad esempio, è possibile che sia stato creato un **PersonGroup** usando una sottoscrizione di valutazione gratuita e che si voglia ora eseguirne la migrazione alla sottoscrizione a pagamento o potrebbe essere necessario sincronizzare i dati sui visi tra aree diverse per le attività di un'azienda di grandi dimensioni.

Questa stessa strategia di migrazione si applica anche agli oggetti **LargePersonGroup** e **LargeFaceList**. Se non si ha familiarità con i concetti presentati in questa guida, vedere le relative definizioni nel [glossario](../Glossary.md). Questa guida usa la libreria client .NET per l'API Viso con C#.

## <a name="prerequisites"></a>Prerequisiti

- Due chiavi di sottoscrizione per l'API Viso (una con i dati esistenti e una come destinazione della migrazione). Seguire le istruzioni in [Creare un account Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per effettuare la sottoscrizione al servizio API Viso e ottenere la chiave.
- La stringa dell'ID di sottoscrizione per l'API Viso corrispondente alla sottoscrizione di destinazione (disponibile nel pannello **Panoramica** nel portale di Azure). 
- Qualsiasi edizione di [Visual Studio 2015 o 2017](https://www.visualstudio.com/downloads/).


## <a name="create-the-visual-studio-project"></a>Creare il progetto di Visual Studio

Questa guida userà una semplice app console per eseguire la migrazione dei dati sui visi. Per un'implementazione completa, vedere l'[esempio Face API Snapshot](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) su GitHub.

1. In Visual Studio creare un nuovo progetto **App console (.NET Framework)** e assegnargli il nome **FaceApiSnapshotSample**. 
1. Ottenere i pacchetti NuGet necessari. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**. Fare clic sulla scheda **Sfoglia** e selezionare **Includi versione preliminare**, quindi cercare e installare il pacchetto seguente:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)


## <a name="create-face-clients"></a>Creare client dell'API Viso

Nel metodo **Main** in *Program.cs*, creare due istanze di **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** per le sottoscrizioni di origine e di destinazione. In questo esempio, si userà una sottoscrizione dell'API Viso nell'area Asia orientale come origine e una sottoscrizione nell'area Stati Uniti occidentali come destinazione. Verrà così dimostrato come eseguire la migrazione dei dati da un'area di Azure a un'altra. Se le sottoscrizioni si trovano in aree diverse, sarà necessario modificare le stringhe `Endpoint`.

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

Sarà necessario compilare i valori delle chiavi di sottoscrizione e gli URL dell'endpoint per le sottoscrizioni di origine e di destinazione.


## <a name="prepare-a-persongroup-for-migration"></a>Preparare un oggetto PersonGroup per la migrazione

È necessario l'ID del **PersonGroup** nella sottoscrizione di origine per eseguirne la migrazione nella sottoscrizione di destinazione. Usare il metodo **[PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet)** per recuperare un elenco degli oggetti **PersonGroup** e quindi ottenere la proprietà **[PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId)**. Questo processo sarà diverso a seconda degli oggetti **PersonGroup** disponibili. In questa guida, l'ID dell'oggetto **PersonGroup** di origine è archiviato in `personGroupId`.

> [!NOTE]
> L'[esempio di codice](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) crea un nuovo **PersonGroup** e ne esegue il training per la migrazione, ma nella maggior parte dei casi è necessario avere già a disposizione un oggetto **PersonGroup** da usare.

## <a name="take-snapshot-of-persongroup"></a>Creare uno snapshot di PersonGroup

Uno snapshot è un archivio remoto temporaneo per determinati tipi di dati sui visi. Agisce come una sorta di area appunti per copiare i dati da una sottoscrizione a un'altra. Prima di tutto l'utente crea uno snapshot dei dati nella sottoscrizione di origine e quindi lo applica a un nuovo oggetto di dati nella sottoscrizione di destinazione.

Usare l'istanza **FaceClient** della sottoscrizione di origine per creare uno snapshot di **PersonGroup** usando **[TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet)** con l'ID di **PersonGroup** e l'ID della sottoscrizione di destinazione. In presenza di più sottoscrizioni di destinazione, è possibile aggiungerle come voci di matrice nel terzo parametro.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Il processo di creazione e applicazione degli snapshot non interromperà le chiamate regolari agli oggetti **PersonGroup** (o **FaceList**) di origine o di destinazione. Tuttavia, si consiglia di non eseguire chiamate simultanee che modificano l'oggetto di origine ([FaceList le chiamate di gestione](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) o il [gruppo di persone Train](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) chiamare, ad esempio), in quanto l'operazione di snapshot potrebbe eseguire prima o dopo queste operazioni o che si verifichino errori.

## <a name="retrieve-the-snapshot-id"></a>Recuperare l'ID dello snapshot

Lo snapshot accettando metodo è asincrono, pertanto sarà necessario attendere il completamento (snapshot operazioni non possono essere annullate). In questo codice, il metodo `WaitForOperation` esegue il monitoraggio della chiamata asincrona, controllando lo stato ogni 100 ms. Al termine dell'operazione, sarà possibile recuperare un ID dell'operazione. È possibile ottenerlo analizzando il campo `OperationLocation`. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Un valore `OperationLocation` tipico sarà simile al seguente:

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

Quando lo stato dell'operazione viene contrassegnato come `Succeeded`, è quindi possibile ottenere l'ID dello snapshot analizzando il campo `ResourceLocation` dell'istanza **OperationStatus** restituita.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Un valore `resourceLocation` tipico sarà simile al seguente:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-snapshot-to-target-subscription"></a>Applicare lo snapshot alla sottoscrizione di destinazione

A questo punto è necessario creare il nuovo **PersonGroup** nella sottoscrizione di destinazione, usando un ID generato in modo casuale. L'istanza **FaceClient** della sottoscrizione di destinazione verrà quindi usata per applicare lo snapshot a questo PersonGroup, passando l'ID dello snapshot e l'ID del nuovo **PersonGroup**. 

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Un oggetto Snapshot è valido solo per 48 ore. È consigliabile creare uno snapshot solo se si prevede di usarlo per la migrazione dei dati entro breve.

Una richiesta di applicazione dello snapshot restituirà un altro ID operazione. È possibile ottenere questo ID analizzando il campo `OperationLocation` dell'istanza **applySnapshotResult** restituita. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Anche il processo di applicazione dello snapshot è asincrono, quindi usare di nuovo `WaitForOperation` per attenderne il completamento.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Testare la migrazione dei dati

Dopo aver applicato lo snapshot, il nuovo **PersonGroup** nella sottoscrizione di destinazione dovrebbe essere popolato con i dati sui visi originali. Per impostazione predefinita, vengono copiati anche i risultati del training, pertanto il nuovo **PersonGroup** sarà pronto per le chiamate di identificazione dei visi senza la necessità di ripetere il training.

Per testare la migrazione dei dati, è possibile eseguire le operazioni seguenti e confrontare i risultati stampati nella console.

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

A questo punto è possibile iniziare a usare il nuovo **PersonGroup** nella sottoscrizione di destinazione. 

Se si desidera aggiornare di nuovo il **PersonGroup** di destinazione in futuro, sarà necessario creare un nuovo **PersonGroup** (seguendo le istruzioni in questa guida) per la ricezione dello snapshot. A un singolo oggetto **PersonGroup** è possibile applicare un solo snapshot alla volta.

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver completato la migrazione dei dati sui visi, si consiglia di eliminare manualmente l'oggetto snapshot.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="related-topics"></a>Argomenti correlati

- [Documentazione di riferimento sugli snapshot (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Esempio Face API Snapshot](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Come aggiungere visi](how-to-add-faces.md)
- [Come rilevare visi nelle immagini](HowtoDetectFacesinImage.md)
- [Come identificare visi nelle immagini](HowtoIdentifyFacesinImage.md)
