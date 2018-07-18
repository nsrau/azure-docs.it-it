---
title: Guida introduttiva a Servizi cognitivi Microsoft per C#, Project Answer Search | Microsoft Docs
description: Codice di esempio per iniziare a usare Project Answer Search, Servizi cognitivi Microsoft in Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: b05b7745bdb5020a4a0912635e040256415b2626
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376153"
---
# <a name="project-answer-search-query-in-c"></a>Query in C# di Project Answer Search

L'esempio in C# seguente crea e invia una query per ottenere informazioni su "third law of calculus".

## <a name="prerequisites"></a>prerequisiti

Per eseguire questo codice in Windows è necessario [Visual Studio 2017](https://www.visualstudio.com/downloads/). È possibile usare la versione gratuita Community Edition.

Ottenere una chiave di accesso per la versione di valutazione gratuita di [Lab di Servizi cognitivi](https://aka.ms/answersearchsubscription)

## <a name="code-scenario"></a>Scenario di codice

Il codice C# seguente crea e invia la query. 

Viene implementato nei passaggi seguenti:
1. Dichiarare le variabili per specificare l'endpoint e un URL di query da visualizzare in anteprima.  
2. Creare la richiesta.
3. Aggiungere l'intestazione *Ocp-Apim-Subscription-Key*. 
4. Eseguire la richiesta Web in modo asincrono. 
5. Leggere la risposta.
6. Stampare le intestazioni e i risultati del file JSON nella console.

**Codice sorgente**

```
using System;
using System.Collections.Generic;
using System.Text;
using System.Net;
using System.IO;

namespace Answers_csharp
{
    class Program
    {
        // Replace the accessKey string value with your valid access key.
        const string accessKey = "YOUR-SUBSCRIPTION-KEY";

        const string uriBase = "https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search "; 

        const string searchTerm = "third law of calculus"; 

        // Used to return news search results including relevant headers
        struct SearchResult
        {
            public String jsonResult;
            public Dictionary<String, String> relevantHeaders;
        }

        static void Main()
        {
            Console.OutputEncoding = System.Text.Encoding.UTF8;
            Console.WriteLine("Searching locally for: " + searchTerm);

            SearchResult result = BingLocalSearch(searchTerm);

            Console.WriteLine("\nRelevant HTTP Headers:\n");
            foreach (var header in result.relevantHeaders)
                Console.WriteLine(header.Key + ": " + header.Value);

            Console.WriteLine("\nJSON Response:\n");
            Console.WriteLine(JsonPrettyPrint(result.jsonResult));

            Console.Write("\nPress Enter to exit ");
            Console.ReadLine();
        }

        /// <summary>
        /// Does Bing knowledge search and returns the results as a SearchResult.
        /// </summary>
        static SearchResult BingLocalSearch(string searchQuery)
        {
            // Construct the URI of the search request
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery) + "&mkt=en-us";

            // Send the Web request and get the response.
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey; 

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            // Create result object for return
            var searchResult = new SearchResult();
            searchResult.jsonResult = json;
            searchResult.relevantHeaders = new Dictionary<String, String>();

            // Extract Bing HTTP headers
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    searchResult.relevantHeaders[header] = response.Headers[header];
            }

            return searchResult;
        }

        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            int offset = 0;
            int indentLength = 3;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\'':
                        if (quote) ignore = !ignore;
                        break;
                }

                if (quote)
                    sb.Append(ch);
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (ch != ' ') sb.Append(ch);
                            break;
                    }
                }
            }

            return sb.ToString().Trim();
        }

    }
}

```
## <a name="running-the-application"></a>Esecuzione dell'applicazione

Per eseguire l'applicazione:

1. Creare una nuova soluzione di Console in Visual Studio.
2. Sostituire `Program.cs` con il codice fornito.
3. Sostituire il valore `YOUR-ACCESS-KEY` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

## <a name="next-steps"></a>Passaggi successivi
[Guida introduttiva in Java](java-quickstart.md)
