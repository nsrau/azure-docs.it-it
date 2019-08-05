---
title: 'Guida introduttiva: Chiamare il servizio Analisi del testo usando Azure SDK per .NET e C#'
titleSuffix: Azure Cognitive Services
description: Informazioni ed esempi di codice utili per iniziare a usare il servizio Analisi del testo e C#.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: assafi
ms.openlocfilehash: 6bd3907392dad626c1eeb1823c929f1a35d544dd
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697667"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>Guida introduttiva: Usare .NET SDK e C# per chiamare il servizio Analisi del testo
<a name="HOLTop"></a>

Questo argomento di avvio rapido fornisce informazioni utili per iniziare a usare Azure SDK per .NET e C# per analizzare il linguaggio. Sebbene l'API REST di [Analisi del testo](//go.microsoft.com/fwlink/?LinkID=759711) sia compatibile con la maggior parte dei linguaggi di programmazione, l'SDK rappresenta un modo semplice per integrare il servizio nelle applicazioni.

> [!NOTE]
> Per semplicità, il codice dimostrativo incluso in questo articolo usa i metodi sincroni dell'SDK per .NET per Analisi del testo. Per scenari di produzione, è però consigliabile usare i metodi asincroni in batch nelle proprie applicazioni per mantenerle scalabili e reattive. È ad esempio possibile usare `SentimentBatchAsync` invece di `Sentiment`.
>
> Una versione asincrona in batch di questo esempio è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics).

Per i dettagli tecnici, vedere le [informazioni di riferimento per Analisi del testo](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet) relative all'SDK per .NET.

## <a name="prerequisites"></a>Prerequisiti

* Qualsiasi edizione di Visual Studio 2017 o versioni successive
* Text Analytics [SDK per .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

## <a name="create-the-visual-studio-solution-and-install-the-sdk"></a>Creare la soluzione in Visual Studio e installare l'SDK

1. Creare un nuovo progetto di app console (.NET Core). [Accedere a Visual Studio](https://visualstudio.microsoft.com/vs/).
1. Fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Gestisci pacchetti NuGet per la soluzione**.
1. Selezionare la scheda **Sfoglia**. Cercare **Microsoft.Azure.CognitiveServices.Language.TextAnalytics**.

## <a name="authenticate-your-credentials"></a>Eseguire l'autenticazione delle proprie credenziali

1. Aggiungere le istruzioni `using` seguenti al file di classe principale (Program.cs per impostazione predefinita).

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

2. Creare una nuova classe `ApiKeyServiceClientCredentials` per archiviare le credenziali e aggiungerle per ogni richiesta.

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

3. Aggiornare la classe `Program`. Aggiungere un membro costante per la chiave API di Analisi del testo e un altro per l'endpoint servizio. Ricordare di usare la località di Azure corretta per la risorsa Analisi del testo.

    ```csharp
    //Enter your Text Analytics (TA) API Key (available in Azure Portal -> your TA resource -> Keys)
    private const string ApiKey = "enter-your-textanalytics-api-key-here";
    //You can get the resource location from Azure Portal -> your TA resource -> Overview
    private const string Endpoint = "enter-your-service-endpoint-here"; // For example: "https://<your-location>.api.cognitive.microsoft.com";
    ```

> [!Tip]
> Per rendere più sicuri i segreti nei sistemi di produzione, è consigliabile usare [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

## <a name="create-a-text-analytics-client"></a>Creare un client di Analisi del testo

Nella funzione `Main` del progetto chiamare il metodo di esempio che si vuole richiamare. Passare i parametri `Endpoint` e `ApiKey` definiti.

```csharp
    public static void Main(string[] args)
    {
        var credentials = new ApiKeyServiceClientCredentials(ApiKey);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = Endpoint
        };

        // Change the console encoding to display non-ASCII characters.
        Console.OutputEncoding = System.Text.Encoding.UTF8;
        SentimentAnalysisExample(client);
        // DetectLanguageExample(client);
        // RecognizeEntitiesExample(client);
        // KeyPhraseExtractionExample(client);
        Console.ReadLine();
    }
```

Le sezioni successive descrivono come chiamare ciascuna delle funzionalità del servizio.

## <a name="perform-sentiment-analysis"></a>Eseguire l'analisi del sentiment

1. Creare una nuova funzione `SentimentAnalysisExample()` che accetti il client creato in precedenza.
2. Nella stessa funzione, chiamare `client.Sentiment()` e ottenere il risultato. Il risultato conterrà il punteggio del sentiment (`Score`) in caso di esito positivo ed `errorMessage` in caso contrario. I punteggi vicini a 0 indicano un sentiment negativo, mentre quelli più vicini a 1 indicano un sentiment positivo.

    ```csharp
    var result = client.Sentiment("I had the best day of my life.", "en");

    // Printing sentiment results
    Console.WriteLine($"Sentiment Score: {result.Score:0.00}");
    ```

### <a name="output"></a>Output

```console
Sentiment Score: 0.87
```

## <a name="perform-language-detection"></a>Eseguire il rilevamento della lingua

1. Creare una nuova funzione `DetectLanguageExample()` che accetti il client creato in precedenza.
2. Nella stessa funzione, chiamare `client.DetectLanguage()` e ottenere il risultato. Il risultato conterrà l'elenco delle lingue rilevate in `DetectedLanguages` in caso di esito positivo ed `errorMessage` in caso contrario. Stampare quindi la prima lingua restituita.

    ```csharp
    var result = client.DetectLanguage("This is a document written in English.");

    // Printing detected languages
    Console.WriteLine($"Language: {result.DetectedLanguages[0].Name}");
    ```

> [!Tip]
> In alcuni casi potrebbe essere difficile distinguere le lingue in base all'input. È possibile usare il parametro `countryHint` per specificare un codice paese di 2 lettere. Per impostazione predefinita, l'API usa "US" come valore predefinito di countryHint. Per rimuovere questo comportamento, è possibile reimpostare questo parametro usando come valore una stringa vuota, `countryHint = ""`.

### <a name="output"></a>Output

```console
Language: English
```

## <a name="perform-entity-recognition"></a>Eseguire il riconoscimento delle entità

1. Creare una nuova funzione `RecognizeEntitiesExample()` che accetti il client creato in precedenza.
2. Nella stessa funzione, chiamare `client.Entities()` e ottenere il risultato. Eseguire quindi l'iterazione attraverso i risultati. Il risultato conterrà l'elenco delle entità rilevate in `Entities` in caso di esito positivo ed `errorMessage` in caso contrario. Per ogni entità rilevata, stampare nome di Wikipedia, tipo e sottotipo (se presenti), nonché le posizioni nel testo originale.

    ```csharp
    var result = client.Entities("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.");

    // Printing recognized entities
    Console.WriteLine("Entities:");
    foreach (var entity in result.Entities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
        }
    }
    ```

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

## <a name="perform-key-phrase-extraction"></a>Eseguire l'estrazione delle frasi chiave

1. Creare una nuova funzione `KeyPhraseExtractionExample()` che accetti il client creato in precedenza.
2. Nella stessa funzione, chiamare `client.KeyPhrases()` e ottenere il risultato. Il risultato conterrà l'elenco delle frasi chiave rilevate in `KeyPhrases` in caso di esito positivo ed `errorMessage` in caso contrario. Stampare quindi tutte le frasi chiave rilevate.

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

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analisi del testo con Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Panoramica di Analisi del testo](../overview.md)
* [Domande frequenti](../text-analytics-resource-faq.md)
