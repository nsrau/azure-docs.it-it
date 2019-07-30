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
ms.date: 07/18/2019
ms.author: assafi
ms.openlocfilehash: 09713528f51675f6e9d7f3073b6c81b095d23631
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356971"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>Guida introduttiva: Usare .NET SDK e C# per chiamare il servizio Analisi del testo
<a name="HOLTop"></a>

Questo argomento di avvio rapido fornisce informazioni utili per iniziare a usare Azure SDK per .NET e C# per analizzare il linguaggio. Sebbene l'API REST di [Analisi del testo](//go.microsoft.com/fwlink/?LinkID=759711) sia compatibile con la maggior parte dei linguaggi di programmazione, l'SDK rappresenta un modo semplice per integrare il servizio nelle applicazioni.

> [!NOTE]
> Il codice sorgente di questo esempio è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics).

Per i dettagli tecnici, vedere le [informazioni di riferimento per Analisi del testo](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet) relative all'SDK per .NET.

## <a name="prerequisites"></a>Prerequisiti

* Qualsiasi edizione di [Visual Studio 2017 o versioni successive]
* Text Analytics [SDK per .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Sono necessari anche [l'endpoint e la chiave di accesso](../How-tos/text-analytics-how-to-access-key.md) generata automaticamente durante l'iscrizione.

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
        SentimentAnalysisExample(client).Wait();
        // DetectLanguageExample(client).Wait();
        // RecognizeEntitiesExample(client).Wait();
        // KeyPhraseExtractionExample(client).Wait();
        Console.ReadLine();
    }
```

Le sezioni successive descrivono come chiamare ciascuna delle funzionalità del servizio.

## <a name="perform-sentiment-analysis"></a>Eseguire l'analisi del sentiment

1. Creare una nuova funzione `SentimentAnalysisExample()` che accetti il client creato in precedenza.
2. Generare un elenco di oggetti `MultiLanguageInput` contenente i documenti da analizzare.

    ```csharp
    public static async Task SentimentAnalysisExample(TextAnalyticsClient client)
    {
        // The documents to be analyzed. Add the language of the document. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "I had the best day of my life.")
            });
        //...
    }
    ```

3. Nella stessa funzione, chiamare `client.SentimentAsync()` e ottenere il risultato. Eseguire quindi l'iterazione attraverso i risultati. Stampare l'ID e il punteggio relativo al sentiment per ogni documento. I punteggi vicini a 0 indicano un sentiment negativo, mentre quelli più vicini a 1 indicano un sentiment positivo.

    ```csharp
    var result = await client.SentimentAsync(false, inputDocuments);

    // Printing sentiment results
    foreach (var document in result.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
    }
    ```

### <a name="output"></a>Output

```console
Document ID: 1 , Sentiment Score: 0.87
```

## <a name="perform-language-detection"></a>Eseguire il rilevamento della lingua

1. Creare una nuova funzione `DetectLanguageExample()` che accetti il client creato in precedenza.
2. Generare un elenco di oggetti `LanguageInput` contenente i documenti.

    ```csharp
    public static async Task DetectLanguageExample(TextAnalyticsClient client)
    {

        // The documents to be submitted for language detection. The ID can be any value.
        var inputDocuments = new LanguageBatchInput(
                new List<LanguageInput>
                    {
                        new LanguageInput(id: "1", text: "This is a document written in English.")
                    });
        //...
    }
    ```

3. Nella stessa funzione, chiamare `client.DetectLanguageAsync()` e ottenere il risultato. Eseguire quindi l'iterazione attraverso i risultati. Stampare l'ID e la prima lingua restituita per ogni documento.

    ```csharp
    var langResults = await client.DetectLanguageAsync(false, inputDocuments);

    // Printing detected languages
    foreach (var document in langResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
    }
    ```

### <a name="output"></a>Output

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
```

## <a name="perform-entity-recognition"></a>Eseguire il riconoscimento delle entità

1. Creare una nuova funzione `RecognizeEntitiesExample()` che accetti il client creato in precedenza.
2. Generare un elenco di oggetti `MultiLanguageBatchInput` contenente i documenti.

    ```csharp
    public static async Task RecognizeEntitiesExample(TextAnalyticsClient client)
    {
        // The documents to be submitted for entity recognition. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.")
            });
        //...
    }
    ```

3. Nella stessa funzione, chiamare `client.EntitiesAsync()` e ottenere il risultato. Eseguire quindi l'iterazione attraverso i risultati. Stampare l'ID di ogni documento. Per ogni entità rilevata, stampare il relativo nome di Wikipedia, il tipo e i sottotipi (se presenti), nonché le posizioni nel testo originale.

    ```csharp
    var entitiesResult = await client.EntitiesAsync(false, inputDocuments);

    // Printing recognized entities
    foreach (var document in entitiesResult.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Entities:");
        foreach (var entity in document.Entities)
        {
            Console.WriteLine($"\t\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
            foreach (var match in entity.Matches)
            {
                Console.WriteLine($"\t\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
            }
        }
    }
    ```

### <a name="output"></a>Output

```console
Document ID: 1
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
2. Generare un elenco di oggetti `MultiLanguageBatchInput` contenente i documenti.

    ```csharp
    public static async Task KeyPhraseExtractionExample(TextAnalyticsClient client)
    {
        var inputDocuments = new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("en", "1", "My cat might need to see a veterinarian.")
                    });
        //...
    }
    ```

3. Nella stessa funzione, chiamare `client.KeyPhrasesAsync()` e ottenere il risultato. Eseguire quindi l'iterazione attraverso i risultati. Stampare l'ID e le eventuali frasi chiave rilevate per ogni documento.

    ```csharp
    var kpResults = await client.KeyPhrasesAsync(false, inputDocuments);

    // Printing keyphrases
    foreach (var document in kpResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Key phrases:");

        foreach (string keyphrase in document.KeyPhrases)
        {
            Console.WriteLine($"\t\t{keyphrase}");
        }
    }
    ```

### <a name="output"></a>Output

```console
Document ID: 1
         Key phrases:
                cat
                veterinarian
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analisi del testo con Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Panoramica di Analisi del testo](../overview.md)
* [Domande frequenti](../text-analytics-resource-faq.md)
