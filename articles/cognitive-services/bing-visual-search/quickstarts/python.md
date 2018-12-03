---
title: 'Guida introduttiva: creare una query di ricerca visiva, Python - Ricerca visiva Bing'
titleSuffix: Azure Cognitive Services
description: Viene illustrato come caricare un'immagine nell'API Ricerca visiva Bing e ottenere informazioni dettagliate sull'immagine.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 3a0d92e42eed097e244118a60ec0a4223c9cedf5
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52440942"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-python"></a>Guida introduttiva: Prima query di Ricerca visiva Bing in Python

L'API Ricerca visiva Bing restituisce informazioni su un'immagine fornita. È possibile fornire l'immagine usando l'URL dell'immagine o un token di informazioni dettagliate oppure caricando l'immagine. Per informazioni su queste opzioni, vedere [Informazioni sull'API Ricerca visiva Bing](../overview.md) Questo articolo illustra come caricare un'immagine. Caricare un'immagine può essere utile negli scenari per dispositivi mobili in cui si acquisisce un'immagine di un punto di riferimento ben noto e si ottengono le relative informazioni. Le informazioni dettagliate, ad esempio, possono includere curiosità sul punto di riferimento. 

Se si carica un'immagine locale, la figura seguente illustra i dati del modulo che è necessario includere nel corpo del POST. I dati dl modulo devono includere l'intestazione Content-Disposition. Il parametro `name` deve essere impostato su "image" e il parametro `filename` può essere impostato su qualsiasi stringa. Il contenuto del modulo è il file binario dell'immagine. La dimensione massima delle immagini che è possibile caricare è 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Questo articolo include una semplice applicazione console che invia una richiesta all'API Ricerca visiva Bing e visualizza i risultati della ricerca JSON. L'applicazione è scritta in Python, ma l'API è un servizio Web RESTful compatibile con qualsiasi linguaggio di programmazione che sia in grado di effettuare richieste HTTP e analizzare una stringa JSON. 

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questo codice è necessario [Python 3](https://www.python.org/).

Per questa guida introduttiva è necessario avviare una sottoscrizione in base al piano tariffario S9, come illustrato in [Prezzi di Servizi cognitivi - API di ricerca Bing](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/). 

Per avviare una sottoscrizione nel portale di Azure:
1. Nella parte superiore del portale di Azure immettere 'BingSearchV7' nella casella di testo `Search resources, services, and docs`.  
2. Nella sezione Marketplace dell'elenco a discesa selezionare `Bing Search v7`.
3. Immettere `Name` per la nuova risorsa.
4. Selezionare la sottoscrizione `Pay-As-You-Go`.
5. Selezionare il piano tariffario `S9`.
6. Fare clic su `Enable` per iniziare a usare la sottoscrizione.

## <a name="running-the-walkthrough"></a>Esecuzione della procedura dettagliata

Per eseguire l'applicazione seguire questa procedura:

1. Creare un nuovo progetto Python nell'ambiente di sviluppo integrato o nell'editor preferito.
2. Creare un file denominato visualsearch.py e aggiungere il codice illustrato in questa guida introduttiva.
3. Sostituire il valore `SUBSCRIPTION_KEY` con la chiave di sottoscrizione.
3. Sostituire il valore di `imagePath` con il percorso dell'immagine da caricare.
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
[Esercitazione sul caricamento dell'immagine di Ricerca visiva Bing di Ricerca visiva Bing](../tutorial-visual-search-image-upload.md)
[Panoramica su Ricerca visiva Bing](../tutorial-bing-visual-search-single-page-app.md)  
[Panoramica di Ricerca visiva Bing](../overview.md)  
[Prova](https://aka.ms/bingvisualsearchtryforfree)  
[Ottenere una chiave di accesso per la versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Informazioni di riferimento sull'API Ricerca visiva Bing](https://aka.ms/bingvisualsearchreferencedoc)
