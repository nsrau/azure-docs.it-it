---
title: "Guida introduttiva: Controllare l'ortografia con l'API REST e Ruby - Controllo ortografico Bing"
titleSuffix: Azure Cognitive Services
description: Questo argomento di avvio rapido illustra come usare l'API REST Controllo ortografico Bing e Ruby per eseguire il controllo ortografico e grammaticale.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.openlocfilehash: 8b347adab20447c542aaee1b7d41476233054824
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366877"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>Guida introduttiva: Controllare l'ortografia con l'API REST Controllo ortografico Bing e Ruby

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020** , è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](https://aka.ms/cogsvcs/bingmove).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](https://aka.ms/cogsvcs/bingmigration).

Usare questa guida introduttiva per effettuare la prima chiamata all'API REST Controllo ortografico Bing con Ruby. Questa semplice applicazione invia una richiesta all'API e restituisce un elenco di correzioni suggerite. 

Anche se l'applicazione è scritta in Ruby, l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione. Il codice sorgente di questa applicazione è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb)

## <a name="prerequisites"></a>Prerequisiti

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) o versione successiva

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare un nuovo file Ruby nell'ambiente di sviluppo integrato o nell'editor preferito e aggiungere i requisiti seguenti: 

    ```ruby
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. Creare le variabili per la chiave di sottoscrizione, l'URI dell'endpoint e il percorso. È possibile usare l'endpoint globale nel codice seguente o l'endpoint del [sottodominio personalizzato](../../../cognitive-services/cognitive-services-custom-subdomains.md) visualizzato nel portale di Azure per la risorsa. Creare i parametri della richiesta:

   1. Assegnare il codice di mercato al parametro `mkt` con l'operatore `=`. Il codice di mercato è il codice del paese o dell'area geografica da cui si effettua la richiesta. 

   1. Aggiungere il parametro `mode` con l'operatore `&` e quindi assegnare la modalità di controllo ortografico. La modalità può essere `proof` (individua la maggior parte degli errori di ortografia/grammatica) o `spell` (individua la maggior parte degli errori di ortografia, ma meno errori di grammatica). 

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>Inviare una richiesta di controllo ortografico

1. Creare un URI dalla stringa con uri, percorso e parametri dell'host. Impostarne la query in modo che contenga il testo di cui si vuole controllare l'ortografia.

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. Creare una richiesta usando l'URI creato in precedenza. Aggiungere la chiave all'intestazione `Ocp-Apim-Subscription-Key`.

    ```ruby
    request = Net::HTTP::Post.new(uri)
    request['Content-Type'] = "application/x-www-form-urlencoded"
    request['Ocp-Apim-Subscription-Key'] = key
    ```

3. Inviare la richiesta.

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request(request)
    end
    ```

4. Ottenere la risposta JSON e stamparla nella console. 

    ```ruby
    result = JSON.pretty_generate(JSON.parse(response.body))
    puts result
    ```

## <a name="run-the-application"></a>Eseguire l'applicazione

Compilare ed eseguire il progetto. Se si usa la riga di comando, usare il comando seguente per eseguire l'applicazione:

   ```bash
   ruby <FILE_NAME>.rb
   ```

## <a name="example-json-response"></a>Risposta JSON di esempio

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare app Web a pagina singola](../tutorials/spellcheck.md)

- [Informazioni sull'API Controllo ortografico Bing](../overview.md)
- [Informazioni di riferimento per l'API Controllo ortografico Bing v7](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)