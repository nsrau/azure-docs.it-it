---
title: Nozioni di base su Riconoscimento del parlante - Servizio Voce
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare Speech SDK per rispondere alla domanda "chi parla". In questo articolo verranno illustrati i modelli di progettazione comuni per l'utilizzo della verifica e dell'identificazione della voce.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/05/2020
ms.author: trbye
ms.openlocfilehash: b8c38847e3228c54091b84218a5d72c79e3e41a8
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632298"
---
# <a name="learn-the-basics-of-speaker-recognition"></a>Informazioni sulle nozioni di base di Riconoscimento del parlante

Questo articolo illustra i modelli di progettazione di base per Riconoscimento del parlante tramite Speech SDK, tra cui:

* Verifica dipendente dal testo e indipendente dal testo
* Identificazione voce per identificare un campione di voce tra un gruppo di voci
* Eliminazione dei profili vocali

Per informazioni generali sui concetti di Riconoscimento del parlante, vedere l'articolo [Panoramica](speaker-recognition-overview.md).

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](get-started.md).

> [!IMPORTANT]
> La funzionalità Riconoscimento del parlante è attualmente supportata *solo* nelle risorse Voce di Azure create nell'area `westus`.

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Prima di poter eseguire qualsiasi operazione, è necessario installare Speech SDK. A seconda della piattaforma, usare le istruzioni seguenti:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Importare le dipendenze

Per eseguire gli esempi di questo articolo, includere le istruzioni `using` seguenti all'inizio dello script.

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>Creare una configurazione di Voce

Per chiamare il servizio Voce con Speech SDK, è necessario creare una classe [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet). In questo esempio viene creato un oggetto [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) usando una chiave e un'area di sottoscrizione. Si crea anche un codice boilerplate di base da usare per la parte restante di questo articolo, che viene modificato per diverse personalizzazioni.

Si noti che l'area è impostata su `westus`, perché è l'unica area supportata per il servizio.

```csharp
public class Program 
{
    static async Task Main(string[] args)
    {
        // replace with your own subscription key 
        string subscriptionKey = "YourSubscriptionKey";
        string region = "westus";
        var config = SpeechConfig.FromSubscription(subscriptionKey, region);
    }
}
```

## <a name="text-dependent-verification"></a>Verifica dipendente dal testo

Verifica voce è l'atto di confermare che un parlante corrisponde a una voce nota o **registrata**. Il primo passaggio consiste nel **registrare** un profilo vocale, in modo che il servizio disponga di un profilo da confrontare con i campioni di voce futuri. In questo esempio, il profilo viene registrato usando una strategia **dipendente dal testo**, che richiede una passphrase specifica da usare per la registrazione e la verifica. Per un elenco delle passphrase supportate, vedere la [documentazione di riferimento](https://docs.microsoft.com/rest/api/speakerrecognition/).

Per iniziare, creare la funzione seguente nella classe `Program` per registrare un profilo vocale.

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextDependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsCount > 0)
            {
                Console.WriteLine("Speak the passphrase, \"My voice is my passport, verify me.\"");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollments needed: {result.RemainingEnrollmentsCount}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

In questa funzione `await client.CreateProfileAsync()` rappresenta ciò che crea effettivamente il nuovo profilo vocale. Dopo la creazione, è possibile specificare la modalità di input dei campioni audio, usando `AudioConfig.FromDefaultMicrophoneInput()` in questo esempio per acquisire l'audio dal dispositivo di input predefinito. Si registrano quindi i campioni audio in un ciclo `while` che tiene traccia del numero di campioni rimanenti e necessari per la registrazione. In ogni iterazione `client.EnrollProfileAsync(profile, audioInput)` chiederà di pronunciare la passphrase nel microfono e aggiungerà il campione al profilo vocale.

Al termine della registrazione, chiamare `await SpeakerVerify(config, profile, profileMapping)` per eseguire la verifica rispetto al profilo appena creato. Aggiungere un'altra funzione per definire `SpeakerVerify`.

```csharp
public static async Task SpeakerVerify(SpeechConfig config, VoiceProfile profile, Dictionary<string, string> profileMapping)
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerVerificationModel.FromProfile(profile);

    Console.WriteLine("Speak the passphrase to verify: \"My voice is my passport, please verify me.\"");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"Verified voice profile for speaker {profileMapping[result.ProfileId]}, score is {result.Score}");
}
```

In questa funzione viene passato l'oggetto `VoiceProfile` appena creato per inizializzare un modello in base a cui eseguire la verifica. Successivamente, `await speakerRecognizer.RecognizeOnceAsync(model)` richiede di pronunciare di nuovo la passphrase, ma questa volta la convaliderà in base al profilo vocale e restituirà un punteggio di somiglianza compreso tra 0,0 e 1,0. L'oggetto `result` restituisce anche `Accept` o `Reject`, a seconda che la passphrase corrisponda o meno.

Modificare quindi la funzione `Main()` per chiamare le nuove funzioni create. Si noti inoltre che è possibile creare un elemento `Dictionary<string, string>` da passare come riferimento tramite le chiamate di funzione. Questo perché il servizio non consente l'archiviazione di un nome leggibile con un profilo `VoiceProfile` creato e archivia solo un numero ID ai fini della privacy. Nella funzione `VerificationEnroll` aggiungere a questo dizionario una voce con l'ID appena creato, insieme a un nome di testo. Negli scenari di sviluppo di applicazioni in cui è necessario visualizzare un nome leggibile, **è necessario archiviare questo mapping in una posizione, perché il servizio non può archiviarlo**.

```csharp
static async Task Main(string[] args)
{
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    await VerificationEnroll(config, profileMapping);

    Console.ReadLine();
}
```

Eseguire lo script. Verrà richiesto di pronunciare la frase *My voice is my passport, verify me* (La mia voce è il mio passaporto, verificami) tre volte per la registrazione e una volta per la verifica. Il risultato restituito è il punteggio di somiglianza, che può essere usato per creare soglie personalizzate per la verifica.

```shell
Enrolling profile id 87-2cef-4dff-995b-dcefb64e203f.
Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 2

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 1

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 0

Speak the passphrase to verify: "My voice is my passport, verify me."
Verified voice profile for speaker Your Name, score is 0.915581
```

## <a name="text-independent-verification"></a>Verifica indipendente dal testo

A differenza della verifica **dipendente dal testo**, la verifica **indipendente dal testo**:

* Non richiede che venga pronunciata una passphrase specifica, ma è possibile pronunciare le parole desiderate
* Non richiede tre campioni audio, ma *richiede* 20 secondi di audio totale

Apportare un paio di modifiche semplici alla funzione `VerificationEnroll` per passare alla verifica **indipendente dal testo**. Prima di tutto, modificare il tipo di verifica impostandola su `VoiceProfileType.TextIndependentVerification`. Modificare quindi il ciclo `while` per tenere traccia di `result.RemainingEnrollmentsSpeechLength`, che continuerà a richiedere di parlare fino a quando non saranno stati acquisiti 20 secondi di audio.

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
            {
                Console.WriteLine("Continue speaking to add to the profile enrollment sample.");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

Eseguire di nuovo il programma e pronunciare le parole desiderate durante la fase di verifica, perché non è necessaria una passphrase. Anche in questo caso viene restituito il punteggio di somiglianza.

```shell
Enrolling profile id 4tt87d4-f2d3-44ae-b5b4-f1a8d4036ee9.
Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:15.3200000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:09.8100008

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:05.1900000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00.8700000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00

Speak the passphrase to verify: "My voice is my passport, please verify me."
Verified voice profile for speaker Your Name, score is 0.849409
```

## <a name="speaker-identification"></a>Identificazione voce

La funzionalità Identificazione voce viene usata per determinare **chi** parla da un gruppo specifico di voci registrate. Il processo è molto simile a quello di **verifica indipendente dal testo**, con la differenza principale che può eseguire la verifica rispetto a più profili vocali contemporaneamente, anziché rispetto a un singolo profilo.

Creare una funzione `IdentificationEnroll` per registrare più profili vocali. Il processo di registrazione per ogni profilo è uguale al processo di registrazione per la **verifica indipendente dal testo** e richiede 20 secondi di audio per ogni profilo. Questa funzione accetta un elenco di stringhe `profileNames` e creerà un nuovo profilo vocale per ogni nome nell'elenco. La funzione restituisce un elenco di oggetti `VoiceProfile`, che è possibile usare nella funzione successiva per l'identificazione di un parlante.

```csharp
public static async Task<List<VoiceProfile>> IdentificationEnroll(SpeechConfig config, List<string> profileNames, Dictionary<string, string> profileMapping)
{
    List<VoiceProfile> voiceProfiles = new List<VoiceProfile>();
    using (var client = new VoiceProfileClient(config))
    {
        foreach (string name in profileNames)
        {
            using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
            {
                var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentIdentification, "en-us");
                Console.WriteLine($"Creating voice profile for {name}.");
                profileMapping.Add(profile.Id, name);

                VoiceProfileEnrollmentResult result = null;
                while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
                {
                    Console.WriteLine($"Continue speaking to add to the profile enrollment sample for {name}.");
                    result = await client.EnrollProfileAsync(profile, audioInput);
                    Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                    Console.WriteLine("");
                }
                voiceProfiles.Add(profile);
            }
        }
    }
    return voiceProfiles;
}
```

Creare la funzione seguente `SpeakerIdentification` per inviare una richiesta di identificazione. La differenza principale in questa funzione rispetto a una richiesta di  **verifica voce** sta nell'utilizzo di `SpeakerIdentificationModel.FromProfiles()`, che accetta un elenco di oggetti `VoiceProfile`. 

```csharp
public static async Task SpeakerIdentification(SpeechConfig config, List<VoiceProfile> voiceProfiles, Dictionary<string, string> profileMapping) 
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerIdentificationModel.FromProfiles(voiceProfiles);

    Console.WriteLine("Speak some text to identify who it is from your list of enrolled speakers.");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"The most similiar voice profile is {profileMapping[result.ProfileId]} with similiarity score {result.Score}");
}
```

Modificare la funzione `Main()` come segue. Verrà creato un elenco di stringhe `profileNames`, che vengono passate alla funzione `IdentificationEnroll()`. Verrà richiesto di creare un nuovo profilo vocale per ogni nome in questo elenco, in modo da poter aggiungere altri nomi per creare profili aggiuntivi per gli amici o i colleghi.

```csharp
static async Task Main(string[] args)
{
    // replace with your own subscription key 
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    var profileNames = new List<string>() { "Your name", "A friend's name" };
    
    var enrolledProfiles = await IdentificationEnroll(config, profileNames, profileMapping);
    await SpeakerIdentification(config, enrolledProfiles, profileMapping);

    foreach (var profile in enrolledProfiles)
    {
        profile.Dispose();
    }
    Console.ReadLine();
}
```

Eseguire lo script. Verrà richiesto di parlare per registrare i campioni vocali per il primo profilo. Al termine della registrazione, verrà richiesto di ripetere questo processo per ogni nome nell'elenco `profileNames`. Al termine di ogni registrazione, verrà richiesto a **chiunque** di parlare e il servizio tenterà di identificare questa persona tra i profili vocali registrati.

Questo esempio restituisce solo la corrispondenza più vicina e il relativo punteggio di somiglianza, ma è possibile ottenere la risposta completa che include i primi cinque punteggi di somiglianza aggiungendo `string json = result.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)` alla funzione `SpeakerIdentification`.

## <a name="changing-audio-input-type"></a>Modifica del tipo di input audio

Gli esempi in questo articolo usano il microfono del dispositivo predefinito come input per i campioni audio. Tuttavia, negli scenari in cui è necessario usare file audio anziché l'input del microfono, è sufficiente modificare qualsiasi istanza di `AudioConfig.FromDefaultMicrophoneInput()` in `AudioConfig.FromWavFileInput(path/to/your/file.wav)` per passare a un input di file. È anche possibile usare una combinazione di input, ad esempio un microfono per la registrazione e i file per la verifica.

## <a name="deleting-voice-profile-enrollments"></a>Eliminazione delle registrazioni dei profili vocali

Per eliminare un profilo registrato, utilizzare la funzione `DeleteProfileAsync()` sull'oggetto `VoiceProfileClient`. La funzione di esempio seguente mostra come eliminare un profilo vocale da un ID profilo vocale noto.

```csharp
public static async Task DeleteProfile(SpeechConfig config, string profileId) 
{
    using (var client = new VoiceProfileClient(config))
    {
        var profile = new VoiceProfile(profileId);
        await client.DeleteProfileAsync(profile);
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni dettagliate sulle classi e sulle funzioni, vedere la documentazione di riferimento di [Riconoscimento del parlante](https://docs.microsoft.com/rest/api/speakerrecognition/).

* Vedere gli esempi per [C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition) e [C++](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition) in GitHub.

