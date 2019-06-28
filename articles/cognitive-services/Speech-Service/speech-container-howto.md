---
title: Installare i contenitori di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Installare ed eseguire i contenitori di riconoscimento vocale. Riconoscimento vocale trascrive in tempo reale flussi audio in testo da usare o visualizzare in applicazioni, dispositivi o strumenti. Sintesi vocale converte il testo di input in una voce sintetizzata simile a quella di un essere umano.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 6df40909e2072ac2126344ba5b696c9e520e9955
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434123"
---
# <a name="install-and-run-speech-service-containers"></a>Installare ed eseguire i contenitori dei servizi di riconoscimento vocale

I contenitori di riconoscimento vocale consentono ai clienti di compilare un'architettura delle applicazioni vocale che è ottimizzata per sfruttare i vantaggi di funzionalità del cloud affidabile e la località di edge. 

I contenitori di riconoscimento due vocale siano **per il riconoscimento vocale** e **sintesi vocale**. 

|Funzione|Funzionalità|più recente|
|-|-|--|
|Riconoscimento vocale| <li>Trascrive continua in tempo reale vocale o un batch registrazioni audio in testo con i risultati intermedi.|1.1.2|
|Sintesi vocale| <li>Converte il testo scritto in un audio che suona naturale. con input di testo normale o linguaggio di Markup sintesi della voce (SSML). |1.1.0|

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di usare i contenitori di riconoscimento vocale, è necessario soddisfare i prerequisiti seguenti:

|Obbligatorio|Scopo|
|--|--|
|Motore Docker| È necessario il motore Docker installato in un [computer host](#the-host-computer). Docker offre pacchetti per la configurazione dell'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> **In Windows** Docker deve essere configurato anche per supportare i contenitori Linux.<br><br>|
|Familiarità con Docker | È opportuno avere una conoscenza di base dei concetti relativi a Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.| 
|Risorse di riconoscimento vocale |Per usare questi contenitori, è necessario avere:<br><br>Oggetto _vocale_ risorse di Azure per ottenere la chiave di fatturazione associata e l'URI dell'endpoint di fatturazione. Entrambi i valori sono disponibili nel portale di Azure **vocale** pagine di panoramica e le chiavi e sono necessari per avviare il contenitore.<br><br>**{BILLING_KEY}** : chiave della risorsa<br><br>**{BILLING_ENDPOINT_URI}** : un esempio di URI dell'endpoint è: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="request-access-to-the-container-registry"></a>Richiedere l'accesso al registro contenitori

È necessario innanzitutto completare e inviare il [modulo di richiesta di contenitori vocale servizi cognitivi](https://aka.ms/speechcontainerspreview/) per richiedere l'accesso al contenitore. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Supporto delle estensioni vettori avanzato

L'**host** è il computer che esegue il contenitore Docker. L'host deve supportare [estensioni Advanced Vector Extension](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). È possibile controllare questo supporto negli host di Linux con il comando seguente: 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

La tabella seguente descrive i minimi e consigliati di core CPU e memoria da allocare per ogni contenitore di riconoscimento vocale.

| Contenitore | Minima | Consigliato |
|-----------|---------|-------------|
|cognitive-services-speech-to-text | 2 core<br>2 GB di memoria  | 4 core<br>4 GB di memoria  |
|cognitive-services-text-to-speech | 1 core, 0,5 GB di memoria| 2 core, 1 GB di memoria |

* Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.

**Nota**; Di fuori dei limiti di Docker, si basano i minimi e consigliati *non* risorse del computer host. Ad esempio, parti mappa di memoria dei contenitori per il riconoscimento vocale di un modello di lingua di grandi dimensioni che è _consigliato_ che l'intero file rientra nella memoria, ossia 4 a 6 GB aggiuntivo. Inoltre, la prima esecuzione dei contenitori potrebbe richiedere più tempo, poiché i modelli sono il paging nella memoria.

## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine del contenitore con `docker pull`

Sono disponibili le immagini del contenitore per il riconoscimento vocale.

| Contenitore | Repository |
|-----------|------------|
| cognitive-services-speech-to-text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| cognitive-services-text-to-speech | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>Impostazioni locali della lingua sono nel tag contenitore

Il `latest` tag pull il `en-us` delle impostazioni locali e `jessarus` vocali.

#### <a name="speech-to-text-locales"></a>Riconoscimento vocale alle impostazioni internazionali di testo

Tutti i tag, ad eccezione di `latest` sono nel formato seguente, dove il `<culture>` indica il contenitore delle impostazioni locali:

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

Il tag seguente è riportato un esempio del formato:

```
1.1.2-amd64-en-us-preview
```

La tabella seguente elenca le impostazioni locali supportate per **vocale-** in 1.1.2 la versione del contenitore:

|Impostazioni locali della lingua|`Tags`|
|--|--|
|Cinese|`zh-cn`|
|Inglese |`en-us`<br>`en-gb`<br>`en-au`<br>`en-in`|
|Francese |`fr-ca`<br>`fr-fr`|
|Tedesco|`de-de`|
|Italiano|`it-it`|
|Giapponese|`ja-jp`|
|Coreano|`ko-kr`|
|Portoghese|`pt-br`|
|Spagnolo|`es-es`<br>`es-mx`|

#### <a name="text-to-speech-locales"></a>Impostazioni locali di sintesi vocale

Tutti i tag, ad eccezione di `latest` sono nel formato seguente, in cui la `<culture>` indica le impostazioni locali e il `<voice>` indica la voce del contenitore:

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

Il tag seguente è riportato un esempio del formato:

```
1.1.0-amd64-en-us-jessarus-preview
```

La tabella seguente elenca le impostazioni locali supportate per **sintesi vocale** della versione 1.1.0 versione del contenitore:

|Impostazioni locali della lingua|`Tags`|Voices supportati|
|--|--|--|
|Cinese|`zh-cn`|huihuirus<br>kangkang-apollo<br>yaoyao-apollo|
|Inglese |`en-au`|ha dichiarato Catherine<br>hayleyrus|
|Inglese |`en-gb`|george-apollo<br>hazelrus<br>susan-apollo|
|Inglese |`en-in`|heera-apollo<br>priyarus<br>ravi-apollo<br>|
|Inglese |`en-us`|jessarus<br>benjaminrus<br>jessa24krus<br>zirarus<br>guy24krus|
|Francese|`fr-ca`|Caroline<br>harmonierus|
|Francese|`fr-fr`|hortenserus<br>julie-apollo<br>paul-apollo|
|Tedesco|`de-de`|hedda<br>heddarus<br>stefan-apollo|
|Italiano|`it-it`|cosimo-apollo<br>luciarus|
|Giapponese|`ja-jp`|ayumi-apollo<br>harukarus<br>ichiro-apollo|
|Coreano|`ko-kr`|heamirus|
|Portoghese|`pt-br`|daniel-apollo<br>heloisarus|
|Spagnolo|`es-es`|elenarus<br>laura-apollo<br>pablo-apollo<br>|
|Spagnolo|`es-mx`|hildarus<br>raul-apollo|

### <a name="docker-pull-for-the-speech-containers"></a>Pull docker per i contenitori di riconoscimento vocale

#### <a name="speech-to-text"></a>Riconoscimento vocale

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

#### <a name="text-to-speech"></a>Sintesi vocale

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

## <a name="how-to-use-the-container"></a>Come usare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usare il contenitore.

1. [Eseguire il contenitore](#run-the-container-with-docker-run), con le impostazioni di fatturazione necessarie, ma non usate. Sono disponibili altri [esempi](speech-container-configuration.md#example-docker-run-commands) del comando `docker run`.
1. [Eseguire le query sull'endpoint di stima del contenitore](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con `docker run`

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire uno qualsiasi dei tre contenitori. Il comando usa i parametri seguenti:

**Durante l'anteprima**, le impostazioni di fatturazione devono essere valide per avviare il contenitore, ma non vengono fatturate per l'utilizzo.

| Placeholder | Value |
|-------------|-------|
|{BILLING_KEY} | Questa chiave viene usata per avviare il contenitore e è disponibile nella pagina chiavi di sintesi vocale del portale di Azure.  |
|{BILLING_ENDPOINT_URI} | Il valore URI dell'endpoint di fatturazione è disponibile nella pagina di panoramica di sintesi vocale del portale di Azure.|

Sostituire i parametri con i valori personalizzati nel comando `docker run` di esempio seguente.

### <a name="text-to-speech"></a>Sintesi vocale

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

### <a name="speech-to-text"></a>Riconoscimento vocale

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Questo comando:

* Esegue un contenitore di riconoscimento vocale dall'immagine del contenitore
* Alloca 2 core CPU e 2 gigabyte (GB) di memoria
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Eseguire query sull'endpoint di stima del contenitore

|Contenitore|Endpoint|
|--|--|
|Riconoscimento vocale|ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1|
|Sintesi vocale|http://localhost:5000/speech/synthesize/cognitiveservices/v1|

### <a name="speech-to-text"></a>Riconoscimento vocale

Il contenitore fornisce query basate sul websocket endpoint API, che sono accessibili tramite il [Speech SDK](index.yml).

Per impostazione predefinita, Speech SDK Usa servizi di riconoscimento vocale in linea. Per usare il contenitore, è necessario modificare il metodo di inizializzazione. Vedere gli esempi seguenti.

#### <a name="for-c"></a>Per C#

Passare dall'uso di questa chiamata di inizializzazione del cloud di Azure:

```C#
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

a questa chiamata che usa l'endpoint del contenitore:

```C#
var config = SpeechConfig.FromEndpoint(
    new Uri("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1"),
    "YourSubscriptionKey");
```

#### <a name="for-python"></a>Per Python

Passare dall'uso di questa chiamata di inizializzazione del cloud di Azure

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

a questa chiamata che usa l'endpoint del contenitore:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

### <a name="text-to-speech"></a>Sintesi vocale

Il contenitore fornisce endpoint REST API che possono essere recuperate [Ecco](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech) e sono disponibili esempi [qui](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/).

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrestare il contenitore

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>risoluzione dei problemi

Quando si esegue il contenitore, questo usa **stdout** e **stderr** per generare informazioni utili per risolvere i problemi che si verificano durante l'avvio o l'esecuzione del contenitore.

## <a name="billing"></a>Fatturazione

L'invio di contenitori vocale fatturazione in Azure, usando un _vocale_ risorse nell'account Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](speech-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Riepilogo

In questo articolo si è appreso i concetti e flusso di lavoro per scaricare, installare ed eseguire i contenitori di riconoscimento vocale. In sintesi:

* Riconoscimento vocale fornisce due contenitori di Linux per Docker, che incapsula vocale in testo e sintesi vocale.
* Le immagini dei contenitori vengono scaricate da un registro contenitori privato in Azure.
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile utilizzare l'API REST o SDK di chiamare le operazioni nei contenitori di riconoscimento vocale, specificando l'URI del contenitore di host.
* Quando si crea un'istanza di un contenitore, è necessario specificare le informazioni di fatturazione.

> [!IMPORTANT]
>  I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di Servizi cognitivi non inviano i dati dei clienti (ad esempio, l'immagine o il testo analizzato) a Microsoft.

## <a name="next-steps"></a>Passaggi successivi

* Rivedere [Configurare i contenitori](speech-container-configuration.md) per informazioni sulle impostazioni di configurazione.
* Usare altri [contenitori di Servizi cognitivi](../cognitive-services-container-support.md)
