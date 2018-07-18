---
title: Chiamare l'endpoint tramite Python - Ricerca personalizzata Bing - Servizi cognitivi Microsoft
description: Questa guida introduttiva mostra come richiedere risultati delle ricerche dall'istanza di ricerca personalizzata usando Python per chiamare l'endpoint di Ricerca personalizzata Bing.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 889762ae0b401438f25546738268c584ddd58389
ms.sourcegitcommit: 15bfce02b334b67aedd634fa864efb4849fc5ee2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "35378497"
---
# <a name="call-bing-custom-search-endpoint-python"></a>Chiamare l'endpoint di Ricerca personalizzata Bing (Python)

Questa guida introduttiva mostra come richiedere risultati delle ricerche dall'istanza di ricerca personalizzata usando Python per chiamare l'endpoint di Ricerca personalizzata Bing. 

## <a name="prerequisites"></a>Prerequisiti
Per completare l'esercitazione introduttiva, sono necessari gli elementi seguenti:

- Un'istanza di ricerca personalizzata. Vedere [Create your first Bing Custom Search instance](quick-start.md) (Creare la prima istanza di Ricerca personalizzata Bing).

-  [Python](https://www.python.org/) installato.

- Un [account dell'API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con **API di ricerca Bing**. Per questa guida introduttiva è sufficiente la [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search). È necessaria la chiave di accesso fornita all'attivazione della versione di valutazione gratuita oppure è possibile usare una chiave di sottoscrizione a pagamento dal dashboard di Azure. 

## <a name="run-the-code"></a>Eseguire il codice

Per chiamare l'endpoint di Ricerca personalizzata Bing, eseguire queste operazioni:

1. Creare una cartella per il codice.
2. Da un terminale o un prompt dei comandi di amministratore passare alla cartella appena creata.
3. Installare il modulo Python **requests**:
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>
7. Creare il file BingCustomSearch.py e copiare il codice seguente nel file.
8. Sostituire **YOUR-SUBSCRIPTION-KEY** e **YOUR-CUSTOM-CONFIG-ID** con la chiave e l'ID configurazione (vedere il passaggio 1).

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
9. Eseguire il codice tramite il comando seguente.
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>Passaggi successivi
- [Configurare l'esperienza dell'interfaccia utente ospitata](./hosted-ui.md)
- [Usare indicatori di effetto per evidenziare il testo](./hit-highlighting.md)
- [Sfogliare pagine Web](./page-webpages.md)