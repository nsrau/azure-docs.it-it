---
title: 'Guida introduttiva: API Traduzione vocale Python'
titlesuffix: Azure Cognitive Services
description: Informazioni ed esempi di codice per iniziare a usare rapidamente l'API Traduzione vocale.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7189563ebbcc5ae1a167f99ff8704aff16d0feac
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966470"
---
# <a name="quickstart-translator-speech-api-with-python"></a>Guida introduttiva: API Traduzione vocale con Python
<a name="HOLTop"></a>

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Questo articolo illustra come usare l'API Traduzione vocale per tradurre il testo parlato in un file con estensione wav.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questo codice è necessario [Python 3.x](https://www.python.org/downloads/).

È necessario installare il [pacchetto websocket-client](https://pypi.python.org/pypi/websocket-client) per Python.

È inoltre necessario un file con estensione wav denominato "speak.wav" nella stessa cartella del file eseguibile che si compila dal codice seguente. Il file con estensione wav deve essere in formato PCM standard, a 16 bit, 16 kHz, mono.

È necessario disporre di un [account delle API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con l'**API Traduzione vocale Microsoft**. È infine necessaria una chiave di sottoscrizione a pagamento configurata nel [dashboard di Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Traduzione vocale

Il codice seguente traduce il parlato da una lingua a un'altra.

1. Creare un nuovo progetto Python nell'ambiente di sviluppo integrato preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore di `key` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

```python
# -*- coding: utf-8 -*-

# To install this package, run:
#   pip install websocket-client
import websocket

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'wss://dev.microsofttranslator.com'
path = '/speech/translate'
params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa'
uri = host + path + params

input_file = 'speak.wav'
output_file = 'speak2.wav'

output = bytearray()


def on_open(client):
    print("Connected.")

# r = read. b = binary.
    with open(input_file, mode='rb') as file:
        data = file.read()

    print("Sending audio.")
    client.send(data, websocket.ABNF.OPCODE_BINARY)
# Make sure the audio file is followed by silence.
# This lets the service know that the audio input is finished.
    print("Sending silence.")
    client.send(bytearray(32000), websocket.ABNF.OPCODE_BINARY)


def on_data(client, message, message_type, is_last):
    global output
    if (websocket.ABNF.OPCODE_TEXT == message_type):
        print("Received text data.")
        print(message)
# For some reason, we receive the data as type websocket.ABNF.OPCODE_CONT.
    elif (websocket.ABNF.OPCODE_BINARY == message_type or websocket.ABNF.OPCODE_CONT == message_type):
        print("Received binary data.")
        print("Is last? " + str(is_last))
        output = output + message
        if (True == is_last):
            # w = write. b = binary.
            with open(output_file, mode='wb') as file:
                file.write(output)
                print("Wrote data to output file.")
            client.close()
    else:
        print("Received data of type: " + str(message_type))


def on_error(client, error):
    print("Connection error: " + str(error))


def on_close(client):
    print("Connection closed.")


client = websocket.WebSocketApp(
    uri,
    header=[
        'Ocp-Apim-Subscription-Key: ' + key
    ],
    on_open=on_open,
    on_data=on_data,
    on_error=on_error,
    on_close=on_close
)

print("Connecting...")
client.run_forever()
```

**Risposta della traduzione vocale**

Se il risultato è positivo, viene creato un file denominato "speak2.wav" contenente la traduzione del testo parlato in "speak.wav".

[Torna all'inizio](#HOLTop)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione su Traduzione vocale](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Vedere anche

[Panoramica di Traduzione vocale](../overview.md)
[Informazioni di riferimento sull'API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
