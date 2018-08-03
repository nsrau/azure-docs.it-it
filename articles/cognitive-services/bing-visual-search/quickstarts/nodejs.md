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
ms.openlocfilehash: 60b1dc9b8ea9eda258e9776b8967df38c97d964e
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071703"
---
# <a name="your-first-bing-visual-search-query-in-javascript"></a>La prima query di Ricerca visiva Bing in JavaScript

L'API Ricerca visiva Bing restituisce informazioni su un'immagine fornita. È possibile fornire l'immagine usando l'URL dell'immagine o un token di informazioni dettagliate oppure caricando l'immagine. Per informazioni su queste opzioni, vedere [Informazioni sull'API Ricerca visiva Bing](../overview.md) Questo articolo illustra come caricare un'immagine. Caricare un'immagine può essere utile negli scenari per dispositivi mobili in cui si acquisisce un'immagine di un punto di riferimento ben noto e si ottengono le relative informazioni. Le informazioni dettagliate, ad esempio, possono includere curiosità sul punto di riferimento. 

Se si carica un'immagine locale, la figura seguente illustra i dati del modulo che è necessario includere nel corpo del POST. I dati dl modulo devono includere l'intestazione Content-Disposition. Il parametro `name` deve essere impostato su "image" e il parametro `filename` può essere impostato su qualsiasi stringa. Il contenuto del modulo è il file binario dell'immagine. La dimensione massima delle immagini che è possibile caricare è 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Questo articolo include una semplice applicazione console che invia una richiesta all'API Ricerca visiva Bing e visualizza i risultati della ricerca JSON. L'applicazione è scritta in JavaScript, ma l'API è un servizio Web RESTful compatibile con qualsiasi linguaggio di programmazione che sia in grado di effettuare richieste HTTP e analizzare una stringa JSON. 

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questo codice è necessario [Node.js 6](https://nodejs.org/en/download/).

Per questa guida introduttiva, è possibile usare una chiave di sottoscrizione [di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) o una chiave di sottoscrizione a pagamento.

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
6. Sostituire il valore di `imagePath` con il percorso dell'immagine da caricare.
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
[Esercitazione sul caricamento dell'immagine di Ricerca visiva Bing di Ricerca visiva Bing](../tutorial-visual-search-image-upload.md)
[Panoramica su Ricerca visiva Bing](../tutorial-bing-visual-search-single-page-app.md)  
[Panoramica di Ricerca visiva Bing](../overview.md)  
[Prova](https://aka.ms/bingvisualsearchtryforfree)  
[Ottenere una chiave di accesso per la versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Informazioni di riferimento sull'API Ricerca visiva Bing](https://aka.ms/bingvisualsearchreferencedoc)
