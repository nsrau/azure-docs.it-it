---
title: Creare un modello di tenant (anteprima) - Servizio Voce
titleSuffix: Azure Cognitive Services
description: È possibile generare automaticamente un modello di tenant sicuro e conforme (Riconoscimento vocale personalizzato con dati di Microsoft 365) che sfrutta i dati di Microsoft 365 per offrire un riconoscimento vocale ottimale per termini specifici dell'organizzazione.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 06/25/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 48cde51ee9941f705aa848d121c419a8f0c9ad1a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95013715"
---
# <a name="tutorial-create-a-tenant-model-preview"></a>Esercitazione: Creare un modello di tenant (anteprima)

Il modello di tenant (Riconoscimento vocale personalizzato con dati di Microsoft 365) è un servizio basato su consenso esplicito per i clienti aziendali di Microsoft 365 che genera automaticamente un modello di riconoscimento vocale personalizzato dai dati di Microsoft 365 dell'organizzazione. Il modello è ottimizzato per termini tecnici, gergo e nomi di persone, in modo sicuro e conforme.

> [!IMPORTANT]
> Se l'organizzazione si registra usando il servizio del modello di tenant, il servizio Voce potrebbe accedere al suo modello linguistico. Il modello viene generato dai messaggi di posta elettronica e dai documenti dei gruppi pubblici di Microsoft 365, che sono visibili a chiunque nell'organizzazione. L'amministratore dell'organizzazione può attivare o disattivare l'uso del modello linguistico a livello di organizzazione tramite il portale di amministrazione.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Eseguire la registrazione nel modello di tenant tramite l'interfaccia di amministrazione di Microsoft 365
> * Ottenere una chiave di sottoscrizione al servizio Voce
> * Creare un modello di tenant
> * Distribuire un modello di tenant
> * Usare il modello di tenant con Speech SDK

## <a name="enroll-in-the-tenant-model-service"></a>Eseguire la registrazione nel servizio del modello di tenant

Prima di distribuire il modello di tenant, è necessario eseguire la registrazione nel relativo servizio. La registrazione viene completata nell'interfaccia di amministrazione di Microsoft 365 e può essere eseguita solo dall'amministratore.

1. Accedere all'[interfaccia di amministrazione di Microsoft 365](https://admin.microsoft.com).

1. Nel riquadro sinistro selezionare **Impostazioni**, quindi selezionare **Impostazioni** dal menu nidificato, infine selezionare i **servizi vocali di Azure** dalla finestra principale.

   ![Riquadro "Servizi e componenti aggiuntivi"](media/tenant-language-model/tenant-language-model-enrollment.png)

1. Selezionare la casella di controllo **Consenti il modello linguistico dell'organizzazione** e quindi selezionare **Salva modifiche**.

   ![Riquadro Azure Speech Services](media/tenant-language-model/tenant-language-model-enrollment-2.png)

Per disattivare l'istanza del modello di tenant:
1. Ripetere i precedenti passaggi 1 e 2.
1. Deselezionare la casella di controllo **Consenti il modello linguistico dell'organizzazione** e quindi selezionare **Salva modifiche**.

## <a name="get-a-speech-subscription-key"></a>Ottenere una chiave di sottoscrizione al servizio Voce

Per usare il modello di tenant con Speech SDK, è necessario avere una risorsa Voce e la chiave di sottoscrizione associata.

1. Accedere al [portale di Azure](https://aka.ms/azureportal).
1. Selezionare **Crea una risorsa**.
1. Nella casella **Cerca** digitare **Voce**.
1. Nell'elenco dei risultati selezionare **Voce** e quindi **Crea**.
1. Seguire le istruzioni visualizzate per creare la risorsa. Assicurarsi che:
   * L'opzione **Località** deve essere impostata su **eastus** o **westus**.
   * L'opzione **Piano tariffario** deve essere impostata su **S0**.
1. Selezionare **Crea**.

   Dopo alcuni minuti, la risorsa viene creata. La chiave di sottoscrizione è disponibile nella sezione **Panoramica** relativa alla risorsa.

## <a name="create-a-language-model"></a>Creare un modello linguistico

Dopo che l'amministratore ha abilitato il modello di tenant per l'organizzazione, è possibile creare un modello linguistico basato sui dati di Microsoft 365.

1. Accedere a [Speech Studio](https://speech.microsoft.com/).
1. Nell'angolo in alto a destra selezionare **Impostazioni** (icona dell'ingranaggio) e quindi **Tenant Model settings** (Impostazioni modello di tenant).

   ![Collegamento "Tenant Model settings"](media/tenant-language-model/tenant-language-settings.png)

   Speech Studio visualizza un messaggio che informa se si è idonei a creare un modello di tenant.

   > [!NOTE]
   > I clienti aziendali in America del Nord sono idonei per la creazione di un modello di tenant (inglese). Per i clienti di Customer Lockbox, Customer Key o Office 365 Government, questa funzionalità non è disponibile. Per determinare se si è un cliente di Customer Lockbox o Customer Key, vedere:
   > * [Customer Lockbox](/microsoft-365/compliance/customer-lockbox-requests)
   > * [Customer Key](/microsoft-365/compliance/customer-key-overview)
   > * [Office 365 Government](https://www.microsoft.com/microsoft-365/government)

1. Selezionare **Acconsenti esplicitamente** .

   Quando il modello di tenant è pronto, si riceverà un messaggio di posta elettronica di conferma con ulteriori istruzioni.

## <a name="deploy-your-tenant-model"></a>Distribuire il modello di tenant

Quando l'istanza del modello di tenant è pronta, eseguire questa procedura per distribuirla:

1. Nel messaggio di posta elettronica di conferma selezionare il pulsante **View model** (Visualizza modello). In alternativa, accedere a [Speech Studio](https://speech.microsoft.com/).
1. Nell'angolo in alto a destra selezionare **Impostazioni** (icona dell'ingranaggio) e quindi **Tenant Model settings** (Impostazioni modello di tenant).

   ![Collegamento "Tenant Model settings"](media/tenant-language-model/tenant-language-settings.png)

1. Selezionare **Distribuisci**.

   Dopo che il modello è stato distribuito, lo stato cambia in *Distribuito*.

## <a name="use-your-tenant-model-with-the-speech-sdk"></a>Usare il modello di tenant con Speech SDK

Ora che è stato distribuito il modello, è possibile usarlo con Speech SDK. In questa sezione viene usato un codice di esempio per chiamare il servizio Voce usando l'autenticazione di Azure Active Directory (Azure AD).

Verrà ora esaminato il codice che verrà usato per chiamare Speech SDK in C#. In questo esempio si esegue il riconoscimento vocale usando il modello di tenant. Questa guida presuppone che la piattaforma sia già configurata. Se è necessaria assistenza per la configurazione, vedere [Avvio rapido: Riconoscimento vocale, C# (.NET Core)](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore).

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

    // ServiceApplicationId is a fixed value. No need to change it.

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

Quindi è necessario ricompilare ed eseguire il progetto dalla riga di comando. Prima di eseguire il comando, aggiornare alcuni parametri eseguendo questa procedura:

1. Sostituire `<Username>` e `<Password>` con i valori di un utente tenant valido.
1. Sostituire `<Subscription-Key>` con la chiave di sottoscrizione della risorsa di riconoscimento vocale. Questo valore è disponibile nella sezione **Panoramica** relativa alla risorsa di riconoscimento vocale nel [portale di Azure](https://aka.ms/azureportal).
1. Sostituire `<Endpoint-Uri>` con l'endpoint seguente. Assicurarsi di sostituire `{your region}` con l'area in cui è stata creata la risorsa di riconoscimento vocale. Sono supportate queste aree: `westus`, `westus2` e `eastus`. Le informazioni sull'area sono disponibili nella sezione **Panoramica** della risorsa Voce nel [portale di Azure](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
1. Eseguire il comando seguente:

   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

In questa esercitazione si è appreso come usare i dati di Microsoft 365 per creare un modello di riconoscimento vocale personalizzato, distribuirlo e usarlo con Speech SDK.

## <a name="next-steps"></a>Passaggi successivi

* [Speech Studio](https://speech.microsoft.com/)
* [Speech SDK](speech-sdk.md)