---
title: 'Avvio rapido: Eseguire la sintesi vocale, Python - Servizi di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: In questo avvio rapido si apprenderà come eseguire la sintesi vocale di testo usando Python e l'API REST Sintesi vocale. Il testo di esempio incluso in questo articolo è strutturato come SSML (Speech Synthesis Markup Language). Questo formato consente di scegliere la voce e la lingua della risposta di sintesi vocale.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 11/16/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 7512c443c49f1518d6a6f7ace7e981d3508b1d7a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090058"
---
# <a name="quickstart-convert-text-to-speech-using-python"></a>Avvio rapido: Eseguire la sintesi vocale con Python

In questo avvio rapido si apprenderà come eseguire la sintesi vocale di testo usando Python e l'API REST Sintesi vocale. In questo articolo il corpo della richiesta è strutturato nel formato [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md), che consente di scegliere la voce e la lingua della risposta.

Per questo avvio rapido è necessario avere un [account di Servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con una risorsa del servizio Voce. Se non si dispone di un account, è possibile usare la [versione di valutazione gratuita](get-started.md) per ottenere una chiave di sottoscrizione.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* Python 2.7.x o 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) o l'editor di testo preferito
* Una chiave di sottoscrizione di Azure per il servizio Voce

## <a name="create-a-project-and-import-required-modules"></a>Creare un progetto e importare i moduli necessari

Creare un nuovo progetto Python tramite l'editor o l'IDE preferito. Copiare quindi questo frammento di codice nel progetto all'interno di un file denominato `tts.py`.

```python
import os, requests, time
```

> [!NOTE]
> Se non si è mai usato questi moduli, sarà necessario installarli prima di eseguire il programma. Per installare questi pacchetti, eseguire: `pip install requests`.

Questi moduli vengono usati per scrivere la risposta di sintesi vocale in un file con un timestamp, costruire la richiesta HTTP e chiamare l'API Sintesi vocale.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Impostare la chiave di sottoscrizione e creare un prompt per la sintesi vocale

Nelle sezioni successive si creeranno i metodi per gestire l'autorizzazione, chiamare l'API Sintesi vocale e convalidare la risposta. Per iniziare, aggiungere il codice necessario affinché questo esempio funzioni con Python 2.7.x e 3.x.

```python
try: input = raw_input
except NameError: pass
```

Creare quindi una classe. In questa classe si inseriranno i metodi per lo scambio di token e la chiamata all'API Sintesi vocale.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

`subscription_key` è la chiave univoca ottenuta dal portale di Azure. `tts` chiede all'utente di immettere il testo di cui eseguire la sintesi vocale. Questo input è un valore letterale di tipo stringa e pertanto i caratteri non devono essere preceduti da caratteri di escape. `timestr` ottiene infine l'ora corrente, che verrà usata per definire il nome del file.

## <a name="get-an-access-token"></a>Ottenere un token di accesso

L'API REST Sintesi vocale necessita di un token di accesso per l'autenticazione. Per ottenere un token di accesso, è necessario uno scambio. In questo esempio viene scambiata la chiave di sottoscrizione del servizio Voce con un token di accesso usando l'endpoint `issueToken`.

L'esempio presuppone che la sottoscrizione del servizio Voce sia nell'area Stati Uniti occidentali. Se si usa un'area diversa, aggiornare il valore per `fetch_token_url`. Per un elenco completo, vedere [Aree](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Copiare questo codice nella classe `TextToSpeech`:

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
> Per altre informazioni sull'autenticazione, vedere [Come ottenere un token di accesso](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#how-to-get-an-access-token).

## <a name="make-a-request-and-save-the-response"></a>Eseguire una richiesta e salvare la risposta

In questa sezione si creerà la richiesta e si salverà la risposta di sintesi vocale. In primo luogo, è necessario impostare `base_url` e `path`. Questo esempio presuppone che si usi l'endpoint Stati Uniti occidentali. Se la risorsa è registrata in un'area diversa, assicurarsi di aggiornare il valore di `base_url`. Per altre informazioni, vedere [Aree del servizio Voce](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

È quindi necessario aggiungere le intestazioni obbligatorie per la richiesta. Assicurarsi di aggiornare `User-Agent` con il nome della risorsa, che si trova nel portale di Azure, e impostare `X-Microsoft-OutputFormat` sul formato di output audio preferito. Per un elenco completo dei formati di output, vedere [Output audio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#audio-outputs).

Creare quindi il corpo della richiesta nel formato SSML (Speech Synthesis Markup Language). Questo esempio definisce la struttura e usa l'input `tts` creato in precedenza.

>[!NOTE]
> Questo esempio usa il carattere voce `ZiraRUS`. Per un elenco completo di voci e lingue fornite da Microsoft, vedere [Supporto per le lingue](language-support.md).
> Se si vuole creare una voce unica, riconoscibile per il proprio marchio, vedere [Creazione di caratteri voce personalizzati](how-to-customize-voice-font.md).

Si creerà infine una richiesta da inviare al servizio. Se la richiesta ha esito positivo e viene restituito un codice di stato 200, la risposta di sintesi vocale viene scritta in un file con timestamp.

Copiare questo codice nella classe `TextToSpeech`:

```python
def save_audio(self):
    base_url = 'https://westus.tts.speech.microsoft.com/'
    path = 'cognitiveservices/v1'
    constructed_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token,
        'Content-Type': 'application/ssml+xml',
        'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
        'User-Agent': 'YOUR_RESOURCE_NAME',
        'cache-control': 'no-cache'
    }
    body = "<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>" + self.tts + "</voice></speak>"

    response = requests.post(constructed_url, headers=headers, data=body)
    if response.status_code == 200:
        with open('sample-' + self.timestr + '.wav', 'wb') as audio:
            audio.write(response.content)
            print("\nStatus code: " + str(response.status_code) + "\nYour TTS is ready for playback.\n")
    else:
        print("\nStatus code: " + str(response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")

```

## <a name="put-it-all-together"></a>Combinare tutti gli elementi

La procedura è quasi terminata. L'ultimo passaggio consiste nel creare un'istanza della classe e chiamare le funzioni.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

A questo punto è possibile eseguire l'app di sintesi vocale di esempio. Dalla riga di comando, o dalla sessione di terminale, passare alla directory del progetto ed eseguire:

```console
python tts.py
```

Quando richiesto, digitare un testo qualsiasi di cui eseguire la sintesi vocale. Se l'operazione ha esito positivo, il file della sintesi vocale viene salvato nella cartella del progetto. Riprodurlo con il lettore multimediale preferito.

## <a name="clean-up-resources"></a>Pulire le risorse

Assicurarsi di rimuovere eventuali informazioni riservate dal codice sorgente dell'app di esempio, ad esempio le chiavi di sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API Sintesi vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)

## <a name="see-also"></a>Vedere anche 

* [Creazione di caratteri voce personalizzati](how-to-customize-voice-font.md)
* [Esempi di campioni vocali per la creazione di una voce personalizzata](record-custom-voice-samples.md)
