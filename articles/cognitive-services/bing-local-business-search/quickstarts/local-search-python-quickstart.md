---
title: "Guida introduttiva: Inviare una query all'API di ricerca di attività locali Bing in Python | Microsoft Docs"
titleSuffix: Azure Cognitive Services
description: Usare questo articolo per iniziare a usare l'API di ricerca di attività locali Bing in Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: cfc4bd95214c56dfbe940b7a2785d297f59105a9
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592722"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Guida introduttiva: Inviare una query all'API di ricerca di attività locali Bing in Python

Seguire questa guida introduttiva per inviare richieste all'API Bing Local Business Search, un servizio cognitivo di Azure. L'applicazione è scritta in Python, ma l'API è un servizio Web RESTful compatibile con qualsiasi linguaggio di programmazione in grado di eseguire richieste HTTP e analizzare dati JSON.

Questa applicazione di esempio recupera i dati di risposta locali dall'API per la query di ricerca `hotel in Bellevue`.

## <a name="prerequisites"></a>Prerequisiti

* [Python](https://www.python.org/) 2.x o 3.x
 
È necessario avere un [account delle API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con le API Bing. Per questa guida introduttiva è sufficiente la [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). Usare la chiave di accesso fornita dalla versione di prova gratuita.  Vedere anche [Prezzi di Servizi cognitivi - API di ricerca Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>Eseguire l'applicazione completa

Il codice seguente restituisce risultati localizzati. Viene implementato nei passaggi seguenti:
1. Dichiarare le variabili per specificare l'endpoint dall'host e il percorso.
2. Specificare il parametro di query. 
3. Definire la funzione di ricerca che crea la richiesta e aggiunge l'intestazione Ocp-Apim-Subscription-Key.
4. Impostare l'intestazione Ocp-Apim-Subscription-Key. 
5. Stabilire la connessione e inviare la richiesta.
6. Stampare i risultati del file JSON.

Il codice completo per questa demo è il seguente:

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com/bing'
path = '/v7.0/localbusinesses/search'

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
- [Local Business Search Java quickstart](local-search-java-quickstart.md) (Guida introduttiva alla ricerca di attività locali in Java)
- [Local Business Search C# Quickstart](local-quickstart.md) (Guida introduttiva alla ricerca di attività locali in C#)
- [Local Business Search Node Quickstart](local-search-node-quickstart.md) (Guida introduttiva alla ricerca di attività locali in Node)
