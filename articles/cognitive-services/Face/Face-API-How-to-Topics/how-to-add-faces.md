---
title: 'Esempio: Aggiungere visi a un oggetto PersonGroup - API Viso'
titleSuffix: Azure Cognitive Services
description: Usare l'API Viso per aggiungere visi nelle immagini.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 0415dcae08c188c1758150c4b8b0df4dee014ce6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448610"
---
# <a name="add-faces-to-a-persongroup"></a>Aggiungere visi a un oggetto PersonGroup

Questa guida illustra la procedura per aggiungere un numero elevato di persone e visi a un oggetto PersonGroup. Questa stessa strategia si applica anche agli oggetti LargePersonGroup, FaceList e LargeFaceList. Questo esempio è scritto in C# usando la libreria client per .NET dell'API Viso di Servizi cognitivi di Azure.

## <a name="step-1-initialization"></a>Passaggio 1: Inizializzazione

Il codice seguente dichiara più variabili e implementa una funzione helper per pianificare le richieste di aggiunta dei visi:

- `PersonCount` è il numero totale di persone.
- `CallLimitPerSecond` è il numero massimo di chiamate al secondo in base al livello di sottoscrizione.
- `_timeStampQueue` è una coda per registrare i timestamp delle richieste.
- `await WaitCallLimitPerSecondAsync()` attende che sia possibile inviare la richiesta successiva.

```csharp
const int PersonCount = 10000;
const int CallLimitPerSecond = 10;
static Queue<DateTime> _timeStampQueue = new Queue<DateTime>(CallLimitPerSecond);

static async Task WaitCallLimitPerSecondAsync()
{
    Monitor.Enter(_timeStampQueue);
    try
    {
        if (_timeStampQueue.Count >= CallLimitPerSecond)
        {
            TimeSpan timeInterval = DateTime.UtcNow - _timeStampQueue.Peek();
            if (timeInterval < TimeSpan.FromSeconds(1))
            {
                await Task.Delay(TimeSpan.FromSeconds(1) - timeInterval);
            }
            _timeStampQueue.Dequeue();
        }
        _timeStampQueue.Enqueue(DateTime.UtcNow);
    }
    finally
    {
        Monitor.Exit(_timeStampQueue);
    }
}
```

## <a name="step-2-authorize-the-api-call"></a>Passaggio 2: Autorizzare la chiamata API

Quando si usa una libreria client, è necessario passare la chiave di sottoscrizione per il costruttore della classe **FaceClient**. Ad esempio:

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

Per ottenere la chiave di sottoscrizione, passare ad Azure Marketplace dal portale di Azure. Per altre informazioni, vedere [Sottoscrizioni](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>Passaggio 3: Creare il PersonGroup

Per salvare le persone viene creato un PersonGroup denominato "MyPersonGroup".
L'ora della richiesta viene accodata in `_timeStampQueue` per garantire la convalida generale.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>Passaggio 4: Creare le persone per l'oggetto PersonGroup

Le persone vengono create contemporaneamente e viene anche applicato il parametro `await WaitCallLimitPerSecondAsync()` per evitare il superamento del limite di chiamate.

```csharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceClient.PersonGroupPerson.CreateAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>Passaggio 5: Aggiungere i visi per le persone

I visi aggiunti a più persone vengono elaborati contemporaneamente. I visi aggiunti a una persona specifica vengono elaborati in sequenza.
Anche in questo caso, viene richiamato il parametro `await WaitCallLimitPerSecondAsync()` per assicurarsi che la frequenza delle richieste rientri nei limiti previsti.

```csharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>Summary

In questa guida si è appreso il processo di creazione di un oggetto PersonGroup con un numero elevato di persone e visi. Alcuni promemoria:

- La stessa strategia si applica anche agli oggetti FaceList e LargePersonGroup.
- L'aggiunta o l'eliminazione di visi da più oggetti FaceList o di persone da oggetti LargePersonGroup viene elaborata contemporaneamente.
- L'aggiunta o l'eliminazione di visi da uno specifico oggetto FaceList o di una persona da un oggetto LargePersonGroup viene elaborata in sequenza.
- Per semplicità, in questa guida viene omessa la spiegazione su come gestire le potenziali eccezioni. Per ottenere maggiore affidabilità, applicare i criteri di ripetizione appropriati.

Sono state spiegate e illustrate le funzionalità seguenti:

- Creazione di oggetti PersonGroup con l'API [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) (PersonGroup - Creazione).
- Creazione di persone con l'API [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) (PersonGroup Person - Creazione).
- Aggiunta di visi alle persone con l'API [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person - Aggiunta viso).

## <a name="related-topics"></a>Argomenti correlati

- [Identificare i visi in un'immagine](HowtoIdentifyFacesinImage.md)
- [Rilevare i visi in un'immagine](HowtoDetectFacesinImage.md)
- [Usare la funzionalità su larga scala](how-to-use-large-scale.md)
