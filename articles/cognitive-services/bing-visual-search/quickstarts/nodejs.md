---
title: Avvio rapido di JavaScript per l'API Ricerca visiva Bing | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Viene illustrato come caricare un'immagine nell'API Ricerca visiva Bing e ottenere informazioni dettagliate sull'immagine.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: dd28c829d8d24980a746244dc6aca880d2d69224
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377684"
---
# <a name="your-first-bing-visual-search-query-in-javascript"></a>La prima query di Ricerca visiva Bing in JavaScript

L'API Ricerca visiva Bing restituisce informazioni su un'immagine fornita. È possibile fornire l'immagine usando l'URL dell'immagine, un token di informazioni dettagliate, oppure caricando l'immagine. Per informazioni su queste opzioni, vedere [Informazioni sull'API Ricerca visiva Bing](../overview.md) Questo articolo illustra come caricare un'immagine. Caricare un'immagine può essere utile negli scenari per dispositivi mobili in cui si acquisisce un'immagine di un luogo noto e si ottengono le relative informazioni. Le informazioni dettagliate, ad esempio, possono includere curiosità sul luogo. 

Se si carica un'immagine locale, la figura seguente illustra i dati di formato che è necessario includere nel corpo del POST. I dati di formato devono includere l'intestazione Content-Disposition. Il relativo parametro `name` deve essere impostato su "image" e il parametro `filename` può essere impostato su qualsiasi stringa. Il contenuto del modulo è il file binario dell'immagine. La dimensione massima delle immagini che è possibile caricare è 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Questo articolo include una semplice applicazione console che invia una richiesta all'API Ricerca visiva Bing e visualizza i risultati della ricerca JSON. L'applicazione è scritta in JavaScript, ma l'API è un servizio Web RESTful compatibile con qualsiasi linguaggio di programmazione che sia in grado di effettuare richieste HTTP e analizzare una stringa JSON. 

## <a name="prerequisites"></a>prerequisiti

Per eseguire questo codice è necessario [Node.js 6](https://nodejs.org/en/download/).

Per questo Avvio rapido, è possibile usare una chiave di sottoscrizione [di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) o una chiave di sottoscrizione a pagamento.

## <a name="running-the-application"></a>Esecuzione dell'applicazione

La schermata seguente mostra come inviare un messaggio con FormData in Node.js.

Per eseguire l'applicazione seguire questa procedura:

1. Creare una cartella del progetto (o usare l'ambiente di sviluppo integrato o l'editor preferito).
2. Da un terminale o un prompt dei comandi passare alla cartella appena creata.
3. Installare i moduli di richiesta:  
  ```  
  npm install request  
  ```  
3. Installare i moduli dati di form:  
  ```  
  npm install form-data  
  ```  
4. Creare un file denominato GetVisualInsights.js e aggiungervi il codice seguente.
5. Sostituire il valore `subscriptionKey` con la chiave di sottoscrizione.
6. Sostituire il valore `imagePath` con il percorso dell'immagine da caricare.
7. Eseguire il programma.  
  ```
  node GetVisualInsights.js
  ```

```javascript
var request = require('request');
var FormData = require('form-data');
var fs = require('fs');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';
var imagePath = "<pathtoyourimagegoeshere>";

var form = new FormData();
form.append("image", fs.createReadStream(imagePath));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```


## <a name="next-steps"></a>Passaggi successivi

[Ottenere informazioni dettagliate su un'immagine usando un token di informazioni dettagliate](../use-insights-token.md)  
[Esercitazione sull'app a singola pagina di Ricerca visiva Bing](../tutorial-bing-visual-search-single-page-app.md)  
[Panoramica di Ricerca visiva Bing](../overview.md)  
[Prova](https://aka.ms/bingvisualsearchtryforfree)  
[Ottenere una chiave di accesso gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Riferimenti dell'API Ricerca visiva Bing](https://aka.ms/bingvisualsearchreferencedoc)
