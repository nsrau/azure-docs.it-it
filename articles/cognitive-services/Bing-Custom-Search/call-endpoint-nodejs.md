---
title: "Guida introduttiva: Chiamare l'endpoint usando Node.js - Ricerca personalizzata Bing"
titlesuffix: Azure Cognitive Services
description: Questa guida introduttiva mostra come richiedere risultati delle ricerche dall'istanza di ricerca personalizzata usando Node.js per chiamare l'endpoint di Ricerca personalizzata Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: aahi
ms.openlocfilehash: c0c97dd52f8fc3ff590c86f32f794beeb00f4b05
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310253"
---
# <a name="quickstart-call-bing-custom-search-endpoint-nodejs"></a>Guida introduttiva: Chiamare l'endpoint di Ricerca personalizzata Bing (Node.js)

Questa guida introduttiva illustra come chiamare l'endpoint di Ricerca personalizzata Bing eseguendo una ricerca dall'istanza di ricerca personalizzata tramite Node.js. 

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione introduttiva, sono necessari gli elementi seguenti:

- Un'istanza di Ricerca personalizzata pronta per l'uso. Vedere [Creare la prima istanza di Ricerca personalizzata Bing](quick-start.md).
- [Node.js](https://www.nodejs.org/) installato.
- Una chiave di sottoscrizione. È possibile ottenere una chiave di sottoscrizione quando si attiva la [versione di prova gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search). In alternativa è possibile usare una chiave di sottoscrizione a pagamento dal dashboard di Azure (vedere [Account delle API di Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).   Vedere anche [Prezzi di Servizi cognitivi - API di ricerca Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-code"></a>Eseguire il codice

Per eseguire l'esempio, seguire questa procedura:

1. Creare una cartella per il codice.  
  
2. Da un terminale o un prompt dei comandi passare alla cartella appena creata.  
  
3. Installare il modulo Node **request**:
    <pre>
    npm install request
    </pre>  
    
4. Creare un file denominato BingCustomSearch.py nella cartella creata e copiare il codice seguente nel file. Sostituire **YOUR-SUBSCRIPTION-KEY** e **YOUR-CUSTOM-CONFIG-ID** con la chiave di sottoscrizione e l'ID di configurazione.  
  
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
  
6. Eseguire il codice tramite il comando seguente:  
  
    ```    
    node BingCustomSearch.js
    ``` 

## <a name="next-steps"></a>Passaggi successivi
- [Configurare l'esperienza dell'interfaccia utente ospitata](./hosted-ui.md)
- [Usare indicatori di effetto per evidenziare il testo](./hit-highlighting.md)
- [Sfogliare pagine Web](./page-webpages.md)
