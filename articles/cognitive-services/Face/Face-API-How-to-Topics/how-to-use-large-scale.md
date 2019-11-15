---
title: 'Esempio: Usare la funzionalità su larga scala - API Viso'
titleSuffix: Azure Cognitive Services
description: Questa guida illustra come eseguire un ridimensionamento passando dagli oggetti PersonGroup e FaceList esistenti agli oggetti LargePersonGroup e LargeFaceList.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.openlocfilehash: 976baaef11251715218ecea71986f08ec5f72996
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73743736"
---
# <a name="example-use-the-large-scale-feature"></a>Esempio: Usare la funzionalità su larga scala

Questo è un articolo di livello avanzato su come passare all'uso della funzionalità su larga scala, dagli oggetti PersonGroup e FaceList esistenti agli oggetti LargePersonGroup e LargeFaceList. Questa guida illustra il processo di migrazione. Presuppone una familiarità di base con gli oggetti PersonGroup e FaceList, con l'operazione [Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) e con le funzioni di riconoscimento volto. Per altre informazioni su questi argomenti, vedere la guida concettuale al [riconoscimento volto](../concepts/face-recognition.md).

LargePersonGroup e LargeFaceList vengono nell'insieme definite operazioni su larga scala. LargePersonGroup può contenere fino a 1 milione di persone, ognuna con un massimo di 248 visi. LargeFaceList può contenere fino a 1 milione di visi. Le operazioni su larga scala sono simili a quelle convenzionali eseguite per PersonGroup e FaceList, ma presentano alcune differenze dovute alla nuova architettura. 

Gli esempi sono scritti in C# usando la libreria client dell'API Viso di Servizi cognitivi di Azure.

> [!NOTE]
> Per abilitare le prestazioni di ricerca dell'API Viso per le operazioni Identification e FindSimilar su larga scala, introdurre un'operazione Train per eseguire l'analisi preliminare degli oggetti LargeFaceList e LargePersonGroup. Il tempo di training varia da alcuni secondi a circa mezz'ora, a seconda della capacità effettiva. Durante il periodo di training, è possibile eseguire le operazioni Identification e FindSimilar se in precedenza è stato eseguito un training corretto. Nuove persone e nuovi visi aggiunti non vengono visualizzati nel risultato fino a quando non viene completata una nuova post-migrazione al training su larga scala.

## <a name="step-1-initialize-the-client-object"></a>Passaggio 1: Inizializzare l'oggetto client

Quando si usa la libreria client dell'API Viso, l'endpoint e la chiave di sottoscrizione vengono passati al costruttore della classe FaceClient. Ad esempio:

```csharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Per ottenere la chiave di sottoscrizione con l'endpoint corrispondente, passare ad Azure Marketplace dal portale di Azure.
Per altre informazioni, vedere [Sottoscrizioni](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>Passaggio 2: Migrazione del codice

Questa sezione illustra come eseguire la migrazione dell'implementazione di PersonGroup o FaceList a LargePersonGroup o LargeFaceList. Anche se gli oggetti LargePersonGroup e LargeFaceList differiscono da PersonGroup e FaceList in termini di progettazione e implementazione interna, le interfacce API sono analoghe per motivi di compatibilità con le versioni precedenti.

La migrazione dei dati non è supportata. È invece possibile ricreare l'oggetto LargePersonGroup o LargeFaceList.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Eseguire la migrazione da un oggetto PersonGroup a un oggetto LargePersonGroup

La migrazione da un oggetto PersonGroup a un oggetto LargePersonGroup è semplice. Questi oggetti condividono esattamente le stesse operazioni a livello di gruppo.

Per l'implementazione correlata a PersonGroup o Person è necessario solo modificare i percorsi dell'API o il modulo o la classe SDK in LargePersonGroup e LargePersonGroup Person.

Aggiungere tutti i visi e tutte le persone dall'oggetto PersonGroup al nuovo oggetto LargePersonGroup. Per altre informazioni, vedere [Aggiungere visi](how-to-add-faces.md).

### <a name="migrate-a-facelist-to-a-largefacelist"></a>Eseguire la migrazione da un oggetto FaceList a un oggetto LargeFaceList

| API di FaceList | API di LargeFaceList |
|:---:|:---:|
| Create | Create |
| Delete | Delete |
| Get | Get |
| Elenco | Elenco |
| Aggiornamento | Aggiornamento |
| - | Eseguire il training |
| - | Get Training Status |

La tabella precedente contiene un confronto delle operazioni a livello di elenco tra FaceList e LargeFaceList. Come illustrato, LargeFaceList include nuove operazioni, Train e Get Training Status, rispetto a FaceList. Il training dell'oggetto LargeFaceList è una condizione preliminare dell'operazione [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237). Il training non è necessario per FaceList. Il frammento di codice seguente è una funzione helper per eseguire il training di un oggetto LargeFaceList:

```csharp
/// <summary>
/// Helper function to train LargeFaceList and wait for finish.
/// </summary>
/// <remarks>
/// The time interval can be adjusted considering the following factors:
/// - The training time which depends on the capacity of the LargeFaceList.
/// - The acceptable latency for getting the training status.
/// - The call frequency and cost.
///
/// Estimated training time for LargeFaceList in different scale:
/// -     1,000 faces cost about  1 to  2 seconds.
/// -    10,000 faces cost about  5 to 10 seconds.
/// -   100,000 faces cost about  1 to  2 minutes.
/// - 1,000,000 faces cost about 10 to 30 minutes.
/// </remarks>
/// <param name="largeFaceListId">The Id of the LargeFaceList for training.</param>
/// <param name="timeIntervalInMilliseconds">The time interval for getting training status in milliseconds.</param>
/// <returns>A task of waiting for LargeFaceList training finish.</returns>
private static async Task TrainLargeFaceList(
    string largeFaceListId,
    int timeIntervalInMilliseconds = 1000)
{
    // Trigger a train call.
    await FaceClient.LargeTrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await faceClient.LargeFaceList.TrainAsync(largeFaceListId);

        if (status.Status == Status.Running)
        {
            continue;
        }
        else if (status.Status == Status.Succeeded)
        {
            break;
        }
        else
        {
            throw new Exception("The train operation is failed!");
        }
    }
}
```

In precedenza, un uso tipico di FaceList con l'aggiunta di visi e FindSimilar era il seguente:

```csharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.FaceList.CreateAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.FaceList.AddFaceFromStreamAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

Quando si esegue la migrazione a LargeFaceList, il frammento di codice diventa il seguente:

```csharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.LargeFaceList.CreateAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.LargeFaceList.AddFaceFromStreamAsync(LargeFaceListId, stream);
            }
        });

// Train() is newly added operation for LargeFaceList.
// Must call it before FindSimilarAsync() to ensure the newly added faces searchable.
await TrainLargeFaceList(LargeFaceListId);

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

Come illustrato in precedenza, la gestione dei dati e la parte relativa a FindSimilar sono quasi identiche. L'unica eccezione è data dal fatto che, per il funzionamento di FindSimilar, è necessario completare un'operazione Train di analisi preliminare nell'oggetto LargeFaceList.

## <a name="step-3-train-suggestions"></a>Passaggio 3: Suggerimento per l'operazione Train

Anche se l'operazione Train velocizza [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e [Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), l'impatto sui tempi di training è negativo, soprattutto su larga scala. I tempi di training stimati per dimensioni diverse sono elencati nella tabella seguente.

| Dimensione per visi o persone | Tempo di training stimato |
|:---:|:---:|
| 1\.000 | 1-2 secondi |
| 10,000 | 5-10 secondi |
| 100,000 | 1-2 minuti |
| 1\.000.000 | 10-30 minuti |

Per usare al meglio la funzionalità su larga scala, è consigliabile adottare le strategie seguenti.

### <a name="step-31-customize-time-interval"></a>Passaggio 3.1: Personalizzare l'intervallo di tempo

Come illustrato in `TrainLargeFaceList()`, è presente un intervallo di tempo, espresso in millisecondi, per ritardare il processo di verifica dello stato di training infinito. Per l'elemento LargeFaceList con più visi, l'uso di un intervallo maggiore riduce il numero e il costo delle chiamate. È quindi necessario personalizzare l'intervallo di tempo in base alla capacità prevista di LargeFaceList.

La stessa strategia si applica anche a LargePersonGroup. Quando, ad esempio, si esegue il training di un oggetto LargePersonGroup con 1 milione di persone, il valore di `timeIntervalInMilliseconds` può essere 60.000, ovvero un intervallo di 1 minuto.

### <a name="step-32-small-scale-buffer"></a>Passaggio 3.2: Buffer su scala ridotta

È possibile eseguire la ricerca di persone o visi in un oggetto LargePersonGroup o LargeFaceList solo dopo l'esecuzione del training. In uno scenario dinamico, nuovi visi o persone vengono continuamente aggiunti e deve essere immediatamente possibile eseguirne la ricerca. Il training può pertanto richiedere più tempo del previsto. 

Per attenuare questo problema, usare un oggetto LargePersonGroup o LargeFaceList di dimensioni molto piccole come buffer solo per le voci appena aggiunte. Grazie alle dimensioni ridotte, questo buffer richiede un tempo di training minore. La funzionalità di ricerca immediata in questo buffer temporaneo dovrebbe funzionare in modo corretto. Usare il buffer in combinazione con il training nell'oggetto LargePersonGroup o LargeFaceList principale eseguendo il training principale a intervalli meno frequenti. Ad esempio, a mezzanotte e con frequenza giornaliera.

Di seguito viene indicato un flusso di lavoro di esempio:

1. Creare un oggetto LargePersonGroup o LargeFaceList principale, che rappresenta la raccolta principale. Creare un buffer LargePersonGroup o LargeFaceList, che rappresenta la raccolta buffer. La raccolta buffer è relativa solo alle persone o ai visi nuovi aggiunti.
1. Aggiungere nuovi visi o persone sia alla raccolta principale che alla raccolta buffer.
1. Eseguire solo il training della raccolta buffer con un breve intervallo di tempo per verificare che le voci appena aggiunte diventino effettive.
1. Chiamare l'operazione Identification o FindSimilar sia sulla raccolta principale che sulla raccolta buffer. Unire i risultati.
1. Quando le dimensioni della raccolta buffer aumentano fino a una soglia stabilita o causano un tempo di inattività del sistema, creare una nuova raccolta buffer. Attivare l'operazione Train nella raccolta principale.
1. Eliminare la raccolta buffer precedente dopo il completamento dell'operazione Train nella raccolta principale.

### <a name="step-33-standalone-training"></a>Passaggio 3.3: Training autonomo

Se una latenza relativamente lunga è accettabile, non è necessario attivare l'operazione Train subito dopo l'aggiunta di nuovi dati. Tale operazione può essere invece divisa dalla logica principale e attivata regolarmente. Questa strategia è adatta per scenari dinamici con una latenza accettabile. È applicabile a scenari statici per ridurre ulteriormente la frequenza dell'operazione Train.

Si supponga che sia disponibile una funzione `TrainLargePersonGroup` analoga alla funzione `TrainLargeFaceList`. Un'implementazione tipica del training autonomo su un oggetto LargePersonGroup richiamando la classe [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) in `System.Timers` è la seguente:

```csharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    faceClient.LargePersonGroup.CreateAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Set up standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1-minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1-hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces, and identification, except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

Per altre informazioni sulla gestione dei dati e sulle implementazioni correlate all'identificazione, vedere [Aggiungere visi](how-to-add-faces.md) e [Identificare visi in un'immagine](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a>Summary

In questa guida si è appreso come eseguire la migrazione del codice PersonGroup o FaceList esistente (non di dati) a un oggetto LargePersonGroup o LargeFaceList:

- LargePersonGroup e LargeFaceList funzionano in modo analogo a PersonGroup o FaceList, ad eccezione del fatto che per LargeFaceList è richiesta l'operazione Train.
- Per aggiornare i dati dinamici per set di dati su larga scala, adottare la strategia di training corretta.

## <a name="next-steps"></a>Passaggi successivi

Seguire le procedure per aggiungere visi a PersonGroup o eseguire l'operazione Identify su PersonGroup.

- [Aggiungere visi](how-to-add-faces.md)
- [Identificare i visi in un'immagine](HowtoIdentifyFacesinImage.md)
