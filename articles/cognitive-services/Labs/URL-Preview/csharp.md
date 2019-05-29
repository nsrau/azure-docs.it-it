---
title: 'Guida introduttiva: Progetto Anteprima URL, C#'
titlesuffix: Azure Cognitive Services
description: Introduzione all'uso di Progetto Anteprima URL con C#.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 2cb3f1de803b8549a88866da1042f4f32609a601
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827154"
---
# <a name="quickstart-url-preview-query-in-c"></a>Guida introduttiva: Query di URL Preview in C#

L'esempio in C# seguente crea un'anteprima URL per il sito Web SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questo codice in Windows, è necessario [Visual Studio 2017 o versioni successive](https://www.visualstudio.com/downloads/). È possibile usare la versione gratuita Community Edition.

Ottenere una chiave di accesso per la versione di valutazione gratuita di [Lab di Servizi cognitivi](https://aka.ms/answersearchsubscription)

## <a name="code-scenario"></a>Scenario di codice

Il codice C# seguente crea un'anteprima URL del sito Web SwiftKey: https://swiftkey.com/en. 

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
using System.IO;
using System.Net;
using System.Text;

namespace UrlPrevCshp
{
    class Program
    {
        static void Main(string[] args)
        {
            String uriBase = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search";
            String searchQuery = "https://swiftkey.com/en"; 
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery);

            // Do the Web request and get response
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR-SUBSCRIPTION-KEY";
            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            Console.WriteLine("\nHTTP Headers:\n");
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    Console.WriteLine(header + ": " + response.Headers[header]);
            }

            Console.WriteLine(JsonPrettyPrint(json));
            

            Console.ReadKey();
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
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
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
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
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
- [Guida introduttiva in Java](java-quickstart.md)
- [Guida introduttiva in JavaScript](javascript.md)
- [Guida introduttiva in Node](node-quickstart.md)
- [Guida introduttiva in Python](python-quickstart.md)
