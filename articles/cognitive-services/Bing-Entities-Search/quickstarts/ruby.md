---
title: 'Guida introduttiva: API Ricerca entità Bing, Ruby'
titlesuffix: Azure Cognitive Services
description: Ottenere informazioni ed esempi di codice per iniziare a usare rapidamente l'API Ricerca entità Bing.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: quickstart
ms.date: 11/28/2017
ms.author: v-jaswel
ms.openlocfilehash: 7e2b3fa520c17bb85fa04ca12bb762ce8f370371
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814458"
---
# <a name="quickstart-for-bing-entity-search-api-with-ruby"></a>Guida introduttiva all'API Ricerca entità Bing con Ruby

Questo articolo spiega come usare l'[API Ricerca entità Bing](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) con Ruby.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire il codice è necessario [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) o versioni successive.

È necessario avere un [account API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con l'**API Ricerca entità Bing**. Per questa guida introduttiva è sufficiente la [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-entity-search-api). È necessaria la chiave di accesso fornita all'attivazione della versione di valutazione gratuita oppure è possibile usare una chiave di sottoscrizione a pagamento dal dashboard di Azure.

## <a name="search-entities"></a>Entità di ricerca

Per eseguire l'applicazione seguire questa procedura.

1. Creare un nuovo progetto Ruby nell'ambiente di sviluppo integrato preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore di `key` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

```ruby
require 'net/https'
require 'cgi'
require 'json'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'https://api.cognitive.microsoft.com'
path = '/bing/v7.0/entities'

mkt = 'en-US'
query = 'italian restaurants near me'

params = '?mkt=' + mkt + '&q=' + CGI.escape(query)
uri = URI (host + path + params)

request = Net::HTTP::Get.new(uri)
request['Ocp-Apim-Subscription-Key'] = subscriptionKey

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Risposta**

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "http://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

[Torna all'inizio](#HOLTop)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione su Ricerca entità Bing](../tutorial-bing-entities-search-single-page-app.md)
> [Panoramica su Ricerca entità Bing](../search-the-web.md )
> [Informazioni di riferimento per l'API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
