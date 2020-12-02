---
title: "Avvio rapido: Inviare una query all'API in Python - Ricerca attività commerciali locali Bing"
titleSuffix: Azure Cognitive Services
description: Usare questo argomento di avvio rapido per iniziare a usare l'API Ricerca attività commerciali locali Bing in Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: a6d2ed78c97e247bafefa957cddd777cc127fe6f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493254"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Avvio rapido: Inviare una query all'API di ricerca di attività locali Bing in Python

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020**, è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Usare questo argomento di avvio rapido per informazioni su come inviare richieste all'API Ricerca attività commerciali locali Bing, un servizio cognitivo di Azure. Anche se questa semplice applicazione è scritta in Python, l'API è un servizio Web RESTful compatibile con qualsiasi linguaggio di programmazione in grado di eseguire richieste HTTP e analizzare codice JSON.

Questa applicazione di esempio recupera i dati di risposta locali dall'API per una query di ricerca.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* [Python](https://www.python.org/) 2.x o 3.x.
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Creare una risorsa di Ricerca Bing"  target="_blank">creare una risorsa di Ricerca Bing <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.

## <a name="run-the-complete-application"></a>Eseguire l'applicazione completa

L'esempio seguente ottiene i risultati localizzati, che vengono implementati nei passaggi seguenti:
1. Dichiarare le variabili per specificare l'endpoint dall'host e il percorso.
2. Specificare il parametro di query. 
3. Definire la funzione di ricerca che crea la richiesta e aggiunge l'intestazione `Ocp-Apim-Subscription-Key`.
4. Impostare l'intestazione `Ocp-Apim-Subscription-Key`. 
5. Stabilire la connessione e inviare la richiesta.
6. Stampare i risultati del file JSON.

Il codice completo per questa demo è il seguente:

```python
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```

## <a name="next-steps"></a>Passaggi successivi
- [Guida introduttiva a Local Business Search in Java](local-search-java-quickstart.md)
- [Guida di avvio rapido a Ricerca attività commerciali locali Bing in C#](local-quickstart.md)
- [Guida di avvio rapido a Ricerca attività commerciali locali Bing in Node.js](local-search-node-quickstart.md)