---
title: 'Guida introduttiva: Libreria client di Analisi del testo per .NET | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Usare questo argomento di avvio rapido per iniziare a usare l'API Analisi del testo di Servizi cognitivi di Azure.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/05/2019
ms.author: assafi
ms.openlocfilehash: deb8c742161d59c8926c1ec139978d15b891bd4a
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019483"
---
# <a name="quickstart-text-analytics-client-library-for-net"></a>Guida introduttiva: Libreria client di Analisi del testo per .NET
<a name="HOLTop"></a>

Introduzione alla libreria client di Analisi del testo per .NET. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. 

Usare la libreria client di Analisi del testo per .NET per eseguire queste operazioni:

* Analisi del sentiment
* Rilevamento della lingua
* Riconoscimento delle entità
* Estrazione di frasi chiave

[Documentazione di riferimento](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [Esempi](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

> [!NOTE] 
> Per semplicità, il codice dimostrativo incluso in questo articolo usa i metodi sincroni dell'SDK per .NET per Analisi del testo. Per scenari di produzione, è però consigliabile usare i metodi asincroni in batch nelle proprie applicazioni per mantenerle scalabili e reattive. Ad esempio, la chiamata di [SentimentBatchAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentimentbatchasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet-preview) invece di [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* Versione corrente di [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Configurazione

### <a name="create-a-text-analytics-azure-resource"></a>Creare una risorsa di Azure per Analisi del testo

Ottenere una chiave per l'autenticazione delle applicazioni tramite Servizi cognitivi di Azure, rappresentati da risorse di Azure di cui si effettua la sottoscrizione. Creare una risorsa per Analisi del testo usando il [portale di Azure](../../cognitive-services-apis-create-account.md) o l'[interfaccia della riga di comando di Azure](../../cognitive-services-apis-create-account-cli.md) nel computer locale. È anche possibile:

* Ottenere un [codice della versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/#decision), valido per 7 giorni. Dopo aver eseguito l'iscrizione, sarà disponibile sul [sito Web di Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visualizzare questa risorsa nel [portale di Azure](https://portal.azure.com/)

Dopo aver ottenuto una chiave dalla sottoscrizione di valutazione o dalla risorsa, [creare una variabile di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave, denominata `TEXT_ANALYTICS_SUBSCRIPTION_KEY`.

### <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

Creare una nuova applicazione .NET Core nell'ambiente di sviluppo integrato o nell'editor preferito. 

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `text-analytics quickstart`. Questo comando crea un semplice progetto C# "Hello World" con un unico file di origine: *program.cs*. 

```console
dotnet new console -n text-analytics-quickstart
```

Spostarsi nella cartella dell'app appena creata. È possibile compilare l'applicazione con il comando seguente:

```console
dotnet build
```

L'output di compilazione non deve contenere alcun avviso o errore. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Dalla directory del progetto aprire il file *program.cs* nell'ambiente di sviluppo integrato o nell'editor preferito. Aggiungere le direttive `using` seguenti:

```csharp
using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using Microsoft.Rest;
```

Nel metodo `Main` dell'applicazione creare variabili per l'endpoint e la chiave di Azure della risorsa. Se la variabile di ambiente è stata creata dopo l'avvio dell'applicazione, per accedervi sarà necessario chiudere e riaprire l'editor, l'IDE o la shell in cui è in esecuzione. Gli altri metodi verranno definiti successivamente.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```csharp
static void Main(string[] args)
{
    // replace this endpoint with the correct one for your Azure resource. 
    string endpoint = $"https://westus.api.cognitive.microsoft.com";
    //This sample assumes you have created an environment variable for your key
    string key = Environment.GetEnvironmentVariable("TEXT_ANALYTICS_SUBSCRIPTION_KEY");

    var credentials = new ApiKeyServiceClientCredentials(key);
    TextAnalyticsClient client = new TextAnalyticsClient(credentials)
    {
        Endpoint = endpoint
    };

    Console.OutputEncoding = System.Text.Encoding.UTF8;
    SentimentAnalysisExample(client);
    // languageDetectionExample(client);
    // RecognizeEntitiesExample(client);
    // KeyPhraseExtractionExample(client);
    Console.ReadLine();
}
```

### <a name="install-the-client-library"></a>Installare la libreria client

Nella directory dell'applicazione installare la libreria client di Analisi del testo per .NET con il comando seguente:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Language.TextAnalytics --version 4.0.0
```

Se si usa l'ambiente di sviluppo integrato di Visual Studio, la libreria client è disponibile come pacchetto NuGet scaricabile.

## <a name="object-model"></a>Modello a oggetti

Il client di Analisi del testo è un oggetto [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) che esegue l'autenticazione in Azure tramite la chiave e fornisce funzioni per accettare il testo come singole stringhe o batch. È possibile inviare testo all'API in modo sincrono o asincrono. L'oggetto risposta conterrà le informazioni di analisi per ogni documento inviato. 


## <a name="code-examples"></a>Esempi di codice

* [Autenticare il client](#authenticate-the-client)
* [Analisi del sentiment](#sentiment-analysis)
* [Rilevamento della lingua](#language-detection)
* [Riconoscimento delle entità](#entity-recognition)
* [Estrazione delle frasi chiave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticare il client

Creare una nuova classe `ApiKeyServiceClientCredentials` per archiviare le credenziali e aggiungerle alle richieste del client. Al suo interno, creare una sostituzione per `ProcessHttpRequestAsync()` per l'aggiunta della chiave all'intestazione `Ocp-Apim-Subscription-Key`.

```csharp
class ApiKeyServiceClientCredentials : ServiceClientCredentials
{
    private readonly string apiKey;

    public ApiKeyServiceClientCredentials(string apiKey)
    {
        this.apiKey = apiKey;
    }

    public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
    {
        if (request == null)
        {
            throw new ArgumentNullException("request");
        }
        request.Headers.Add("Ocp-Apim-Subscription-Key", this.apiKey);
        return base.ProcessHttpRequestAsync(request, cancellationToken);
    }
}
```

Nel metodo `main()` creare un'istanza del client con la chiave e l'endpoint.

```csharp
var credentials = new ApiKeyServiceClientCredentials(key);
TextAnalyticsClient client = new TextAnalyticsClient(credentials)
{
    Endpoint = endpoint
};
```

## <a name="sentiment-analysis"></a>Analisi del sentiment

Creare una nuova funzione denominata `SentimentAnalysisExample()` che accetta il client creato prima e chiama la relativa funzione [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet). L'oggetto [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) restituito otterrà l'oggetto `Score` della valutazione, se l'operazione riesce, oppure `errorMessage` in caso contrario. 

I punteggi vicini a 0 indicano un sentiment negativo, mentre quelli più vicini a 1 indicano un sentiment positivo.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client){
    var result = client.Sentiment("I had the best day of my life.", "en");
    Console.WriteLine($"Sentiment Score: {result.Score:0.00}");
}
```

### <a name="output"></a>Output

```console
Sentiment Score: 0.87
```

## <a name="language-detection"></a>Rilevamento della lingua

Creare una nuova funzione denominata `languageDetectionExample()` che accetta il client creato prima e chiama la relativa funzione [DetectLanguage()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). L'oggetto [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) restituito conterrà l'elenco delle lingue rilevate in `DetectedLanguages`, se l'operazione riesce, oppure `errorMessage` in caso contrario.  Stampare la prima lingua restituita.

> [!Tip]
> In alcuni casi potrebbe essere difficile distinguere le lingue in base all'input. È possibile usare il parametro `countryHint` per specificare un codice paese di 2 lettere. Per impostazione predefinita, l'API usa "US" come valore predefinito di countryHint. Per rimuovere questo comportamento, è possibile reimpostare questo parametro usando come valore una stringa vuota, `countryHint = ""`.

```csharp
static void languageDetectionExample(TextAnalyticsClient client){
    var result = client.DetectLanguage("This is a document written in English.");
    Console.WriteLine($"Language: {result.DetectedLanguages[0].Name}");
}
```
<!--
[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/language/Program.cs?name=language-detection)]
-->

### <a name="output"></a>Output

```console
Language: English
```

## <a name="entity-recognition"></a>Riconoscimento delle entità

Creare una nuova funzione denominata `RecognizeEntitiesExample()` che accetta il client creato prima e chiama la relativa funzione [Entities()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). Eseguire l'iterazione dei risultati. L'oggetto [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) restituito conterrà l'elenco delle entità rilevate in `Entities`, se l'operazione riesce, oppure `errorMessage` in caso contrario. Per ogni entità rilevata, stampare nome di Wikipedia, tipo e sottotipo (se presenti), nonché le posizioni nel testo originale.

```csharp
static void entityRecognitionExample(TextAnalyticsClient client){

    var result = client.Entities("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.");
    Console.WriteLine("Entities:");
    foreach (var entity in result.Entities){
        Console.WriteLine($"\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
        foreach (var match in entity.Matches){
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
        }
    }
}
```
<!--
[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/language/Program.cs?name=language-detection)]
-->

### <a name="output"></a>Output

```console
Entities:
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0,      Length: 9,      Score: 1.000
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25,     Length: 10,     Score: 1.000
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40,     Length: 10,     Score: 0.999
    Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54,     Length: 7,      Score: 0.800
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54,     Length: 13,     Score: 0.800
    Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89,     Length: 5,      Score: 0.800
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116,    Length: 11,     Score: 0.800
```

## <a name="key-phrase-extraction"></a>Estrazione di frasi chiave

Creare una nuova funzione denominata `KeyPhraseExtractionExample()` che accetta il client creato prima e chiama la relativa funzione [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). Il risultato conterrà l'elenco delle frasi chiave rilevate in `KeyPhrases` in caso di esito positivo ed `errorMessage` in caso contrario. Stampare tutte le frasi chiave rilevate.

```csharp
var result = client.KeyPhrases("My cat might need to see a veterinarian.");

// Printing key phrases
Console.WriteLine("Key phrases:");

foreach (string keyphrase in result.KeyPhrases)
{
    Console.WriteLine($"\t{keyphrase}");
}
```

### <a name="output"></a>Output

```console
Key phrases:
    cat
    veterinarian
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche le altre risorse associate al gruppo.

* [Portale](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analisi del testo con Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Panoramica di Analisi del testo](../overview.md)
* [Analisi del sentiment](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Riconoscimento delle entità](../how-tos/text-analytics-how-to-entity-linking.md)
* [Rilevare la lingua](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Riconoscimento della lingua](../how-tos/text-analytics-how-to-language-detection.md)
