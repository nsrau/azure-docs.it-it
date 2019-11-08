---
title: Creare un modello di tenant (anteprima) - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Generare automaticamente un modello conversione voce/testo personalizzato che usa i dati di Office365 per offrire un riconoscimento vocale ottimale per termini specifici dell'organizzazione che garantisce sia sicurezza che conformità.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: 85b9291ee24c024ebc8ce81ddba46d04f7744081
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504596"
---
# <a name="create-a-tenant-model-preview"></a>Creare un modello di tenant (anteprima)

Il modello di tenant è un servizio di consenso esplicito per clienti aziendali Office365 che genera automaticamente un modello di riconoscimento vocale personalizzato dai dati Office365 dell'organizzazione. Il modello creato è ottimizzato per termini tecnici, gergo e nomi di persone in modo sicuro e conforme.

> [!IMPORTANT]
> Se l'organizzazione esegue la registrazione con il modello di tenant, il servizio di riconoscimento vocale può accedere al modello linguistico dell'organizzazione, generato dalle risorse di Office 365, ad esempio messaggi e-mail e documenti. L'amministratore di Office 365 dell'organizzazione può attivare o disattivare l'uso del modello linguistico dell'organizzazione usando il portale di amministrazione di Office 365.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Eseguire la registrazione per usare un modello di tenant nell'interfaccia di amministrazione di Microsoft 365
> * Ottenere una chiave di sottoscrizione al servizio Voce
> * Creare un modello di tenant
> * Distribuire un modello di tenant
> * Usare un modello di tenant con Speech SDK

![Diagramma del modello di tenant](media/tenant-language-model/tenant-language-model-diagram.png)

## <a name="enroll-using-the-microsoft-365-admin-center"></a>Eseguire la registrazione tramite l'interfaccia di amministrazione di Microsoft 365

Prima di distribuire un modello di tenant, è necessario eseguire la registrazione usando l'interfaccia di amministrazione di Microsoft 365. Questa attività può essere completata solo dall'amministratore di Microsoft 365.

1. Accedere all'[interfaccia di amministrazione di Microsoft 365](https://admin.microsoft.com ).
2. Nel pannello sinistro selezionare **Impostazioni** e quindi **App**.

   ![Diagramma del modello di tenant](media/tenant-language-model/tenant-language-model-enrollment.png)

3. Individuare e selezionare **Azure Speech Services**.

   ![Diagramma del modello di tenant](media/tenant-language-model/tenant-language-model-enrollment-2.png)

4. Fare clic sulla casella di controllo e salvare.

Se è necessario disattivare il modello di tenant, tornare a questa schermata, deselezionare la casella di controllo e salvare.

## <a name="get-a-speech-subscription-key"></a>Ottenere una chiave di sottoscrizione al servizio Voce

Per usare un modello di tenant con Speech SDK, è necessario disporre di una risorsa di riconoscimento vocale e della relativa chiave di sottoscrizione associata.

1. Accedere al [portale di Azure](https://aka.ms/azureportal).
2. Selezionare **Crea una risorsa**.
3. Nella barra di ricerca digitare: **Riconoscimento vocale**.
4. Selezionare **Riconoscimento vocale** e quindi fare clic su **Crea**.
5. Seguire le istruzioni visualizzate per creare la risorsa. Verificare quanto segue:
   * L'opzione **Località** deve essere impostata su **eastus** o **westus**.
   * L'opzione **Piano tariffario** deve essere impostata su **S0**.
6. Fare clic su **Create**(Crea).
7. Dopo alcuni minuti, la risorsa viene creata. La chiave di sottoscrizione è disponibile nella sezione **Panoramica** relativa alla risorsa.

## <a name="create-a-model"></a>Creare il modello

Dopo che l'amministratore ha abilitato il modello di tenant per l'organizzazione, è possibile creare un modello linguistico basato sui dati di Office365.

1. Accedere a [Speech Studio](https://speech.microsoft.com/).
2. Nell'angolo in alto a destra individuare e fare clic sull'icona a forma di ingranaggio (impostazioni) e quindi selezionare **Tenant Model settings** (Impostazioni modello di tenant).

   ![Menu Impostazioni](media/tenant-language-model/tenant-language-settings.png)

3. A questo punto verrà visualizzato un messaggio che informa se si è qualificati per la creazione di un modello di tenant.
   > [!NOTE]
   > I clienti di Office 365 Enterprise nell'America del Nord sono idonei per la creazione di un modello di tenant (inglese). Per i clienti Customer Lockbox (CLB) o Customer Key (CK) questa funzionalità non è disponibile. Per determinare se si è un cliente Customer Lockbox o Customer Key, seguire queste istruzioni:
   > * [Customer Lockbox](https://docs.microsoft.com/office365/securitycompliance/controlling-your-data-using-customer-key#FastTrack)
   > * [Customer Key](https://docs.microsoft.com/microsoft-365/compliance/customer-lockbox-requests)

4. Selezionare quindi **Acconsenti esplicitamente** . Quando il modello di tenant è pronto, si riceve un messaggio e-mail con le istruzioni.

## <a name="deploy-your-model"></a>Distribuire il modello

Quando il modello di tenant è pronto, seguire questa procedura per distribuirlo:

1. Fare clic sul pulsante **View model** (Visualizza modello) nel messaggio e-mail di conferma ricevuto oppure accedere a [Speech Studio](https://speech.microsoft.com/).
2. Nell'angolo in alto a destra individuare e fare clic sull'icona a forma di ingranaggio (impostazioni) e quindi selezionare **Tenant Model settings** (Impostazioni modello di tenant).

   ![Menu Impostazioni](media/tenant-language-model/tenant-language-settings.png)

3. Fare clic su **Distribuisci**.
4. Dopo che il modello è stato distribuito, lo stato cambia in **Distribuito**.

## <a name="use-your-model-with-the-speech-sdk"></a>Usare il modello con Speech SDK

Ora che è stato distribuito il modello, è possibile usarlo con Speech SDK. In questa sezione verrà usato il codice di esempio fornito per chiamare il servizio di riconoscimento vocale usando l'autenticazione di Azure AD.

Verrà ora esaminato il codice che verrà usato per chiamare Speech SDK in C#. In questo esempio si eseguirà il riconoscimento vocale usando un modello di tenant. Questa guida presuppone che la piattaforma sia già configurata. Per assistenza con la configurazione, vedere [Avvio rapido: Riconoscimento vocale, C# (.NET Core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).

Copiare questo codice nel progetto:

```csharp
namespace PrincetonSROnly.FrontEnd.Samples
{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json.Linq;

    // Note: ServiceApplicationId is a fixed value.  No need to change.

    public class TenantLMSample
    {
        private const string EndpointUriArgName = "EndpointUri";
        private const string SubscriptionKeyArgName = "SubscriptionKey";
        private const string UsernameArgName = "Username";
        private const string PasswordArgName = "Password";
        private const string ClientApplicationId = "f87bc118-1576-4097-93c9-dbf8f45ef0dd";
        private const string ServiceApplicationId = "18301695-f99d-4cae-9618-6901d4bdc7be";

        public static async Task ContinuousRecognitionWithTenantLMAsync(Uri endpointUri, string subscriptionKey, string audioDirPath, string username, string password)
        {
            var config = SpeechConfig.FromEndpoint(endpointUri, subscriptionKey);

            // Passing client specific information for obtaining LM
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                config.AuthorizationToken = await AcquireAuthTokenWithInteractiveLoginAsync().ConfigureAwait(false);
            }
            else
            {
                config.AuthorizationToken = await AcquireAuthTokenWithUsernamePasswordAsync(username, password).ConfigureAwait(false);
            }

            var stopRecognition = new TaskCompletionSource<int>();

            // Creates a speech recognizer using file as audio input.
            // Replace with your own audio file name.
            using (var audioInput = AudioConfig.FromWavFileInput(audioDirPath))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    // Subscribes to events
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");
                        if (e.Reason == CancellationReason.Error)
                        {
                            Exception exp = new Exception(string.Format("Error Code: {0}\nError Details{1}\nIs your subscription information updated?", e.ErrorCode, e.ErrorDetails));
                            throw exp;
                        }

                        stopRecognition.TrySetResult(0);
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\n    Session started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\n    Session stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopRecognition.TrySetResult(0);
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopRecognition.Task });

                    // Stops recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }
        }

        public static void Main(string[] args)
        {
            var arguments = new Dictionary<string, string>();
            string inputArgNamePattern = "--";
            Regex regex = new Regex(inputArgNamePattern);
            if (args.Length > 0)
            {
                foreach (var arg in args)
                {
                    var userArgs = arg.Split("=");
                    arguments[regex.Replace(userArgs[0], string.Empty)] = userArgs[1];
                }
            }

            var endpointString = arguments.GetValueOrDefault(EndpointUriArgName, $"wss://westus.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1");
            var endpointUri = new Uri(endpointString);

            if (!arguments.ContainsKey(SubscriptionKeyArgName))
            {
                Exception exp = new Exception("Subscription Key missing! Please pass in a Cognitive services subscription Key using --SubscriptionKey=\"your_subscription_key\"" +
                    "Find more information on creating a Cognitive services resource and accessing your Subscription key here: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows");
                throw exp;
            }

            var subscriptionKey = arguments[SubscriptionKeyArgName];
            var username = arguments.GetValueOrDefault(UsernameArgName, null);
            var password = arguments.GetValueOrDefault(PasswordArgName, null);

            var audioDirPath = Path.Combine(Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location), "../../../AudioSamples/DictationBatman.wav");
            if (!File.Exists(audioDirPath))
            {
                Exception exp = new Exception(string.Format("Audio File does not exist at path: {0}", audioDirPath));
                throw exp;
            }

            ContinuousRecognitionWithTenantLMAsync(endpointUri, subscriptionKey, audioDirPath, username, password).GetAwaiter().GetResult();
        }

        private static async Task<string> AcquireAuthTokenWithUsernamePasswordAsync(string username, string password)
        {
            var tokenEndpoint = "https://login.microsoftonline.com/common/oauth2/token";
            var postBody = $"resource={ServiceApplicationId}&client_id={ClientApplicationId}&grant_type=password&username={username}&password={password}";
            var stringContent = new StringContent(postBody, Encoding.UTF8, "application/x-www-form-urlencoded");
            using (HttpClient httpClient = new HttpClient())
            {
                var response = await httpClient.PostAsync(tokenEndpoint, stringContent).ConfigureAwait(false);

                if (response.IsSuccessStatusCode)
                {
                    var result = await response.Content.ReadAsStringAsync().ConfigureAwait(false);

                    JObject jobject = JObject.Parse(result);
                    return jobject["access_token"].Value<string>();
                }
                else
                {
                    throw new Exception($"Requesting token from {tokenEndpoint} failed with status code {response.StatusCode}: {await response.Content.ReadAsStringAsync().ConfigureAwait(false)}");
                }
            }
        }

        private static async Task<string> AcquireAuthTokenWithInteractiveLoginAsync()
        {
            var authContext = new AuthenticationContext("https://login.windows.net/microsoft.onmicrosoft.com");
            var deviceCodeResult = await authContext.AcquireDeviceCodeAsync(ServiceApplicationId, ClientApplicationId).ConfigureAwait(false);

            Console.WriteLine(deviceCodeResult.Message);

            var authResult = await authContext.AcquireTokenByDeviceCodeAsync(deviceCodeResult).ConfigureAwait(false);

            return authResult.AccessToken;
        }
    }
}
```

Sarà quindi necessario ricompilare ed eseguire il progetto dalla riga di comando. Prima di eseguire il comando, è necessario aggiornare alcuni parametri.

1. Sostituire `<Username>` e `<Password>` con i valori di un utente tenant valido.
2. Sostituire `<Subscription-Key>` con la chiave di sottoscrizione della risorsa di riconoscimento vocale. Questo valore è disponibile nella sezione **Panoramica** relativa alla risorsa di riconoscimento vocale nel [portale di Azure](https://aka.ms/azureportal).
3. Sostituire `<Endpoint-Uri>` con l'endpoint riportato di seguito. Assicurarsi di sostituire `{your-region}` con l'area in cui è stata creata la risorsa di riconoscimento vocale. Sono supportate queste aree: `westus`, `westus2` e `eastus`. Queste informazioni relative all'area sono disponibili nella sezione **Panoramica** relativa alla risorsa di riconoscimento vocale nel [portale di Azure](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
4. Eseguire il comando:
   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

## <a name="next-steps"></a>Passaggi successivi

* [Speech Studio](https://speech.microsoft.com/)
* [Speech SDK](speech-sdk.md)
