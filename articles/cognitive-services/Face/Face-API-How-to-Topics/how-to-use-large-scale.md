---
title: Usare la funzionalità su larga scala nell'API Viso | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Usare la funzionalità su larga scala nell'API Viso di Servizi cognitivi.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: f7b3ac57cf6b24c8a90b4ea59757d3a2cfafd781
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373377"
---
# <a name="how-to-use-the-large-scale-feature"></a>Come usare la funzionalità su larga scala

Questa guida è un articolo avanzato sulla migrazione del codice per estendere le operazioni da elementi PersonGroup e FaceList esistenti rispettivamente a elementi LargePersonGroup e LargeFaceList.
Questa guida illustra il processo di migrazione con l'ipotesi di conoscere l'uso di base degli elementi PersonGroup e FaceList.
Per acquisire familiarità con le operazioni di base, vedere altre esercitazioni, ad esempio [How to identify faces in images](HowtoIdentifyFacesinImage.md) (Come identificare visi nelle immagini).

L'API Viso di Microsoft ha rilasciato di recente due funzionalità per abilitare scenari su larga scala, ovvero LargePersonGroup e LargeFaceList, cui si fa riferimento complessivamente con il termine operazioni su larga scala.
L'elemento LargePersonGroup può contenere fino a 1.000.000 persone, ognuna con un massimo di 248 visi, mentre LargeFaceList può contenere fino a 1.000.000 visi.

Le operazioni su larga scala sono simili a quelle convenzionali eseguite per PersonGroup e FaceList, ma presentano alcune differenze rilevanti a causa della nuova architettura.
Questa guida illustra il processo di migrazione con l'ipotesi di conoscere l'uso di base degli elementi PersonGroup e FaceList.
Gli esempi sono scritti in C# usando la libreria del client dell'API Viso.

Per abilitare le prestazioni di ricerca dell'API Viso per le operazioni Identification e FindSimilar su larga scala, è necessario introdurre un'operazione Train per eseguire l'analisi preliminare degli elementi LargeFaceList e LargePersonGroup.
Il tempo di training varia da alcuni secondi a circa mezz'ora, a seconda della capacità effettiva.
Durante il periodo di training, è comunque possibile eseguire le operazioni Identification e FindSimilar se in precedenza è stato eseguito un training corretto.
Nuove persone o nuovi visi aggiunti, tuttavia, non vengono visualizzati nel risultato fino a quando non viene completata una nuova post-migrazione al training su larga scala.

## <a name="concepts"></a> Concetti

Se non si ha familiarità con i concetti seguenti in questa guida, cercare le definizioni nel [glossario](../Glossary.md).

- LargePersonGroup: raccolta di persone con capacità fino a 1.000.000.
- LargeFaceList: raccolta di visi con capacità fino a 1.000.000.
- Train: analisi preliminare per verificare le prestazioni delle operazioni Identification/FindSimilar.
- Identification: identificazione di uno o più visi in un elemento PersonGroup o LargePersonGroup.
- FindSimilar: ricerca di visi simili in un elemento FaceList o LargeFaceList.

## <a name="initialization"></a> Passaggio 1: Autorizzare la chiamata API

Quando si usa la libreria client dell'API Viso, l'endpoint e la chiave di sottoscrizione vengono passati al costruttore della classe FaceServiceClient. Ad esempio: 

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionRegion = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/";
FaceServiceClient FaceServiceClient = new FaceServiceClient(SubscriptionKey, SubscriptionRegion);
```

La chiave di sottoscrizione e l'endpoint corrispondente possono essere ottenuti nella pagina Marketplace del portale di Azure.
Vedere [Sottoscrizioni](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="migrate"></a> Passaggio 2: Migrazione del codice in azione

Questa sezione illustra solo la migrazione dell'implementazione di PersonGroup/FaceList a LargePersonGroup/LargeFaceList.
Sebbene gli elementi LargePersonGroup e LargeFaceList differiscano dagli elementi PersonGroup e FaceList in termini di progettazione e implementazione interna, le interfacce API sono analoghe per motivi di compatibilità retroattiva.

La migrazione dei dati non è supportata ed è necessario ricreare LargePersonGroup e LargeFaceList.

## <a name="largepersongroup"></a> Passaggio 2.1: Eseguire la migrazione da PersonGroup a LargePersonGroup

La migrazione da PersonGroup a LargePersonGroup è molto semplice perché i due elementi condividono esattamente le stesse operazioni a livello di gruppo.

Per l'implementazione correlata a PersonGroup/Person è necessario solo modificare i percorsi dell'API o il modulo/classe SDK in LargePersonGroup e LargePersonGroup/Person.

In termini di migrazione dei dati, vedere [How to Add Faces](how-to-add-faces.md) (Come aggiungere visi) per riferimento.

## <a name="largefacelist"></a> Passaggio 2.2: Eseguire la migrazione di FaceList in LargeFaceList

| API di FaceList | API di LargeFaceList |
|:---:|:---:|
| Create | Create |
| Delete | Delete |
| Get | Get |
| List | List |
| Update | Update |
| - | Train |
| - | Get Training Status |

La tabella precedente contiene un confronto delle operazioni a livello di elenco tra FaceList e LargeFaceList.
Come illustrato, LargeFaceList include nuove operazioni, Train e Get Training Status, rispetto a FaceList.
Il recupero dell'elemento LargeFaceList sottoposto a training è una precondizione dell'operazione [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), mentre non è richiesta alcuna operazione Train per FaceList.
Il frammento di codice seguente è una funzione helper per eseguire il training di un elemento LargeFaceList.

```CSharp
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
    await FaceServiceClient.TrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await FaceServiceClient.GetLargeFaceListTrainingStatusAsync(largeFaceListId);

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

In precedenza, un uso tipico di FaceList con l'aggiunta di visi sarebbe stato il seguente:

```CSharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateFaceListAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToFaceListAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

Quando si esegue la migrazione a LargeFaceList, il frammento di codice diventa il seguente:

```CSharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateLargeFaceListAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToLargeFaceListAsync(LargeFaceListId, stream);
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
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

Come illustrato in precedenza, la gestione dei dati e la parte FindSimilar sono quasi le stesse.
L'unica eccezione è data dal fatto che, per il funzionamento di FindSimilar, è necessario completare un'operazione Train di analisi preliminare in LargeFaceList.

## <a name="train"></a>Passaggio 3: Suggerimento per l'operazione Train

Anche se l'operazione Train velocizza [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e [Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), l'impatto sui tempi di training è negativo, soprattutto su larga scala.
I tempi di training stimati per dimensioni diverse sono elencati nella tabella seguente:

| Dimensione (visi o persone) | Tempo di training stimato |
|:---:|:---:|
| 1.000 | 1-2 s |
| 10.000 | 5-10 s |
| 100.000 | 1-2 min |
| 1.000.000 | 10-30 min |

Per usare al meglio la funzionalità su larga scala, è consigliabile prendere in considerazione alcune strategie.

## <a name="interval"></a>Passaggio 3.1: Personalizzare l'intervallo di tempo

Come illustrato in `TrainLargeFaceList()`, è presente un valore `timeIntervalInMilliseconds` per ritardare il processo di verifica dello stato di training infinito.
Per l'elemento LargeFaceList con più visi, l'uso di un intervallo maggiore riduce il numero e il costo delle chiamate.
L'intervallo di tempo deve essere personalizzato in base alla capacità prevista di LargeFaceList.

La stessa strategia si applica anche a LargePersonGroup.
Ad esempio, quando si esegue il training di un elemento LargePersonGroup con 1.000.000 persone, il valore `timeIntervalInMilliseconds` può essere pari a 60.000, ovvero un intervallo di 1 minuto.

## <a name="buffer"></a>Passaggio 3.2: Buffer su piccola scala

È possibile eseguire la ricerca di persone/visi in LargePersonGroup/LargeFaceList solo dopo che ne è stato eseguito il training.
In uno scenario dinamico nuovi visi/persone vengono continuamente aggiunti e deve essere immediatamente possibile eseguirne la ricerca. Il training può pertanto richiedere più tempo rispetto a quello desiderato.
Per attenuare questo problema, è possibile usare elementi LargePersonGroup/LargeFaceList di dimensioni molto piccole come buffer solo per le voci appena aggiunte.
Grazie alle dimensioni ridotte, tale buffer richiede un tempo di training minore e la ricerca immediata in questo buffer temporaneo dovrebbe funzionare in modo corretto.
Usare il buffer in combinazione con il training negli elementi LargePersonGroup/LargeFaceList principali eseguendo il training principale a intervalli meno frequenti, ad esempio a mezzanotte e giornalmente.

Di seguito viene indicato un flusso di lavoro di esempio:
1. Creare un elemento LargePersonGroup/LargeFaceList principale (raccolta principale) e un elemento LargePersonGroup/LargeFaceList buffer (raccolta buffer). La raccolta buffer è relativa solo alle persone e ai visi nuovi aggiunti.
1. Aggiungere nuovi visi/persone sia alla raccolta principale che alla raccolta buffer.
1. Eseguire solo il training della raccolta con un breve intervallo di tempo per verificare che le voci appena aggiunte diventino effettive.
1. Chiamare le operazioni Identification/FindSimilar sia sulla raccolta principale che sulla raccolta buffer e unire i risultati.
1. Quando le dimensioni della raccolta buffer aumentano fino a una soglia stabilita o causano un tempo di inattività del sistema, creare una nuova raccolta buffer e attivare il training nella raccolta principale.
1. Eliminare la raccolta buffer precedente dopo il completamento del training nella raccolta principale.

## <a name="standalone"></a>Passaggio 3.3: Training autonomo

Se una latenza relativamente lunga è accettabile, non è necessario attivare l'operazione Train subito dopo l'aggiunta di nuovi dati.
Tale operazione può essere invece divisa dalla logica principale e attivata regolarmente.
Questa strategia è adatta agli scenari dinamici con una latenza accettabile e può essere applicata a scenari statici per ridurre ulteriormente la frequenza dell'operazione Train.

Si supponga che sia disponibile una funzione `TrainLargePersonGroup` analoga alla funzione `TrainLargeFaceList`.
Un'implementazione tipica del training autonomo su LargePersonGroup richiamando la classe [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) in `System.Timers` sarebbe la seguente:

```CSharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    FaceServiceClient.CreateLargePersonGroupAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Setup a standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1 minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1 hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces and Identification except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

Per altre informazioni sulla gestione dei dati e sulle implementazioni correlate all'identificazione, vedere [How to Add Faces](how-to-add-faces.md) (Come aggiungere visi) e [How to Identify Faces in Image](HowtoIdentifyFacesinImage.md) (Come identificare visi nelle immagini).

## <a name="summary"></a> Riepilogo

In questa guida si è appreso come eseguire la migrazione del codice PersonGroup/FaceList esistente (non di dati) a LargePersonGroup/LargeFaceList:

- LargePersonGroup e LargeFaceList funzionano in modo analogo a PersonGroup/FaceList, ad eccezione del fatto che per LargeFaceList è richiesta l'operazione Train.
- Per aggiornare i dati dinamici per set di dati su larga scala, adottare la strategia di training corretta.

## <a name="related"></a> Argomenti correlati

- [How to Identify Faces in Image](HowtoIdentifyFacesinImage.md) (Come identificare visi nelle immagini)
- [How to Add Faces](how-to-add-faces.md) (Come aggiungere visi)
