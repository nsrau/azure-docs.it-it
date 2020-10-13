---
title: API Long audio (anteprima)-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Scopri in che modo l'API Long audio è progettata per la sintesi asincrona del testo in formato esteso.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: be38d3e78108a15c9f7875a15156e0eeba5a6211
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88167760"
---
# <a name="long-audio-api-preview"></a>API Long audio (anteprima)

L'API Long audio è progettata per la sintesi asincrona del testo a lungo termine (ad esempio, libri audio, articoli di notizie e documenti). Questa API non restituisce audio sintetizzato in tempo reale, ma l'aspettativa è che si esegua il polling delle risposte e si utilizzino gli output non appena vengono resi disponibili dal servizio. A differenza dell'API sintesi vocale usata dall'SDK per la sintesi vocale, l'API Long audio può creare audio sintetizzato per più di 10 minuti, rendendola ideale per gli editori e le piattaforme di contenuto audio.

Vantaggi aggiuntivi dell'API Long audio:

* Il riconoscimento vocale sintetizzato restituito dal servizio usa le migliori voci neurali.
* Non è necessario distribuire un endpoint vocale perché sintetizza le voci in modalità batch None in tempo reale.

> [!NOTE]
> L'API Long audio supporta ora sia le [voci neurali pubbliche](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) che le [voci neurali personalizzate](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices).

## <a name="workflow"></a>Flusso di lavoro

In genere, quando si usa l'API Long audio, si invierà un file di testo o file da sintetizzare, eseguire il polling dello stato, quindi, se lo stato ha esito positivo, è possibile scaricare l'output audio.

Questo diagramma fornisce una panoramica di alto livello del flusso di lavoro.

![Diagramma del flusso di lavoro API Long audio](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Preparare il contenuto per la sintesi

Quando si prepara il file di testo, verificare che:

* Testo normale (con estensione txt) o testo SSML (. txt)
* Codificata come [UTF-8 con BOM (Byte Order Mark)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* È un singolo file, non un file zip
* Contiene più di 400 caratteri per testo normale o 400 [caratteri fatturabili](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) per il testo SSML e meno di 10.000 paragrafi
  * Per testo normale, ogni paragrafo viene **separato premendo l'** esempio di [input di testo normale](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * Per il testo SSML, ogni elemento SSML è considerato un paragrafo. Le parti SSML devono essere separate da paragrafi diversi, [ad esempio visualizzare input di testo SSML](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> Per il cinese (terraferma), il cinese (Hong Kong SAR), il cinese (Taiwan), il giapponese e il coreano, una parola viene conteggiata come due caratteri. 

## <a name="python-example"></a>Esempio in Python

Questa sezione contiene esempi Python che mostrano l'uso di base dell'API Long audio. Creare un nuovo progetto Python tramite l'editor o l'IDE preferito. Copiare quindi questo frammento di codice in un file denominato `voice_synthesis_client.py` .

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

Queste librerie vengono usate per analizzare gli argomenti, costruire la richiesta HTTP e chiamare l'API REST audio di sintesi vocale.

### <a name="get-a-list-of-supported-voices"></a>Ottenere un elenco di voci supportate

Questo codice consente di ottenere un elenco completo delle voci per un'area o un endpoint specifico che è possibile usare. Aggiungere il codice a `voice_synthesis_client.py` :

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

Eseguire lo script usando il comando `python voice_synthesis_client.py --voices -key <your_key> -region <region>` e sostituire i valori seguenti:

* Sostituire `<your_key>` con la chiave di sottoscrizione per il Servizio di riconoscimento vocale. Queste informazioni sono disponibili nella scheda **Panoramica** per la risorsa nel [portale di Azure](https://aka.ms/azureportal).
* Sostituire `<region>` con l'area in cui è stata creata la risorsa vocale, ad esempio `eastus` o `westus` . Queste informazioni sono disponibili nella scheda **Panoramica** per la risorsa nel [portale di Azure](https://aka.ms/azureportal).

Verrà visualizzato un output simile al seguente:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

Se il parametro **PublicVoice** è **true**, la voce è public Neural Voice. In caso contrario, si tratta di una voce neurale personalizzata.

### <a name="convert-text-to-speech"></a>Converti testo in sintesi vocale

Preparare un file di testo di input, in formato testo normale o SSML, quindi aggiungere il codice seguente a `voice_synthesis_client.py` :

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

Eseguire lo script usando il comando `python voice_synthesis_client.py --submit -key <your_key> -region <region> -file <input> -locale <locale> -voiceId <voice_guid>` e sostituire i valori seguenti:

* Sostituire `<your_key>` con la chiave di sottoscrizione per il Servizio di riconoscimento vocale. Queste informazioni sono disponibili nella scheda **Panoramica** per la risorsa nel [portale di Azure](https://aka.ms/azureportal).
* Sostituire `<region>` con l'area in cui è stata creata la risorsa vocale, ad esempio `eastus` o `westus` . Queste informazioni sono disponibili nella scheda **Panoramica** per la risorsa nel [portale di Azure](https://aka.ms/azureportal).
* Sostituire `<input>` con il percorso del file di testo preparato per la sintesi vocale.
* Sostituire `<locale>` con le impostazioni locali di output desiderate. Per ulteriori informazioni, vedere [supporto](language-support.md#neural-voices)per le lingue.
* Sostituire `<voice_guid>` con la voce di output desiderata. Usare una delle voci restituite dalla chiamata precedente all' `/voicesynthesis/voices` endpoint.

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

> [!NOTE]
> Se si dispone di più di 1 file di input, sarà necessario inviare più richieste. È necessario tenere presenti alcune limitazioni. 
> * Il client può inviare fino a **5** richieste al server al secondo per ogni account della sottoscrizione di Azure. Se supera la limitazione, il client otterrà un codice di errore 429 (troppe richieste). Ridurre il numero di richieste al secondo
> * Il server può eseguire e accodare fino a **120** richieste per ogni account della sottoscrizione di Azure. Se supera la limitazione, il server restituirà un codice di errore 429 (troppe richieste). Attendere ed evitare di inviare una nuova richiesta fino al completamento di alcune richieste

### <a name="remove-previous-requests"></a>Rimuovi richieste precedenti

Il servizio manterrà fino a **20.000** richieste per ogni account della sottoscrizione di Azure. Se la quantità di richieste supera questa limitazione, rimuovere le richieste precedenti prima di renderne nuove. Se non si rimuovono le richieste esistenti, si riceverà una notifica di errore.

Aggiungere il codice seguente a `voice_synthesis_client.py`:

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

Eseguire `python voice_synthesis_client.py --syntheses -key <your_key> -region <region>` per ottenere un elenco di richieste di sintesi effettuate. Verrà visualizzato un output simile al seguente:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Per eliminare una richiesta, eseguire `python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>` e sostituire `<synthesis_id>` con un valore ID richiesta restituito dalla richiesta precedente.

> [!NOTE]
> Le richieste con stato ' running '/' Waiting ' non possono essere rimosse o eliminate.

Il completamento `voice_synthesis_client.py` è disponibile su [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="http-status-codes"></a>Codici di stato HTTP

La tabella seguente illustra in dettaglio i messaggi e i codici di risposta HTTP dell'API REST.

| API | Codice di stato HTTP | Descrizione | Soluzione |
|-----|------------------|-------------|----------|
| Crea | 400 | La sintesi vocale non è abilitata in questa area. | Modificare la chiave di sottoscrizione vocale con un'area supportata. |
|        | 400 | Per questa area è valida solo la sottoscrizione vocale **standard** . | Modificare la chiave di sottoscrizione vocale nel piano tariffario "standard". |
|        | 400 | Superare il limite di richieste 20.000 per l'account Azure. Rimuovere alcune richieste prima di inviarne di nuove. | Il server manterrà fino a 20.000 richieste per ogni account Azure. Eliminare alcune richieste prima di inviarne di nuove. |
|        | 400 | Non è possibile usare questo modello nella sintesi vocale: {modelID}. | Verificare che lo stato di {modelID} sia corretto. |
|        | 400 | L'area per la richiesta non corrisponde all'area del modello: {modelID}. | Verificare che l'area di {modelID} corrisponda all'area della richiesta. |
|        | 400 | La sintesi vocale supporta solo il file di testo nella codifica UTF-8 con il marcatore dell'ordine dei byte. | Verificare che i file di input siano nella codifica UTF-8 con il marcatore dell'ordine dei byte. |
|        | 400 | Nella richiesta di sintesi vocale sono consentiti solo input SSML validi. | Verificare che le espressioni SSML di input siano corrette. |
|        | 400 | Il nome vocale {voicename} non è stato trovato nel file di input. | Il nome della voce SSML di input non è allineato con l'ID modello. |
|        | 400 | Il numero di paragrafi nel file di input deve essere minore di 10.000. | Verificare che il numero di paragrafi nel file sia inferiore a 10.000. |
|        | 400 | Il file di input deve contenere più di 400 caratteri. | Verificare che il file di input superi 400 caratteri. |
|        | 404 | Il modello dichiarato nella definizione di sintesi vocale non è stato trovato: {modelID}. | Assicurarsi che {modelID} sia corretto. |
|        | 429 | Superare il limite di sintesi vocale attiva. Attendere il completamento di alcune richieste. | Il server può eseguire e accodare fino a 120 richieste per ogni account Azure. Attendere ed evitare di inviare nuove richieste finché alcune richieste non vengono completate. |
| Tutti       | 429 | Troppe richieste. | Il client può inviare fino a 5 richieste al server al secondo per ogni account Azure. Ridurre il numero di richieste al secondo. |
| Elimina    | 400 | L'attività di sintesi vocale è ancora in uso. | È possibile eliminare solo le richieste **completate** o **non riuscite**. |
| GetByID   | 404 | Impossibile trovare l'entità specificata. | Verificare che l'ID di sintesi sia corretto. |

## <a name="regions-and-endpoints"></a>Aree ed endpoint

L'API Long audio è disponibile in più aree con endpoint univoci.

| Region | Endpoint |
|--------|----------|
| Australia orientale | `https://australiaeast.customvoice.api.speech.microsoft.com` |
| Canada centrale | `https://canadacentral.customvoice.api.speech.microsoft.com` |
| Stati Uniti orientali | `https://eastus.customvoice.api.speech.microsoft.com` |
| India centrale | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Stati Uniti centro-meridionali | `https://southcentralus.customvoice.api.speech.microsoft.com` |
| Asia sud-orientale | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Regno Unito meridionale | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europa occidentale | `https://westeurope.customvoice.api.speech.microsoft.com` |
| Stati Uniti occidentali 2 | `https://westus2.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Formati di output audio

Sono supportati formati di output audio flessibili. È possibile generare output audio per paragrafo o concatenare gli output audio in un singolo output impostando il parametro ' concatenateResult '. I formati di output audio seguenti sono supportati da Long audio API:

> [!NOTE]
> Il formato audio predefinito è riff-16kHz-16 bit-mono-PCM.

* riff-8kHz-16 bit-mono-PCM
* riff-16kHz-16 bit-mono-PCM
* riff-24kHz-16 bit-mono-PCM
* riff-kHz-16 bit-mono-PCM
* audio-16kHz-32kbitrate-mono-MP3
* audio-16kHz-64kbitrate-mono-MP3
* audio-16kHz-128kbitrate-mono-MP3
* audio-24kHz-48kbitrate-mono-MP3
* audio-24kHz-96kbitrate-mono-MP3
* audio-24kHz-160kbitrate-mono-MP3

## <a name="sample-code"></a>Codice di esempio
Il codice di esempio per l'API Long audio è disponibile su GitHub.

* [Codice di esempio: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Codice di esempio: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Codice di esempio: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
