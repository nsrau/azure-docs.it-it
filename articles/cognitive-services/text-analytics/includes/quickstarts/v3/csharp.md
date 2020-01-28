---
title: 'Avvio rapido: Libreria client di Analisi del testo v3 per C# | Microsoft Docs'
description: Introduzione alla libreria client di Analisi del testo v3 per C#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 2cf9a006e1f6f1edb996aa9beaf8934a14af29df
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281165"
---
<a name="HOLTop"></a>

[Documentazione di riferimento](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [Esempi](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

> [!NOTE]
> * Questo argomento di avvio rapido la versione `3.0-preview` della libreria client di Analisi del testo, che include un'anteprima pubblica con funzionalità migliorate di [Analisi del sentiment](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) e [riconoscimento di entità denominate](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
> * Il codice di questo articolo usa metodi sincroni e archiviazione con credenziali non protette per motivi di semplicità. Per gli scenari di produzione, è consigliabile usare metodi asincroni in batch per favorire prestazioni e scalabilità. Ad esempio, chiamare `AnalyzeSentimentAsync()` invece di `AnalyzeSentiment()`.

## <a name="prerequisites"></a>Prerequisites

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* L'[IDE di Visual Studio](https://visualstudio.microsoft.com/vs/)

## <a name="setting-up"></a>Configurazione

### <a name="create-a-text-analytics-azure-resource"></a>Creare una risorsa di Azure per Analisi del testo

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-net-core-application"></a>Creare una nuova applicazione .NET Core

Usando l'IDE di Visual Studio, creare una nuova app console .NET Core. Verrà creato un progetto "Hello World" con un singolo file di origine C#, *program.cs*.

Installare la libreria client facendo clic con il pulsante destro del mouse sulla soluzione in **Esplora soluzioni** e scegliendo **Gestisci pacchetti NuGet**. Nella finestra di dialogo Gestione pacchetti visualizzata selezionare **Sfoglia**, **Includi versione preliminare** e cercare `Azure.AI.TextAnalytics`. Fare clic sulla voce e selezionare **Installa**. È anche possibile usare la [console di gestione pacchetti](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

Aprire il file *program.cs* e aggiungere le direttive `using` seguenti:

```csharp
using System;
using Azure.AI.TextAnalytics;
```

Nella classe `Program` dell'applicazione creare variabili per l'endpoint e la chiave della risorsa.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Sostituire il metodo `Main` dell'applicazione. I metodi chiamati qui verranno definiti in un secondo momento.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, key);

    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityPIIExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

## <a name="object-model"></a>Modello a oggetti

Il client di Analisi del testo è un oggetto `TextAnalyticsClient` che esegue l'autenticazione in Azure tramite la chiave e fornisce funzioni per accettare il testo come singole stringhe o batch. È possibile inviare testo all'API in modo sincrono o asincrono. L'oggetto risposta conterrà le informazioni di analisi per ogni documento inviato. È possibile usare un'istanza di `TextAnalyticsClientOptions` facoltativa per inizializzare il client con varie impostazioni predefinite, ad esempio la lingua o il suggerimento sul paese.

Altri esempi, tra cui sull'autenticazione di AAD e l'uso delle impostazioni predefinite del client, sono disponibili [qui](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples).

## <a name="code-examples"></a>Esempi di codice

* [Analisi del sentiment](#sentiment-analysis) (anteprima pubblica)
* [Rilevamento della lingua](#language-detection)
* [Riconoscimento di entità denominate](#named-entity-recognition-public-preview) (anteprima pubblica)
* [Riconoscimento di entità denominate - informazioni personali](#named-entity-recognition---personal-information-public-preview) (anteprima pubblica)
* [Collegamento di entità](#entity-linking)
* [Estrazione delle frasi chiave](#key-phrase-extraction)

Nel metodo `main()` del programma chiamare il metodo di autenticazione per creare un'istanza del client.

## <a name="sentiment-analysis-public-preview"></a>Analisi del sentiment (anteprima pubblica)

> [!NOTE]
> Il codice seguente riguarda l'analisi del sentiment v3, disponibile in anteprima pubblica.

Creare una nuova funzione denominata `SentimentAnalysisExample()`, che accetta il client creato in precedenza e chiama la relativa funzione `AnalyzeSentiment()`. L'oggetto `Response<AnalyzeSentimentResult>` restituito conterrà l'etichetta e il punteggio del sentiment dell'intero documento di input, oltre all'analisi del sentiment per ogni frase, se l'operazione riesce, oppure `Value.ErrorMessage` in caso contrario.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    var response = client.AnalyzeSentiment("I had the best day of my life. I wish you were there with me.");
    Console.WriteLine($"Document sentiment: {response.Value.DocumentSentiment.SentimentClass}\n");
    foreach (var sentence in response.Value.SentenceSentiments)
    {
        Console.WriteLine($"\tSentence [offset {sentence.Offset}, length {sentence.Length}]");
        Console.WriteLine($"\tSentence sentiment: {sentence.SentimentClass}");
        Console.WriteLine($"\tPositive score: {sentence.PositiveScore:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.NegativeScore:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.NeutralScore:0.00}\n");
    }
}
```

### <a name="output"></a>Output

```console
Document sentiment: Positive

        Sentence [offset 0, length 30]
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [offset 31, length 30]
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

## <a name="language-detection"></a>Rilevamento della lingua

Creare una nuova funzione denominata `LanguageDetectionExample()`, che accetta il client creato in precedenza e chiama la relativa funzione `DetectLanguage()`. L'oggetto `Response<DetectLanguageResult>` restituito conterrà la lingua rilevata in `Value.PrimaryLanguage`, se l'operazione riesce, oppure `Value.ErrorMessage` in caso contrario.

> [!Tip]
> In alcuni casi potrebbe essere difficile distinguere le lingue in base all'input. È possibile usare il parametro `countryHint` per specificare un codice paese di 2 lettere. Per impostazione predefinita, l'API usa "US" come valore predefinito di countryHint. Per rimuovere questo comportamento, è possibile reimpostare questo parametro usando come valore una stringa vuota, `countryHint = ""`. Per impostare un valore predefinito diverso, impostare la proprietà `TextAnalyticsClientOptions.DefaultCountryHint` e passarla durante l'inizializzazione del client.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    var response = client.DetectLanguage("Ce document est rédigé en Français.");
    var detectedLanguage = response.Value.PrimaryLanguage;
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Output

```console
Language:
        French, ISO-6391: fr
```

## <a name="named-entity-recognition-public-preview"></a>Riconoscimento di entità denominate (anteprima pubblica)

> [!NOTE]
> Il codice seguente riguarda il riconoscimento di entità denominate v3, disponibile in anteprima pubblica.

Creare una nuova funzione denominata `EntityRecognitionExample()`, che accetta il client creato in precedenza, chiama la relativa funzione `RecognizeEntities()` e scorre i risultati. L'oggetto `Response<RecognizeEntitiesResult>` restituito conterrà l'elenco di entità rilevate in `Value.NamedEntities`, se l'operazione riesce, oppure `Value.ErrorMessage` in caso contrario. Per ogni entità rilevata, stamparne il tipo e il sottotipo se esiste.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Output

```console
Named Entities:
        Text: Seattle,  Type: Location, Sub-Type: N/A
                Offset: 26,     Length: 7,      Score: 0.806

        Text: last week,        Type: DateTime, Sub-Type: N/A
                Offset: 34,     Length: 9,      Score: 0.800
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>Riconoscimento di entità denominate - informazioni personali (anteprima pubblica)

> [!NOTE]
> Il codice seguente riguarda il rilevamento di informazioni personali tramite il riconoscimento di entità denominate v3, disponibile in anteprima pubblica.

Creare una nuova funzione denominata `EntityPIIExample()`, che accetta il client creato in precedenza, chiama la relativa funzione `RecognizePiiEntities()` e scorre i risultati. Analogamente alla funzione precedente, l'oggetto `Response<RecognizeEntitiesResult>` restituito conterrà l'elenco delle entità rilevate in `Value.NamedEntities`, se l'operazione riesce, oppure `Value.ErrorMessage` in caso contrario.

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    var response = client.RecognizePiiEntities("Insurance policy for SSN on file 123-12-1234 is here by approved.");
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Output

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Type: U.S. Social Security Number (SSN),        Sub-Type: N/A
                Offset: 33,     Length: 11,     Score: 0.850
```

## <a name="entity-linking"></a>Collegamento delle entità

Creare una nuova funzione denominata `EntityLinkingExample()`, che accetta il client creato in precedenza, chiama la relativa funzione `RecognizeLinkedEntities()` e scorre i risultati. L'oggetto `Response<RecognizeLinkedEntitiesResult>` restituito conterrà l'elenco di entità rilevate in `Value.LinkedEntities`, se l'operazione riesce, oppure `Value.ErrorMessage` in caso contrario. Poiché le entità collegate vengono identificate in modo univoco, le occorrenze della stessa entità vengono raggruppate in un oggetto `LinkedEntity` come elenco di oggetti `LinkedEntityMatch`.

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
    foreach (var entity in response.Value.LinkedEntities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.Id},\tURL: {entity.Uri}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.Score:F3}\n");
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
                Offset: 116,    Length: 11,     Score: 0.777

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25,     Length: 10,     Score: 0.555

                Text: Gates
                Offset: 161,    Length: 5,      Score: 0.555

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40,     Length: 10,     Score: 0.533

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0,      Length: 9,      Score: 0.469

                Text: Microsoft
                Offset: 150,    Length: 9,      Score: 0.469

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54,     Length: 7,      Score: 0.248

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89,     Length: 5,      Score: 0.281
```

## <a name="key-phrase-extraction"></a>Estrazione di frasi chiave

Creare una nuova funzione denominata `KeyPhraseExtractionExample()`, che accetta il client creato in precedenza e chiama la relativa funzione `ExtractKeyPhrases()`. Il risultato conterrà l'elenco delle frasi chiave rilevate in `Value.KeyPhrases`, se l'operazione riesce, oppure `Value.ErrorMessage` in caso contrario. Stampare tutte le frasi chiave rilevate.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value.KeyPhrases)
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
