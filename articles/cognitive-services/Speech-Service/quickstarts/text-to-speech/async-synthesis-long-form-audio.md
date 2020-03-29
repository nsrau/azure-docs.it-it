---
title: 'Guida introduttiva: sintesi asincrona per audio in formato lungo (anteprima) - Servizio di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: Usa l'API Audio lungo per convertire in modo asincrono il testo in sintesi vocale e recuperare l'output audio da un URI fornito dal servizio. Questa API REST è ideale per i provider di contenuti che devono convertire file di testo di dimensioni superiori a 10.000 caratteri o 50 paragrafi in sintesi vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: d3cd330001bcf53e7bd4fb9e6955c76a9ef20511
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331077"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>Guida introduttiva: sintesi asincrona per l'audio in formato lungo in Python (anteprima)Quickstart: Asynchronous synthesis for long-form audio in Python (Preview)

In questa guida introduttiva si userà l'API Long Audio per convertire in modo asincrono il testo in sintesi vocale e recuperare l'output audio da un URI fornito dal servizio. Questa API REST è ideale per i provider di contenuti che devono sintetizzare l'audio da testo superiore a 5.000 caratteri (o più di 10 minuti). Per ulteriori informazioni, consultate [API Audio lungo.](../../long-audio-api.md)

> [!NOTE]
> La sintesi asincrona per l'audio in formato lungo può essere utilizzata solo con [Voci neurali personalizzate](../../how-to-custom-voice.md#custom-neural-voices).

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* Python 2.7.x o 3.x.
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)o l'editor di testo preferito.
* Una sottoscrizione di Azure e una chiave di sottoscrizione del servizio di riconoscimento vocale. [Creare un account Azure](../../get-started.md#new-resource) e creare una risorsa di [riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-resource) per ottenere la chiave. Quando si crea la risorsa di riconoscimento vocale, assicurarsi che il piano tariffario sia impostato su **S0**e che la posizione sia impostata su [un'area supportata.](../../regions.md#standard-and-neural-voices)

## <a name="create-a-project-and-import-required-modules"></a>Creare un progetto e importare i moduli necessari

Creare un nuovo progetto Python tramite l'editor o l'IDE preferito. Copiare quindi questo frammento `voice_synthesis_client.py`di codice in un file denominato .

```python
import argparse
import json
import ntpath
import urllib3
import requests
import time
from json import dumps, loads, JSONEncoder, JSONDecoder
import pickle

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
```

> [!NOTE]
> Se questi moduli non sono stati utilizzati, è necessario installarli prima di eseguire il programma. Per installare questi pacchetti, eseguire: `pip install requests urllib3`.

Questi moduli vengono utilizzati per analizzare gli argomenti, costruire la richiesta HTTP e chiamare l'API REST audio lunga di sintesi vocale.

## <a name="get-a-list-of-supported-voices"></a>Ottenere un elenco delle voci supportateGet a list of supported voices

Questo codice ottiene un elenco di voci disponibili che è possibile utilizzare per convertire la sintesi vocale. Aggiungere il `voice_synthesis_client.py`codice a :

```python
parser = argparse.ArgumentParser(description='Cris client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the cris subscription key, like ff1eb62d06d34767bda0207acb1da7d7 ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.cris.ai/api/texttospeech/v3.0-beta1/' % args.region

def getVoices():
    response=requests.get(baseAddress+"voicesynthesis/voices", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    voices = json.loads(response.text)
    return voices

if args.voices:
    voices = getVoices()
    print("There are %d voices available:" % len(voices))
    for voice in voices:
        print ("Name: %s, Description: %s, Id: %s, Locale: %s, Gender: %s, PublicVoice: %s, Created: %s" % (voice['name'], voice['description'], voice['id'], voice['locale'], voice['gender'], voice['isPublicVoice'], voice['created']))
```

### <a name="test-your-code"></a>Testare il codice

Testiamo quello che hai fatto finora. È necessario aggiornare alcune cose nella richiesta qui sotto:

* Sostituire `<your_key>` con la chiave di sottoscrizione per il Servizio di riconoscimento vocale. Queste informazioni sono disponibili nella scheda **Panoramica** della risorsa nel portale di [Azure.](https://aka.ms/azureportal)
* Sostituire `<region>` con l'area in cui è `eastus` stata `westus`creata la risorsa riconoscimento vocale (ad esempio: o ). Queste informazioni sono disponibili nella scheda **Panoramica** della risorsa nel portale di [Azure.](https://aka.ms/azureportal)

Eseguire questo comando:

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

Verrà visualizzato un output simile al seguente:You'll see an output that looks like this:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

## <a name="prepare-input-files"></a>Preparare i file di input

Preparare un file di testo di input. Può essere testo normale o Testo SSML. Per i requisiti del file di input, vedere come preparare il contenuto per la [sintesi.](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#prepare-content-for-synthesis)

## <a name="convert-text-to-speech"></a>Convertire il testo in voce

Dopo aver preparato il file di testo `voice_synthesis_client.py`di input, aggiungere questo codice per la sintesi vocale a :

> [!NOTE]
> 'concatenateResult' è un parametro facoltativo. Se questo parametro non è impostato, le uscite audio verranno generate per paragrafo. È inoltre possibile concatenare gli audio in 1 output impostando il parametro. Per impostazione predefinita, l'uscita audio è impostata su riff-16khz-16bit-mono-pcm. Per ulteriori informazioni sulle uscite audio supportate, consultate Formati di [output audio.](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats)

```python
parser.add_argument('--submit', action="store_true", default=False, help='submit a synthesis request')
parser.add_argument('--concatenateResult', action="store_true", default=False, help='If concatenate result in a single wave file')
parser.add_argument('-file', action="store", dest="file", help='the input text script file path')
parser.add_argument('-voiceId', action="store", nargs='+', dest="voiceId", help='the id of the voice which used to synthesis')
parser.add_argument('-locale', action="store", dest="locale", help='the locale information like zh-CN/en-US')
parser.add_argument('-format', action="store", dest="format", default='riff-16khz-16bit-mono-pcm', help='the output audio format')

def submitSynthesis():
    modelList = args.voiceId
    data={'name': 'simple test', 'description': 'desc...', 'models': json.dumps(modelList), 'locale': args.locale, 'outputformat': args.format}
    if args.concatenateResult:
        properties={'ConcatenateResult': 'true'}
        data['properties'] = json.dumps(properties)
    if args.file is not None:
        scriptfilename=ntpath.basename(args.file)
        files = {'script': (scriptfilename, open(args.file, 'rb'), 'text/plain')}
    response = requests.post(baseAddress+"voicesynthesis", data, headers={"Ocp-Apim-Subscription-Key":args.key}, files=files, verify=False)
    if response.status_code == 202:
        location = response.headers['Location']
        id = location.split("/")[-1]
        print("Submit synthesis request successful")
        return id
    else:
        print("Submit synthesis request failed")
        print("response.status_code: %d" % response.status_code)
        print("response.text: %s" % response.text)
        return 0

def getSubmittedSynthesis(id):
    response=requests.get(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    synthesis = json.loads(response.text)
    return synthesis

if args.submit:
    id = submitSynthesis()
    if (id == 0):
        exit(1)

    while(1):
        print("\r\nChecking status")
        synthesis=getSubmittedSynthesis(id)
        if synthesis['status'] == "Succeeded":
            r = requests.get(synthesis['resultsUrl'])
            filename=id + ".zip"
            with open(filename, 'wb') as f:  
                f.write(r.content)
                print("Succeeded... Result file downloaded : " + filename)
            break
        elif synthesis['status'] == "Failed":
            print("Failed...")
            break
        elif synthesis['status'] == "Running":
            print("Running...")
        elif synthesis['status'] == "NotStarted":
            print("NotStarted...")
        time.sleep(10)
```

### <a name="test-your-code"></a>Testare il codice

Facciamo una richiesta per sintetizzare il testo usando il file di input come origine. È necessario aggiornare alcune cose nella richiesta qui sotto:

* Sostituire `<your_key>` con la chiave di sottoscrizione per il Servizio di riconoscimento vocale. Queste informazioni sono disponibili nella scheda **Panoramica** della risorsa nel portale di [Azure.](https://aka.ms/azureportal)
* Sostituire `<region>` con l'area in cui è `eastus` stata `westus`creata la risorsa riconoscimento vocale (ad esempio: o ). Queste informazioni sono disponibili nella scheda **Panoramica** della risorsa nel portale di [Azure.](https://aka.ms/azureportal)
* Sostituire `<input>` con il percorso del file di testo preparato per la sintesi vocale.
* Sostituire `<locale>` con le impostazioni locali di output desiderate. Per ulteriori informazioni, vedere [Supporto della lingua](../../language-support.md#neural-voices).
* Sostituire `<voice_guid>` con la voce di uscita desiderata. Utilizzare una delle voci restituite da [Get a list of supported voices](#get-a-list-of-supported-voices).

Converti il testo in voce con questo comando:

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> Se si dispone di più di 1 file di input, sarà necessario inviare più richieste. Ci sono alcune limitazioni che devono essere consapevoli. 
> * Il client può inviare fino a 5 richieste al server al secondo per ogni account di sottoscrizione di Azure.The client is allowed to submit up to **5** requests to server per second for each Azure subscription account. Se supera il limite, client otterrà un codice di errore 429 (troppe richieste). Si prega di ridurre l'importo della richiesta al secondo
> * Il server può eseguire e accodare fino a 120 richieste per ogni account di sottoscrizione di Azure.The server is allowed to run and queue up to **120** requests for each Azure subscription account. Se supera il limite, il server restituirà un codice di errore 429 (troppe richieste). Si prega di attendere ed evitare di inviare nuova richiesta fino a quando alcune richieste sono completate

Verrà visualizzato un output simile al seguente:You'll see an output that looks like this:

```console
Submit synthesis request successful

Checking status
NotStarted...

Checking status
Running...

Checking status
Running...

Checking status
Succeeded... Result file downloaded : xxxx.zip
```

Il risultato contiene il testo di input e i file di output audio generati dal servizio. È possibile scaricare questi file in un file zip.

## <a name="remove-previous-requests"></a>Rimuovere le richieste precedenti

Il server manterrà fino a 20.000 richieste per ogni account di sottoscrizione di Azure.The server will keep up to **20,000** requests for each Azure subscription account. Se l'importo della richiesta supera questa limitazione, rimuovere le richieste precedenti prima di effettuarne di nuove. Se non rimuovi le richieste esistenti, riceverai una notifica di errore.

Aggiungere il `voice_synthesis_client.py`codice a :

```python
parser.add_argument('--syntheses', action="store_true", default=False, help='print synthesis list')
parser.add_argument('--delete', action="store_true", default=False, help='delete a synthesis request')
parser.add_argument('-synthesisId', action="store", nargs='+', dest="synthesisId", help='the id of the voice synthesis which need to be deleted')

def getSubmittedSyntheses():
    response=requests.get(baseAddress+"voicesynthesis", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    syntheses = json.loads(response.text)
    return syntheses

def deleteSynthesis(ids):
    for id in ids:
        print("delete voice synthesis %s " % id)
        response = requests.delete(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
        if (response.status_code == 204):
            print("delete successful")
        else:
            print("delete failed, response.status_code: %d, response.text: %s " % (response.status_code, response.text))

if args.syntheses:
    synthese = getSubmittedSyntheses()
    print("There are %d synthesis requests submitted:" % len(synthese))
    for synthesis in synthese:
        print ("ID : %s , Name : %s, Status : %s " % (synthesis['id'], synthesis['name'], synthesis['status']))

if args.delete:
    deleteSynthesis(args.synthesisId)
```

### <a name="test-your-code"></a>Testare il codice

Ora, controlliamo quali richieste hai inviato in precedenza. Prima di continuare, è necessario aggiornare alcuni aspetti in questa richiesta:Before you continue, you'll need to update a few things in this request:

* Sostituire `<your_key>` con la chiave di sottoscrizione per il Servizio di riconoscimento vocale. Queste informazioni sono disponibili nella scheda **Panoramica** della risorsa nel portale di [Azure.](https://aka.ms/azureportal)
* Sostituire `<region>` con l'area in cui è `eastus` stata `westus`creata la risorsa riconoscimento vocale (ad esempio: o ). Queste informazioni sono disponibili nella scheda **Panoramica** della risorsa nel portale di [Azure.](https://aka.ms/azureportal)

Eseguire questo comando:

```console
python voice_synthesis_client.py --syntheses -key <your_key> -region <Region>
```

Verrà restituito un elenco di richieste di sintesi effettuate. Vedrai un output come questo:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

A questo punto, rimuovere una richiesta inviata in precedenza. È necessario aggiornare alcune cose nel codice seguente:You'll need to update a few things in the code below:

* Sostituire `<your_key>` con la chiave di sottoscrizione per il Servizio di riconoscimento vocale. Queste informazioni sono disponibili nella scheda **Panoramica** della risorsa nel portale di [Azure.](https://aka.ms/azureportal)
* Sostituire `<region>` con l'area in cui è `eastus` stata `westus`creata la risorsa riconoscimento vocale (ad esempio: o ). Queste informazioni sono disponibili nella scheda **Panoramica** della risorsa nel portale di [Azure.](https://aka.ms/azureportal)
* Sostituire `<synthesis_id>` con il valore restituito nella richiesta precedente.

> [!NOTE]
> Le richieste con stato 'In esecuzione'/'In attesa' non possono essere rimosse o eliminate.

Eseguire questo comando:

```console
python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

Vedrai un output come questo:

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>Ottieni il cliente completo

Il `voice_synthesis_client.py` completamento è disponibile per il download su [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ulteriori informazioni sull'API Long Audio](../../long-audio-api.md)
