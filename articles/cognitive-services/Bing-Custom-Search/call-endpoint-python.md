---
title: "Guida introduttiva: Chiamare l'endpoint usando Python - Ricerca personalizzata Bing"
titlesuffix: Azure Cognitive Services
description: Questa guida introduttiva mostra come richiedere risultati delle ricerche dall'istanza di ricerca personalizzata usando Python per chiamare l'endpoint di Ricerca personalizzata Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: aahi
ms.openlocfilehash: e3e4256d3654f532f16d33c77f4c7e8cb7e93dd4
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52162637"
---
# <a name="quickstart-call-bing-custom-search-endpoint-python"></a>Guida introduttiva: Chiamare l'endpoint di Ricerca personalizzata Bing (Python)

Questa guida introduttiva illustra come richiedere risultati di ricerca dall'istanza di ricerca personalizzata usando Python per chiamare l'endpoint di Ricerca personalizzata Bing. 

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione introduttiva, sono necessari gli elementi seguenti:

- Un'istanza di Ricerca personalizzata pronta per l'uso. Vedere [Creare la prima istanza di Ricerca personalizzata Bing](quick-start.md).
- [Python](https://www.python.org/) installato.
- Una chiave di sottoscrizione. È possibile ottenere una chiave di sottoscrizione quando si attiva la [versione di prova gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search). In alternativa è possibile usare una chiave di sottoscrizione a pagamento dal dashboard di Azure (vedere [Account delle API di Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    


## <a name="run-the-code"></a>Eseguire il codice

Per eseguire l'esempio, seguire questa procedura:

1. Creare una cartella per il codice.  
  
2. Da un terminale o un prompt dei comandi di amministratore passare alla cartella appena creata.  
  
3. Installare il modulo Python **requests**:  
  
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>  
      
4. Creare un file denominato BingCustomSearch.py nella cartella creata e copiare il codice seguente al suo interno. Sostituire **YOUR-SUBSCRIPTION-KEY** e **YOUR-CUSTOM-CONFIG-ID** con la chiave di sottoscrizione e l'ID di configurazione.  
  
    ``` Python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=' + searchTerm + '&customconfig=' + customConfigId
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```  
  
7. Eseguire il codice tramite il comando seguente.  
  
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>Passaggi successivi
- [Configurare l'esperienza dell'interfaccia utente ospitata](./hosted-ui.md)
- [Usare indicatori di effetto per evidenziare il testo](./hit-highlighting.md)
- [Sfogliare pagine Web](./page-webpages.md)
