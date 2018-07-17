---
title: Chiamata e risposta - Guida introduttiva a Python per Servizi cognitivi di Azure, API Ricerca Web Bing | Microsoft Docs
description: Informazioni ed esempi di codice per iniziare rapidamente a usare l'API Ricerca Web Bing in Servizi cognitivi Microsoft in Azure.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 9/18/2017
ms.author: v-jerkin
ms.openlocfilehash: 8d4df9db60c7a74a5b9e53d4622528c0054b4f19
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374145"
---
# <a name="call-and-response-your-first-bing-web-search-query-in-python"></a>Chiamata e risposta: la prima query di Ricerca Web Bing in Python

L'API Ricerca Web Bing offre un'esperienza simile a Bing.com/Search restituendo i risultati della ricerca che Bing ritiene rilevanti per la query dell'utente. I risultati possono includere pagine Web, immagini, video, notizie ed entità, oltre a query di ricerca correlate, correzioni ortografiche, fusi orari, conversioni di unità, traduzioni e calcoli. I tipi di risultati che si ottengono sono basati sulla pertinenza e sul livello delle API di ricerca Bing di cui si effettua la sottoscrizione.

Per i dettagli tecnici sulle API, vedere le [informazioni di riferimento sull'API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).

È possibile eseguire questo esempio come notebook Jupyter in [MyBinder](https://mybinder.org) facendo clic sulla notifica di avvio di Binder: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)


## <a name="prerequisites"></a>prerequisiti
È necessario avere un [account API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con le **API di ricerca Bing**. Per questa guida introduttiva è sufficiente la [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). È necessaria la chiave di accesso fornita all'attivazione della versione di valutazione gratuita oppure è possibile usare una chiave di sottoscrizione a pagamento dal dashboard di Azure.

## <a name="running-the-walkthrough"></a>Eseguire la procedura dettagliata

Impostare `subscription_key` sulla chiave API per il servizio API Bing.


```python
subscription_key = None
assert subscription_key
```

Verificare quindi che l'endpoint `search_url` sia corretto. Al momento della pubblicazione di questo documento, per le API di ricerca Bing viene usato un solo endpoint. Se si verificano errori di autorizzazione, verificare di nuovo questo valore nell'endpoint di ricerca Bing nel dashboard di Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

Impostare `search_term` per cercare Servizi cognitivi Microsoft in Bing.


```python
search_term = "Microsoft Cognitive Services"
```

Il blocco seguente usa la libreria `requests` di Python per chiamare le API di ricerca Bing e restituire i risultati come oggetto JSON. Osservare che la chiave API viene passata attraverso il dizionario `headers` e il termine di ricerca attraverso il dizionario `params`. Per visualizzare l'intero elenco di opzioni che è possibile usare per filtrare i risultati della ricerca, vedere la documentazione dell'[API REST](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

L'oggetto `search_results` contiene i risultati della ricerca, oltre ai metadati, ad esempio pagine e query correlate. Le righe di codice seguenti formattano le prime pagine restituite dalla query.


```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione sull'app a singola pagina di Ricerca Web Bing](../tutorial-bing-web-search-single-page-app.md)

## <a name="see-also"></a>Vedere anche  

[Panoramica di Ricerca Web Bing](../overview.md)  
[Prova](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)  
[Ottenere una chiave di accesso di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)
[Bing Web Search API reference (Informazioni di riferimento sull'API Ricerca Web Bing)](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
