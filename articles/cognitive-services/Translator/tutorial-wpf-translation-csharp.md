---
title: "Esercitazione: Scrivere un'applicazione WPF per Traduzione testuale con C# | Microsoft Docs"
titleSuffix: Microsoft Cognitive Services
description: In questa esercitazione verrà descritto come usare l'API Traduzione testuale per tradurre testo, ottenere un elenco localizzato delle lingue supportate e altro ancora, creando un'applicazione WPF con C#.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: nolachar
ms.openlocfilehash: 353c1d91b7925a84667ef1bb7c38ab87c6a89cc1
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2018
ms.locfileid: "39716373"
---
# <a name="tutorial-write-a-wpf-application-for-translator-text-using-c35"></a>Esercitazione: Scrivere un'applicazione WPF per Traduzione testuale con C&#35;

In questa esercitazione verrà creato uno strumento di traduzione di testo interattivo usando l'API Traduzione testuale (V3), parte di Servizi cognitivi Microsoft in Azure. Si apprenderà come:

> [!div class="checklist"]
> * Ottenere un elenco delle lingue supportate dal servizio
> * Eseguire la traduzione del testo immesso dall'utente da una lingua a un'altra

Questa applicazione si integra anche con altri due servizi di Servizi cognitivi Microsoft.

|||
|-|-|
|[Analisi del testo](https://azure.microsoft.com/services/cognitive-services/text-analytics/)|Consente di rilevare facoltativamente e in modo automatico la lingua di origine del testo da tradurre|
|[Controllo ortografico Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/)|Per un testo di origine in lingua inglese, consente di correggere gli errori di ortografia in modo che la traduzione sia più accurata

![[Programma dell'esercitazione in esecuzione]](media/translator-text-csharp-session.png)

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questo codice in Windows, è necessario [Visual Studio 2017](https://www.visualstudio.com/downloads/). È possibile usare la versione gratuita Community Edition.

Sono anche necessarie le chiavi di sottoscrizione per i tre servizi Azure usati nel programma. È possibile ottenere una chiave per il servizio Traduzione testuale nel dashboard di Azure. È disponibile un piano tariffario gratuito che consente di tradurre fino a due milioni di caratteri al mese senza alcun addebito.

Entrambi i servizi Analisi del testo e Controllo ortografico Bing offrono versioni di valutazioni gratuite, che è possibile ottenere in [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) (Prova Servizi Cognitivi). È anche possibile creare una sottoscrizione per un servizio nel dashboard di Azure. Per Analisi del testo è disponibile un piano tariffario gratuito.

Il codice sorgente per questa esercitazione è disponibile di seguito. Le chiavi di sottoscrizione devono essere copiate nel codice sorgente come le variabili `TEXT_TRANSLATION_API_SUBSCRIPTION_KEY`, e così via, in `MainWindow.xaml.cs`.

> [!IMPORTANT]
> Il servizio Analisi del testo è disponibile in più aree. L'URI nel codice sorgente dell'esercitazione si trova nell'area `westus`, ovvero quella usata per le versioni di valutazione gratuite. Se si dispone di una sottoscrizione in un'altra area, aggiornare l'URI di conseguenza.

## <a name="source-code"></a>Codice sorgente

Codice sorgente per l'API Traduzione testuale di Microsoft. Per eseguire l'app, copiare il codice sorgente nel file appropriato in un nuovo progetto WPF in Visual Studio.

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

Questo è il file code-behind che implementa le funzionalità dell'applicazione.

```csharp
using System;
using System.Windows;
using System.Net;
using System.Net.Http;
using System.IO;
using System.Collections.Generic;
using System.Linq;
using System.Text;

// NOTE: Add assembly references to System.Runtime.Serialization, System.Web, and System.Web.Extensions.

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace MSTranslatorTextDemo
{
    /// <summary>
    /// This WPF application demonstrates the use of the Microsoft Translator Text API to translate a brief text string from
    /// one language to another. The languages are selected from a drop-down menu. The text of the translation is displayed.
    /// The source language may optionally be automatically detected. English text is spell-checked.
    /// </summary>
    public partial class MainWindow : Window
    {
        // Translator text subscription key from Microsoft Azure dashboard
        const string TEXT_TRANSLATION_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
        const string TEXT_ANALYTICS_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
        const string BING_SPELL_CHECK_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";

        public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
        const string TEXT_ANALYTICS_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/";
        const string BING_SPELL_CHECK_API_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

        private string[] languageCodes;     // array of language codes

        // Dictionary to map language code from friendly name (sorted case-insensitively on language name)
        private SortedDictionary<string, string> languageCodesAndTitles =
            new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

        public MainWindow()
        {
            // at least show an error dialog if there's an unexpected error
            AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

            if (TEXT_TRANSLATION_API_SUBSCRIPTION_KEY.Length != 32
                || TEXT_ANALYTICS_API_SUBSCRIPTION_KEY.Length != 32
                || BING_SPELL_CHECK_API_SUBSCRIPTION_KEY.Length != 32)
            {
                MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                    "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                    "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
                System.Windows.Application.Current.Shutdown();
            }
            else
            {
                InitializeComponent();          // start the GUI
                GetLanguagesForTranslate();     // get codes and friendly names of languages that can be translated
                PopulateLanguageMenus();        // fill the drop-down language lists
            }
        }

        // Global exception handler to display error message and exit
        private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
        {
            Exception e = (Exception)args.ExceptionObject;
            MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
            System.Windows.Application.Current.Shutdown();
        }

        // ***** POPULATE LANGUAGE MENUS
        private void PopulateLanguageMenus()
        {
            // Add option to automatically detect the source language
            FromLanguageComboBox.Items.Add("Detect");

            int count = languageCodesAndTitles.Count;
            foreach (string menuItem in languageCodesAndTitles.Keys)
            {
                FromLanguageComboBox.Items.Add(menuItem);
                ToLanguageComboBox.Items.Add(menuItem);
            }

            // set default languages
            FromLanguageComboBox.SelectedItem = "Detect";
            ToLanguageComboBox.SelectedItem = "English";
        }

        // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
        private string DetectLanguage(string text)
        {
            string uri = TEXT_ANALYTICS_API_ENDPOINT + "languages?numberOfLanguagesToDetect=1";

            // create request to Text Analytics API
            HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(uri);
            detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_ANALYTICS_API_SUBSCRIPTION_KEY);
            detectLanguageWebRequest.Method = "POST";

            // create and send body of request
            var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
            string jsonText = serializer.Serialize(text);

            string body = "{ \"documents\": [ { \"id\": \"0\", \"text\": " + jsonText + "} ] }";
            byte[] data = Encoding.UTF8.GetBytes(body);
            detectLanguageWebRequest.ContentLength = data.Length;

            using (var requestStream = detectLanguageWebRequest.GetRequestStream())
                requestStream.Write(data, 0, data.Length);

            HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

            // read and parse JSON response
            var responseStream = response.GetResponseStream();
            var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
            dynamic jsonResponse = serializer.DeserializeObject(jsonString);

            // fish out the detected language code
            var languageInfo = jsonResponse["documents"][0]["detectedLanguages"][0];
            if (languageInfo["score"] > (decimal)0.5)
                return languageInfo["iso6391Name"];
            else
                return "";
        }

        // ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
        private string CorrectSpelling(string text)
        {
            string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

            // create request to Bing Spell Check API
            HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
            spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", BING_SPELL_CHECK_API_SUBSCRIPTION_KEY);
            spellCheckWebRequest.Method = "POST";
            spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

            // create and send body of request
            string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
            byte[] data = Encoding.UTF8.GetBytes(body);
            spellCheckWebRequest.ContentLength = data.Length;
            using (var requestStream = spellCheckWebRequest.GetRequestStream())
                requestStream.Write(data, 0, data.Length);
            HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

            // read and parse JSON response and get spelling corrections
            var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
            var responseStream = response.GetResponseStream();
            var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
            dynamic jsonResponse = serializer.DeserializeObject(jsonString);
            var flaggedTokens = jsonResponse["flaggedTokens"];

            // construct sorted dictionary of corrections in reverse order in string (right to left)
            // so that making a correction can't affect later indexes
            var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
            for (int i = 0; i < flaggedTokens.Length; i++)
            {
                var correction = flaggedTokens[i];
                var suggestion = correction["suggestions"][0];  // consider only first suggestion
                if (suggestion["score"] > (decimal)0.7)         // take it only if highly confident
                    corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                        { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
            }

            // apply the corrections in order from right to left
            foreach (int i in corrections.Keys)
            {
                var oldtext = corrections[i][0];
                var newtext = corrections[i][1];

                // apply capitalization from original text to correction - all caps or initial caps
                if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
                else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

                text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
            }

            return text;
        }

        // ***** GET TRANSLATABLE LANGUAGE CODES
        private void GetLanguagesForTranslate()
        {
            // send request to get supported language codes
            string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
            WebRequest WebRequest = WebRequest.Create(uri);
            WebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
            WebRequest.Headers.Add("Accept-Language", "en");
            WebResponse response = null;
            // read and parse the JSON response
            response = WebRequest.GetResponse();
            using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
            {
                var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
                var languages = result["translation"];

                languageCodes = languages.Keys.ToArray();
                foreach (var kv in languages)
                {
                    languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
                }
            }
        }

        // ***** PERFORM TRANSLATION ON BUTTON CLICK
        private async void TranslateButton_Click(object sender, EventArgs e)
        {
            string textToTranslate = TextToTranslate.Text.Trim();

            string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
            string fromLanguageCode;

            // auto-detect source language if requested
            if (fromLanguage == "Detect")
            {
                fromLanguageCode = DetectLanguage(textToTranslate);
                if (!languageCodes.Contains(fromLanguageCode))
                {
                    MessageBox.Show("The source language could not be detected automatically " +
                        "or is not supported for translation.", "Language detection failed",
                        MessageBoxButton.OK, MessageBoxImage.Error);
                    return;
                }
            }
            else
                fromLanguageCode = languageCodesAndTitles[fromLanguage];

            string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

            // spell-check the source text if the source language is English
            if (fromLanguageCode == "en")
            {
                if (textToTranslate.StartsWith("-"))    // don't spell check in this case
                    textToTranslate = textToTranslate.Substring(1);
                else
                {
                    textToTranslate = CorrectSpelling(textToTranslate);
                    TextToTranslate.Text = textToTranslate;     // put corrected text into input field
                }
            }

            // handle null operations: no text or same source/target languages
            if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
            {
                TranslatedTextLabel.Content = textToTranslate;
                return;
            }

            // send HTTP request to perform the translation
            string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
            string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

            System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
            var requestBody = JsonConvert.SerializeObject(body);

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
                request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
                var translations = result[0]["translations"];
                var translation = translations[0]["text"];

                // Update the translation field
                TranslatedTextLabel.Content = translation;
            }
        }
    }
}
```

### <a name="mainwindowxaml"></a>MainWindow.xaml

Questo file definisce l'interfaccia utente per l'applicazione, ovvero un modulo WPF. Se si vuole progettare la propria versione del modulo, questo codice XAML non è necessario.

```xml
<Window x:Class="MSTranslatorTextDemo.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:MSTranslatorTextDemo"
        mc:Ignorable="d"
        Title="Microsoft Translator" Height="400" Width="700" BorderThickness="0">
    <Grid>
        <Label x:Name="label" Content="Microsoft Translator" HorizontalAlignment="Left" Margin="39,6,0,0" VerticalAlignment="Top" Height="49" FontSize="26.667"/>
        <TextBox x:Name="TextToTranslate" HorizontalAlignment="Left" Height="23" Margin="42,160,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="600" FontSize="14" TabIndex="3"/>
        <Label x:Name="EnterTextLabel" Content="Text to translate:" HorizontalAlignment="Left" Margin="40,129,0,0" VerticalAlignment="Top" FontSize="14"/>
        <Label x:Name="toLabel" Content="Translate to:" HorizontalAlignment="Left" Margin="304,58,0,0" VerticalAlignment="Top" FontSize="14"/>

        <Button x:Name="TranslateButton" Content="Translate" HorizontalAlignment="Left" Margin="39,206,0,0" VerticalAlignment="Top" Width="114" Height="31" Click="TranslateButton_Click" FontSize="14" TabIndex="4" IsDefault="True"/>
        <ComboBox x:Name="ToLanguageComboBox"
                HorizontalAlignment="Left"
                Margin="306,88,0,0"
                VerticalAlignment="Top"
                Width="175" FontSize="14" TabIndex="2">

        </ComboBox>
        <Label x:Name="fromLabel" Content="Translate from:" HorizontalAlignment="Left" Margin="40,58,0,0" VerticalAlignment="Top" FontSize="14"/>
        <ComboBox x:Name="FromLanguageComboBox"
            HorizontalAlignment="Left"
            Margin="42,88,0,0"
            VerticalAlignment="Top"
            Width="175" FontSize="14" TabIndex="1"/>
        <Label x:Name="TranslatedTextLabel" Content="Translation appears here" HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
    </Grid>
</Window>
```

## <a name="service-endpoints"></a>Endpoint di servizio

Nel servizio Microsoft Translator sono presenti numerosi endpoint che consentono di eseguire diverse funzionalità di traduzione. Quelli usati in questa esercitazione sono i seguenti:

|||
|-|-|
|`Languages`|Restituisce il set di lingue attualmente supportate da altre operazioni dell'API Traduzione testuale.|
|`Translate`|Dato un testo di origine e un codice di lingua di origine e di destinazione, restituisce una traduzione del testo di origine nella lingua destinazione.|

## <a name="the-translation-app"></a>App per la traduzione

L'interfaccia utente dell'applicazione per la traduzione viene creata con Windows Presentation Foundation (WPF). Per creare un nuovo progetto WPF in Visual Studio, seguire questa procedura.

* Nel menu **File** scegliere **Nuovo > Progetto**.
* Nella finestra Nuovo progetto aprire **Installati > Modelli > Visual C#**. Al centro della finestra di dialogo viene visualizzato un elenco dei modelli di progetto disponibili.
* Verificare che **.NET Framework 4.5.2** sia selezionato nel menu a discesa sopra l'elenco dei modelli di progetto.
* Fare clic su **APP WPF (.NET Framework)** nell'elenco dei modelli di progetto.
* Usare i campi nella parte inferiore della finestra di dialogo per denominare il nuovo progetto e la soluzione che lo contiene.
* Fare clic su **OK** per creare il nuovo progetto e la soluzione.

![[Creazione di una nuova app WPF in Visual Studio]](media/translator-text-csharp-new-project.png)

Aggiungere al progetto i riferimenti agli assembly di .NET Framework seguenti.

* System.Runtime.Serialization
* System.Web
* System.Web.Extensions

Installare anche il pacchetto NuGet `Newtonsoft.Json` nel progetto.

Individuare il file `MainWindow.xaml` in Esplora soluzioni e aprirlo. Il file è inizialmente vuoto. L'interfaccia utente completata dovrebbe avere un aspetto analogo al seguente, con i nomi dei controlli in blu. Usare gli stessi nomi per i controlli nell'interfaccia utente, poiché vengono visualizzati anche nel codice.

![[Visualizzazione con annotazioni della finestra principale nella finestra di progettazione di Visual Studio]](media/translator-text-csharp-xaml.png)

> [!NOTE]
> Il codice sorgente per questa esercitazione include l'origine XAML per questo modulo. È possibile incollare il codice nel progetto anziché compilare il modulo in Visual Studio.

* `FromLanguageComboBox` *(casella combinata)*  - Visualizza un elenco di lingue supportate da Microsoft Translator per la traduzione del testo. L'utente seleziona la lingua da cui eseguire la traduzione.
* `ToLanguageComboBox` *(casella combinata)* - Visualizza lo stesso elenco di lingue di `FromComboBox`, ma viene usata per selezionare la lingua verso cui l'utente esegue la traduzione.
* `TextToTranslate` *(casella di testo)* - Consente di immettere il testo da tradurre.
* `TranslateButton` *(pulsante)* - L'utente fa clic su questo pulsante (o preme Invio) per tradurre il testo.
* `TranslatedTextLabel` *(etichetta)* - La traduzione del testo dell'utente viene visualizzata qui.

Se si crea la propria versione di questo modulo, non è necessario renderlo *esattamente* analogo a questo. Verificare tuttavia che gli elenchi a discesa per le lingue siano sufficientemente ampi per evitare di troncare un nome di lingua.

## <a name="the-mainwindow-class"></a>Classe MainWindow

Il file code-behind `MainWindow.xaml.cs` indica al programma le attività da eseguire. L'operazione ha luogo due volte:

* Quando il programma viene avviato e viene creata l'istanza di `MainWindow`, recupera l'elenco delle lingue usando le API di Microsoft Translator e le inserisce nei menu a discesa. Questa operazione viene eseguita una volta sola all'inizio di ogni sessione.

* Quando l'utente fa clic sul pulsante **Translate**, vengono recuperate le selezioni della lingua dell'utente e il testo immesso, quindi viene chiamata l'API `Translate` per eseguire la traduzione. È anche possibile chiamare altre funzioni per determinare la lingua del testo ed eseguire il controllo ortografico prima della traduzione.

Esaminare l'inizio della classe:

```csharp
public partial class MainWindow : Window
{
    // Translator text subscription key from Microsoft Azure dashboard
    const string TEXT_TRANSLATION_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
    const string TEXT_ANALYTICS_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
    const string BING_SPELL_CHECK_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";

    public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
    const string TEXT_ANALYTICS_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/";
    const string BING_SPELL_CHECK_API_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

    private string[] languageCodes;     // array of language codes

    // Dictionary to map language code from friendly name (sorted case-insensitively on language name)
    private SortedDictionary<string, string> languageCodesAndTitles =
        new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

    public MainWindow()
    {
        // at least show an error dialog if there's an unexpected error
        AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

        if (TEXT_TRANSLATION_API_SUBSCRIPTION_KEY.Length != 32
            || TEXT_ANALYTICS_API_SUBSCRIPTION_KEY.Length != 32
            || BING_SPELL_CHECK_API_SUBSCRIPTION_KEY.Length != 32)
        {
            MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
            System.Windows.Application.Current.Shutdown();
        }
        else
        {
            InitializeComponent();          // start the GUI
            GetLanguagesForTranslate();     // get codes and friendly names of languages that can be translated
            PopulateLanguageMenus();        // fill the drop-down language lists
        }
    }
// more to come
}
```

Due variabili membri dichiarate qui contengono informazioni sulle lingue:

|||
|-|-|
|`languageCodes`<br>matrice di valori string|Memorizza nella cache i codici di lingua. Il servizio Microsoft Translator usa codici brevi, ad esempio `en` per l'inglese, per identificare le lingue.|
|`languageCodesAndTitles`<br>SortedDictionary|Esegue il mapping dei nomi descrittivi dell'interfaccia utente nei codici brevi usati nell'API. Il mapping rispetta l'ordine alfabetico indipendentemente dalle maiuscole/minuscole.|

Il primo codice eseguito dall'applicazione è il costruttore `MainWindow`. Impostare innanzitutto il metodo `HandleExceptions` come gestore di errori globale. In questo modo è presente almeno un avviso di errore se non viene gestita alcuna eccezione.

In seguito si verifica che le chiavi di sottoscrizione dell'API siano lunghe tutte esattamente 32 caratteri. In caso contrario, il motivo più probabile è che *qualcuno* non abbia incollato i dati nelle relative chiavi API. In questo caso, viene visualizzato un messaggio di errore e si prosegue. Il passaggio di questo test non significa ovviamente che le chiavi siano valide.

Se sono presenti chiavi che hanno almeno la lunghezza corretta, la chiamata a `InitializeComponent()` visualizza l'interfaccia utente individuando, caricando e creando un'istanza della descrizione XAML della finestra principale dell'applicazione.

Vengono infine configurati i menu a discesa delle lingue. Questa attività richiede tre chiamate ai metodi distinte, che sono illustrate in dettaglio nelle sezioni seguenti.

## <a name="get-supported-languages"></a>Ottenere le lingue supportate

Al momento della stesura del presente articolo, il servizio Microsoft Translator supporta un totale di 61 lingue e altre verranno aggiunte di tanto in tanto. È consigliabile pertanto non impostare come hardcoded le lingue supportate nel programma e individuare invece nel servizio Microsoft Translator quali lingue supporta. Tutte le lingue supportate possono essere tradotte in qualsiasi altra lingua supportata.

Chiamare l'API `Languages` per ottenere l'elenco di lingue supportate.

L'API `Languages` accetta un parametro di query GET facoltativo, ovvero *scope*. Il parametro *scope* può assumere solo uno dei tre valori seguenti: `translation`, `transliteration` e `dictionary`. Questo codice usa il valore `translation`.

L'API `Languages` accetta anche un'intestazione HTTP facoltativa, ovvero `Accept-Language`. Il valore di questa intestazione determina la lingua in cui vengono restituiti i nomi delle lingue supportate. Il valore deve essere un tag di lingua BCP 47 ben formato. Questo codice usa il valore `en` per ottenere i nomi di lingua in inglese.

L'API `Languages` restituisce una risposta JSON analoga alla seguente.

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
}
```

Per estrarre i codici di lingua (ad esempio, `af`) e i nomi delle lingue (ad esempio, `Afrikaans`), questo codice usa il metodo NewtonSoft.Json [JsonConvert.DeserializeObject](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm).

Con queste informazioni di base, viene creato il metodo seguente per recuperare i codici di lingua e i relativi nomi.

```csharp
private void GetLanguagesForTranslate()
{
    // send request to get supported language codes
    string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
    WebRequest WebRequest = WebRequest.Create(uri);
    WebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
    WebRequest.Headers.Add("Accept-Language", "en");
    WebResponse response = null;
    // read and parse the JSON response
    response = WebRequest.GetResponse();
    using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
    {
        var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
        var languages = result["translation"];

        languageCodes = languages.Keys.ToArray();
        foreach (var kv in languages)
        {
            languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
        }
    }
}
```

`GetLanguagesForTranslate()` crea innanzitutto la richiesta HTTP. Il parametro della stringa di query `scope=translation` richiede solo le lingue supportate per la traduzione del testo. La chiave di sottoscrizione dell'API Traduzione testuale viene aggiunta alle intestazioni della richiesta. Viene aggiunta l'intestazione `Accept-Language` con il valore `en`, in modo che le lingue supportate vengano restituite in inglese.

Dopo il completamento della richiesta, la risposta JSON viene analizzata e convertita in un dizionario e quindi vengono aggiunti i codici di lingua alla variabile membro `languageCodes`. Viene quindi eseguita la ricerca delle coppie chiave/valore che contengono i codici di lingua e i nomi di lingua descrittivi e tali coppie vengono aggiunte alla variabile membro `languageCodesAndTitles`. I menu a discesa nel modulo includono i nomi descrittivi, ma per richiedere la traduzione sono necessari i codici.

## <a name="populate-the-language-menus"></a>Popolare i menu delle lingue

La maggior parte dell'interfaccia utente è definita in XAML, pertanto non è necessario eseguire molte altre attività per configurarla oltre alla chiamata a `InitializeComponent()`. L'unica altra attività che è necessario eseguire consiste nell'aggiunta di nomi di lingua descrittivi negli elenchi a discesa **Translate from** e **Translate to**, operazione eseguita in `PopulateLanguageMenus()`.

```csharp
private void PopulateLanguageMenus()
{
    // Add option to automatically detect the source language
    FromLanguageComboBox.Items.Add("Detect");

    int count = languageCodesAndTitles.Count;
    foreach (string menuItem in languageCodesAndTitles.Keys)
    {
        FromLanguageComboBox.Items.Add(menuItem);
        ToLanguageComboBox.Items.Add(menuItem);
    }

    // set default languages
    FromLanguageComboBox.SelectedItem = "Detect";
    ToLanguageComboBox.SelectedItem = "English";
}
```

Il popolamento dei menu consiste nell'iterazione del dizionario `languageCodesAndTitles` e nell'aggiunta di ogni chiave, ovvero del nome descrittivo, a entrambi i menu. Dopo avere popolato i menu, come impostazione predefinita per le lingue di origine e di destinazione vengono specificati **Detect** (per il rilevamento automatico della lingua) e **English**.

> [!TIP]
> Senza una selezione predefinita per i menu, l'utente può fare clic su **Translate** senza prima scegliere una lingua di origine o di destinazione. Le impostazioni predefinite eliminano la necessità di affrontare il problema.

Dopo aver inizializzato `MainWindow` e creata l'interfaccia utente, il codice attende che l'utente faccia clic sul pulsante **Translate**.

## <a name="perform-translation"></a>Eseguire la traduzione

Quando l'utente fa clic su **Translate** (Traduci), WPF richiama il `TranslateButton_Click()` gestore dell'evento, come illustrato di seguito.

```csharp
private async void TranslateButton_Click(object sender, EventArgs e)
{
    string textToTranslate = TextToTranslate.Text.Trim();

    string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
    string fromLanguageCode;

    // auto-detect source language if requested
    if (fromLanguage == "Detect")
    {
        fromLanguageCode = DetectLanguage(textToTranslate);
        if (!languageCodes.Contains(fromLanguageCode))
        {
            MessageBox.Show("The source language could not be detected automatically " +
                "or is not supported for translation.", "Language detection failed",
                MessageBoxButton.OK, MessageBoxImage.Error);
            return;
        }
    }
    else
        fromLanguageCode = languageCodesAndTitles[fromLanguage];

    string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

    // spell-check the source text if the source language is English
    if (fromLanguageCode == "en")
    {
        if (textToTranslate.StartsWith("-"))    // don't spell check in this case
            textToTranslate = textToTranslate.Substring(1);
        else
        {
            textToTranslate = CorrectSpelling(textToTranslate);
            TextToTranslate.Text = textToTranslate;     // put corrected text into input field
        }
    }

    // handle null operations: no text or same source/target languages
    if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
    {
        TranslatedTextLabel.Content = textToTranslate;
        return;
    }

    // send HTTP request to perform the translation
    string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
    string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

    System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
    var requestBody = JsonConvert.SerializeObject(body);

    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Post;
        request.RequestUri = new Uri(uri);
        request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
        request.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
        request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();

        var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
        var translations = result[0]["translations"];
        var translation = translations[0]["text"];

        // Update the translation field
        TranslatedTextLabel.Content = translation;
    }
}
```

Il primo passaggio consiste nel recuperare dal modulo le lingue di origine e di destinazione, nonché il testo immesso dall'utente.

Se la lingua di origine è impostata su **Detect**, effettuare una chiamata a `DetectLanguage()` per determinare la lingua del testo. Il testo può essere scritto in una lingua che le API di Microsoft Translator non supportano (è possibile rilevare molte più lingue che possono essere tradotte) o l'API Analisi del testo potrebbe non essere in grado di rilevarlo. In tal caso, viene visualizzato un messaggio per informare l'utente e non viene restituita alcuna traduzione.

Se la lingua di origine è l'inglese (specificata o rilevata), eseguire il controllo ortografico del testo con `CorrectSpelling()` e applicare le correzioni. Il testo corretto viene inserito nel campo di input per informare l'utente che la correzione è stata eseguita. L'utente può inserire un trattino prima del testo da tradurre per eliminare la correzione ortografica.

Se l'utente non ha immesso alcun testo o se le lingue di origine e di destinazione sono le stesse, non è necessaria alcuna traduzione e la richiesta può essere evitata.

Il codice per eseguire la richiesta di traduzione dovrebbe risultare familiare: creare l'URI, creare una richiesta, inviarla e analizzare la risposta. Per visualizzare il testo, inviarlo al controllo `TranslatedTextLabel`.

Passare quindi il testo all'API `Translate` in una matrice JSON serializzata nel corpo di una richiesta POST. La matrice JSON può contenere più parti di testo da tradurre, ma qui ne è richiesta solo una.

L'intestazione HTTP denominata `X-ClientTraceId` è facoltativa. Il valore deve essere un GUID. L'ID di traccia specificato dal client è utile per tenere traccia delle richieste quando il funzionamento non è quello previsto. Per essere utile, il valore di X-ClientTraceID deve essere tuttavia registrato dal client. Un ID di traccia client e la data delle richieste consentono a Microsoft di diagnosticare i problemi che possono verificarsi.

> [!NOTE]
> Questa esercitazione è incentrata sul servizio Microsoft Translator e di conseguenza i metodi `DetectLanguage()` e `CorrectSpelling()` non vengono trattati in modo dettagliato.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Microsoft Translator Text API reference](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) (Riferimento all'API Traduzione testuale di Microsoft)
