---
title: Installare i contenitori di sintesi vocale-servizio vocale
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
ms.openlocfilehash: 7708133fcba0d594ecd420afd8da1b2881055aa7
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241024"
---
# <a name="install-and-run-speech-service-containers"></a>Installare ed eseguire i contenitori dei servizi vocali

I contenitori di sintesi vocale consentono ai clienti di creare un'architettura di applicazione vocale ottimizzata per sfruttare le funzionalità cloud affidabili e la località perimetrale. 

I due contenitori di riconoscimento vocale sono **sintesi vocale** e sintesi **vocale**. 

|Funzione|Funzionalità|Ultimo|
|-|-|--|
|Riconoscimento vocale| <li>Trascrive registrazioni audio continue in tempo reale o batch in testo con risultati intermedi.|1.2.0|
|Sintesi vocale| <li>Converte il testo scritto in un audio che suona naturale. con input di testo normale o SSML (Speech Synthesis Markup Language). |1.2.0|

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di usare i contenitori di sintesi vocale, è necessario soddisfare i prerequisiti seguenti:

|Obbligatoria|Scopo|
|--|--|
|Motore Docker| È necessario il motore Docker installato in un [computer host](#the-host-computer). Docker offre pacchetti per la configurazione dell'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> **In Windows** Docker deve essere configurato anche per supportare i contenitori Linux.<br><br>|
|Familiarità con Docker | È opportuno avere una conoscenza di base dei concetti relativi a Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.| 
|Risorsa vocale |Per usare questi contenitori, è necessario avere:<br><br>Una risorsa _vocale_ di Azure per ottenere la chiave API e l'URI dell'endpoint associati. Entrambi i valori sono disponibili nelle pagine relative alla panoramica e alle chiavi del **discorso** del portale di Azure. Sono entrambi necessari per avviare il contenitore.<br><br>**{API_KEY}** : Una delle due chiavi di risorsa disponibili nella pagina **chiavi**<br><br>**{ENDPOINT_URI}** : Endpoint fornito nella pagina **Panoramica**|

## <a name="request-access-to-the-container-registry"></a>Richiedere l'accesso al registro contenitori

È necessario prima completare e inviare il [modulo di richiesta di contenitori di sintesi vocale di servizi cognitivi](https://aka.ms/speechcontainerspreview/) per richiedere l'accesso al contenitore. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Supporto Advanced Vector Extension

L'**host** è il computer che esegue il contenitore Docker. L'host deve supportare [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). È possibile controllare questo supporto in host Linux con il comando seguente: 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

La tabella seguente descrive i core CPU minimi e consigliati e la memoria da allocare per ogni contenitore vocale.

| Contenitore | Minima | Consigliato |
|-----------|---------|-------------|
|cognitive-Services-riconoscimento vocale | 2 Core<br>2 GB di memoria  | 4 core<br>4 GB di memoria  |
|cognitive-Services-sintesi vocale | 1 core, 0,5 GB di memoria| 2 Core, 1 GB di memoria |

* Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.

**Nota**; Il valore minimo e consigliato sono basati sui limiti di Docker, *non* sulle risorse del computer host. Ad esempio, i contenitori di sintesi vocale mappano parti di un modello di linguaggio di grandi dimensioni ed è _consigliabile_ che l'intero file si trovi in memoria, che è un 4-6 GB aggiuntivo. Inoltre, la prima esecuzione di uno dei due contenitori potrebbe richiedere più tempo, perché i modelli vengono inseriti in una pagina di memoria.

## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine del contenitore con `docker pull`

Sono disponibili le immagini del contenitore per la sintesi vocale.

| Contenitore | Repository |
|-----------|------------|
| cognitive-Services-riconoscimento vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| cognitive-Services-sintesi vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>Le impostazioni locali della lingua sono incluse nel tag del contenitore

Il `latest` Tag estrae le `en-us` impostazioni locali e `jessarus` la voce.

#### <a name="speech-to-text-locales"></a>Impostazioni locali sintesi vocale

Tutti i tag, ad `latest` eccezione di, sono nel formato seguente, `<culture>` dove indica il contenitore delle impostazioni locali:

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

Il seguente tag è un esempio del formato:

```
1.2.0-amd64-en-us-preview
```

La tabella seguente elenca le impostazioni locali supportate per la **sintesi vocale** nella versione 1.2.0 del contenitore:

|Impostazioni locali della lingua|Tag|
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

#### <a name="text-to-speech-locales"></a>Impostazioni locali sintesi vocale

Tutti i tag, ad `latest` eccezione di, sono nel formato seguente, `<culture>` dove indica `<voice>` le impostazioni locali e indica la voce del contenitore:

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

Il seguente tag è un esempio del formato:

```
1.2.0-amd64-en-us-jessarus-preview
```

La tabella seguente elenca le impostazioni locali supportate per la **sintesi vocale** nella versione 1.2.0 del contenitore:

|Impostazioni locali della lingua|Tag|Voci supportate|
|--|--|--|
|Cinese|`zh-cn`|huihuirus<br>kangkang-apollo<br>Yaoyao-Apollo|
|Inglese |`en-au`|Catherine<br>hayleyrus|
|Inglese |`en-gb`|Giorgio-Apollo<br>hazelrus<br>susan-apollo|
|Inglese |`en-in`|si-Apollo<br>priyarus<br>ravi-apollo<br>|
|Inglese |`en-us`|jessarus<br>benjaminrus<br>jessa24krus<br>zirarus<br>guy24krus|
|Francese|`fr-ca`|Caroline<br>harmonierus|
|Francese|`fr-fr`|hortenserus<br>Julie-Apollo<br>Paul-Apollo|
|Tedesco|`de-de`|Hedda<br>heddarus<br>stefan-apollo|
|Italiano|`it-it`|cosimo-apollo<br>luciarus|
|Giapponese|`ja-jp`|ayumi-apollo<br>harukarus<br>ichiro-apollo|
|Coreano|`ko-kr`|heamirus|
|Portoghese|`pt-br`|Daniel-Apollo<br>heloisarus|
|Spagnolo|`es-es`|elenarus<br>laura-apollo<br>Pablo-Apollo<br>|
|Spagnolo|`es-mx`|hildarus<br>raul-apollo|

### <a name="docker-pull-for-the-speech-containers"></a>Pull Docker per i contenitori di riconoscimento vocale

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

1. [Eseguire il contenitore](#run-the-container-with-docker-run), con le impostazioni di fatturazione necessarie. Sono disponibili altri [esempi](speech-container-configuration.md#example-docker-run-commands) del comando `docker run`.
1. [Eseguire le query sull'endpoint di stima del contenitore](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con `docker run`

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire uno qualsiasi dei tre contenitori. Il comando usa i parametri seguenti:

**Durante l'anteprima**, le impostazioni di fatturazione devono essere valide per avviare il contenitore, ma non viene addebitato l'utilizzo.

| Segnaposto | Value |
|-------------|-------|
|{API_KEY} | Questa chiave viene usata per avviare il contenitore ed è disponibile nella pagina relativa alle chiavi vocali del portale di Azure.  |
|{ENDPOINT_URI} | Il valore dell'URI dell'endpoint di fatturazione è disponibile nella pagina di panoramica del discorso del portale di Azure.|

Sostituire i parametri con i valori personalizzati nel comando `docker run` di esempio seguente.

### <a name="text-to-speech"></a>Sintesi vocale

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="speech-to-text"></a>Riconoscimento vocale

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue un contenitore vocale dall'immagine del contenitore
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

Il contenitore fornisce le API dell'endpoint di query basate su WebSocket, a cui si accede tramite l' [SDK di riconoscimento vocale](index.yml).

Per impostazione predefinita, l'SDK vocale usa i servizi di riconoscimento vocale online. Per usare il contenitore, è necessario modificare il metodo di inizializzazione. Vedere gli esempi seguenti.

#### <a name="for-c"></a>Per C#

Passare dall'uso di questa chiamata di inizializzazione del cloud di Azure:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

a questa chiamata che usa l'endpoint del contenitore:

```csharp
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

Il contenitore fornisce le API dell'endpoint REST disponibili [qui](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech) ed è possibile trovare gli esempi [qui](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/).

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrestare il contenitore

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

Quando si esegue il contenitore, questo usa **stdout** e **stderr** per generare informazioni utili per risolvere i problemi che si verificano durante l'avvio o l'esecuzione del contenitore.

## <a name="billing"></a>Fatturazione

I contenitori di riconoscimento vocale inviano informazioni di fatturazione ad Azure, usando una risorsa _vocale_ nell'account Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](speech-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Riepilogo

In questo articolo sono stati appresi concetti e flussi di lavoro per il download, l'installazione e l'esecuzione di contenitori di sintesi vocale. In sintesi:

* Il riconoscimento vocale fornisce due contenitori Linux per Docker, incapsulando sintesi vocale in testo e sintesi vocale.
* Le immagini dei contenitori vengono scaricate da un registro contenitori privato in Azure.
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile usare l'API REST o l'SDK per chiamare le operazioni nei contenitori di sintesi vocale specificando l'URI host del contenitore.
* È necessario fornire informazioni di fatturazione quando si crea un'istanza di un contenitore.

> [!IMPORTANT]
>  I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di Servizi cognitivi non inviano i dati dei clienti (ad esempio, l'immagine o il testo analizzato) a Microsoft.

## <a name="next-steps"></a>Passaggi successivi

* Rivedere [Configurare i contenitori](speech-container-configuration.md) per informazioni sulle impostazioni di configurazione.
* Usare altri [contenitori di Servizi cognitivi](../cognitive-services-container-support.md)
