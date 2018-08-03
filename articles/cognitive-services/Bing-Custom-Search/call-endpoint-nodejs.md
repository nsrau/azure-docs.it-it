---
title: Chiamare l'endpoint tramite Node.js - Ricerca personalizzata Bing - Servizi cognitivi Microsoft
description: Questa guida introduttiva mostra come richiedere risultati delle ricerche dall'istanza di ricerca personalizzata usando Node.js per chiamare l'endpoint di Ricerca personalizzata Bing.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 5d9391cc486dc868a1a291ccc7095291cddd3e4c
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2018
ms.locfileid: "37858460"
---
# <a name="call-bing-custom-search-endpoint-nodejs"></a>Chiamare l'endpoint di Ricerca personalizzata Bing (Node.js)

Questa guida introduttiva mostra come richiedere risultati delle ricerche dall'istanza di ricerca personalizzata usando Node.js per chiamare l'endpoint di Ricerca personalizzata Bing. 

## <a name="prerequisites"></a>prerequisiti
Per completare l'esercitazione introduttiva, sono necessari gli elementi seguenti:

- Un'istanza di ricerca personalizzata. Vedere [Create your first Bing Custom Search instance](quick-start.md) (Creare la prima istanza di Ricerca personalizzata Bing).

- [Node.js](https://www.nodejs.org/) installato.

-  Un [account dell'API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con **API di ricerca Bing**. La [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) è sufficiente per questa guida introduttiva. È necessaria la chiave di accesso fornita all'attivazione della versione di valutazione gratuita oppure è possibile usare una chiave di sottoscrizione a pagamento dal dashboard di Azure.

## <a name="run-the-code"></a>Eseguire il codice

Per chiamare l'endpoint di Ricerca personalizzata Bing, eseguire queste operazioni:

1. Creare una cartella per il codice.

2. Da un terminale o un prompt dei comandi passare alla cartella appena creata.

3. Installare il modulo Node **request**:
    <pre>
    npm install request
    </pre>
    
4. Creare il file BingCustomSearch.js e copiare il codice seguente nel file.

5. Sostituire **YOUR-SUBSCRIPTION-KEY** e **YOUR-CUSTOM-CONFIG-ID** con la chiave e l'ID configurazione (vedere il passaggio 1).

    ``` javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    
    var options = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
          'q=' + searchTerm + 
          '&customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    
    request(options, function(error, response, body){
        var searchResponse = JSON.parse(body);
        for(var i = 0; i < searchResponse.webPages.value.length; ++i){
            var webPage = searchResponse.webPages.value[i];
            console.log('name: ' + webPage.name);
            console.log('url: ' + webPage.url);
            console.log('displayUrl: ' + webPage.displayUrl);
            console.log('snippet: ' + webPage.snippet);
            console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
            console.log();
        }
    })
    ```
6. Eseguire il codice tramite il comando seguente.
    ```    
    node BingCustomSearch.js
   ``` 

## <a name="next-steps"></a>Passaggi successivi
- [Configurare l'esperienza dell'interfaccia utente ospitata](./hosted-ui.md)
- [Usare indicatori di effetto per evidenziare il testo](./hit-highlighting.md)
- [Sfogliare pagine Web](./page-webpages.md)
