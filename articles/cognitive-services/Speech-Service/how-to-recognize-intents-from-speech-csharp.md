---
title: 'Esercitazione: Riconoscere le finalità dai contenuti vocali con Speech SDK per C#'
titleSuffix: Azure Cognitive Services
description: Questa esercitazione fornirà informazioni su come riconoscere le finalità dai contenuti vocali con Speech SDK per C#.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: a9b3d8a2670a0b4e6bed2d5e9a9b64e597adcb16
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57855725"
---
# <a name="tutorial-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Esercitazione: Riconoscere le finalità dai contenuti vocali con Speech SDK per C#

[!INCLUDE [Article selector](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-selector.md)]

I servizi cognitivi [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) si integrano con il servizio [Language Understanding Intelligent Service (LUIS)](https://www.luis.ai/home) per fornire il **riconoscimento finalità.** Una finalità è qualcosa che l'utente desidera fare: prenotare un volo, controllare il meteo o effettuare una chiamata. L'utente può usare qualunque termine suoni naturale. Con l'uso di machine learning, LUIS esegue il mapping delle richieste degli utenti per le finalità che sono state definite.

> [!NOTE]
> Un'applicazione LUIS definisce le finalità e le entità che si desidera riconoscere. È separata dall'applicazione C# che usa il servizio di riconoscimento vocale. In questo articolo, "app" sta per app LUIS, mentre "applicazione" sta per codice C#.

In questa esercitazione, si usa Speech SDK per sviluppare un'applicazione console C# che ricava le finalità da espressioni dell'utente tramite il microfono del dispositivo. Si apprenderà come:

> [!div class="checklist"]
> * Creare un progetto di Visual Studio che fa riferimento al pacchetto Speech SDK NuGet
> * Creare una configurazione di riconoscimento vocale e ricevere un sistema di riconoscimento delle finalità
> * Ottenere il modello per l'app LUIS e aggiungere la finalità necessarie
> * Specificare la lingua per il riconoscimento vocale
> * Riconoscere la sintesi vocale da un file
> * Usare riconoscimento asincrono continuo basato su eventi

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* Account LUIS. È possibile ottenerne uno gratuitamente tramite il [portale di LUIS](https://www.luis.ai/home).
* Visual Studio 2017, qualsiasi edizione.

## <a name="luis-and-speech"></a>LUIS e riconoscimento vocale

LUIS si integra con i servizi Voce per distinguere le finalità dai contenuti vocali. Non è necessaria una sottoscrizione ai servizi Voce, LUIS è sufficiente.

LUIS usa due tipi di chiave:

|Tipo di chiave|Scopo|
|--------|-------|
|creazione|consente di creare e modificare a livello di codice app LUIS in maniera programmatica|
|endpoint |autorizza l'accesso a una particolare app LUIS|

La chiave di endpoint è la chiave LUIS necessaria per questa esercitazione. Questa esercitazione usa l'esempio dell'app Automazione home di LUIS, che è possibile creare seguendo [Usare l'app predefinita Automazione home](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app). Se è stata creata un'app LUIS personalizzata, è possibile invece usare quest'ultima.

Quando si crea un'app LUIS, viene generata automaticamente una chiave di avvio in modo da testare l'app usando le query di testo. Questa chiave non attiva l'integrazione dei servizi Voce e non funziona con questa esercitazione. È necessario creare una risorsa di LUIS nella dashboard di Azure e assegnarla all'app LUIS. È possibile usare il livello di sottoscrizione gratuita per questa esercitazione.

Dopo aver creato la risorsa di LUIS nella dashboard di Azure, accedere al [portale di LUIS](https://www.luis.ai/home), scegliere l'applicazione dalla pagina delle app personali, quindi passare alla pagina di gestione dell'app. Infine, fare clic su **Chiavi ed endpoint** nella barra laterale.

![Chiavi del portale LUIS e impostazioni di endpoint](media/sdk/luis-keys-endpoints-page.png)

Nella pagina delle impostazioni di Chiavi ed endpoint:

1. Scorrere verso il basso fino alla sezione Risorse e chiavi e fare clic su **Assegnare risorse**.
1. Nella finestra di dialogo **Assegnare una chiave all'app**, scegliere le opzioni seguenti:

    * Scegliere Microsoft come tenant.
    * In Nome della sottoscrizione, scegliere la sottoscrizione di Azure che contiene la risorsa di LUIS che si desidera usare.
    * In Chiave, scegliere la risorsa di LUIS che si desidera usare con l'app.

A breve, la nuova sottoscrizione viene visualizzata nella tabella nella parte inferiore della pagina. Fare clic sull'icona accanto a una chiave per copiarla negli Appunti. (È possibile usare una qualsiasi chiave.)

![Chiavi di sottoscrizione dell'app LUIS](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Creare un progetto di sintesi vocale in Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>Aggiungere il codice

Aprire il file `Program.cs` nel progetto di Visual Studio e sostituire il blocco di `using` istruzioni all'inizio del file con le seguenti dichiarazioni.

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

All'interno del `Main()` metodo fornito, aggiungere il codice seguente.

```csharp
RecognizeIntentAsync().Wait();
Console.WriteLine("Please press Enter to continue.");
Console.ReadLine();
```

Creare un metodo asincrono vuoto `RecognizeIntentAsync()`, come illustrato di seguito.

```csharp
static async Task RecognizeIntentAsync()
{
}
```

Nel corpo di questo nuovo metodo, aggiungere questo codice.

[!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

Sostituire i segnaposto in questo metodo con la chiave di sottoscrizione LUIS, l'area e l'ID dell'app come indicato di seguito.

|Placeholder|Sostituire con|
|-----------|------------|
|`YourLanguageUnderstandingSubscriptionKey`|La propria chiave di endpoint LUIS. Come indicato in precedenza, deve trattarsi di una chiave ottenuta dalla dashboard di Azure, non di una "chiave di avvio". È possibile trovarla nella pagina Chiavi ed endpoint dell'app (in Gestione) nel [portale di LUIS](https://www.luis.ai/home).|
|`YourLanguageUnderstandingServiceRegion`|L'identificatore breve indica la propria area di sottoscrizione LUIS, ad esempio `westus` per Stati Uniti occidentali. Vedere [Tutte le aree](regions.md).|
|`YourLanguageUnderstandingAppId`|L'ID dell'app LUIS. È possibile trovarlo nella pagina delle impostazioni dell'app del [portale LUIS](https://www.luis.ai/home).|

Apportate queste modifiche, è possibile compilare (CTRL-MAIUSC-B) ed eseguire (F5) l'applicazione di esercitazione. Quando richiesto, provare a formulare "Spegni le luci" nel microfono del PC. Il risultato viene visualizzato nella finestra della console.

Le sezioni seguenti includono una discussione del codice.


## <a name="create-an-intent-recognizer"></a>Creare un sistema di riconoscimento delle finalità

Il primo passaggio del riconoscimento finalità nel riconoscimento vocale consiste nel creare una configurazione di riconoscimento vocale dalla chiave endpoint e area LUIS. Configurazione riconoscimento vocale può essere usata per creare sistemi di riconoscimento per le diverse funzionalità di Speech SDK. La configurazione di riconoscimento vocale specifica in diversi modi la sottoscrizione da usare; In questo caso, usare `FromSubscription`, che accetta la chiave di sottoscrizione e la regione.

> [!NOTE]
> Usare la chiave e l'area della sottoscrizione LUIS, non di una sottoscrizione dei servizi Voce.

Creare un sistema di riconoscimento delle finalità tramite `new IntentRecognizer(config)`. La configurazione conosce già quale sottoscrizione usare, non serve specificare nuovamente la chiave di sottoscrizione e l'endpoint quando si crea un sistema di riconoscimento.

## <a name="import-a-luis-model-and-add-intents"></a>Importare un modello LUIS e aggiungere le finalità

Ora importare il modello dalla app LUIS tramite `LanguageUnderstandingModel.FromAppId()` e aggiungere le finalità di LUIS che si desidera riconoscere tramite il metodo del sistema di riconoscimento `AddIntent()`. Questi due passaggi migliorano l'accuratezza del riconoscimento vocale, indicando le parole che l'utente, con probabilità, usa nelle richieste. Non è necessario aggiungere tutte le finalità dell'applicazione se non è necessario riconoscerle tutte nella propria applicazione.

L'aggiunta di finalità richiede tre argomenti: il modello LUIS (che è stato appena creato e denominato `model`), il nome della finalità e un ID finalità. La differenza tra l'ID e il nome è come indicato di seguito.

|`AddIntent()` argomento|Scopo|
|--------|-------|
|intentName |Il nome delle finalità va in base a quanto definito nell'app LUIS. Deve corrispondere esattamente al nome finalità di LUIS.|
|intentID    |ID assegnato a una finalità riconosciuta da Speech SDK. Può essere uno qualsiasi; non è necessario che corrisponda al nome finalità, come definito nell'app LUIS. Se più finalità vengono gestite tramite lo stesso codice, ad esempio, è possibile usare per queste lo stesso ID.|

La app Automazione home di LUIS presenta due finalità: una per accendere il dispositivo e l'altra per spegnerlo. Le righe in basso aggiungono queste finalità al sistema di riconoscimento; sostituire le tre linee `AddIntent` nel metodo `RecognizeIntentAsync()` con questo codice.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

## <a name="start-recognition"></a>Avviare il riconoscimento

Con il riconoscimento creato e le finalità aggiunte, è possibile iniziare il riconoscimento. Speech SDK supporta sia il riconoscimento singolo che quello continuo.

|Modalità di riconoscimento|Metodi di chiamata|Risultato|
|----------------|-----------------|---------|
|Singolo|`RecognizeOnceAsync()`|Restituisce la finalità riconosciuta, se presente, dopo una singola espressione.|
|Continuo|`StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()`|Riconosce più espressioni. Genera eventi (ad esempio `IntermediateResultReceived`) quando sono disponibili. i risultati.|

L'applicazione di esercitazione usa la modalità singola e quindi chiama `RecognizeOnceAsync()` per iniziare il riconoscimento. Il risultato è un oggetto `IntentRecognitionResult` contenente le informazioni sulla finalità riconosciuta. La risposta JSON di LUIS viene estratta dalla seguente espressione:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

L'applicazione di esercitazione non analizza il risultato JSON, ma lo visualizza nella finestra della console.

![Risultati del riconoscimento LUIS](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Specificare lingua di riconoscimento

Per impostazione predefinita, LUIS riconosce le finalità in inglese Americano (`en-us`). Tramite l'assegnazione di un codice impostazioni locali alla proprietà `SpeechRecognitionLanguage` della configurazione del riconoscimento vocale, questa è in grado di riconoscere le finalità in altre lingue. Ad esempio, aggiungere `config.SpeechRecognitionLanguage = "de-de";` nell'applicazione dell'esercitazione prima di creare il sistema di riconoscimento per riconoscere le finalità in tedesco. Vedere [Lingue supportate](language-support.md#speech-to-text).

## <a name="continuous-recognition-from-a-file"></a>Riconoscimento vocale continuo da un file

Il codice seguente illustra due funzionalità aggiuntive del riconoscimento finalità tramite Speech SDK. Il primo, menzionato in precedenza, è il riconoscimento continuo, in cui il sistema di riconoscimento genera eventi quando sono disponibili i risultati. Questi eventi possono quindi essere elaborati dai gestori eventi forniti. Con il riconoscimento continuo, si effettua una chiamata al sistema di riconoscimento `StartContinuousRecognitionAsync()` per avviare il riconoscimento al posto di `RecognizeOnceAsync()`.

L'altra funzionalità legge l'audio che contiene il riconoscimento vocale per essere elaborato da un file WAV. Ciò comporta la creazione di una configurazione audio che può essere usata quando si crea il riconoscimento finalità. Il file deve essere singolo canale (mono) con una frequenza di campionamento di 16 kHz.

Per provare queste funzionalità, sostituire il corpo del metodo `RecognizeIntentAsync()` con il codice seguente.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Aggiornare il codice per includere la propria chiave endpoint LUIS, la regione e l'ID dell'app per aggiungere le finalità Automazione home, come visto in precedenza. Modificare `whatstheweatherlike.wav` nel nome del proprio file audio. Quindi compilare ed eseguire.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Cercare il codice usato in questo articolo nella cartella samples/csharp/sharedcontent/console.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Riconoscimento vocale](how-to-recognize-speech-csharp.md)
