---
title: 'Guida introduttiva: sintesi asincrona per audio a lungo termine (anteprima)-servizio di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: Usare l'API Long audio per convertire in modo asincrono il testo in sintesi vocale e recuperare l'output audio da un URI fornito dal servizio. Questa API REST è ideale per i provider di contenuti che devono convertire file di testo di dimensioni superiori a 10.000 caratteri o 50 paragrafi in sintesi vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 62236b472aa5c4812cd62af44a15b805b5326271
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592560"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>Guida introduttiva: sintesi asincrona per audio a lungo termine in Python (anteprima)

In questa Guida introduttiva si userà l'API Long audio per convertire in modo asincrono il testo in sintesi vocale e recuperare l'output audio da un URI fornito dal servizio. Questa API REST è ideale per i provider di contenuti che devono sintetizzare l'audio da un testo di dimensioni maggiori di 5.000 o più di 10 minuti. Per altre informazioni, vedere [API audio lungo](../../long-audio-api.md).

La sintesi asincrona per l'audio a lungo termine può essere usata con le [voci neurali pubbliche](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) e le [voci neurali personalizzate](../../how-to-custom-voice.md#custom-neural-voices), ognuna delle quali supporta un linguaggio e un dialetto specifici. 

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* Python 2.7. x o 3. x.
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)o l'editor di testo preferito.
* Una sottoscrizione di Azure e una chiave di sottoscrizione del servizio vocale. [Creare un account Azure](../../get-started.md#new-resource) e [creare una risorsa vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-resource) per ottenere la chiave. Quando si crea la risorsa di sintesi vocale, assicurarsi che il piano tariffario sia impostato su **S0**e che location sia impostato su un' [area supportata](../../regions.md#standard-and-neural-voices).

## <a name="create-a-project-and-import-required-modules"></a>Creare un progetto e importare i moduli necessari

Creare un nuovo progetto Python tramite l'editor o l'IDE preferito. Copiare quindi questo frammento di codice in un file denominato `voice_synthesis_client.py` .

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
> Se non sono stati usati questi moduli, è necessario installarli prima di eseguire il programma. Per installare questi pacchetti, eseguire: `pip install requests urllib3`.

Questi moduli vengono usati per analizzare gli argomenti, costruire la richiesta HTTP e chiamare l'API REST audio di sintesi vocale.

## <a name="get-a-list-of-supported-voices"></a>Ottenere un elenco di voci supportate

Questo codice consente di ottenere un elenco completo delle voci per un'area o un endpoint specifico che è possibile usare. Verificare l' [area/endpoint supportati](../../long-audio-api.md). Aggiungere il codice a `voice_synthesis_client.py` :

```python
parser = argparse.ArgumentParser(description='Text-to-speech client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the speech subscription key, like fg1f763i01d94768bda32u7a******** ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0-beta1/' % args.region

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

Ora è possibile testare le operazioni eseguite fino a questo momento. È necessario aggiornare alcuni elementi nella richiesta seguente:

* Sostituire `<your_key>` con la chiave di sottoscrizione per il Servizio di riconoscimento vocale. Queste informazioni sono disponibili nella scheda **Panoramica** per la risorsa nel [portale di Azure](https://aka.ms/azureportal).
* Sostituire `<region>` con l'area in cui è stata creata la risorsa vocale, ad esempio `eastus` o `westus` . Queste informazioni sono disponibili nella scheda **Panoramica** per la risorsa nel [portale di Azure](https://aka.ms/azureportal).

Eseguire questo comando:

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

Verrà visualizzato un output simile al seguente:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

Se il parametro **PublicVoice** è **true**, la voce è public Neural Voice. In caso contrario, si tratta di una voce neurale personalizzata. 

## <a name="prepare-input-files"></a>Preparare i file di input

Preparare un file di testo di input. Può essere testo normale o testo SSML. Per i requisiti dei file di input, vedere How to [Prepare content for SYNTHESIS](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#prepare-content-for-synthesis).

## <a name="convert-text-to-speech"></a>Converti testo in sintesi vocale

Dopo aver preparato il file di testo di input, aggiungere questo codice per la sintesi vocale a `voice_synthesis_client.py` :

> [!NOTE]
> ' concatenateResult ' è un parametro facoltativo. Se questo parametro non è impostato, verranno generati gli output audio per paragrafo. È anche possibile concatenare gli audio in 1 output impostando il parametro. Per impostazione predefinita, l'output audio è impostato su riff-16kHz-16 bit-mono-PCM. Per altre informazioni sugli output audio supportati, vedere [formati di output audio](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats).

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

Viene ora fatta una richiesta per sintetizzare il testo usando il file di input come origine. È necessario aggiornare alcuni elementi nella richiesta seguente:

* Sostituire `<your_key>` con la chiave di sottoscrizione per il Servizio di riconoscimento vocale. Queste informazioni sono disponibili nella scheda **Panoramica** per la risorsa nel [portale di Azure](https://aka.ms/azureportal).
* Sostituire `<region>` con l'area in cui è stata creata la risorsa vocale, ad esempio `eastus` o `westus` . Queste informazioni sono disponibili nella scheda **Panoramica** per la risorsa nel [portale di Azure](https://aka.ms/azureportal).
* Sostituire `<input>` con il percorso del file di testo preparato per la sintesi vocale.
* Sostituire `<locale>` con le impostazioni locali di output desiderate. Per ulteriori informazioni, vedere [supporto](../../language-support.md#neural-voices)per le lingue.
* Sostituire `<voice_guid>` con la voce di output desiderata. Usare una delle voci restituite da [ottenere un elenco di voci supportate](#get-a-list-of-supported-voices).

Converte il testo in sintesi vocale con questo comando:

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> Se si dispone di più di 1 file di input, sarà necessario inviare più richieste. È necessario tenere presenti alcune limitazioni. 
> * Il client può inviare fino a **5** richieste al server al secondo per ogni account della sottoscrizione di Azure. Se supera la limitazione, il client otterrà un codice di errore 429 (troppe richieste). Ridurre il numero di richieste al secondo
> * Il server può eseguire e accodare fino a **120** richieste per ogni account della sottoscrizione di Azure. Se supera la limitazione, il server restituirà un codice di errore 429 (troppe richieste). Attendere ed evitare di inviare una nuova richiesta fino al completamento di alcune richieste

Verrà visualizzato un output simile al seguente:

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

## <a name="remove-previous-requests"></a>Rimuovi richieste precedenti

Il server manterrà fino a **20.000** richieste per ogni account della sottoscrizione di Azure. Se la quantità di richieste supera questa limitazione, rimuovere le richieste precedenti prima di renderne nuove. Se non si rimuovono le richieste esistenti, si riceverà una notifica di errore.

Aggiungere il codice a `voice_synthesis_client.py` :

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

A questo punto, è possibile esaminare le richieste inviate in precedenza. Prima di continuare, è necessario aggiornare alcuni elementi in questa richiesta:

* Sostituire `<your_key>` con la chiave di sottoscrizione per il Servizio di riconoscimento vocale. Queste informazioni sono disponibili nella scheda **Panoramica** per la risorsa nel [portale di Azure](https://aka.ms/azureportal).
* Sostituire `<region>` con l'area in cui è stata creata la risorsa vocale, ad esempio `eastus` o `westus` . Queste informazioni sono disponibili nella scheda **Panoramica** per la risorsa nel [portale di Azure](https://aka.ms/azureportal).

Eseguire questo comando:

```console
python voice_synthesis_client.py --syntheses -key <your_key> -region <Region>
```

Verrà restituito un elenco di richieste di sintesi effettuate. Verrà visualizzato un output simile al seguente:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

A questo punto, rimuovere una richiesta inviata in precedenza. È necessario aggiornare alcuni elementi nel codice riportato di seguito:

* Sostituire `<your_key>` con la chiave di sottoscrizione per il Servizio di riconoscimento vocale. Queste informazioni sono disponibili nella scheda **Panoramica** per la risorsa nel [portale di Azure](https://aka.ms/azureportal).
* Sostituire `<region>` con l'area in cui è stata creata la risorsa vocale, ad esempio `eastus` o `westus` . Queste informazioni sono disponibili nella scheda **Panoramica** per la risorsa nel [portale di Azure](https://aka.ms/azureportal).
* Sostituire `<synthesis_id>` con il valore restituito nella richiesta precedente.

> [!NOTE]
> Le richieste con stato ' running '/' Waiting ' non possono essere rimosse o eliminate.

Eseguire questo comando:

```console
python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

Verrà visualizzato un output simile al seguente:

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>Ottenere il client completo

Il completamento `voice_synthesis_client.py` è disponibile per il download in [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sull'API Long audio](../../long-audio-api.md)
