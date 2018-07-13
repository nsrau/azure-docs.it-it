---
title: Avvio rapido di Python per l'API Ricerca visiva Bing | Microsoft Docs
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
ms.openlocfilehash: a520466825eb429e45e0500b52bd7af502c0a38c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377660"
---
# <a name="your-first-bing-visual-search-query-in-python"></a>La prima query di Ricerca visiva Bing in Python

L'API Ricerca visiva Bing restituisce informazioni su un'immagine fornita. È possibile fornire l'immagine usando l'URL dell'immagine, un token di informazioni dettagliate, oppure caricando l'immagine. Per informazioni su queste opzioni, vedere [Informazioni sull'API Ricerca visiva Bing](../overview.md) Questo articolo illustra come caricare un'immagine. Caricare un'immagine può essere utile negli scenari per dispositivi mobili in cui si acquisisce un'immagine di un luogo noto e si ottengono le relative informazioni. Le informazioni dettagliate, ad esempio, possono includere curiosità sul luogo. 

Se si carica un'immagine locale, la figura seguente illustra i dati di formato che è necessario includere nel corpo del POST. I dati di formato devono includere l'intestazione Content-Disposition. Il relativo parametro `name` deve essere impostato su "image" e il parametro `filename` può essere impostato su qualsiasi stringa. Il contenuto del modulo è il file binario dell'immagine. La dimensione massima delle immagini che è possibile caricare è 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Questo articolo include una semplice applicazione console che invia una richiesta all'API Ricerca visiva Bing e visualizza i risultati della ricerca JSON. L'applicazione è scritta in Python, ma l'API è un servizio Web RESTful compatibile con qualsiasi linguaggio di programmazione che sia in grado di effettuare richieste HTTP e analizzare una stringa JSON. 

## <a name="prerequisites"></a>prerequisiti

Per eseguire questo codice è necessario [Python 3](https://www.python.org/).

Per questo Avvio rapido, è possibile usare una chiave di sottoscrizione [di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) o una chiave di sottoscrizione a pagamento.

## <a name="running-the-walkthrough"></a>Esecuzione della procedura dettagliata

Per eseguire l'applicazione seguire questa procedura:

1. Creare un nuovo progetto Python nell'ambiente di sviluppo integrato o nell'editor preferito.
2. Creare un file denominato visualsearch.py e aggiungere il codice illustrato in questo Avvio rapido.
3. Sostituire il valore `SUBSCRIPTION_KEY` con la chiave di sottoscrizione.
3. Sostituire il valore `imagePath` con il percorso dell'immagine da caricare.
4. Eseguire il programma.



La schermata seguente mostra come inviare un messaggio usando dati di formato multipart in Python.

```python
"""Bing Visual Search upload image example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests, json


BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

imagePath = '<pathtoyourimagetouploadgoeshere>'

file = {'image' : ('myfile', open(imagePath, 'rb'))}

def main():
    
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))



# Main execution
if __name__ == '__main__':
    main()
```


## <a name="next-steps"></a>Passaggi successivi

[Ottenere informazioni dettagliate su un'immagine usando un token di informazioni dettagliate](../use-insights-token.md)  
[Esercitazione sull'app a singola pagina di Ricerca visiva Bing](../tutorial-bing-visual-search-single-page-app.md)  
[Panoramica di Ricerca visiva Bing](../overview.md)  
[Prova](https://aka.ms/bingvisualsearchtryforfree)  
[Ottenere una chiave di accesso gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Riferimenti dell'API Ricerca visiva Bing](https://aka.ms/bingvisualsearchreferencedoc)
