---
title: Avvio rapido di Python per Servizi cognitivi di Azure, API Ricerca notizie Bing | Microsoft Docs
description: Ottenere informazioni ed esempi di codice per iniziare rapidamente a usare l'API Ricerca notizie Bing in Servizi cognitivi Microsoft in Azure.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: 0fde478b650513aa1527c1d47f5b453ba094506c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374033"
---
# <a name="quickstart-for-bing-news-search-api-with-python"></a>Avvio rapido per l'API Ricerca notizie Bing con Python
Questa procedura dettagliata illustra un esempio semplice di chiamare l'API Ricerca notizie Bing e la post-elaborazione dell'oggetto JSON risultante. Per altre informazioni, vedere la [documentazione di Ricerca notizie Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).  

È possibile eseguire questo esempio come Jupyter Notebook in [MyBinder](https://mybinder.org) facendo clic sulla notifica di avvio del Binder: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

## <a name="prerequisites"></a>prerequisiti

È necessario avere un [account API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con le **API di ricerca Bing**. Per questo Avvio rapido è sufficiente la [versione di prova gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). È necessaria la chiave di accesso fornita all'attivazione della versione di valutazione gratuita oppure è possibile usare una chiave di sottoscrizione a pagamento dal dashboard di Azure.

## <a name="running-the-walkthrough"></a>Esecuzione della procedura dettagliata
Impostare prima di tutto `subscription_key` sulla chiave API per il servizio API Bing.


```python
subscription_key = None
assert subscription_key
```

Verificare quindi che l'endpoint `search_url` sia corretto. Al momento della pubblicazione di questo documento, per le API di ricerca Bing viene usato un solo endpoint. Se si verificano errori di autorizzazione, verificare di nuovo questo valore nell'endpoint di ricerca Bing nel dashboard di Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

Impostare `search_term` per cercare articoli di notizie su Microsoft.


```python
search_term = "Microsoft"
```

Il blocco seguente usa la libreria `requests` di Python per chiamare le API di ricerca Bing e restituire i risultati come oggetto JSON. Osservare che la chiave API viene passata attraverso il dizionario `headers` e il termine di ricerca attraverso il dizionario `params`. Per visualizzare l'intero elenco di opzioni che è possibile usare per filtrare i risultati della ricerca, vedere la documentazione dell'[API REST](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

L'oggetto `search_results` contiene gli articoli di notizie rilevanti insieme a metadati dettagliati. La riga di codice seguente, ad esempio, consente di estrarre le descrizioni degli articoli.


```python
descriptions = [article["description"] for article in search_results["value"]]
```

Si può quindi eseguire il rendering di queste descrizioni come una tabella con la parola chiave di ricerca evidenziata in **grassetto**.


```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Paging di notizie ](paging-news.md)
> [Uso degli indicatori di effetto per evidenziare il testo](hit-highlighting.md)

## <a name="see-also"></a>Vedere anche  

 [Ricerca di notizie sul Web](search-the-web.md)  
 [Prova](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/)
