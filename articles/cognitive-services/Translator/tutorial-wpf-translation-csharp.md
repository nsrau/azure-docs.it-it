---
title: "Esercitazione: Creare un'app di traduzione con WPF, C# - API Traduzione testuale"
titleSuffix: Azure Cognitive Services
description: In questa esercitazione verrà creata un'app WPF (Windows Presentation Foundation) che usa le API Servizi cognitivi per la traduzione testuale, il rilevamento della lingua e il controllo ortografico con una chiave di sottoscrizione singola. L'esercizio illustrerà come usare le funzionalità dell'API Traduzione testuale e dell'API Controllo ortografico Bing.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: f8488195ed9e115843c2dc551af52d5da010ffe7
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036724"
---
# <a name="tutorial-create-a-translation-app-with-wpf"></a>Esercitazione: Creare un'app di traduzione con WPF

In questa esercitazione verrà creata un'app [WPF (Windows Presentation Foundation)](https://docs.microsoft.com/visualstudio/designers/getting-started-with-wpf?view=vs-2019) che usa Servizi cognitivi di Azure per la traduzione testuale, il rilevamento della lingua e il controllo ortografico con una chiave di sottoscrizione singola. In particolare, l'app chiamerà le API Traduzione testuale e [Controllo ortografico Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/).

Cos'è WPF? È un framework interfaccia utente che crea app client desktop. La piattaforma di sviluppo WPF supporta un ampio set di funzionalità di sviluppo di app, tra cui un modello di app, risorse, controlli, grafica, layout, associazione dati, documenti e sicurezza. Si tratta di un sottoinsieme di .NET Framework, quindi se sono già state create app con .NET Framework usando ASP.NET o Windows Forms, l'esperienza di programmazione dovrebbe risultare familiare. WPF usa il linguaggio XAML (Extensible app Markup Language) per fornire un modello dichiarativo per la programmazione di app, che verrà esaminato nelle prossime sezioni.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un progetto WPF in Visual Studio
> * Aggiungere assembly e pacchetti NuGet al progetto
> * Creare l'interfaccia utente dell'app con XAML
> * Usare l'API Traduzione testuale per recuperare le lingue, tradurre il testo e rilevare la lingua di origine
> * Usare l'API Controllo ortografico Bing per convalidare l'input e migliorare l'accuratezza della traduzione
> * Eseguire l'app WPF

### <a name="cognitive-services-used-in-this-tutorial"></a>Servizi cognitivi usati in questa esercitazione

Questo elenco include i Servizi cognitivi usati nell'esercitazione. Seguire il collegamento per esplorare il riferimento API per ogni funzionalità.

| Service | Funzionalità | DESCRIZIONE |
|---------|---------|-------------|
| Traduzione testuale | [Recupera lingue](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) | Recupera un elenco completo di lingue supportate per la traduzione testuale. |
| Traduzione testuale | [Translate](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) | Traduce il testo in più di 60 lingue. |
| Traduzione testuale | [Detect](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect) | Rileva la lingua del testo di input. Include il punteggio di attendibilità per il rilevamento. |
| Controllo ortografico Bing | [Controllo ortografico](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference) | Corregge gli errori di ortografia per migliorare l'accuratezza della traduzione. |

## <a name="prerequisites"></a>Prerequisiti

Prima di continuare, è necessario disporre di quanto segue:

* Una sottoscrizione a Servizi cognitivi di Azure. [Ottenere una chiave per Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#multi-service-resource).
* Un computer Windows
* [Visual Studio 2019](https://www.visualstudio.com/downloads/), versione Community o Enterprise

> [!NOTE]
> Per questa esercitazione, è consigliabile creare una sottoscrizione nell'area Stati Uniti occidentali. In caso contrario, sarà necessario cambiare endpoint e aree nel codice mentre si procede con l'esercizio.  

## <a name="create-a-wpf-app-in-visual-studio"></a>Creare un'app WPF in Visual Studio

Prima di tutto, è necessario configurare il progetto in Visual Studio.

1. Aprire Visual Studio. Selezionare **Crea un nuovo progetto**.
1. In **Crea un nuovo progetto**individuare e selezionare **App WPF (.NET Framework)** . È possibile selezionare C# in **Linguaggio** per limitare le opzioni.
1. Scegliere **Avanti** e quindi assegnare al progetto il nome `MSTranslatorTextDemo`.
1. Impostare la versione del framework su **.NET Framework 4.7.2** o versioni successive e selezionare **Crea**.
   ![Immettere il nome e la versione del framework in Visual Studio](media/name-wpf-project-visual-studio.png)

Il progetto è stato creato. Come si può notare, ci sono due schede aperte: `MainWindow.xaml` e `MainWindow.xaml.cs`. Nel corso dell'esercitazione verrà aggiunto codice in questi due file, Si modificherà `MainWindow.xaml` per l'interfaccia utente dell'app. Si modificherà `MainWindow.xaml.cs` per le chiamate a Traduzione testuale e Controllo ortografico Bing.
   ![Esaminare l'ambiente](media/blank-wpf-project.png)

Nella sezione successiva verranno aggiunti assembly e un pacchetto NuGet al progetto per includere altre funzionalità, come l'analisi JSON.

## <a name="add-references-and-nuget-packages-to-your-project"></a>Aggiungere riferimenti e pacchetti NuGet al progetto

Per il progetto sono necessari alcuni assembly .NET Framework e NewtonSoft.Json, che verranno installati con Gestione pacchetti NuGet.

### <a name="add-net-framework-assemblies"></a>Aggiungere gli assembly .NET Framework

Aggiungere gli assembly al progetto per serializzare e deserializzare oggetti, nonché per gestire le richieste e le risposte HTTP.

1. Individuare il progetto in Esplora soluzioni di Visual Studio. Fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Aggiungi > Riferimento** per aprire **Gestione riferimenti**.
1. La scheda **Assembly** include un elenco di tutti gli assembly .NET Framework disponibili come riferimenti. Usare la barra di ricerca in alto a destra per cercare riferimenti.
   ![Aggiungere riferimenti ad assembly](media/add-assemblies-2019.png)
1. Selezionare i riferimenti seguenti per il progetto:
   * [System.Runtime.Serialization](https://docs.microsoft.com/dotnet/api/system.runtime.serialization)
   * [System.Web](https://docs.microsoft.com/dotnet/api/system.web)
   * System.Web.Extensions
   * [System.Windows](https://docs.microsoft.com/dotnet/api/system.windows)
1. Dopo aver aggiunto questi riferimenti al progetto, è possibile fare clic su **OK** per chiudere **Gestione riferimenti**.

> [!NOTE]
> Per altre informazioni sui riferimenti ad assembly, vedere [Procedura: Aggiungere o rimuovere riferimenti tramite Gestione riferimenti](https://docs.microsoft.com/visualstudio/ide/how-to-add-or-remove-references-by-using-the-reference-manager?view=vs-2019).

### <a name="install-newtonsoftjson"></a>Installare Newtonsoft.Json

L'app userà NewtonSoft.Json per deserializzare gli oggetti JSON. Seguire queste istruzioni per installare il pacchetto.

1. Individuare il progetto in Esplora soluzioni di Visual Studio e fare clic con il pulsante destro del mouse sul progetto. Scegliere **Gestisci pacchetti NuGet**.
1. Individuare e selezionare la scheda **Sfoglia**.
1. Immettere [NewtonSoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) nella barra di ricerca.

    ![Individuare e installare NewtonSoft.Json](media/nuget-package-manager.png)

1. Selezionare il pacchetto e fare clic su **Installa**.
1. Al termine dell'installazione, chiudere la scheda.

## <a name="create-a-wpf-form-using-xaml"></a>Creare un modulo WPF tramite XAML

Per usare l'app, è necessaria un'interfaccia utente. Usando XAML, verrà creato un modulo che consente agli utenti di selezionare le lingue di input e di traduzione, immettere il testo da tradurre e visualizzare l'output della traduzione.

Ecco il modulo da creare.

![Interfaccia utente XAML WPF](media/translator-text-csharp-xaml.png)

L'interfaccia utente include questi componenti:

| NOME | Type | DESCRIZIONE |
|------|------|-------------|
| `FromLanguageComboBox` | Casella combinata | Visualizza un elenco delle lingue supportate da Microsoft Translator per la traduzione testuale. L'utente seleziona la lingua da cui eseguire la traduzione. |
| `ToLanguageComboBox` | Casella combinata | Visualizza lo stesso elenco di lingue di `FromComboBox`, ma viene usata per selezionare la lingua verso cui l'utente esegue la traduzione. |
| `TextToTranslate` | TextBox | Consente all'utente di immettere il testo da tradurre. |
| `TranslateButton` | Pulsante | Usare questo pulsante per tradurre il testo. |
| `TranslatedTextLabel` | Etichetta | Visualizza la traduzione. |
| `DetectedLanguageLabel` | Etichetta | Visualizza la lingua rilevata del testo da tradurre (`TextToTranslate`). |

> [!NOTE]
> Questo modulo viene creato usando codice sorgente XAML, ma è anche possibile crearlo con l'editor di Visual Studio.

Aggiungere il codice al progetto.

1. In Visual Studio selezionare la scheda corrispondente a `MainWindow.xaml`.
1. Copiare questo codice nel progetto e quindi selezionare **File > Salva MainWindow.xaml** per salvare le modifiche.
   ```xaml
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
           <Label x:Name="TranslatedTextLabel" Content="Translation is displayed here." HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
           <Label x:Name="DetectedLanguageLabel" Content="Autodetected language is displayed here." HorizontalAlignment="Left" Margin="39,288,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="84" BorderThickness="0"/>
       </Grid>
   </Window>
   ```
Dovrebbe essere visualizzata un'anteprima dell'interfaccia utente dell'app in Visual Studio, simile a quella riportata di seguito:

Il modulo è ora pronto. A questo punto è necessario scrivere il codice per usare Traduzione testuale e Controllo ortografico Bing.

> [!NOTE]
> È possibile personalizzare questo modulo o crearne uno diverso.

## <a name="create-your-app"></a>Creare l'app

`MainWindow.xaml.cs` contiene il codice che controlla l'app. Nelle sezioni successive verrà aggiunto il codice per popolare i menu a discesa e per chiamare diverse API esposte da Traduzione testuale e Controllo ortografico Bing.

* Quando viene avviato il programma e viene creata un'istanza di `MainWindow`, viene chiamato il metodo `Languages` dell'API Traduzione testuale per recuperare e popolare i menu a discesa di selezione della lingua. Questa operazione viene eseguita una volta all'inizio di ogni sessione.
* Quando si fa clic sul pulsante **Traduci**, vengono recuperati la selezione della lingua e il testo dell'utente, viene eseguito il controllo ortografico dell'input e vengono visualizzate la traduzione e la lingua rilevata.
  * Viene chiamato il metodo `Translate` dell'API Traduzione testuale per tradurre il testo di `TextToTranslate`. Questa chiamata include anche le lingue `to` e `from` selezionate tramite i menu a discesa.
  * Viene chiamato il metodo `Detect` dell'API Traduzione testuale per determinare la lingua del testo di `TextToTranslate`.
  * Viene usato Controllo ortografico Bing per convalidare `TextToTranslate` e correggere gli errori di ortografia.

Tutto il progetto è incapsulato nella classe `MainWindow : Window`. Per iniziare, aggiungere il codice per impostare la chiave della sottoscrizione, dichiarare gli endpoint per Traduzione testuale e Controllo ortografico Bing e inizializzare l'app.

1. In Visual Studio selezionare la scheda corrispondente a `MainWindow.xaml.cs`.
1. Sostituire le istruzioni `using` prepopolate con quanto segue.  
   ```csharp
   using System;
   using System.Windows;
   using System.Net;
   using System.Net.Http;
   using System.IO;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using Newtonsoft.Json;
   ```
1. Individuare la classe `MainWindow : Window` e sostituirla con questo codice:
   ```csharp
   {
       // This sample uses the Cognitive Services subscription key for all services. To learn more about
       // authentication options, see: https://docs.microsoft.com/azure/cognitive-services/authentication.
       const string COGNITIVE_SERVICES_KEY = "YOUR_COG_SERVICES_KEY";
       // Endpoints for Translator Text and Bing Spell Check
       public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
       const string BING_SPELL_CHECK_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/bing/v7.0/spellcheck/";
       // An array of language codes
       private string[] languageCodes;

       // Dictionary to map language codes from friendly name (sorted case-insensitively on language name)
       private SortedDictionary<string, string> languageCodesAndTitles =
           new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

       // Global exception handler to display error message and exit
       private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
       {
           Exception e = (Exception)args.ExceptionObject;
           MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
           System.Windows.Application.Current.Shutdown();
       }
       // MainWindow constructor
       public MainWindow()
       {
           // Display a message if unexpected error is encountered
           AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

           if (COGNITIVE_SERVICES_KEY.Length != 32)
           {
               MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                   "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                   "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
               System.Windows.Application.Current.Shutdown();
           }
           else
           {
               // Start GUI
               InitializeComponent();
               // Get languages for drop-downs
               GetLanguagesForTranslate();
               // Populate drop-downs with values from GetLanguagesForTranslate
               PopulateLanguageMenus();
           }
       }
   // NOTE:
   // In the following sections, we'll add code below this.
   }
   ```
1. Aggiungere la chiave di sottoscrizione di Servizi cognitivi e salvare.

In questo blocco di codice vengono dichiarate due variabili membro che contengono informazioni sulle lingue disponibili per la traduzione:

| Variabile | Type | DESCRIZIONE |
|----------|------|-------------|
|`languageCodes` | Matrice di stringhe |Memorizza nella cache i codici di lingua. Il servizio Microsoft Translator usa codici brevi, ad esempio `en` per l'inglese, per identificare le lingue. |
|`languageCodesAndTitles` | Dizionario ordinato | Esegue il mapping dei nomi descrittivi dell'interfaccia utente nei codici brevi usati nell'API. Il mapping rispetta l'ordine alfabetico indipendentemente dalle maiuscole/minuscole. |

Quindi, all'interno del costruttore `MainWindow`, è stata aggiunta la gestione degli errori con `HandleExceptions`. In questo modo, ci si assicura che venga inviato un avviso se un'eccezione non viene gestita. Quindi viene eseguito un controllo per verificare che la chiave di sottoscrizione specificata sia di 32 caratteri. Se la chiave è composta da meno/più di 32 caratteri, viene generato un errore.

Se sono presenti chiavi che hanno almeno la lunghezza corretta, la chiamata a `InitializeComponent()` visualizza l'interfaccia utente individuando, caricando e creando un'istanza della descrizione XAML della finestra principale dell'app.

Infine, è stato aggiunto il codice per chiamare i metodi per recuperare le lingue per la traduzione e per popolare i menu a discesa dell'interfaccia utente dell'app. Il codice alla base di queste chiamate verrà descritto più avanti.

## <a name="get-supported-languages"></a>Ottenere le lingue supportate

L'API Traduzione testuale attualmente supporta più di 60 lingue. Nel corso del tempo verranno aggiunte altre lingue supportate, quindi è consigliabile chiamare la risorsa Languages esposta da Traduzione testuale invece di impostare come hardcoded l'elenco di lingue nell'app.

In questa sezione verrà creata una richiesta `GET` per la risorsa Languages, specificando che è necessario un elenco di lingue disponibili per la traduzione.

> [!NOTE]
> La risorsa Languages consente di filtrare le lingue supportate con i parametri di query seguenti: traslitterazione, dizionario e traduzione. Per altre informazioni, vedere il [riferimento API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

Prima di proseguire, esaminare l'output di esempio relativo a una chiamata alla risorsa Languages:

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
    }
    // Additional languages are provided in the full JSON output.
}
```

Da questo output è possibile estrarre il codice lingua e il valore `name` di una specifica lingua. L'app usa NewtonSoft.Json per deserializzare l'oggetto JSON ([`JsonConvert.DeserializeObject`](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm)).

Riprendendo da dove è stata interrotta l'ultima sezione, aggiungere un metodo per recuperare le lingue supportate nell'app.

1. In Visual Studio aprire la scheda corrispondente a `MainWindow.xaml.cs`.
2. Aggiungere il codice seguente al progetto:
   ```csharp
   // ***** GET TRANSLATABLE LANGUAGE CODES
   private void GetLanguagesForTranslate()
   {
       // Send request to get supported language codes
       string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
       WebRequest WebRequest = WebRequest.Create(uri);
       WebRequest.Headers.Add("Accept-Language", "en");
       WebResponse response = null;
       // Read and parse the JSON response
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
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Il metodo `GetLanguagesForTranslate()` crea una richiesta HTTP GET e usa il parametro della stringa di query `scope=translation` per limitare l'ambito della richiesta alle lingue supportate per la traduzione. Viene aggiunta l'intestazione `Accept-Language` con il valore `en`, in modo che le lingue supportate vengano restituite in inglese.

La risposta JSON viene analizzata e convertita in un dizionario. Quindi vengono aggiunti i codici lingua alla variabile membro `languageCodes`. Viene quindi eseguita la ricerca delle coppie chiave/valore che contengono i codici di lingua e i nomi di lingua descrittivi e tali coppie vengono aggiunte alla variabile membro `languageCodesAndTitles`. I menu a discesa nel modulo visualizzano i nomi descrittivi, ma per richiedere la traduzione sono necessari i codici.

## <a name="populate-language-drop-down-menus"></a>Popolare i menu a discesa delle lingue

L'interfaccia utente è definita con XAML, pertanto non è necessario eseguire molte altre operazioni per configurarla oltre alla chiamata a `InitializeComponent()`. L'unica altra attività che è necessario eseguire consiste nell'aggiunta di nomi di lingua descrittivi negli elenchi a discesa **Traduci da** e **Traduci in**. Il metodo `PopulateLanguageMenus()` aggiunge i nomi.

1. In Visual Studio aprire la scheda corrispondente a `MainWindow.xaml.cs`.
2. Aggiungere il codice seguente al progetto sotto il metodo `GetLanguagesForTranslate()`:
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

       // Set default languages
       FromLanguageComboBox.SelectedItem = "Detect";
       ToLanguageComboBox.SelectedItem = "English";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Questo metodo esegue l'iterazione sul dizionario `languageCodesAndTitles` e aggiunge ogni chiave a entrambi i menu. Dopo aver popolato i menu, le lingue predefinite di origine e destinazione vengono impostate rispettivamente su **Detect** e **English**.

> [!TIP]
> Senza una selezione predefinita per i menu, l'utente può fare clic su **Translate** senza prima scegliere una lingua di origine o di destinazione. Le impostazioni predefinite eliminano la necessità di affrontare il problema.

Dopo aver inizializzato `MainWindow` e creato l'interfaccia utente, il codice attende che l'utente faccia clic sul pulsante **Traduci**.

## <a name="detect-language-of-source-text"></a>Rilevare la lingua del testo di origine

A questo punto è necessario creare il metodo per rilevare la lingua del testo di origine, ossia quello immesso nell'area del testo, usando l'API Traduzione testuale. Il valore restituito da questa richiesta verrà usato più avanti nella richiesta di traduzione.

1. In Visual Studio aprire la scheda corrispondente a `MainWindow.xaml.cs`.
2. Aggiungere il codice seguente al progetto sotto il metodo `PopulateLanguageMenus()`:
   ```csharp
   // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
   private string DetectLanguage(string text)
   {
       string detectUri = string.Format(TEXT_TRANSLATION_API_ENDPOINT ,"detect");

       // Create request to Detect languages with Translator Text
       HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(detectUri);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
       detectLanguageWebRequest.ContentType = "application/json; charset=utf-8";
       detectLanguageWebRequest.Method = "POST";

       // Send request
       var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
       string jsonText = serializer.Serialize(text);

       string body = "[{ \"Text\": " + jsonText + " }]";
       byte[] data = Encoding.UTF8.GetBytes(body);

       detectLanguageWebRequest.ContentLength = data.Length;

       using (var requestStream = detectLanguageWebRequest.GetRequestStream())
           requestStream.Write(data, 0, data.Length);

       HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

       // Read and parse JSON response
       var responseStream = response.GetResponseStream();
       var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
       dynamic jsonResponse = serializer.DeserializeObject(jsonString);

       // Fish out the detected language code
       var languageInfo = jsonResponse[0];
       if (languageInfo["score"] > (decimal)0.5)
       {
           DetectedLanguageLabel.Content = languageInfo["language"];
           return languageInfo["language"];
       }
       else
           return "Unable to confidently detect input language.";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Questo metodo crea una richiesta HTTP `POST` alla risorsa Detect. Accetta un singolo argomento, `text`, che viene passato come corpo della richiesta. In seguito, quando verrà creata la richiesta di traduzione, il testo immesso nell'interfaccia utente verrà passato a questo metodo per il rilevamento della lingua.

Inoltre, questo metodo valuta il punteggio di attendibilità della risposta. Se il punteggio è maggiore di `0.5`, la lingua rilevata viene visualizzata nell'interfaccia utente.

## <a name="spell-check-the-source-text"></a>Eseguire il controllo ortografico del testo di origine

A questo punto creare un metodo per eseguire il controllo ortografico del testo di origine usando l'API Controllo ortografico Bing. In questo modo, ci si assicura che le traduzioni dell'API Traduzione testuale siano accurate. Le eventuali correzioni del testo di origine vengono passate nella richiesta di traduzione quando si fa clic sul pulsante **Traduci**.

1. In Visual Studio aprire la scheda corrispondente a `MainWindow.xaml.cs`.
2. Aggiungere il codice seguente al progetto sotto il metodo `DetectLanguage()`:

```csharp
// ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
private string CorrectSpelling(string text)
{
    string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

    // Create a request to Bing Spell Check API
    HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
    spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
    spellCheckWebRequest.Method = "POST";
    spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

    // Create and send the request
    string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
    byte[] data = Encoding.UTF8.GetBytes(body);
    spellCheckWebRequest.ContentLength = data.Length;
    using (var requestStream = spellCheckWebRequest.GetRequestStream())
        requestStream.Write(data, 0, data.Length);
    HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

    // Read and parse the JSON response; get spelling corrections
    var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
    var responseStream = response.GetResponseStream();
    var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
    dynamic jsonResponse = serializer.DeserializeObject(jsonString);
    var flaggedTokens = jsonResponse["flaggedTokens"];

    // Construct sorted dictionary of corrections in reverse order (right to left)
    // This ensures that changes don't impact later indexes
    var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
    for (int i = 0; i < flaggedTokens.Length; i++)
    {
        var correction = flaggedTokens[i];
        var suggestion = correction["suggestions"][0];  // Consider only first suggestion
        if (suggestion["score"] > (decimal)0.7)         // Take it only if highly confident
            corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
    }

    // Apply spelling corrections, in order, from right to left
    foreach (int i in corrections.Keys)
    {
        var oldtext = corrections[i][0];
        var newtext = corrections[i][1];

        // Apply capitalization from original text to correction - all caps or initial caps
        if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
        else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

        text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
    }
    return text;
}
// NOTE:
// In the following sections, we'll add code below this.
```

## <a name="translate-text-on-click"></a>Tradurre un testo al clic del mouse

Infine, è necessario creare un metodo che viene richiamato quando si fa clic sul pulsante **Traduci** dell'interfaccia utente.

1. In Visual Studio aprire la scheda corrispondente a `MainWindow.xaml.cs`.
1. Aggiungere il codice seguente al progetto sotto il metodo `CorrectSpelling()` e salvare:  
   ```csharp
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
           request.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
           request.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
           request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

           var response = await client.SendAsync(request);
           var responseBody = await response.Content.ReadAsStringAsync();

           var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
           var translation = result[0]["translations"][0]["text"];

           // Update the translation field
           TranslatedTextLabel.Content = translation;
       }
   }
   ```

Il primo passaggio consiste nel recuperare le lingue di origine e di destinazione, oltre al testo immesso dall'utente nel modulo. Se la lingua di origine è impostata su **Detect**, viene effettuata una chiamata a `DetectLanguage()` per identificarla. La lingua del testo potrebbe non essere supportata dall'API Traduzione. In tal caso, viene visualizzato un messaggio per informare l'utente e non viene restituita alcuna traduzione.

Se la lingua di origine è l'inglese (specificata o rilevata), eseguire il controllo ortografico del testo con `CorrectSpelling()` e applicare le correzioni. Il testo corretto viene reinserito nell'area del testo in modo che l'utente verifichi che è stata effettuata una traduzione.

Il codice per tradurre il testo dovrebbe risultare familiare: creare l'URI, creare una richiesta, inviarla e analizzare la risposta. La matrice JSON potrebbe contenere più oggetti per la traduzione, ma l'app ne richiede solo uno.

Dopo una richiesta riuscita, `TranslatedTextLabel.Content` viene sostituito con `translation`, che aggiorna l'interfaccia utente per visualizzare il testo tradotto.

## <a name="run-your-wpf-app"></a>Eseguire l'app WPF

A questo punto è disponibile un'app di traduzione funzionante creata con WPF. Per eseguirla, fare clic sul pulsante **Avvia** in Visual Studio.

## <a name="source-code"></a>Codice sorgente

Il codice sorgente di questo esempio è disponibile in GitHub.

* [Esplorare il codice sorgente](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp-Tutorial)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Microsoft Translator Text API reference](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) (Riferimento all'API Traduzione testuale di Microsoft)
