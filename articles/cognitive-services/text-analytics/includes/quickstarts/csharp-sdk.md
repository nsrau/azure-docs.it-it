---
title: 'Avvio rapido: Libreria client di Analisi del testo per C# | Microsoft Docs'
description: Introduzione alla libreria client di Analisi del testo per C#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/27/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 7e074bf898667bb0019d4147ec9abaea2c674d74
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400959"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)

[Documentazione di riferimento v3.1](https://docs.microsoft.com/dotnet/api/azure.ai.textanalytics?view=azure-dotnet-previews&preserve-view=true) | [Codice sorgente della libreria v3.1](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics) | [Pacchetto (NuGet) v3.1](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.1.0-beta.1) | [Esempi v3.1](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

# <a name="version-30"></a>[Versione 3.0](#tab/version-3)

[Documentazione di riferimento v3](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [Codice sorgente della libreria v3](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics) | [Pacchetto (NuGet) v3](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [Esempi v3](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

# <a name="version-21"></a>[Versione 2.1](#tab/version-2)

[Documentazione di riferimento v2](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics) | [Codice sorgente della libreria v2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [Pacchetto (NuGet) v2](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [Esempi v2](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* L'[IDE di Visual Studio](https://visualstudio.microsoft.com/vs/)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="creare una risorsa di Analisi del testo"  target="_blank">creare una risorsa di Analisi del testo <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint.  Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Analisi del testo. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-net-core-application"></a>Creare una nuova applicazione .NET Core

Usando l'IDE di Visual Studio, creare una nuova app console .NET Core. Verrà creato un progetto "Hello World" con un singolo file di origine C#, *program.cs*.

# <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)

Installare la libreria client facendo clic con il pulsante destro del mouse sulla soluzione in **Esplora soluzioni** e scegliendo **Gestisci pacchetti NuGet**. Nella finestra di dialogo Gestione pacchetti visualizzata selezionare **Sfoglia** e cercare `Azure.AI.TextAnalytics`. Selezionare la casella **Includi versione preliminare**, selezionare la versione `5.1.0-beta.1` e quindi **Installa**. È anche possibile usare la [console di gestione pacchetti](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

# <a name="version-30"></a>[Versione 3.0](#tab/version-3)

Installare la libreria client facendo clic con il pulsante destro del mouse sulla soluzione in **Esplora soluzioni** e scegliendo **Gestisci pacchetti NuGet**. Nella finestra di dialogo Gestione pacchetti visualizzata selezionare **Sfoglia** e cercare `Azure.AI.TextAnalytics`. Selezionare la versione `5.0.0`, quindi **Installa**. È anche possibile usare la [console di gestione pacchetti](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).


> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), che contiene gli esempi di codice di questo argomento. 

# <a name="version-21"></a>[Versione 2.1](#tab/version-2)

Installare la libreria client facendo clic con il pulsante destro del mouse sulla soluzione in **Esplora soluzioni** e scegliendo **Gestisci pacchetti NuGet**. Nella finestra di dialogo Gestione pacchetti visualizzata selezionare **Sfoglia** e cercare `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Fare clic sulla voce e selezionare **Installa**. È anche possibile usare la [console di gestione pacchetti](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs), che contiene gli esempi di codice di questo argomento. 

---

# <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)

Aprire il file *program.cs* e aggiungere le direttive `using` seguenti:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Nella classe `Program` dell'applicazione creare variabili per l'endpoint e la chiave della risorsa.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Sostituire il metodo `Main` dell'applicazione. I metodi chiamati qui verranno definiti in un secondo momento.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    SentimentAnalysisWithOpinionMiningExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    RecognizePIIExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

# <a name="version-30"></a>[Versione 3.0](#tab/version-3)

Aprire il file *program.cs* e aggiungere le direttive `using` seguenti:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Nella classe `Program` dell'applicazione creare variabili per l'endpoint e la chiave della risorsa.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Sostituire il metodo `Main` dell'applicazione. I metodi chiamati qui verranno definiti in un secondo momento.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

# <a name="version-21"></a>[Versione 2.1](#tab/version-2)

Aprire il file *program.cs* e aggiungere le direttive `using` seguenti:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

Nella classe `Program` dell'applicazione creare variabili per l'endpoint e la chiave della risorsa. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Sostituire il metodo `Main` dell'applicazione. I metodi chiamati qui verranno definiti in un secondo momento.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Modello a oggetti

Il client di Analisi del testo è un oggetto `TextAnalyticsClient` che esegue l'autenticazione in Azure tramite la chiave e fornisce funzioni per accettare il testo come singole stringhe o batch. È possibile inviare testo all'API in modo sincrono o asincrono. L'oggetto risposta conterrà le informazioni di analisi per ogni documento inviato. 

Se si usa la versione `3.x` del servizio, è possibile usare un'istanza facoltativa di `TextAnalyticsClientOptions` per inizializzare il client con varie impostazioni predefinite, ad esempio il suggerimento sul paese/area geografica o la lingua predefinita. È anche possibile eseguire l'autenticazione con un token di Azure Active Directory. 

## <a name="code-examples"></a>Esempi di codice

* [Analisi del sentiment](#sentiment-analysis)
* [Rilevamento della lingua](#language-detection)
* [Riconoscimento di entità denominate](#named-entity-recognition-ner)
* [Collegamento di entità](#entity-linking)
* [Estrazione delle frasi chiave](#key-phrase-extraction)


## <a name="authenticate-the-client"></a>Autenticare il client

# <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)

Verificare che il metodo main creato in precedenza crei un nuovo oggetto client con l'endpoint e le credenziali specificate.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

# <a name="version-30"></a>[Versione 3.0](#tab/version-3)

Verificare che il metodo main creato in precedenza crei un nuovo oggetto client con l'endpoint e le credenziali specificate.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

# <a name="version-21"></a>[Versione 2.1](#tab/version-2)

Creare una nuova classe `ApiKeyServiceClientCredentials` per archiviare le credenziali e aggiungerle alle richieste del client. Al suo interno, creare una sostituzione per `ProcessHttpRequestAsync()` per l'aggiunta della chiave all'intestazione `Ocp-Apim-Subscription-Key`.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Creare un metodo per creare un'istanza dell'oggetto [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient) con l'endpoint e un oggetto `ApiKeyServiceClientCredentials` contenente la chiave.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Analisi del sentiment

# <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)

Creare una nuova funzione denominata `SentimentAnalysisExample()`, che accetta il client creato in precedenza e chiama la relativa funzione `AnalyzeSentiment()`. Se l'operazione riesce, l'oggetto `Response<DocumentSentiment>` restituito conterrà l'etichetta e il punteggio del sentiment dell'intero documento di input, oltre all'analisi del sentiment per ogni frase. Se si è verificato un errore, verrà generata un'eccezione `RequestFailedException`.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Output

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

### <a name="opinion-mining"></a>Opinion mining

Creare una nuova funzione denominata `SentimentAnalysisWithOpinionMiningExample()`, che accetta il client creato in precedenza e chiama la relativa funzione `AnalyzeSentimentBatch()` con l'opzione `AdditionalSentimentAnalyses.OpinionMining`. L'oggetto `AnalyzeSentimentResultCollection` restituito conterrà la raccolta di `AnalyzeSentimentResult` in cui rappresenta `Response<DocumentSentiment>`. La differenza tra `SentimentAnalysis()` e `SentimentAnalysisWithOpinionMiningExample()` è che quest'ultimo conterrà `MinedOpinion` in ogni frase, che mostra un aspetto analizzato e le opinioni correlate. Se si è verificato un errore, verrà generata un'eccezione `RequestFailedException`.

```csharp
static void SentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    var documents = new List<string>
    {
        "The food and service were unacceptable, but the concierge were nice.",
        "The rooms were beautiful. The AC was good and quiet.",
        "The breakfast was good, but the toilet was smelly.",
        "Loved this hotel - good breakfast - nice shuttle service - clean rooms.",
        "I had a great unobstructed view of the Microsoft campus.",
        "Nice rooms but bathrooms were old and the toilet was dirty when we arrived.",
        "We changed rooms as the toilet smelled."
    };

    AnalyzeSentimentResultCollection reviews = client.AnalyzeSentimentBatch(documents, options: new AnalyzeSentimentOptions()
    {
        AdditionalSentimentAnalyses = AdditionalSentimentAnalyses.OpinionMining
    });

    foreach (AnalyzeSentimentResult review in reviews)
    {
        Console.WriteLine($"Document sentiment: {review.DocumentSentiment.Sentiment}\n");
        foreach (SentenceSentiment sentence in review.DocumentSentiment.Sentences)
        {
            Console.WriteLine($"\tText: \"{sentence.Text}\"");
            Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
            Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
            Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
            Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");

            foreach (MinedOpinion minedOpinion in sentence.MinedOpinions)
            {
                Console.WriteLine($"\tAspect: {minedOpinion.Aspect.Text}, Value: {minedOpinion.Aspect.Sentiment}");
                foreach (OpinionSentiment opinion in minedOpinion.Opinions)
                {
                    Console.WriteLine($"\t\tRelated Opinion: {opinion.Text}, Value: {opinion.Sentiment}");
                }
            }
        }
        Console.WriteLine($"\n");
    }
}
```

### <a name="output"></a>Output

```console
Document sentiment: Positive

        Text: "The food and service were unacceptable, but the concierge were nice."
        Sentence sentiment: Positive
        Positive score: 0.84
        Negative score: 0.16
        Neutral score: 0.00

        Aspect: food, Value: Negative
                Related Opinion: unacceptable, Value: Negative
        Aspect: service, Value: Negative
                Related Opinion: unacceptable, Value: Negative
        Aspect: concierge, Value: Positive
                Related Opinion: nice, Value: Positive


Document sentiment: Positive

        Text: "The rooms were beautiful."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Aspect: rooms, Value: Positive
                Related Opinion: beautiful, Value: Positive
        Text: "The AC was good and quiet."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Aspect: AC, Value: Positive
                Related Opinion: good, Value: Positive
                Related Opinion: quiet, Value: Positive


Document sentiment: Negative

        Text: "The breakfast was good, but the toilet was smelly."
        Sentence sentiment: Negative
        Positive score: 0.01
        Negative score: 0.99
        Neutral score: 0.00

        Aspect: breakfast, Value: Positive
                Related Opinion: good, Value: Positive
        Aspect: toilet, Value: Negative
                Related Opinion: smelly, Value: Negative


Document sentiment: Positive

        Text: "Loved this hotel - good breakfast - nice shuttle service - clean rooms."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Aspect: hotel, Value: Positive
                Related Opinion: good, Value: Positive
        Aspect: breakfast, Value: Positive
                Related Opinion: nice, Value: Positive
        Aspect: shuttle service, Value: Positive
                Related Opinion: loved, Value: Positive
        Aspect: rooms, Value: Positive
                Related Opinion: good, Value: Positive
                Related Opinion: nice, Value: Positive
                Related Opinion: clean, Value: Positive
                Related Opinion: loved, Value: Positive


Document sentiment: Positive

        Text: "I had a great unobstructed view of the Microsoft campus."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Aspect: view, Value: Positive
                Related Opinion: great, Value: Positive
                Related Opinion: unobstructed, Value: Positive


Document sentiment: Negative

        Text: "Nice rooms but bathrooms were old and the toilet was dirty when we arrived."
        Sentence sentiment: Negative
        Positive score: 0.00
        Negative score: 1.00
        Neutral score: 0.00

        Aspect: rooms, Value: Positive
                Related Opinion: nice, Value: Positive
        Aspect: bathrooms, Value: Negative
                Related Opinion: old, Value: Negative
        Aspect: toilet, Value: Negative
                Related Opinion: dirty, Value: Negative


Document sentiment: Neutral

        Text: "We changed rooms as the toilet smelled."
        Sentence sentiment: Neutral
        Positive score: 0.01
        Negative score: 0.03
        Neutral score: 0.96

        Aspect: rooms, Value: Negative
                Related Opinion: smelled, Value: Negative
        Aspect: toilet, Value: Negative
                Related Opinion: smelled, Value: Negative
```

# <a name="version-30"></a>[Versione 3.0](#tab/version-3)

Creare una nuova funzione denominata `SentimentAnalysisExample()`, che accetta il client creato in precedenza e chiama la relativa funzione `AnalyzeSentiment()`. Se l'operazione riesce, l'oggetto `Response<DocumentSentiment>` restituito conterrà l'etichetta e il punteggio del sentiment dell'intero documento di input, oltre all'analisi del sentiment per ogni frase. Se si è verificato un errore, verrà generata un'eccezione `RequestFailedException`.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Output

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

# <a name="version-21"></a>[Versione 2.1](#tab/version-2)

Creare una nuova funzione denominata `SentimentAnalysisExample()` che accetta il client creato prima e chiama la relativa funzione [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment). L'oggetto [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult) restituito otterrà l'oggetto `Score` della valutazione, se l'operazione riesce, oppure `errorMessage` in caso contrario. 

I punteggi vicini a 0 indicano un sentiment negativo, mentre quelli più vicini a 1 indicano un sentiment positivo.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Rilevamento della lingua

# <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)


Creare una nuova funzione denominata `LanguageDetectionExample()`, che accetta il client creato in precedenza e chiama la relativa funzione `DetectLanguage()`. L'oggetto `Response<DetectedLanguage>` restituito conterrà la lingua rilevata insieme al nome e al codice ISO-6391. Se si è verificato un errore, verrà generata un'eccezione `RequestFailedException`.

> [!Tip]
> In alcuni casi potrebbe essere difficile distinguere le lingue in base all'input. È possibile usare il parametro `countryHint` per specificare un codice paese/area geografica di 2 lettere. Per impostazione predefinita, l'API usa "US" come valore predefinito di countryHint. Per rimuovere questo comportamento, è possibile reimpostare questo parametro usando come valore una stringa vuota, `countryHint = ""`. Per impostare un valore predefinito diverso, impostare la proprietà `TextAnalyticsClientOptions.DefaultCountryHint` e passarla durante l'inizializzazione del client.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Output

```console
Language:
        French, ISO-6391: fr
```

# <a name="version-30"></a>[Versione 3.0](#tab/version-3)


Creare una nuova funzione denominata `LanguageDetectionExample()`, che accetta il client creato in precedenza e chiama la relativa funzione `DetectLanguage()`. L'oggetto `Response<DetectedLanguage>` restituito conterrà la lingua rilevata insieme al nome e al codice ISO-6391. Se si è verificato un errore, verrà generata un'eccezione `RequestFailedException`.

> [!Tip]
> In alcuni casi potrebbe essere difficile distinguere le lingue in base all'input. È possibile usare il parametro `countryHint` per specificare un codice paese/area geografica di 2 lettere. Per impostazione predefinita, l'API usa "US" come valore predefinito di countryHint. Per rimuovere questo comportamento, è possibile reimpostare questo parametro usando come valore una stringa vuota, `countryHint = ""`. Per impostare un valore predefinito diverso, impostare la proprietà `TextAnalyticsClientOptions.DefaultCountryHint` e passarla durante l'inizializzazione del client.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Output

```console
Language:
        French, ISO-6391: fr
```

# <a name="version-21"></a>[Versione 2.1](#tab/version-2)

Creare una nuova funzione denominata `languageDetectionExample()` che accetta il client creato prima e chiama la relativa funzione [DetectLanguage()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). L'oggetto [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult) restituito conterrà l'elenco delle lingue rilevate in `DetectedLanguages`, se l'operazione riesce, oppure `errorMessage` in caso contrario. Stampare la prima lingua restituita.

> [!Tip]
> In alcuni casi potrebbe essere difficile distinguere le lingue in base all'input. È possibile usare il parametro `countryHint` per specificare un codice paese/area geografica di 2 lettere. Per impostazione predefinita, l'API usa "US" come valore predefinito di countryHint. Per rimuovere questo comportamento, è possibile reimpostare questo parametro usando come valore una stringa vuota, `countryHint = ""`.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Output

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Riconoscimento delle entità denominate (NER)

# <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)


Creare una nuova funzione denominata `EntityRecognitionExample()`, che accetta il client creato in precedenza, chiama la relativa funzione `RecognizeEntities()` e scorre i risultati. L'oggetto `Response<CategorizedEntityCollection>` restituito conterrà la raccolta delle entità rilevate `CategorizedEntity`. Se si è verificato un errore, verrà generata un'eccezione `RequestFailedException`.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2},\tLength: {entity.Length},\tOffset: {entity.Offset}\n");
    }
}
```

### <a name="output"></a>Output

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61,    Length: 4,      Offset: 18

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82,    Length: 7,      Offset: 26

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80,    Length: 9,      Offset: 34
```

### <a name="entity-linking"></a>Collegamento di entità

Creare una nuova funzione denominata `EntityLinkingExample()`, che accetta il client creato in precedenza, chiama la relativa funzione `RecognizeLinkedEntities()` e scorre i risultati. L'oggetto `Response<LinkedEntityCollection>` restituito conterrà la raccolta delle entità rilevate `LinkedEntity`. Se si è verificato un errore, verrà generata un'eccezione `RequestFailedException`. Poiché le entità collegate vengono identificate in modo univoco, le occorrenze della stessa entità vengono raggruppate in un oggetto `LinkedEntity` come elenco di oggetti `LinkedEntityMatch`.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}");
            Console.WriteLine($"\t\tLength: {match.Length}");
            Console.WriteLine($"\t\tOffset: {match.Offset}\n");
        }
    }
}
```

### <a name="output"></a>Output

```console
Linked Entities:
        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 0

                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 150

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63
                Length: 10
                Offset: 25

                Text: Gates
                Score: 0.63
                Length: 5
                Offset: 161

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60
                Length: 10
                Offset: 40

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32
                Length: 7
                Offset: 54

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
                Length: 5
                Offset: 89

        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88
                Length: 11
                Offset: 116
```

### <a name="personally-identifiable-information-recognition"></a>Riconoscimento di informazioni personali

Creare una nuova funzione denominata `RecognizePIIExample()`, che accetta il client creato in precedenza, chiama la relativa funzione `RecognizePiiEntities()` e scorre i risultati. L'oggetto `PiiEntityCollection` restituito rappresenta l'elenco delle entità PII rilevate. Se si è verificato un errore, verrà generata un'eccezione `RequestFailedException`.

```csharp
static void RecognizePIIExample(TextAnalyticsClient client)
{
    string document = "A developer with SSN 859-98-0987 whose phone number is 800-102-1100 is building tools with our APIs.";

    PiiEntityCollection entities = client.RecognizePiiEntities(document).Value;

    Console.WriteLine($"Redacted Text: {entities.RedactedText}");
    if (entities.Count > 0)
    {
        Console.WriteLine($"Recognized {entities.Count} PII entit{(entities.Count > 1 ? "ies" : "y")}:");
        foreach (PiiEntity entity in entities)
        {
            Console.WriteLine($"Text: {entity.Text}, Category: {entity.Category}, SubCategory: {entity.SubCategory}, Confidence score: {entity.ConfidenceScore}");
        }
    }
    else
    {
        Console.WriteLine("No entities were found.");
    }
}
```

### <a name="output"></a>Output

```console
Redacted Text: A developer with SSN *********** whose phone number is ************ is building tools with our APIs.
Recognized 2 PII entities:
Text: 859-98-0987, Category: U.S. Social Security Number (SSN), SubCategory: , Confidence score: 0.65
Text: 800-102-1100, Category: Phone Number, SubCategory: , Confidence score: 0.8
```

# <a name="version-30"></a>[Versione 3.0](#tab/version-3)


> [!NOTE]
> Novità nella versione `3.0`:
> * Il collegamento di entità è ora separato dal riconoscimento delle entità.


Creare una nuova funzione denominata `EntityRecognitionExample()`, che accetta il client creato in precedenza, chiama la relativa funzione `RecognizeEntities()` e scorre i risultati. L'oggetto `Response<IReadOnlyCollection<CategorizedEntity>>` restituito conterrà l'elenco di entità rilevate. Se si è verificato un errore, verrà generata un'eccezione `RequestFailedException`.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Output

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80
```

### <a name="entity-linking"></a>Collegamento di entità

Creare una nuova funzione denominata `EntityLinkingExample()`, che accetta il client creato in precedenza, chiama la relativa funzione `RecognizeLinkedEntities()` e scorre i risultati. L'oggetto `Response<IReadOnlyCollection<LinkedEntity>>` restituito rappresenta l'elenco delle entità rilevate. Se si è verificato un errore, verrà generata un'eccezione `RequestFailedException`. Poiché le entità collegate vengono identificate in modo univoco, le occorrenze della stessa entità vengono raggruppate in un oggetto `LinkedEntity` come elenco di oggetti `LinkedEntityMatch`.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}\n");
        }
    }
}
```

### <a name="output"></a>Output

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63

                Text: Gates
                Score: 0.63

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55

                Text: Microsoft
                Score: 0.55

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
```

# <a name="version-21"></a>[Versione 2.1](#tab/version-2)

> [!NOTE]
> Nella versione 2.1 il collegamento di entità è incluso nella risposta del riconoscimento di entità denominate.

Creare una nuova funzione denominata `RecognizeEntitiesExample()` che accetta il client creato prima e chiama la relativa funzione [Entities()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). Eseguire l'iterazione dei risultati. L'oggetto [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult) restituito conterrà l'elenco delle entità rilevate in `Entities`, se l'operazione riesce, oppure `errorMessage` in caso contrario. Per ogni entità rilevata, stampare nome di Wikipedia, tipo e sottotipo (se presenti), nonché le posizioni nel testo originale.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


### <a name="key-phrase-extraction"></a>Estrazione di frasi chiave

# <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)

Creare una nuova funzione denominata `KeyPhraseExtractionExample()`, che accetta il client creato in precedenza e chiama la relativa funzione `ExtractKeyPhrases()`. L'oggetto `<Response<KeyPhraseCollection>` restituito conterrà l'elenco di frasi chiave rilevate. Se si è verificato un errore, verrà generata un'eccezione `RequestFailedException`.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Output

```console
Key phrases:
    cat
    veterinarian
```

# <a name="version-30"></a>[Versione 3.0](#tab/version-3)

Creare una nuova funzione denominata `KeyPhraseExtractionExample()`, che accetta il client creato in precedenza e chiama la relativa funzione `ExtractKeyPhrases()`. L'oggetto `<Response<IReadOnlyCollection<string>>` restituito conterrà l'elenco di frasi chiave rilevate. Se si è verificato un errore, verrà generata un'eccezione `RequestFailedException`.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Output

```console
Key phrases:
    cat
    veterinarian
```

# <a name="version-21"></a>[Versione 2.1](#tab/version-2)

Creare una nuova funzione denominata `KeyPhraseExtractionExample()` che accetta il client creato prima e chiama la relativa funzione [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). Il risultato conterrà l'elenco delle frasi chiave rilevate in `KeyPhrases` in caso di esito positivo ed `errorMessage` in caso contrario. Stampare tutte le frasi chiave rilevate.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Output

```console
Key phrases:
    cat
    veterinarian
```

---