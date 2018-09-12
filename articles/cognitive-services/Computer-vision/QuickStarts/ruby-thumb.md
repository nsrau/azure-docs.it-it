---
title: API Visione artificiale - Guida introduttiva a Ruby | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: In questa guida introduttiva si genera un'anteprima da un'immagine usando Visione artificiale con Ruby in Servizi cognitivi.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: a5f6c345b3e1f5e4dab923d43dd239344c66aab1
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772142"
---
# <a name="quickstart-generate-a-thumbnail---rest-ruby"></a>Guida introduttiva: Generare un'anteprima - REST, Ruby

In questa guida introduttiva si genera un'anteprima da un'immagine usando Visione artificiale.

## <a name="prerequisites"></a>Prerequisiti

Per usare Visione artificiale, è necessario avere una chiave di sottoscrizione. A tale scopo, vedere [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Come ottenere chiavi di sottoscrizione).

## <a name="get-thumbnail-request"></a>Richiesta di generazione di un'anteprima

Con il [metodo Get Thumbnail](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb), è possibile generare un'anteprima di un'immagine. Si specificano l'altezza e la larghezza, che possono essere diverse rispetto alle proporzioni dell'immagine di input. Visione artificiale usa il ritaglio intelligente per identificare l'area di interesse in modo intelligente e generare le coordinate di ritaglio in base a tale area.

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
uri = URI('https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail')
uri.query = URI.encode_www_form({
    # Request parameters
    'width' => '100',
    'height' => '100',
    'smartCropping' => 'true'
})

request = Net::HTTP::Post.new(uri.request_uri)

# Request headers
# Replace <Subscription Key> with your valid subscription key.
request['Ocp-Apim-Subscription-Key'] = '<Subscription Key>'
request['Content-Type'] = 'application/json'

request.body =
    "{\"url\": \"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/" +
    "Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

#puts response.body
```

## <a name="get-thumbnail-response"></a>Risposta alle richiesta di generazione di un'anteprima

Una risposta con esito positivo contiene il file binario dell'immagine di anteprima. Se invece la richiesta ha esito negativo, la risposta contiene un codice di errore e un messaggio per determinarne la causa.

## <a name="next-steps"></a>Passaggi successivi

Esaminare le API Visione artificiale usate per analizzare un'immagine, rilevare celebrità e luoghi di interesse, creare un'anteprima ed estrarre testo scritto a mano e stampato. Per sperimentare rapidamente le API Visione artificiale, provare la [console di test delle API aperta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Esaminare le API Visione artificiale](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
