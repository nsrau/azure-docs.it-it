---
title: 'Esercitazione: API Traduzione vocale C#'
titleSuffix: Azure Cognitive Services
description: Usare l'API Traduzione vocale per tradurre il testo in tempo reale.
services: cognitive-services
author: v-jerkin
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: tutorial
ms.date: 3/5/2018
ms.author: v-jerkin
ms.openlocfilehash: 383e17e0a9e60b52a63420af19c2bca4337083d4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55876913"
---
# <a name="tutorial-translator-speech-application-in-c"></a>Esercitazione: Applicazione Traduzione vocale in C#

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Questa esercitazione consente di esplorare uno strumento di traduzione vocale interattivo che usa l'API Traduzione vocale inclusa in Servizi cognitivi di Azure. Si apprenderà come:

> [!div class="checklist"]
> * Richiedere un elenco delle lingue supportate dal servizio
> * Acquisire l'audio e trasmetterlo al servizio
> * Ricevere e visualizzare le traduzioni del contenuto vocale come testo
> * Facoltativamente, riprodurre una versione parlata (sintesi vocale) della traduzione

[In GitHub è disponibile](https://github.com/MicrosoftTranslator/SpeechTranslator) il file di una soluzione di Visual Studio per questa applicazione.

## <a name="prerequisites"></a>Prerequisiti

Per questa esercitazione è necessario disporre di un'edizione qualsiasi di Visual Studio 2017, inclusa la Community Edition. 

La soluzione di Visual Studio crea anche un programma di installazione per l'applicazione. Per il supporto di questa funzionalità sono necessari [WiX Toolset](http://wixtoolset.org/) e l'[estensione di Visual Studio per WiX Toolset](https://marketplace.visualstudio.com/items?itemName=RobMensching.WixToolsetVisualStudio2017Extension).

È necessario anche avere una chiave di sottoscrizione per il servizio Traduzione vocale, che è possibile ottenere dal dashboard di Microsoft Azure. È disponibile un piano tariffario gratuito che consente di tradurre gratuitamente fino a 10 ore di contenuto vocale al mese. Questo piano è sufficiente per l'esercitazione.

È inoltre necessaria la [libreria JSON.Net](https://www.newtonsoft.com/json) di terze parti (di Newtonsoft). Questo assembly viene installato automaticamente da NuGet se nelle opzioni di Visual Studio sono abilitate entrambe le caselle di controllo di Ripristino pacchetto.

## <a name="trying-the-translation-app"></a>Prova dell'app di traduzione

Dopo aver aperto la soluzione Traduzione vocale (`SpeechTranslator.sln`) in Visual Studio, premere F5 per compilare e avviare l'applicazione.  Verrà visualizzata la finestra principale del programma.

![[Finestra principale di Traduzione vocale]](media/speech-translator-main-window.png)

Alla prima esecuzione scegliere **Impostazioni account** dal menu **Impostazioni** per aprire la finestra visualizzata di seguito.

![[Finestra principale di Traduzione vocale]](media/speech-translator-settings-window.png)

Incollare la chiave di sottoscrizione di Traduzione vocale in questa finestra e quindi fare clic su **Salva**. La chiave viene salvata da un'esecuzione all'altra.

Nella finestra principale scegliere i dispositivi audio di input e output da usare e le lingue di origine e di destinazione. Se si vuole ascoltare l'audio della traduzione, assicurarsi che l'opzione **TTS** (text-to-speech, sintesi vocale) sia selezionata. Per visualizzare ipotetiche traduzioni parziali mentre si parla, abilitare l'opzione **Partial Results** (Risultati parziali).

Fare infine clic su **Start** (Avvia) per avviare la traduzione. Pronunciare una frase che si vuole tradurre. Nella finestra verranno visualizzati il testo riconosciuto e la relativa traduzione. Se l'opzione di sintesi vocale è abilitata, sarà anche possibile ascoltare la traduzione.

## <a name="obtaining-supported-languages"></a>Acquisizione delle lingue supportate

Al momento della stesura di questo articolo, il servizio Traduzione vocale supporta oltre 60 lingue per la traduzione testuale, mentre è supportato un numero minore di lingue per la traduzione vocale. Per queste lingue è necessario il supporto sia per la trascrizione (riconoscimento vocale) sia per l'output della sintesi vocale.

In altre parole, per la traduzione vocale la lingua di origine deve essere tra quelle supportate per la trascrizione. La lingua di output può essere una delle lingue supportate per la traduzione testuale, supponendo che si voglia ottenere un risultato in formato testo. Se si vuole un output vocale, è possibile tradurre soltanto in una delle lingue supportate per la sintesi vocale.

Microsoft potrebbe periodicamente aggiungere il supporto per nuove lingue. È pertanto consigliabile non impostare come hardcoded nell'applicazione le informazioni relative alle lingue supportate. L'API Traduzione vocale include infatti un endpoint Languages che consente di recuperare le lingue supportate in fase di esecuzione. È possibile scegliere di ricevere uno o più elenchi di lingue: 

| | |
|-|-|
|`speech`|Lingue supportate per la trascrizione vocale. Possono essere le lingue di origine per la traduzione vocale.|
|`text`|Lingue supportate per la traduzione testuale. Possono essere le lingue di destinazione per la traduzione vocale quando viene usato un output di testo.|
|`tts`|Voci supportate per la sintesi vocale, ciascuna associata a una determinata lingua. Possono essere le lingue di destinazione per la traduzione vocale quando viene usata la sintesi vocale. Una determinata lingua può essere supportata da più voci.|

L'endpoint Languages non richiede una chiave di sottoscrizione e il relativo uso non influisce sulla quota. L'URI corrispondente è `https://dev.microsofttranslator.com/languages` e restituisce i risultati in formato JSON.

Il metodo `UpdateLanguageSettingsAsync()` in `MainWindow.xaml.cs`, illustrato di seguito, chiama l'endpoint Languages per ottenere l'elenco delle lingue supportate. 

```csharp
private async Task UpdateLanguageSettingsAsync()
{
    Uri baseUri = new Uri("https://" + baseUrl);
    string fullUriString = "/Languages?api-version=1.0&scope=text,speech,tts";
    if (MenuItem_Experimental.IsChecked) fullUriString += "&flight=experimental";            
    Uri fullUri = new Uri(baseUri, fullUriString);

    using (HttpClient client = new HttpClient()) //'client' is the var - using statement ensures the dispose method is used even after an exception.
    {
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, fullUri);

        // get language names for current UI culture:
        request.Headers.Add("Accept-Language", CultureInfo.CurrentUICulture.TwoLetterISOLanguageName);

        // add a client-side trace Id. In case of issues, one can contact support and provide this:
        //string traceId = "SpeechTranslator" + Guid.NewGuid().ToString();
        //request.Headers.Add("X-ClientTraceId", traceId);
        //Debug.Print("TraceId: {0}", traceId);

        client.Timeout = TimeSpan.FromMilliseconds(10000);
        HttpResponseMessage response = await client.SendAsync(request); //make the async call to the web using the client var and passing the built up URI
        response.EnsureSuccessStatusCode(); //causes exception if the return is false

        Debug.Print("Request Id returned: {0}", GetRequestId(response));

        //create dictionaries to hold the language specific data
        spokenLanguages = new Dictionary<string, string>();
        fromLanguages = new Dictionary<string, string>();
        textLanguages = new Dictionary<string, string>();
        isLTR = new Dictionary<string, bool>();
        voices = new Dictionary<string, List<TTsDetail>>();

        JObject jResponse = JObject.Parse(await response.Content.ReadAsStringAsync()); //get the json from the async call with the response var created above, parse it and put it in a var called jResponse - JObject is a newton class

        //Gather the set of TTS voices
        foreach (JProperty jTts in jResponse["tts"])
        {
            JObject ttsDetails = (JObject)jTts.Value;

            string code = jTts.Name;
            string language = ttsDetails["language"].ToString();
            string displayName = ttsDetails["displayName"].ToString();
            string gender = ttsDetails["gender"].ToString();

            if (!voices.ContainsKey(language)) //check dictionary for a specific key value
            {
                voices.Add(language, new List<TTsDetail>()); //add to the dictionary the locale key and a ttsDetail object
            }

            voices[language].Add(new TTsDetail() { Code = code, DisplayName = string.Format("{0} ({1})", displayName, gender) });
        }

        // Gather the set of speech translation languages
        foreach (JProperty jSpeech in jResponse["speech"])
        {
            JObject languageDetails = (JObject)jSpeech.Value;
            string code = jSpeech.Name;
            string simplecode = languageDetails["language"].ToString();
            string displayName = languageDetails["name"].ToString();
            spokenLanguages.Add(code, displayName);
            fromLanguages.Add(code,simplecode);
        }

        spokenLanguages = spokenLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        FromLanguage.Items.Clear();
        foreach (var language in spokenLanguages)
        {
            FromLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key});
        }

        // Gather the set of text translation languages
        foreach (JProperty jText in jResponse["text"])
        {
            JObject languageDetails = (JObject)jText.Value;
            string code = jText.Name;
            string displayName = languageDetails["name"].ToString();
            textLanguages.Add(code, displayName);

            string direction = languageDetails["dir"].ToString().ToLowerInvariant();
            bool LTR = true;
            if (direction.ToLowerInvariant() == "rtl") LTR = false;
            isLTR.Add(code, LTR);
        }

        textLanguages = textLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        ToLanguage.Items.Clear();
        foreach (var language in textLanguages)
        {
            ToLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key });
        }

        if (Properties.Settings.Default.FromLanguageIndex >= 0) FromLanguage.SelectedIndex = Properties.Settings.Default.FromLanguageIndex;
        else
        {
            for(int i=0; i < FromLanguage.Items.Count; ++i)
            {
                ComboBoxItem item = (ComboBoxItem)FromLanguage.Items[i];
                if(CultureInfo.CurrentUICulture.Name.Equals((string)item.Tag, StringComparison.OrdinalIgnoreCase))
                {
                    FromLanguage.SelectedIndex = i;
                }
            }
        }
        if (Properties.Settings.Default.ToLanguageIndex >= 0) ToLanguage.SelectedIndex = Properties.Settings.Default.ToLanguageIndex;
        else
        {
            Random rnd = new Random(DateTime.Now.Millisecond);
            ToLanguage.SelectedIndex = (rnd.Next() % textLanguages.Count);
        }
    }
}
```

Questo metodo crea innanzitutto una richiesta HTTP all'endpoint Languages per ottenere tutti e tre gli elenchi di lingue (`text`, `speech` e `tts`).

L'endpoint Languages usa l'intestazione `Accept-Languages` della richiesta per determinare la lingua in cui sono rappresentati i nomi delle lingue. Ad esempio, la lingua nota agli anglofoni come "German" è detta "Deutsch" in tedesco e "Alemán" in spagnolo, pertanto l'elenco delle lingue riflette queste differenze. Per questa intestazione viene usata la lingua predefinita del sistema.

Dopo che è stata inviata la richiesta ed è stata ricevuta la risposta JSON, la risposta viene analizzata in strutture di dati interne. Queste strutture vengono quindi usate per costruire i menu della lingua di origine e di quella di destinazione. 

Poiché le voci disponibili dipendono dalla lingua di destinazione scelta dall'utente, non è ancora possibile impostare il menu relativo alle voci. Le voci disponibili per ogni lingua vengono archiviate per un uso successivo. Il gestore `ToLanguage_SelectionChanged` (nello stesso file di origine) aggiorna in un secondo momento il menu relativo alle voci chiamando `UpdateVoiceComboBox()` quando l'utente sceglie una lingua di destinazione. 

Se l'utente non ha mai eseguito l'applicazione, viene selezionata casualmente una lingua di destinazione. Le impostazioni di menu vengono archiviate da una sessione all'altra.

## <a name="authenticating-requests"></a>Autenticazione delle richieste

Per l'autenticazione al servizio Traduzione vocale Microsoft è necessario inviare la chiave di sottoscrizione di Azure nell'intestazione come valore di `Ocp-Apim-Subscription-Key` nella richiesta di connessione.

## <a name="translation-overview"></a>Panoramica della traduzione

L'API di traduzione (endpoint WebSockets `wss://dev.microsofttranslator.com/speech/translate`) accetta la traduzione dell'audio in formato WAVE firmato, monofonico, a 16 bit e 16 kHz. Il servizio restituisce una o più risposte JSON contenenti sia il testo riconosciuto sia quello tradotto. Se è stata richiesta la sintesi vocale, viene inviato un file audio.

L'utente sceglie l'origine audio tramite il menu Microfono/Input file. L'audio può provenire da un dispositivo audio, ad esempio un microfono, o da un file `.WAV`.

Il metodo `StartListening_Click` viene richiamato quando l'utente fa clic sul pulsante Start (Avvia). Questo gestore eventi chiama a sua volta `Connect()` per avviare il processo di invio dell'audio all'endpoint API del servizio. Il metodo `Connect()` esegue le attività seguenti:


> [!div class="checklist"]
> * Recuperare le impostazioni utente dalla finestra principale e convalidarle
> * Inizializzare i flussi di input e output dell'audio
> * Chiamare `ConnectAsync()` per gestire il resto del lavoro

`ConnectAsync()` gestisce a sua volta le attività seguenti:

> [!div class="checklist"]
> * Eseguire l'autenticazione con la chiave di sottoscrizione di Azure nell'intestazione `Ocp-Apim-Subscription-Key`
> * Creare un'istanza di `SpeechClient` (inclusa in `SpeechClient.cs`) per comunicare con il servizio
> * Inizializzare le istanze di `TextMessageDecoder` e `BinaryMessageDecoder` (vedere `SpeechResponseDecoder.cs`) per la gestione delle risposte
> * Inviare l'audio tramite l'istanza di `SpeechClient` al servizio Traduzione vocale
> * Ricevere ed elaborare i risultati della traduzione

Le attività gestite da `SpeechClient` sono minori:

> [!div class="checklist"]
> * Stabilire una connessione WebSocket al servizio Traduzione vocale
> * Inviare i dati audio e ricevere le risposte attraverso il socket

## <a name="a-closer-look"></a>Approfondimento

A questo punto dovrebbe essere più chiaro come le varie parti dell'applicazione interagiscono per eseguire la richiesta di traduzione. Si esaminerà ora il codice con particolare attenzione alle parti rilevanti.

Di seguito è riportata una versione parziale di `Connect()` che illustra l'impostazione dei flussi audio:

```csharp
private void Connect()
{
    if (this.currentState != UiState.ReadyToConnect) return;

    Stopwatch watch = Stopwatch.StartNew();
    UpdateUiState(UiState.Connecting);

    // Omitted: code to validate UI settings

    string tag = ((ComboBoxItem)Mic.SelectedItem).Tag as string;
    string audioFileInputPath = null;
    if (tag == "File")
    {
        audioFileInputPath = this.AudioFileInput.Text;
        foreach (string currFile in audioFileInputPath.Split('|'))
        {
            if (!File.Exists(currFile))
            {
                SetMessage(String.Format($"Invalid audio source: selected file {currFile} does not exist."), "", MessageKind.Error);
                UpdateUiState(UiState.ReadyToConnect);
                return;
            }
        }
    }
    bool shouldSuspendInputAudioDuringTTS = this.CutInputAudioCheckBox.IsChecked.HasValue ? this.CutInputAudioCheckBox.IsChecked.Value : false;

    correlationId = Guid.NewGuid().ToString("D").Split('-')[0].ToUpperInvariant();

    // Setup speech translation client options
    SpeechClientOptions options;

    string voicename = "";
    if (this.Voice.SelectedItem != null)
    {
        voicename = ((ComboBoxItem)this.Voice.SelectedItem).Tag.ToString();
    }
    options = new SpeechTranslateClientOptions()
    {
        TranslateFrom = ((ComboBoxItem)this.FromLanguage.SelectedItem).Tag.ToString(),
        TranslateTo = ((ComboBoxItem)this.ToLanguage.SelectedItem).Tag.ToString(),
        Voice = voicename,
    };
    
    options.Hostname = baseUrl;
    options.AuthHeaderKey = "Authorization";
    options.AuthHeaderValue = ""; // set later in ConnectAsync.
    options.ClientAppId = new Guid("EA66703D-90A8-436B-9BD6-7A2707A2AD99");
    options.CorrelationId = this.correlationId;
    options.Features = GetFeatures().ToString().Replace(" ", "");
    options.Profanity = ((SpeechClient.ProfanityFilter)Enum.Parse(typeof(SpeechClient.ProfanityFilter), GetProfanityLevel(), true)).ToString();
    options.Experimental = MenuItem_Experimental.IsChecked;

    // Setup player and recorder but don't start them yet.
    WaveFormat waveFormat = new WaveFormat(16000, 16, 1);

    // WaveProvider for incoming TTS
    // We use a rather large BufferDuration because we need to be able to hold an entire utterance.
    // TTS audio is received in bursts (faster than real-time).
    textToSpeechBytes = 0;
    playerTextToSpeechWaveProvider = new BufferedWaveProvider(waveFormat);
    playerTextToSpeechWaveProvider.BufferDuration = TimeSpan.FromMinutes(5);

    ISampleProvider sampleProvider = null;
    if (audioFileInputPath != null)
    {
        // Setup mixing of audio from input file and from TTS
        playerAudioInputWaveProvider = new BufferedWaveProvider(waveFormat);
        var srce1 = new Pcm16BitToSampleProvider(playerTextToSpeechWaveProvider);
        var srce2 = new Pcm16BitToSampleProvider(playerAudioInputWaveProvider);
        var mixer = new MixingSampleProvider(srce1.WaveFormat);
        mixer.AddMixerInput(srce1);
        mixer.AddMixerInput(srce2);
        sampleProvider = mixer;
    }
    else
    {
        recorder = new WaveIn();
        recorder.DeviceNumber = (int)((ComboBoxItem)Mic.SelectedItem).Tag;
        recorder.WaveFormat = waveFormat;
        recorder.DataAvailable += OnRecorderDataAvailable;
        sampleProvider = playerTextToSpeechWaveProvider.ToSampleProvider();
    }

    if (!batchMode)
    {
        player = new WaveOut();
        player.DeviceNumber = (int)((ComboBoxItem)Speaker.SelectedItem).Tag;
        player.Init(sampleProvider);
    }

    this.audioBytesSent = 0;

    string logAudioFileName = null;
    if (LogSentAudio.IsChecked|| LogReceivedAudio.IsChecked)
    {
        string logAudioPath = System.IO.Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.ApplicationData), Properties.Settings.Default.OutputDirectory);
        try
        {
            Directory.CreateDirectory(logAudioPath);
        }
        catch
        {
            this.AddItemToLog(string.Format("Could not create folder {0}", logAudioPath));
        }

        if (LogSentAudio.IsChecked)
        {
            logAudioFileName = System.IO.Path.Combine(logAudioPath, string.Format("audiosent_{0}.wav", this.correlationId));
        }

        if (LogReceivedAudio.IsChecked)
        {
            string fmt = System.IO.Path.Combine(logAudioPath, string.Format("audiotts_{0}_{{0}}.wav", this.correlationId));
            this.audioReceived = new BinaryMessageDecoder(fmt);
        }
    }
}
```

Una parte considerevole di `Connect()` riguarda la creazione di un'istanza di `SpeechClientOptions` (vedere `SpeechClientOptions.cs`) che contiene le opzioni per la traduzione. Le opzioni includono le informazioni necessarie per connettersi al servizio, ad esempio la chiave di autenticazione e il nome host, e le funzionalità usate per la traduzione. I campi qui riportati corrispondono ai campi di intestazione e ai parametri HTTP esposti dall'[API Traduzione vocale](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference).

Inoltre, `Connect()` crea e inizializza il dispositivo di input audio (variabile `sampleProvider`) che funge da origine del contenuto vocale da tradurre. L'origine può essere costituita da un dispositivo hardware di input, ad esempio un microfono, oppure da un file contenente dati audio WAVE.

Questo è il metodo `ConnectAsync()` che crea un'istanza della classe `speechClient` e definisce un hook a funzioni anonime per la gestione del testo e delle risposte binarie provenienti dal servizio.

```csharp
private async Task ConnectAsync(SpeechClientOptions options, bool suspendInputAudioDuringTTS)
{
    await ADMAuthenticate(options);
    
    TextMessageDecoder textDecoder;
    
    s2smtClient = new SpeechClient((SpeechTranslateClientOptions)options, CancellationToken.None);
    
    s2smtClient.OnBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnEndOfBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnTextData += (c, a) => { textDecoder.AppendData(a); lastReceivedPacketTick = DateTime.Now.Ticks; };
    s2smtClient.OnEndOfTextData += (c, a) =>
    {
        textDecoder.AppendData(a);
        lastReceivedPacketTick = DateTime.Now.Ticks;
        textDecoder
            .Decode()
            .ContinueWith(t =>
            {
                if (t.IsFaulted)
                {
                    Log(t.Exception, "E: Failed to decode incoming text message.");
                }
                else
                {
                    object msg = t.Result;
                    if (msg.GetType() == typeof(FinalResultMessage))
                    {
                        // omitted: code to process final binary result
                    }
                    if (msg.GetType() == typeof(PartialResultMessage))
                    {
                        // omitted: code to process partial binary result
                    }
                }
            });
    };
    s2smtClient.Failed += (c, ex) =>
    {
        Log(ex, "E: SpeechTranslation client reported an error.");
    };
    s2smtClient.Disconnected += (c, ea) =>
    {
        SafeInvoke(() =>
        {
            // We only care to react to server disconnect when our state is Connected. 
            if (currentState == UiState.Connected)
            {
                Log("E: Connection has been lost.");
                Log($"E: Errors (if any): \n{string.Join("\n", s2smtClient.Errors)}");
                Disconnect();
            }
        });
    };
    await s2smtClient.Connect();
}
```

Dopo l'autenticazione, il metodo crea l'istanza di `SpeechClient`. La classe `SpeechClient` (in `SpeechClient.cs`) richiama i gestori di eventi dopo la ricezione di dati binari e di testo. Quando la connessione non riesce o si interrompe, vengono richiamati gestori aggiuntivi.

I dati binari sono dati audio (output della sintesi locale) inviati dal servizio quando è abilitata la sintesi vocale. I dati di testo sono una traduzione parziale o completa del testo parlato. Dopo la creazione dell'istanza, il metodo definisce un hook a funzioni per la gestione di questi messaggi. I messaggi audio vengono archiviati per la successiva riproduzione e il testo viene visualizzato nella finestra.

## <a name="next-steps"></a>Passaggi successivi

Questo esempio di codice rappresenta un'applicazione con numerose funzionalità che illustra l'uso dell'API Traduzione vocale. Vi sono pertanto diverse parti da comprendere. Sono state esaminate in dettaglio le più importanti. Per le parti restanti può essere utile impostare alcuni punti di interruzione in Visual Studio e seguire i passaggi del processo di traduzione. Dopo aver compreso l'applicazione di esempio, è possibile iniziare a usare il servizio Traduzione vocale nelle proprie applicazioni.

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API Traduzione vocale Microsoft](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
