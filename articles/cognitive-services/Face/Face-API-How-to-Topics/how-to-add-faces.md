---
title: Aggiungere visi con l'API Viso | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Usare l'API Viso in Servizi cognitivi per aggiungere visi nelle immagini.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 3306c13d6c3d231ddbda38cfcbc5419fcdbd30db
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372993"
---
# <a name="how-to-add-faces"></a>Come aggiungere visi

Questa guida illustra la procedura consigliata per aggiungere un numero elevato di persone e visi in un PersonGroup.
La stessa strategia si applica anche a FaceList e LargePersonGroup.
Gli esempi sono scritti in C# usando la libreria client dell'API Viso.

## <a name="step1"></a> Passaggio 1: Inizializzazione

Vengono dichiarate più variabili e viene implementata una funzione helper per pianificare le richieste.

- `PersonCount` è il numero totale di persone.
- `CallLimitPerSecond` è il numero massimo di chiamate al secondo in base al livello di sottoscrizione.
- `_timeStampQueue` è una coda per registrare i timestamp delle richieste.
- `await WaitCallLimitPerSecondAsync()` imposta un'attesa fino a quando non viene confermata la validità dell'invio della richiesta successiva.

```CSharp
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

## <a name="step2"></a> Passaggio 2: Autorizzare la chiamata all'API

Quando si usa una libreria client, la chiave di sottoscrizione viene passata tramite il costruttore della classe FaceServiceClient. Ad esempio: 

```CSharp
FaceServiceClient faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

La chiave di sottoscrizione può essere ottenuta nella pagina Marketplace del portale di Azure. Vedere [Sottoscrizioni](https://www.microsoft.com/cognitive-services/en-us/sign-up).

## <a name="step3"></a> Passaggio 3: Creare il PersonGroup

Per salvare le persone viene creato un PersonGroup denominato "MyPersonGroup".
L'ora della richiesta viene accodata in `_timeStampQueue` per garantire la convalida generale.

```CSharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step4"></a> Passaggio 4: Creare le persone nel PersonGroup

Le persone vengono create contemporaneamente e viene anche applicata l'attesa `await WaitCallLimitPerSecondAsync()` per evitare il superamento del limite di chiamate.

```CSharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceServiceClient.CreatePersonAsync(personGroupId, personName);
});
```

## <a name="step5"></a> Passaggio 5: Aggiungere i visi per le persone

L'aggiunta di visi per le diverse persone viene elaborata in contemporanea, mentre per una persona specifica è un processo in sequenza.
Anche in questo caso viene richiamata la funzione `await WaitCallLimitPerSecondAsync()` per assicurarsi che la frequenza delle richieste rientri nei limiti previsti.

```CSharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceServiceClient.AddPersonFaceAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a> Riepilogo

In questa guida si è appreso il processo di creazione di un PersonGroup con un numero elevato di persone e visi. Alcuni promemoria:

- La stessa strategia si applica anche a FaceList e LargePersonGroup.
- L'aggiunta/eliminazione di visi in più FaceList o Person in LargePersonGroup può essere elaborata in contemporanea.
- Le stesse operazioni per un'istanza specifica di FaceList o Person in LargePersonGroup devono essere eseguite in modo sequenziale.
- Per semplicità, in questa guida viene omessa la gestione delle potenziali eccezioni. Per ottenere una maggiore affidabilità, è consigliabile applicare criteri di ripetizione appropriati.

Di seguito è riportata una rapida sintesi delle funzionalità illustrate e dimostrate in precedenza:

- Creazione di PersonGroup con l'API [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) (PersonGroup - Creazione)
- Creazione di persone con l'API[PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) (PersonGroup Person - Creazione)
- Aggiunta di visi per le persone con l'API [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person - Aggiunta viso)

## <a name="related"></a> Argomenti correlati
- [Come identificare visi nelle immagini](HowtoIdentifyFacesinImage.md)
- [Come rilevare visi nelle immagini](HowtoDetectFacesinImage.md)
- [Come usare la funzionalità su larga scala](how-to-use-large-scale.md)
