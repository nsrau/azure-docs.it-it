---
title: 'Guida introduttiva: Progetto Anteprima URL, Python'
titlesuffix: Azure Cognitive Services
description: Esempio di script per iniziare a usare rapidamente Progetto Anteprima URL con Python.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/29/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: e9b145b0786b2ab59552e5dfda6f0db63677443e
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706876"
---
# <a name="quickstart-url-preview-with-python"></a>Guida introduttiva: Anteprima URL con Python

L'esempio in Python seguente crea un'anteprima URL per il sito Web SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Prerequisiti

Ottenere una chiave di accesso per la versione di valutazione gratuita di [Lab di Servizi cognitivi](https://labs.cognitive.microsoft.com/en-us/project-answer-search)

Questo esempio usa Python 3.6.

## <a name="code-scenario"></a>Scenario di codice 

Il codice seguente crea un'anteprima dell'URL.
Viene implementato nei passaggi seguenti:
1. Dichiarare le variabili per specificare l'endpoint dall'host e il percorso.
2. Specificare l'URL della query da visualizzare in anteprima e aggiungere il parametro di query.  
3. Impostare il parametro di query.
4. Definire la funzione di ricerca che crea la richiesta e aggiunge l'intestazione *Ocp-Apim-Subscription-Key*.
5. Impostare l'intestazione *Ocp-Apim-Subscription-Key*. 
6. Effettuare la connessione e inviare la richiesta.
7. Stampare i risultati del file JSON.

Il codice completo per questa demo è il seguente:

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'your-subscription-key'

host = 'api.labs.cognitive.microsoft.com'
path = '/urlpreview/v7.0/search'

query = 'https://SwiftKey.com'

params = '?q=' + urllib.parse.quote (query)

def get_preview ():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_preview ()
print (json.dumps(json.loads(result), indent=4))
```
## <a name="next-steps"></a>Passaggi successivi
- [Guida introduttiva in C#](csharp.md)
- [Guida introduttiva in Java](java-quickstart.md)
- [Guida introduttiva in JavaScript](javascript.md)
- [Guida introduttiva in Node](node-quickstart.md)
