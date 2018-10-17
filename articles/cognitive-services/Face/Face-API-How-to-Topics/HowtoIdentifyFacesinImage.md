---
title: 'Esempio: Identificare i visi nelle immagini - API Viso'
titleSuffix: Azure Cognitive Services
description: Usare l'API Viso per identificare visi nelle immagini.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: a26f7d6057f92fd3ab92405ecca6965dbd6e37ad
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129072"
---
# <a name="example-how-to-identify-faces-in-images"></a>Esempio: Come identificare i visi nelle immagini

Questa guida illustra come identificare visi sconosciuti tramite gruppi di persone (PersonGroup) creati anticipatamente da persone note. Gli esempi sono scritti in C# usando la libreria client dell'API Viso.

## <a name="concepts"></a>Concetti

Se non si ha familiarità con i concetti seguenti in questa guida, cercare le definizioni nel [glossario](../Glossary.md) in qualsiasi momento:

- Viso - Rilevamento
- Viso - Identificazione
- PersonGroup

## <a name="preparation"></a>Operazioni preliminari

In questo esempio vengono dimostrate le operazioni seguenti:

- Come creare un PersonGroup: questo PersonGroup contiene un elenco di persone note.
- Come assegnare i visi a ogni persona: questi visi vengono usati come baseline per identificare le persone. È consigliabile usare visi ben visibili in posizione frontale, come per le foto dei documenti. Un buon set di foto dovrebbe contenere visi della stessa persona in posizioni e con vestiti e pettinature diversi.

Per eseguire la dimostrazione di questo esempio, è necessario preparare un gruppo di immagini:

- Alcune foto con il viso della persona. [Fare clic qui per scaricare foto di esempio](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) per Anna, Bill e Clare.
- Una serie di foto di test, che possono o meno contenere i visi di Anna, Bill o Clare per testare l'identificazione. È anche possibile selezionare alcune immagini di esempio dal collegamento precedente.

## <a name="step-1-authorize-the-api-call"></a>Passaggio 1: Autorizzare la chiamata API

Ogni chiamata all'API Viso richiede una chiave di sottoscrizione. Questa chiave può essere passata tramite un parametro di stringa di query o essere specificata nell'intestazione della richiesta. Per passare la chiave di sottoscrizione tramite una stringa di query, fare riferimento all'URL della richiesta [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Viso - Rilevamento) a titolo di esempio:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

In alternativa, la chiave di sottoscrizione può anche essere specificata nell'intestazione della richiesta HTTP: **ocp-apim-subscription-key: &lt;Chiave sottoscrizione&gt;** Quando si usa una libreria client, la chiave di sottoscrizione viene passata tramite il costruttore della classe FaceServiceClient. Ad esempio: 
 
```CSharp 
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```
 
La chiave di sottoscrizione può essere ottenuta nella pagina Marketplace del portale di Azure. Vedere [Sottoscrizioni](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step-2-create-the-persongroup"></a>Passaggio 2: Creare il PersonGroup

In questo passaggio viene creato un PersonGroup denominato "MyFriends" che contiene tre persone: Anna, Bill e Clare. Per ogni persona sono registrati più visi. I visi devono essere rilevati dalle immagini. Dopo tutti questi passaggi si ottiene un PersonGroup come nell'immagine seguente:

![HowToIdentify1](../Images/group.image.1.jpg)

### <a name="21-define-people-for-the-persongroup"></a>2.1 Definire le persone per il PersonGroup
Una persona è un'unità di base di identificazione. Per una persona possono essere registrati uno o più visi. Tuttavia, un PersonGroup è una raccolta di persone e ogni persona viene definita all'interno di un particolare PersonGroup. L'identificazione viene eseguita in base a un PersonGroup. L'attività prevede quindi la creazione di un PersonGroup e poi la creazione di persone al suo interno, ad esempio Anna, Bill e Clare.

È prima di tutto necessario creare un nuovo PersonGroup. Questa operazione viene eseguita tramite l'API [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) (PersonGroup - Creazione). L'API della libreria client corrispondente è il metodo CreatePersonGroupAsync per la classe FaceServiceClient. L'ID di gruppo specificato per creare il gruppo è univoco per ogni sottoscrizione. È anche possibile ottenere, aggiornare o eliminare PersonGroup usando altre API PersonGroup. Dopo aver definito un gruppo, le persone possono quindi essere definite al suo interno usando l'API [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) (PersonGroup Person - Creazione). Il metodo della libreria client è CreatePersonAsync. È possibile aggiungere visi per ogni persona, dopo averle create.

```CSharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceServiceClient.CreatePersonGroupAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceServiceClient.CreatePersonAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step2-2"></a> 2.2 Rilevare i visi e registrarli per la persona corretta
Il rilevamento viene eseguito inviando una richiesta Web "POST" all'API [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Viso - Rilevamento) con il file di immagine nel corpo della richiesta HTTP. Quando si usa la libreria client, il rilevamento dei visi viene eseguito tramite il metodo DetectAsync per la classe FaceServiceClient.

Per ogni viso rilevato è possibile chiamare [PersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person – Aggiunta viso) per aggiungerlo per la persona corretta.

Il codice seguente illustra il processo di rilevamento di un viso da un'immagine e l'aggiunta del viso per una persona:
```CSharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceServiceClient.AddPersonFaceAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
Si noti che, se l'immagine contiene più visi, viene aggiunto solo il viso più grande. È possibile aggiungere altri visi alla persona passando una stringa nel formato "targetFace=sinistra,alto,larghezza,altezza" al parametro di query targetFace dell'API [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person – Aggiunta viso) o tramite il parametro facoltativo targetFace per il metodo AddPersonFaceAsync per aggiungere altri visi. A ogni viso aggiunto per una persona verrà assegnato un ID viso persistente univoco, che può essere usato in [PersonGroup Person - Delete Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) (PersonGroup Person - Eliminazione viso) e [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (Viso - Identificazione).

## <a name="step-3-train-the-persongroup"></a>Passaggio 3: Eseguire il training del PersonGroup

Il PersonGroup deve essere sottoposto a training prima di poterlo usare per eseguire un'identificazione. Inoltre, è necessario ripetere il training dopo l'aggiunta o rimozione di qualsiasi persona oppure se viene modificato il viso registrato di qualsiasi persona. Il training viene eseguito dall'API [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) (PersonGroup - Training). Quando si usa la libreria client, è richiesta semplicemente una chiamata al metodo TrainPersonGroupAsync:
 
```CSharp 
await faceServiceClient.TrainPersonGroupAsync(personGroupId);
```
 
Il training è un processo asincrono. È possibile che l'operazione non sia terminata anche dopo il completamento dell'esecuzione del metodo TrainPersonGroupAsync. Potrebbe essere necessario richiedere lo stato del training tramite l'API [PersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) (PersonGroup - Recupero stato training) o il metodo GetPersonGroupTrainingStatusAsync della libreria client. Il codice seguente illustra una semplice logica di attesa del completamento del training di PersonGroup:
 
```CSharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceServiceClient.GetPersonGroupTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != Status.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>Passaggio 4: Identificare un viso in base a un PersonGroup definito

Quando si eseguono le identificazioni, l'API Viso può calcolare la somiglianza di un viso di test tra tutti i visi all'interno di un gruppo e restituisce le persone più somiglianti al viso di test. Questa operazione viene eseguita tramite l'API[Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (Viso - Identificazione) o il metodo IdentifyAsync della libreria client.

Il viso di test deve essere rilevato tramite i passaggi precedenti, quindi l'ID viso viene passato all'API di identificazione come secondo argomento. Possono essere identificati contemporaneamente più ID viso e il risultato conterrà tutti i risultati di identificazione. Per impostazione predefinita, l'identificazione restituisce solo una persona che rappresenta la corrispondenza migliore per il viso di test. Se si preferisce, è possibile specificare il parametro facoltativo maxNumOfCandidatesReturned per consentire all'identificazione di restituire più candidati.

Il codice seguente illustra il processo di identificazione:

```CSharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceServiceClient.DetectAsync(s);
    var faceIds = faces.Select(face => face.FaceId).ToArray();
 
    var results = await faceServiceClient.IdentifyAsync(personGroupId, faceIds);
    foreach (var identifyResult in results)
    {
        Console.WriteLine("Result of face: {0}", identifyResult.FaceId);
        if (identifyResult.Candidates.Length == 0)
        {
            Console.WriteLine("No one identified");
        }
        else
        {
            // Get top 1 among all candidates returned
            var candidateId = identifyResult.Candidates[0].PersonId;
            var person = await faceServiceClient.GetPersonAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

Al termine della procedura, è possibile provare a identificare visi diversi e vedere se i visi di Anna, Bill o Clare possono essere identificati correttamente, in base alle immagini caricate per il rilevamento dei visi. Vedere gli esempi seguenti:

![HowToIdentify2](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Passaggio 5: Richiesta di larga scala

Come è noto, un PersonGroup può contenere al massimo 10.000 persone a causa della limitazione della progettazione precedente.
Per altre informazioni sugli scenari con milioni di persone, vedere [Come usare la funzionalità su larga scala](how-to-use-large-scale.md).

## <a name="summary"></a>Summary

In questa guida si è appreso il processo di creazione di un PersonGroup e di identificazione di una persona. Di seguito è riportata una rapida sintesi delle funzionalità illustrate e dimostrate in precedenza:

- Rilevamento dei visi con l'API [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) (Viso - Rilevamento)
- Creazione di PersonGroup con l'API [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) (PersonGroup - Creazione)
- Creazione di persone con l'API[PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) (PersonGroup Person - Creazione)
- Training di un PersonGroup con l'API [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) (PersonGroup - Training)
- Identificazione dei visi sconosciuti in base a un PersonGroup con l'API [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (Viso - Identificazione)

## <a name="related-topics"></a>Argomenti correlati

- [Come rilevare visi nelle immagini](HowtoDetectFacesinImage.md)
- [Come aggiungere visi](how-to-add-faces.md)
- [Come usare la funzionalità su larga scala](how-to-use-large-scale.md)
