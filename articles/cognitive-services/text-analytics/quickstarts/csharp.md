---
title: "Guida introduttiva: Uso di C# per chiamare l'API Analisi del testo"
titleSuffix: Azure Cognitive Services
description: Ottenere informazioni ed esempi di codice per iniziare a usare rapidamente l'API Analisi del testo.
services: cognitive-services
author: ashmaka
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 01/02/2019
ms.author: assafi
ms.openlocfilehash: 9b56104934c1ddcc60222c988efdf173ca33d77b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56871010"
---
# <a name="quickstart-using-c-to-call-the-text-analytics-cognitive-service"></a>Avvio rapido: Uso di C# per chiamare il servizio cognitivo Analisi del testo
<a name="HOLTop"></a>

Questo articolo mostra come rilevare la lingua, analizzare il sentiment ed estrarre frasi chiave usando le  [API Analisi del testo](//go.microsoft.com/fwlink/?LinkID=759711)  con C#. Il codice è stato scritto per lavorare su un'applicazione .Net Core, con riferimenti minimi a librerie esterne, in modo che sia possibile eseguirlo in Linux o MacOS.

Per la documentazione tecnica delle API, vedere le [definizioni delle API](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

È inoltre necessario avere la [chiave di accesso e l'endpoint](../How-tos/text-analytics-how-to-access-key.md) generati automaticamente durante l'iscrizione.


## <a name="install-the-nuget-sdk-package"></a>Installare il pacchetto NuGet SDK
1. Creare una nuova soluzione Console in Visual Studio.
1. Fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Manage NuGet Packages for Solution** (Gestisci pacchetti NuGet per la soluzione)
1. Selezionare la casella di controllo **Include Prerelease** (Includi versione provvisoria).
1. Selezionare la scheda **Sfoglia** e cercare **Microsoft.Azure.CognitiveServices.Language.TextAnalytics**
1. Selezionare il pacchetto NuGet e installarlo.

> [!Tip]
>  Sebbene sia possibile chiamare direttamente gli [endpoint HTTP](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) da C#, Microsoft.Azure.CognitiveServices.Language SDK agevola la chiamata al servizio perché non rende più necessarie la serializzazione e la deserializzazione di JSON.
>
> Collegamenti utili:
> - [Pagina SDK Nuget](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [Codice SDK](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)


## <a name="call-the-text-analytics-api-using-the-sdk"></a>Chiamare l'API Analisi del testo usando l'SDK
1. Sostituire Program.cs con il codice fornito di seguito. Questo programma illustra le funzionalità dell'API Analisi del testo in tre sezioni (estrazione del linguaggio, estrazione di frasi chiave e analisi del sentiment).
1. Sostituire il valore dell'intestazione `Ocp-Apim-Subscription-Key` con una chiave di accesso valida per la sottoscrizione.
1. Sostituire la località in `Endpoint` con l'endpoint per cui è stata fatta l'iscrizione. È possibile trovare l'endpoint nella risorsa del portale di Azure. L'endpoint inizia in genere con "https://[region].api.cognitive.microsoft.com". Includere solo il protocollo e il nome host.
1. Eseguire il programma.

```csharp
using System;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using System.Collections.Generic;
using Microsoft.Rest;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        /// <summary>
        /// Container for subscription credentials. Make sure to enter your valid key.
        private const string SubscriptionKey = ""; //Insert your Text Anaytics subscription key

        /// </summary>
        class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", SubscriptionKey);
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static void Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials())
            {
                Endpoint = "https://westus.api.cognitive.microsoft.com"
            }; //Replace 'westus' with the correct region for your Text Analytics subscription

            Console.OutputEncoding = System.Text.Encoding.UTF8;

            // Extracting language
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var result = client.DetectLanguageAsync(new BatchInput(
                    new List<Input>()
                        {
                          new Input("1", "This is a document written in English."),
                          new Input("2", "Este es un document escrito en Español."),
                          new Input("3", "这是一个用中文写的文件")
                    })).Result;

            // Printing language results.
            foreach (var document in result.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
            }

            // Getting key-phrases
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            KeyPhraseBatchResult result2 = client.KeyPhrasesAsync(new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("ja", "1", "猫は幸せ"),
                          new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                          new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                          new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                        })).Result;

            // Printing keyphrases
            foreach (var document in result2.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} ");

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine($"\t\t{keyphrase}");
                }
            }

            // Extracting sentiment
            Console.WriteLine("\n\n===== SENTIMENT ANALYSIS ======");

            SentimentBatchResult result3 = client.SentimentAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "I had the best day of my life."),
                          new MultiLanguageInput("en", "1", "This was a waste of my time. The speaker put me to sleep."),
                          new MultiLanguageInput("es", "2", "No tengo dinero ni nada que dar..."),
                          new MultiLanguageInput("it", "3", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
                        })).Result;


            // Printing sentiment results
            foreach (var document in result3.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
            }


            // Identify entities
            Console.WriteLine("\n\n===== ENTITIES ======");

            EntitiesBatchResultV2dot1 result4 = client.EntitiesAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%.")
                        })).Result;

            // Printing entities results
            foreach (var document in result4.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} ");

                Console.WriteLine("\t Entities:");

                foreach (EntityRecordV2dot1 entity in document.Entities)
                {
                    Console.WriteLine($"\t\t{entity.Name}\t\t{entity.WikipediaUrl}\t\t{entity.Type}\t\t{entity.SubType}");
                }
            }

            Console.ReadLine();
        }
    }
}
```

## <a name="application-output"></a>Output dell'applicazione

L'applicazione visualizza le informazioni seguenti:

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified


===== KEY-PHRASE EXTRACTION ======
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol

===== SENTIMENT ANALYSIS ======
Document ID: 0 , Sentiment Score: 0.87
Document ID: 1 , Sentiment Score: 0.11
Document ID: 2 , Sentiment Score: 0.44
Document ID: 3 , Sentiment Score: 1.00
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analisi del testo con Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Vedere anche 

 [Panoramica di Analisi del testo](../overview.md) [Domande frequenti](../text-analytics-resource-faq.md)

