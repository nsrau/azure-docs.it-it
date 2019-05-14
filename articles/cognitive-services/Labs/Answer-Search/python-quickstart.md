---
title: 'Avvio rapido: Project Answer Search, Python'
titlesuffix: Azure Cognitive Services
description: Esempio di Python per iniziare a usare Project Answer Search.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 2eaf07e041998efade1091861144a2dc4d78c56d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860304"
---
# <a name="quickstart-project-answer-search-with-python"></a>Guida introduttiva: Project Answer Search con Python

L'esempio in Python seguente crea e invia una richiesta di informazioni su "Rock of Gibraltar".

## <a name="prerequisites"></a>Prerequisiti

Ottenere una chiave di accesso per la versione di valutazione gratuita di [Lab di Servizi cognitivi](https://aka.ms/answersearchsubscription)

Questo esempio usa Python 3.6.4

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
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'https://api.labs.cognitive.microsoft.com'
path = '/answerSearch/v7.0/search '

query = 'Rock of Gibraltar'

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
- [Guida introduttiva in C#](c-sharp-quickstart.md)
- [Guida introduttiva in Java](java-quickstart.md)
- [Guida introduttiva in Node](node-quickstart.md)
