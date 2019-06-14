---
title: 'Avvio rapido: Elenco voci sintesi vocale, Python - servizi di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: In questa Guida introduttiva si apprenderà come ottenere l'elenco completo delle voci neurale e standard per un'area/endpoint usando Python. Viene restituito l'elenco in formato JSON e disponibilità vocale varia in base all'area.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 03/22/2019
ms.author: erhopf
ms.openlocfilehash: 5d43060ee5303c1df3a1b8448f086ecc32a1f8c3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056867"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-python"></a>Avvio rapido: Ottiene l'elenco di voci di sintesi vocale con Python

In questa Guida introduttiva si apprenderà come ottenere l'elenco completo delle voci neurale e standard per un'area/endpoint usando Python. Viene restituito l'elenco in formato JSON e disponibilità vocale varia in base all'area. Per un elenco delle aree supportate, vedere [aree](regions.md).

Questa Guida introduttiva richiede un [account di servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con una risorsa di servizi di riconoscimento vocale. Se non si dispone di un account, è possibile usare la [versione di valutazione gratuita](get-started.md) per ottenere una chiave di sottoscrizione.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* Python 2.7.x o 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) o l'editor di testo preferito
* Una chiave di sottoscrizione di Azure per i servizi di riconoscimento vocale

## <a name="create-a-project-and-import-required-modules"></a>Creare un progetto e importare i moduli necessari

Creare un nuovo progetto Python tramite l'editor o l'IDE preferito. Copiare quindi questo frammento di codice nel progetto all'interno di un file denominato `get-voices.py`.

```python
import requests
```

> [!NOTE]
> Se non si è mai usato questi moduli, sarà necessario installarli prima di eseguire il programma. Per installare questi pacchetti, eseguire: `pip install requests`.

Le richieste viene usata per le richieste HTTP al servizio di sintesi vocale.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Impostare la chiave di sottoscrizione e creare un prompt per la sintesi vocale

Nelle sezioni successive si creeranno i metodi per gestire l'autorizzazione, chiamare l'API Sintesi vocale e convalidare la risposta. Iniziamo creando una classe. In questa classe si inseriranno i metodi per lo scambio di token e la chiamata all'API Sintesi vocale.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

`subscription_key` è la chiave univoca ottenuta dal portale di Azure.

## <a name="get-an-access-token"></a>Ottenere un token di accesso

Questo endpoint richiede un token di accesso per l'autenticazione. Per ottenere un token di accesso, è necessario uno scambio. In questo esempio la chiave di sottoscrizione di servizi di riconoscimento vocale per un token di accesso tramite lo scambio di `issueToken` endpoint.

In questo esempio si presuppone che la sottoscrizione di servizi di riconoscimento vocale è nell'area Stati Uniti occidentali. Se si usa un'area diversa, aggiornare il valore per `fetch_token_url`. Per un elenco completo, vedere [Aree](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Copiare questo codice nella classe `GetVoices`:

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> Per altre informazioni sull'autenticazione, vedere [Eseguire l'autenticazione con un token di autenticazione](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Eseguire una richiesta e salvare la risposta

In questo caso si intende la richiesta di compilazione e salvare l'elenco di voci restituite. In primo luogo, è necessario impostare `base_url` e `path`. Questo esempio presuppone che si usi l'endpoint Stati Uniti occidentali. Se la risorsa è registrata in un'area diversa, assicurarsi di aggiornare il valore di `base_url`. Per altre informazioni, vedere [aree di servizi di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Successivamente, aggiungere le intestazioni obbligatorie per la richiesta. Si creerà infine una richiesta da inviare al servizio. Se la richiesta ha esito positivo e viene restituito un codice di 200 stato, la risposta viene scritto in un file.

Copiare questo codice nella classe `GetVoices`:

```python
def get_voices(self):
    base_url = 'https://westus.tts.speech.microsoft.com'
    path = '/cognitiveservices/voices/list'
    get_voices_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token
    }
    response = requests.get(get_voices_url, headers=headers)
    if response.status_code == 200:
        with open('voices.json', 'wb') as voices:
            voices.write(response.content)
            print("\nStatus code: " + str(response.status_code) + "\nvoices.json is ready to view.\n")
    else:
        print("\nStatus code: " + str(
            response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Combinare tutti gli elementi

La procedura è quasi terminata. L'ultimo passaggio consiste nel creare un'istanza della classe e chiamare le funzioni.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

Questo punto, si è pronti per eseguire l'esempio. Dalla riga di comando, o dalla sessione di terminale, passare alla directory del progetto ed eseguire:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Pulire le risorse

Assicurarsi di rimuovere eventuali informazioni riservate dal codice sorgente dell'app di esempio, ad esempio le chiavi di sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice Python su GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Vedere anche

* [Informazioni di riferimento sull'API Sintesi vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Creazione di caratteri voce personalizzati](how-to-customize-voice-font.md)
* [Esempi di campioni vocali per la creazione di una voce personalizzata](record-custom-voice-samples.md)
