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
ms.date: 08/28/2019
ms.author: wolfma
ms.openlocfilehash: cf5bf3dfd7b6a408179bb267156433168e562a8e
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326841"
---
# <a name="tutorial-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Esercitazione: Riconoscere le finalità dai contenuti vocali con Speech SDK per C#

I servizi cognitivi [Speech SDK](speech-sdk.md) si integrano con il servizio [Language Understanding Intelligent Service (LUIS)](https://www.luis.ai/home) per fornire il **riconoscimento finalità**. Una finalità è qualcosa che l'utente desidera fare: prenotare un volo, controllare il meteo o effettuare una chiamata. L'utente può usare qualunque termine suoni naturale. Con l'uso di machine learning, LUIS esegue il mapping delle richieste degli utenti per le finalità che sono state definite.

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

Prima di iniziare questa esercitazione, è necessario disporre degli elementi seguenti:

* Account LUIS. È possibile ottenerne uno gratuitamente tramite il [portale di LUIS](https://www.luis.ai/home).
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (qualsiasi edizione).

## <a name="luis-and-speech"></a>LUIS e riconoscimento vocale

LUIS si integra con i servizi Voce per distinguere le finalità dai contenuti vocali. Non è necessaria una sottoscrizione ai servizi Voce, LUIS è sufficiente.

LUIS usa due tipi di chiave:

|Tipo di chiave|Scopo|
|--------|-------|
|Creazione|Consente di creare e modificare a livello di codice app LUIS|
|Endpoint |Autorizza l'accesso a una particolare app LUIS|

Per questa esercitazione è necessario il tipo di chiave dell'endpoint. L'esercitazione usa l'esempio dell'app di domotica LUIS, che è possibile creare seguendo l'avvio rapido [Usare l'app di domotica predefinita](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app). Se è stata creata un'app LUIS personalizzata, è possibile invece usare quest'ultima.

Quando si crea un'app LUIS, viene generata automaticamente una chiave di avvio in modo da testare l'app usando le query di testo. Questa chiave non abilita l'integrazione dei servizi Voce e non funziona con questa esercitazione. Creare una risorsa LUIS nel dashboard di Azure e assegnarla all'app LUIS. È possibile usare il livello di sottoscrizione gratuita per questa esercitazione.

Dopo aver creato la risorsa LUIS nel dashboard di Azure, accedere al [portale LUIS](https://www.luis.ai/home), scegliere l'applicazione nella pagina **App personali**, quindi passare alla pagina **Gestione** dell'app. Infine, selezionare **Chiavi ed endpoint** nella barra laterale.

![Chiavi del portale LUIS e impostazioni di endpoint](media/sdk/luis-keys-endpoints-page.png)

Nella pagina **Impostazioni chiavi ed endpoint**:

1. Scorrere verso il basso fino alla sezione **Risorse e chiavi** e selezionare **Assegnare risorsa**.
1. Nella finestra di dialogo **Assegnare una chiave all'app** apportare le modifiche seguenti:

   * In **Tenant** scegliere **Microsoft**.
   * In **Nome della sottoscrizione** scegliere la sottoscrizione di Azure che contiene la risorsa LUIS che si vuole usare.
   * In **Chiave** scegliere la risorsa LUIS che si vuole usare con l'app.

   A breve, la nuova sottoscrizione viene visualizzata nella tabella nella parte inferiore della pagina. 

1. Selezionare l'icona accanto a una chiave per copiarla negli Appunti. (È possibile usare una qualsiasi chiave.)

![Chiavi di sottoscrizione dell'app LUIS](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Creare un progetto di sintesi vocale in Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>Aggiungere il codice

Aggiungere il codice al progetto.

1. In **Esplora soluzioni** aprire il file **Program.cs**.

1. Sostituire il blocco di istruzioni `using` all'inizio del file con le dichiarazioni seguenti:

   [!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

1. All'interno del metodo `Main()` fornito aggiungere il codice seguente:

   ```csharp
   RecognizeIntentAsync().Wait();
   Console.WriteLine("Please press Enter to continue.");
   Console.ReadLine();
   ```

1. Creare un metodo asincrono vuoto `RecognizeIntentAsync()`, come illustrato di seguito:

   ```csharp
   static async Task RecognizeIntentAsync()
   {
   }
   ```

1. Nel corpo di questo nuovo metodo aggiungere questo codice:

   [!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

1. Sostituire i segnaposto in questo metodo con la chiave di sottoscrizione LUIS, l'area e l'ID dell'app come indicato di seguito.

   |Placeholder|Sostituire con|
   |-----------|------------|
   |`YourLanguageUnderstandingSubscriptionKey`|La propria chiave di endpoint LUIS. Anche in questo caso, è necessario ottenere questo elemento dal dashboard di Azure, non da una "chiave di avvio". È possibile trovarla nella pagina **Chiavi ed endpoint** dell'app (in **Gestione**) nel [portale LUIS](https://www.luis.ai/home).|
   |`YourLanguageUnderstandingServiceRegion`|L'identificatore breve indica la propria area di sottoscrizione LUIS, ad esempio `westus` per Stati Uniti occidentali. Vedere [Tutte le aree](regions.md).|
   |`YourLanguageUnderstandingAppId`|L'ID dell'app LUIS. È possibile trovarlo nella pagina **Impostazioni** dell'app nel [portale LUIS](https://www.luis.ai/home).|

Apportate queste modifiche, è possibile compilare (**CTRL+MAIUSC+B**) ed eseguire (**F5**) l'applicazione di esercitazione. Quando richiesto, provare a dire "Spegni le luci" nel microfono del PC. L'applicazione visualizza il risultato nella finestra della console.

Le sezioni seguenti includono una discussione del codice.

## <a name="create-an-intent-recognizer"></a>Creare un sistema di riconoscimento delle finalità

In primo luogo, è necessario creare una configurazione per il riconoscimento vocale dalla chiave e dall'area dell'endpoint LUIS. È possibile usare le configurazioni del riconoscimento vocale per creare sistemi di riconoscimento per le diverse funzionalità di Speech SDK. La configurazione di riconoscimento vocale specifica in diversi modi la sottoscrizione da usare. In questo caso, usare `FromSubscription`, che accetta la chiave e l'area della sottoscrizione.

> [!NOTE]
> Usare la chiave e l'area della sottoscrizione LUIS, non di una sottoscrizione dei servizi Voce.

Creare un sistema di riconoscimento delle finalità tramite `new IntentRecognizer(config)`. La configurazione conosce già quale sottoscrizione usare, non serve specificare nuovamente la chiave di sottoscrizione e l'endpoint quando si crea un sistema di riconoscimento.

## <a name="import-a-luis-model-and-add-intents"></a>Importare un modello LUIS e aggiungere le finalità

Ora importare il modello dalla app LUIS tramite `LanguageUnderstandingModel.FromAppId()` e aggiungere le finalità di LUIS che si desidera riconoscere tramite il metodo del sistema di riconoscimento `AddIntent()`. Questi due passaggi migliorano l'accuratezza del riconoscimento vocale, indicando le parole che l'utente, con probabilità, usa nelle richieste. Non è necessario aggiungere tutte le finalità dell'app se non è necessario riconoscerle tutte nella propria applicazione.

Per aggiungere le finalità, è necessario specificare tre argomenti: il modello LUIS (che è stato creato e denominato `model`), il nome della finalità e un ID finalità. La differenza tra l'ID e il nome è come indicato di seguito.

|Argomento `AddIntent()`&nbsp;|Scopo|
|--------|-------|
|intentName|Il nome delle finalità va in base a quanto definito nell'app LUIS. Questo valore deve corrispondere esattamente al nome finalità LUIS.|
|intentID|ID assegnato a una finalità riconosciuta da Speech SDK. Questo valore può essere un qualsiasi valore; non è necessario che corrisponda al nome finalità come definito nell'app LUIS. Se più finalità vengono gestite tramite lo stesso codice, ad esempio, è possibile usare per queste lo stesso ID.|

La app LUIS di domotica presenta due finalità: una per accendere il dispositivo e l'altra per spegnerlo. Le righe in basso aggiungono queste finalità al sistema di riconoscimento; sostituire le tre linee `AddIntent` nel metodo `RecognizeIntentAsync()` con questo codice.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

Anziché aggiungere singole finalità, è anche possibile usare il metodo `AddAllIntents` per aggiungere tutte le finalità in un modello al sistema di riconoscimento.

## <a name="start-recognition"></a>Avviare il riconoscimento

Con il riconoscimento creato e le finalità aggiunte, è possibile iniziare il riconoscimento. Speech SDK supporta sia il riconoscimento singolo che quello continuo.

|Modalità di riconoscimento|Metodi di chiamata|Risultato|
|----------------|-----------------|---------|
|Singolo|`RecognizeOnceAsync()`|Restituisce la finalità riconosciuta, se presente, dopo una singola espressione.|
|Continuo|`StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()`|Riconosce più espressioni; genera eventi (ad esempio, `IntermediateResultReceived`) quando i risultati sono disponibili.|

L'applicazione di esercitazione usa la modalità singola e quindi chiama `RecognizeOnceAsync()` per iniziare il riconoscimento. Il risultato è un oggetto `IntentRecognitionResult` contenente le informazioni sulla finalità riconosciuta. La risposta LUIS JSON viene estratta mediante l'espressione seguente:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

L'applicazione di esercitazione non analizza il risultato JSON. Viene visualizzato solo il testo JSON nella finestra della console.

![Risultati del singolo riconoscimento LUIS](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Specificare lingua di riconoscimento

Per impostazione predefinita, LUIS riconosce le finalità in inglese Americano (`en-us`). Tramite l'assegnazione di un codice impostazioni locali alla proprietà `SpeechRecognitionLanguage` della configurazione del riconoscimento vocale, questa è in grado di riconoscere le finalità in altre lingue. Ad esempio, aggiungere `config.SpeechRecognitionLanguage = "de-de";` nell'applicazione dell'esercitazione prima di creare il sistema di riconoscimento per riconoscere le finalità in tedesco. Per altre informazioni, vedere [Linguaggi supportati](language-support.md#speech-to-text).

## <a name="continuous-recognition-from-a-file"></a>Riconoscimento vocale continuo da un file

Il codice seguente illustra due funzionalità aggiuntive del riconoscimento finalità tramite Speech SDK. Il primo, menzionato in precedenza, è il riconoscimento continuo, in cui il sistema di riconoscimento genera eventi quando sono disponibili i risultati. Questi eventi possono quindi essere elaborati dai gestori eventi forniti. Con il riconoscimento continuo, si effettua una chiamata al metodo`StartContinuousRecognitionAsync()` del sistema di riconoscimento per avviare il riconoscimento anziché chiamare `RecognizeOnceAsync()`.

L'altra funzionalità legge l'audio che contiene il riconoscimento vocale per essere elaborato da un file WAV. L'implementazione comporta la creazione di una configurazione audio che può essere usata quando si crea il riconoscimento finalità. Il file deve essere singolo canale (mono) con una frequenza di campionamento di 16 kHz.

Per provare queste funzionalità, eliminare o impostare come commento il corpo del metodo `RecognizeIntentAsync()` e sostituirlo con il codice seguente.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Aggiornare il codice per includere la propria chiave endpoint LUIS, la regione e l'ID dell'app per aggiungere le finalità Automazione home, come visto in precedenza. Modificare `whatstheweatherlike.wav` nel nome del proprio file audio registrato. Quindi compilare, copiare il file audio nella directory di compilazione ed eseguire l'applicazione.

Ad esempio, se si dice "Spegni le luci", pausa e quindi "Accendi le luci" nel file audio registrato, è possibile che venga visualizzato un output della console simile al seguente:

![Risultati del riconoscimento LUIS del file audio](media/sdk/luis-results-2.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Cercare il codice usato in questo articolo nella cartella **samples/csharp/sharedcontent/console**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Riconoscimento vocale](quickstart-csharp-dotnetcore-windows.md)
