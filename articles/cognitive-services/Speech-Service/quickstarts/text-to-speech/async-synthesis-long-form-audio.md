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
ms.openlocfilehash: 2c1e6ef84a472ccec5116b12e18ad80c92b68960
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681891"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>Guida introduttiva: sintesi asincrona per audio a lungo termine in Python (anteprima)

In questa Guida introduttiva si userà l'API Long audio per convertire in modo asincrono il testo in sintesi vocale e recuperare l'output audio da un URI fornito dal servizio. Questa API REST è ideale per i provider di contenuti che devono convertire file di testo di dimensioni superiori a 10.000 caratteri o 50 paragrafi in sintesi vocale. Per altre informazioni, vedere [API audio lungo](../../long-audio-api.md).

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* Python 2.7. x o 3. x.
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)o l'editor di testo preferito.
* Una sottoscrizione di Azure e una chiave di sottoscrizione del servizio vocale. [Creare un account Azure](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-azure-account) e [creare una risorsa vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) per ottenere la chiave. Quando si crea la risorsa di sintesi vocale, assicurarsi che il piano tariffario sia impostato su **S0**e che location sia impostato su un' [area supportata](../../regions.md#standard-and-neural-voices).

## <a name="create-a-project-and-import-required-modules"></a>Creare un progetto e importare i moduli necessari

Creare un nuovo progetto Python tramite l'editor o l'IDE preferito. Copiare quindi questo frammento di codice in un file denominato `voice_synthesis_client.py`.

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
> Se non si è mai usato questi moduli, sarà necessario installarli prima di eseguire il programma. Per installare questi pacchetti, eseguire: `pip install requests urllib3`.

Questi moduli vengono usati per analizzare gli argomenti, costruire la richiesta HTTP e chiamare l'API REST audio di sintesi vocale.

## <a name="get-a-list-of-supported-voices"></a>Ottenere un elenco di voci supportate

Questo codice ottiene un elenco di voci disponibili che è possibile usare per convertire sintesi vocale. Aggiungere questo codice `voice_synthesis_client.py`:

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

Ora è possibile testare le operazioni eseguite fino a questo momento. Eseguire questo comando, sostituendo `<your_key>` con la chiave di sottoscrizione vocale e `<region>` con l'area in cui è stata creata la risorsa di riconoscimento vocale (ad esempio: `eastus` o `westus`). Queste informazioni sono disponibili nella scheda **Panoramica** per la risorsa nel [portale di Azure](https://aka.ms/azureportal).

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

Verrà visualizzato un output simile al seguente:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

## <a name="convert-text-to-speech"></a>Converti testo in sintesi vocale

Il passaggio successivo consiste nel preparare un file di testo di input. Può essere testo normale o SSML, ma deve contenere più di 10.000 caratteri o 50 paragrafi. Per un elenco completo dei requisiti, vedere [API audio lungo](../../long-audio-api.md).

Dopo aver preparato il file di testo. Il passaggio successivo consiste nell'aggiungere il codice per la sintesi vocale al progetto. Aggiungere questo codice a `voice_synthesis_client.py`:

> [!NOTE]
> Per impostazione predefinita, l'output audio è impostato su riff-16kHz-16 bit-mono-PCM. Per altre informazioni sugli output audio supportati, vedere [API audio lungo](../../long-audio-api.md#audio-output-formats).

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
        location = response.headers['Operation-Location']
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

Si proverà a effettuare una richiesta per sintetizzare il testo usando il file di input come origine. È necessario aggiornare alcuni elementi nella richiesta seguente:

* Sostituire `<your_key>` con la chiave di sottoscrizione per il Servizio di riconoscimento vocale. Queste informazioni sono disponibili nella scheda **Panoramica** per la risorsa nel [portale di Azure](https://aka.ms/azureportal).
* Sostituire `<region>` con l'area in cui è stata creata la risorsa vocale, ad esempio `eastus` o `westus`. Queste informazioni sono disponibili nella scheda **Panoramica** per la risorsa nel [portale di Azure](https://aka.ms/azureportal).
* Sostituire `<input>` con il percorso del file di testo che si sta cercando di convertire da sintesi vocale.
* Sostituire `<locale>` con le impostazioni locali di output desiderate. Per ulteriori informazioni, vedere [supporto](../../language-support.md#neural-voices)per le lingue.
* Sostituire `<voice_guid>` con la voce desiderata per l'output audio. Usare una delle voci restituite da [ottenere un elenco di voci supportate](#get-a-list-of-supported-voices) o usare l'elenco di voci neurali fornite nel [supporto della lingua](../../language-support.md#neural-voices).

Converte il testo in sintesi vocale con questo comando:

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> ' concatenateResult ' è un parametro facoltativo. se questo parametro non viene specificato, l'output verrà fornito come file Wave multipli, uno per ogni riga.

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

Il risultato fornito contiene il testo di input e i file di output audio generati dal servizio. Questi vengono scaricati come file zip.

## <a name="remove-previous-requests"></a>Rimuovi richieste precedenti

È previsto un limite di 2.000 richieste per ogni sottoscrizione. Di conseguenza, sarà necessario rimuovere le richieste inviate in precedenza prima di poterne creare di nuove. Se non si rimuovono le richieste esistenti, si riceverà un errore quando si supera 2.000.

Aggiungere questo codice a `voice_synthesis_client.py`:

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

Eseguire questo comando, sostituendo `<your_key>` con la chiave di sottoscrizione vocale e `<region>` con l'area in cui è stata creata la risorsa di riconoscimento vocale (ad esempio: `eastus` o `westus`). Queste informazioni sono disponibili nella scheda **Panoramica** per la risorsa nel [portale di Azure](https://aka.ms/azureportal).

```console
python voice_synthesis_client.py – syntheses -key <your_key> -region <Region>
```

Verrà restituito un elenco di sintesi richieste. Verrà visualizzato un output simile al seguente:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Verranno ora usati alcuni di questi valori per rimuovere o eliminare le richieste inviate in precedenza. Eseguire questo comando, sostituendo `<your_key>` con la chiave di sottoscrizione vocale e `<region>` con l'area in cui è stata creata la risorsa di riconoscimento vocale (ad esempio: `eastus` o `westus`). Queste informazioni sono disponibili nella scheda **Panoramica** per la risorsa nel [portale di Azure](https://aka.ms/azureportal). Il `<synthesis_id>` deve essere uno dei valori restituiti nella richiesta precedente.

> [!NOTE]
> Le richieste con stato ' running '/' Waiting ' non possono essere rimosse o eliminate.

```console
python voice_synthesis_client.py – delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

Verrà visualizzato un output simile al seguente:

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>Ottenere il client completo

Il `voice_synthesis_client.py` completo è disponibile per il download in [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sull'API Long audio](../../long-audio-api.md)
