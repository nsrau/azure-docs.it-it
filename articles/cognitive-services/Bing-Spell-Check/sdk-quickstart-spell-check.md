---
title: "Avvio rapido: Controllare l'ortografia con l'SDK Controllo ortografico Bing per C#"
titlesuffix: Azure Cognitive Services
description: Introduzione all'uso dell'API REST Controllo ortografico Bing per controllare l'ortografia e la grammatica.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: v-gedod
ms.openlocfilehash: ae558c40a3eb30cb239b19a59542d9d83d5a9566
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886146"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Avvio rapido: Controllare l'ortografia con l'SDK Controllo ortografico Bing per C#

Usare questa guida introduttiva per iniziare a eseguire il controllo ortografico con l'SDK Controllo ortografico Bing per C#. Mentre Controllo ortografico Bing ha un'API REST compatibile con la maggior parte dei linguaggi di programmazione, l'SDK fornisce un modo semplice per integrare il servizio nelle applicazioni. Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck).

## <a name="application-dependencies"></a>Dipendenze dell'applicazione

* Qualsiasi edizione di [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
* [Pacchetto NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck) di Controllo ortografico Bing

Per aggiungere l'SDK Controllo ortografico Bing a un progetto, fare cli su `Manage NuGet Packages` in Esplora soluzioni in Visual Studio. Aggiungere il pacchetto `Microsoft.Azure.CognitiveServices.Language.SpellCheck`. Il pacchetto installa anche le dipendenze seguenti:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare una nuova soluzione di console C# in Visual Studio. Aggiungere quindi l'istruzione `using` seguente.
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. Creare una nuova classe. Creare quindi una funzione asincrona denominata `SpellCheckCorrection()` che accetta una chiave di sottoscrizione e invia la richiesta di controllo ortografico.

3. Creare un'istanza del client creando un nuovo oggetto `ApiKeyServiceClientCredentials`. 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>Inviare la richiesta e leggere la risposta

1. Nella funzione creata sopra eseguire i passaggi seguenti. Inviare la richiesta di controllo ortografico con il client. Aggiungere il testo da controllare al parametro `text` e impostare la modalità su `proof`.  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. Ottenere il primo risultato del controllo ortografico, se presente. Stampare la prima parola con errore di ortografia (token) restituita, il tipo di token e il numero di suggerimenti.

    ```csharp
    if (firstspellCheckResult != null){
        var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. Ottenere la prima correzione suggerita, se presente. Stampare il punteggio del suggerimento e la parola suggerita. 

    ```csharp
            var suggestions = firstspellCheckResult.Suggestions;

            if (suggestions?.Count > 0)
            {
                var firstSuggestion = suggestions.FirstOrDefault();
                Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
                Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
            }
}

## Next steps

> [!div class="nextstepaction"]
> [Create a single page web-app](tutorials/spellcheck.md)

- [What is the Bing Spell Check API?](overview.md)
- [Bing Spell Check C# SDK reference guide](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)