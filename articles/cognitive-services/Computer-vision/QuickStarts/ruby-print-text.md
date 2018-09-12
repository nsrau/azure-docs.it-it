---
title: API Visione artificiale - Guida introduttiva al riconoscimento ottico dei caratteri con Ruby | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: In questa guida introduttiva si estrae testo stampato da un'immagine usando Visione artificiale con Ruby in Servizi cognitivi.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 4f381444401718906bb352860aec525d73da1eb2
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772407"
---
# <a name="quickstart-extract-printed-text-ocr---rest-ruby"></a>Guida introduttiva: Estrarre testo stampato (OCR) - REST, Ruby

In questa guida introduttiva si estrae testo stampato da un'immagine, con una tecnica nota come riconoscimento ottico dei caratteri (OCR, Optical Character Recognition), usando Visione artificiale.

## <a name="prerequisites"></a>Prerequisiti

Per usare Visione artificiale, è necessario avere una chiave di sottoscrizione. A tale scopo, vedere [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Come ottenere chiavi di sottoscrizione).

## <a name="ocr-request"></a>Richiesta di riconoscimento ottico dei caratteri

Con il [metodo OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) è possibile rilevare il testo stampato in un'immagine ed estrarre i caratteri riconosciuti in un flusso utilizzabile da computer.

Per eseguire l'esempio, seguire questa procedura:

1. Copiare il codice seguente in un editor.
1. Sostituire `<Subscription Key>` con la propria chiave di sottoscrizione valida.
1. Modificare il valore di `uri` impostando l'indirizzo in cui si sono ottenute le chiavi di sottoscrizione, se necessario.
1. Facoltativamente, modificare l'immagine (`{\"url\":\"...`) da analizzare.
1. Salvare il file con estensione `.rb`.
1. Aprire il prompt dei comandi di Ruby ed eseguire il file, ad esempio: `ruby myfile.rb`.

```ruby
require 'net/http'

# You must use the same location in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from westus,
# replace "westcentralus" in the URL below with "westus".
uri = URI('https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/ocr')
uri.query = URI.encode_www_form({
    # Request parameters
    'language' => 'unk',
    'detectOrientation' => 'true'
})

request = Net::HTTP::Post.new(uri.request_uri)

# Request headers
# Replace <Subscription Key> with your valid subscription key.
request['Ocp-Apim-Subscription-Key'] = '<Subscription Key>'
request['Content-Type'] = 'application/json'

request.body =
    "{\"url\": \"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/" +
    "Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

puts response.body
```

## <a name="ocr-response"></a>Risposta alla richiesta di riconoscimento ottico dei caratteri

Se l'esito è positivo, i risultati restituiti dal riconoscimento ottico dei caratteri includono il testo e i rettangoli delimitatori per aree, linee e parole, ad esempio:

```json
{
  "language": "en",
  "textAngle": -2.0000000000000338,
  "orientation": "Up",
  "regions": [
    {
      "boundingBox": "462,379,497,258",
      "lines": [
        {
          "boundingBox": "462,379,497,74",
          "words": [
            {
              "boundingBox": "462,379,41,73",
              "text": "A"
            },
            {
              "boundingBox": "523,379,153,73",
              "text": "GOAL"
            },
            {
              "boundingBox": "694,379,265,74",
              "text": "WITHOUT"
            }
          ]
        },
        {
          "boundingBox": "565,471,289,74",
          "words": [
            {
              "boundingBox": "565,471,41,73",
              "text": "A"
            },
            {
              "boundingBox": "626,471,150,73",
              "text": "PLAN"
            },
            {
              "boundingBox": "801,472,53,73",
              "text": "IS"
            }
          ]
        },
        {
          "boundingBox": "519,563,375,74",
          "words": [
            {
              "boundingBox": "519,563,149,74",
              "text": "JUST"
            },
            {
              "boundingBox": "683,564,41,72",
              "text": "A"
            },
            {
              "boundingBox": "741,564,153,73",
              "text": "WISH"
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Esaminare le API Visione artificiale usate per analizzare un'immagine, rilevare celebrità e luoghi di interesse, creare un'anteprima ed estrarre testo scritto a mano e stampato. Per sperimentare rapidamente le API Visione artificiale, provare la [console di test delle API aperta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Esaminare le API Visione artificiale](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
