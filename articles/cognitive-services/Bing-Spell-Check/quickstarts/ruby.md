---
title: 'Guida introduttiva: API Controllo ortografico Bing, Ruby'
titlesuffix: Azure Cognitive Services
description: Ottenere informazioni ed esempi di codice per iniziare a usare rapidamente l'API Controllo ortografico Bing.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: quickstart
ms.date: 09/14/2017
ms.author: v-jaswel
ms.openlocfilehash: 3fe8729a9e2524cc2ccda168a857d58664a98b10
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801081"
---
# <a name="quickstart-for-bing-spell-check-api-with-ruby"></a>Guida introduttiva all'API Controllo ortografico Bing con Ruby 

Questo articolo spiega come usare l'[API Controllo ortografico Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) con Ruby. L'API Controllo ortografico restituisce un elenco di parole che non riconosce insieme a suggerimenti di parole sostitutive. In genere si invia il testo a questa API e quindi si effettuano le sostituzioni suggerite nel testo oppure si mostrano i suggerimenti all'utente dell'applicazione in modo che possa decidere se effettuare le sostituzioni. Questo articolo illustra come inviare una richiesta che contiene il testo "Hollo, wrld!" Le parole sostitutive suggerite sono "Hello" e "world".

## <a name="prerequisites"></a>Prerequisiti

Per eseguire il codice è necessario [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) o versioni successive.

È necessario avere un [account API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con l'**API Controllo ortografico Bing versione 7**. Per questa guida introduttiva è sufficiente la [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/#lang). È necessaria la chiave di accesso fornita all'attivazione della versione di valutazione gratuita oppure è possibile usare una chiave di sottoscrizione a pagamento dal dashboard di Azure.

## <a name="get-spell-check-results"></a>Ottenere i risultati del controllo ortografico

1. Creare un nuovo progetto Ruby nell'ambiente di sviluppo integrato preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore di `subscriptionKey` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

```ruby
require 'net/http'
require 'uri'
require 'json'

uri = 'https://api.cognitive.microsoft.com'
path = '/bing/v7.0/spellcheck?'
params = 'mkt=en-us&mode=proof'

uri = URI(uri + path + params)
uri.query = URI.encode_www_form({
    # Request parameters
    'text' => 'Hollo, wrld!'
})

# NOTE: Replace this example key with a valid subscription key.
key = 'ENTER KEY HERE'

# The headers in the following example 
# are optional but should be considered as required:
#
# X-MSEdge-ClientIP: 999.999.999.999  
# X-Search-Location: lat: +90.0000000000000;long: 00.0000000000000;re:100.000000000000
# X-MSEdge-ClientID: <Client ID from Previous Response Goes Here>
#
# See below for more information.

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/x-www-form-urlencoded"

request['Ocp-Apim-Subscription-Key'] = key

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

result = JSON.pretty_generate(JSON.parse(response.body))
puts result
```

**Risposta**

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
> [Esercitazione sul Controllo ortografico Bing](../tutorials/spellcheck.md)

## <a name="see-also"></a>Vedere anche 

- [Panoramica del Controllo ortografico Bing](../proof-text.md)
- [Informazioni di riferimento per l'API Controllo ortografico Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
