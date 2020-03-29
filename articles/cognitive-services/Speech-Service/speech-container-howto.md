---
title: Installare i contenitori di riconoscimento vocale - Servizio di riconoscimento vocaleInstall Speech containers - Speech service
titleSuffix: Azure Cognitive Services
description: Installare ed eseguire contenitori vocali. Riconoscimento vocale trascrive in tempo reale flussi audio in testo da usare o visualizzare in applicazioni, dispositivi o strumenti. Sintesi vocale converte il testo di input in una voce sintetizzata simile a quella di un essere umano.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 2beee81bc365d00e59a62cacabacc5f5d6b62a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474782"
---
# <a name="install-and-run-speech-service-containers-preview"></a>Installare ed eseguire contenitori del servizio di riconoscimento vocale (anteprima)Install and run Speech service containers (Preview)

I contenitori consentono di eseguire alcune API del servizio di riconoscimento vocale nel proprio ambiente. I contenitori sono ideali per requisiti specifici di sicurezza e governance dei dati. In questo articolo imparerai a scaricare, installare ed eseguire un contenitore Di riconoscimento vocale.

I contenitori vocali consentono ai clienti di creare un'architettura di applicazione vocale ottimizzata sia per le funzionalità cloud affidabili che per la localizzazione perimetrale. Sono disponibili quattro diversi contenitori. I due contenitori standard sono **sintesi vocale** e **sintesi vocale**. I due contenitori personalizzati sono **sintesi vocale personalizzata** e **Sintesi vocale personalizzata**.

> [!IMPORTANT]
> Tutti i contenitori vocali sono attualmente offerti come parte di [un'anteprima pubblica "Gated".](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio) Verrà eseguito un annuncio quando i contenitori vocali avanzano alla disponibilità generale (GA).

| Funzione | Funzionalità | Ultima versione |
|--|--|--|
| Riconoscimento vocale | Trascrive registrazioni audio continue in tempo reale o in batch in testo con risultati intermedi. | 2.1.1 |
| Sintesi vocale personalizzata | Utilizzando un modello personalizzato dal [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech), trascrive registrazioni audio continue in tempo reale o batch in testo con risultati intermedi. | 2.1.1 |
| Sintesi vocale | Converte il testo in linguaggio naturale con input di testo normale o SSML (Speech Synthesis Markup Language). | 1.3.0 |
| Sintesi vocale personalizzata | Utilizzando un modello personalizzato dal [portale di voce personalizzata](https://aka.ms/custom-voice-portal), converte il testo in un discorso dal suono naturale con input di testo normale o Speech Synthesis Markup Language (SSML). | 1.3.0 |

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di usare i contenitori riconoscimento vocale, è necessario prerequisiti seguenti:The following prerequisites before using Speech containers:

| Obbligatoria | Scopo |
|--|--|
| Motore Docker | È necessario il motore Docker installato in un [computer host](#the-host-computer). Docker offre pacchetti che configurano l'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica sui concetti fondamentali relativi a Docker e al contenitore, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica su Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> **In Windows** Docker deve essere configurato anche per supportare i contenitori Linux.<br><br> |
| Familiarità con Docker | È opportuno avere una conoscenza di base dei concetti relativi a Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base. |
| Risorsa di riconoscimento vocale | Per usare questi contenitori, è necessario avere:<br><br>Una risorsa _di riconoscimento vocale_ di Azure per ottenere la chiave API associata e l'URI dell'endpoint. Entrambi i valori sono disponibili nelle pagine Panoramica vocale e Chiavi del portale di Azure.Both values are available on the Azure portal's **Speech** Overview and Keys pages. Entrambi sono necessari per avviare il contenitore.<br><br>**API_KEY :Una**delle due chiavi di risorsa disponibili nella pagina **Chiavi**<br><br>**ENDPOINT_URI :** l'endpoint come indicato nella pagina **Panoramica** |

## <a name="request-access-to-the-container-registry"></a>Richiedere l'accesso al registro contenitori

Compilare e inviare il modulo richiesta di contenitori vocali di [Servizi cognitivi](https://aka.ms/speechcontainerspreview/) per richiedere l'accesso al contenitore. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Supporto per Advanced Vector Extension

L'**host** è il computer che esegue il contenitore Docker. L'host *deve supportare* [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). È possibile verificare il supporto AVX2 su host Linux con il seguente comando:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> Il computer host è *necessario* per supportare AVX2. Il contenitore *non funzionerà* correttamente senza il supporto DI AVX2.

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

Nella tabella seguente viene descritta l'allocazione minima e consigliata delle risorse per ogni contenitore di riconoscimento vocale.

# <a name="speech-to-text"></a>[Sintesi vocale](#tab/stt)

| Contenitore | Minima | Consigliato |
|-----------|---------|-------------|
| Riconoscimento vocale | 2 core, 2-GB di memoria | 4 core, 4 GB di memoria |

# <a name="custom-speech-to-text"></a>[Sintesi vocale personalizzata](#tab/cstt)

| Contenitore | Minima | Consigliato |
|-----------|---------|-------------|
| Sintesi vocale personalizzata | 2 core, 2-GB di memoria | 4 core, 4 GB di memoria |

# <a name="text-to-speech"></a>[Sintesi vocale](#tab/tts)

| Contenitore | Minima | Consigliato |
|-----------|---------|-------------|
| Sintesi vocale | 1 core, 2 GB di memoria | 2 core, 3 GB di memoria |

# <a name="custom-text-to-speech"></a>[Sintesi vocale personalizzata](#tab/ctts)

| Contenitore | Minima | Consigliato |
|-----------|---------|-------------|
| Sintesi vocale personalizzata | 1 core, 2 GB di memoria | 2 core, 3 GB di memoria |

***

* Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.

> [!NOTE]
> Il valore minimo e consigliato sono basati sui limiti di Docker, *non* sulle risorse della macchina host. Ad esempio, i contenitori di sintesi vocale eseguono il mapping delle parti di un modello linguistico di grandi dimensioni ed è *consigliabile* che l'intero file si adatti alla memoria, ovvero altri 4-6 GB. Inoltre, la prima esecuzione di entrambi i contenitori può richiedere più tempo, poiché i modelli vengono sottoposti a paging in memoria.

## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine del contenitore con `docker pull`

Le immagini del contenitore per il riconoscimento vocale sono disponibili nel Registro di sistema del contenitore seguente.

# <a name="speech-to-text"></a>[Sintesi vocale](#tab/stt)

| Contenitore | Archivio |
|-----------|------------|
| Riconoscimento vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Sintesi vocale personalizzata](#tab/cstt)

| Contenitore | Archivio |
|-----------|------------|
| Sintesi vocale personalizzata | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[Sintesi vocale](#tab/tts)

| Contenitore | Archivio |
|-----------|------------|
| Sintesi vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[Sintesi vocale personalizzata](#tab/ctts)

| Contenitore | Archivio |
|-----------|------------|
| Sintesi vocale personalizzata | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Pull Docker per i contenitori Speech

# <a name="speech-to-text"></a>[Sintesi vocale](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Pull di Docker per il contenitore di sintesi vocale per il testoDocker pull for the Speech-to-text container

Usare il comando docker pull per scaricare un'immagine del contenitore dal Registro di sistema di Container Preview.Use the [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) command to download a container image from Container Preview registry.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> Il `latest` tag estrae le `en-US` impostazioni locali. Per altre impostazioni locali, vedere [Impostazioni locali di sintesi vocale](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Impostazioni locali di sintesi vocale

Tutti i tag, ad `latest` eccezione di quelli riportati nel formato seguente, fanno distinzione tra maiuscole e minuscole:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

Il tag seguente è un esempio del formato:

```
2.1.1-amd64-en-us-preview
```

Per tutte le impostazioni locali supportate del contenitore **di sintesi** vocale, vedere Tag di [immagini di sintesi vocale](../containers/container-image-tags.md#speech-to-text).

# <a name="custom-speech-to-text"></a>[Sintesi vocale personalizzata](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Pull docker per il contenitore di sintesi vocale personalizzato

Usare il comando docker pull per scaricare un'immagine del contenitore dal Registro di sistema di Container Preview.Use the [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) command to download a container image from Container Preview registry.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> I `locale` `voice` contenitori e per Speech personalizzati sono determinati dal modello personalizzato ingerito dal contenitore.

# <a name="text-to-speech"></a>[Sintesi vocale](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Pull docker per il contenitore di sintesi vocale

Usare il comando docker pull per scaricare un'immagine del contenitore dal Registro di sistema di Container Preview.Use the [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) command to download a container image from Container Preview registry.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> Il `latest` tag estrae le `en-US` impostazioni locali e `jessarus` la voce. Per altre impostazioni locali, vedere Impostazioni locali di [sintesi vocale](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Impostazioni locali di sintesi vocale

Tutti i tag, ad `latest` eccezione di quelli riportati nel formato seguente, fanno distinzione tra maiuscole e minuscole:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

Il tag seguente è un esempio del formato:

```
1.3.0-amd64-en-us-jessarus-preview
```

Per tutte le impostazioni locali supportate e le voci corrispondenti del contenitore di **sintesi vocale,** vedere Tag delle immagini di sintesi [vocale](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> Quando si costruisce un HTTP POST di *sintesi vocale standard,* il messaggio [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md) richiede un `voice` elemento con un `name` attributo. Il valore è le impostazioni locali e la voce del contenitore corrispondenti, noto anche come ["nome breve".](language-support.md#standard-voices) Ad esempio, `latest` il tag avrà `en-US-JessaRUS`un nome vocale di .

# <a name="custom-text-to-speech"></a>[Sintesi vocale personalizzata](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Pull docker per il contenitore di sintesi vocale personalizzatoDocker pull for the Custom Text-to-speech container

Usare il comando docker pull per scaricare un'immagine del contenitore dal Registro di sistema di Container Preview.Use the [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) command to download a container image from Container Preview registry.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> I `locale` `voice` contenitori e per Speech personalizzati sono determinati dal modello personalizzato ingerito dal contenitore.

***

## <a name="how-to-use-the-container"></a>Come usare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usare il contenitore.

1. [Eseguire il contenitore](#run-the-container-with-docker-run), con le impostazioni di fatturazione necessarie. Sono disponibili altri [esempi](speech-container-configuration.md#example-docker-run-commands) del comando `docker run`.
1. [Eseguire una query sull'endpoint di stima del contenitore.](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con `docker run`

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire il contenitore. Fare riferimento alla raccolta dei [parametri](#gathering-required-parameters) `{Endpoint_URI}` obbligatori `{API_Key}` per informazioni dettagliate su come ottenere i valori e . Sono [examples](speech-container-configuration.md#example-docker-run-commands) disponibili `docker run` anche altri esempi del comando.

# <a name="speech-to-text"></a>[Sintesi vocale](#tab/stt)

Per eseguire il contenitore *di sintesi* vocale, eseguire il comando seguente. `docker run`

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue un contenitore *di sintesi* vocale dall'immagine del contenitore.
* Alloca 4 core CPU e 4 gigabyte (GB) di memoria.
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.

# <a name="custom-speech-to-text"></a>[Sintesi vocale personalizzata](#tab/cstt)

Il contenitore *di sintesi vocale personalizzato* si basa su un modello di riconoscimento vocale personalizzato. È necessario eseguire [il training](how-to-custom-speech-train-model.md) del modello personalizzato utilizzando il portale [di riconoscimento vocale personalizzato.](https://speech.microsoft.com/customspeech)

> [!IMPORTANT]
> Il modello di riconoscimento vocale personalizzato deve essere sottoposto a training da una delle seguenti versioni del modello:
> * **20181201 (v3.3 Unified)**
> * **20190520 (v4.14 Unificato)**
> * **20190701 (v4.17 Unified)**<br>
> ![Modello di contenitore del training vocale personalizzatoCustom Speech train container model](media/custom-speech/custom-speech-train-model-container-scoped.png)

**L'ID modello** vocale personalizzato è necessario per eseguire il contenitore. È disponibile nella pagina **Formazione** del portale vocale personalizzato. Dal portale di riconoscimento vocale personalizzato passare alla pagina **Formazione** e selezionare il modello.
<br>

![Pagina di formazione vocale personalizzata](media/custom-speech/custom-speech-model-training.png)

Ottenere **l'ID modello** da utilizzare `ModelId` come `docker run` argomento del parametro del comando.
<br>

![Dettagli del modello vocale personalizzato](media/custom-speech/custom-speech-model-details.png)

La tabella seguente `docker run` rappresenta i vari parametri e le relative descrizioni:

| Parametro | Descrizione |
|---------|---------|
| `{VOLUME_MOUNT}` | Il volume del computer host [mount](https://docs.docker.com/storage/volumes/), che viene utilizzato da docker per rendere persistente il modello personalizzato. Ad esempio, *C:, CustomSpeech* in cui si trova *l'unità C* nel computer host. |
| `{MODEL_ID}` | ID **modello** di riconoscimento vocale personalizzato dalla pagina **Formazione** del portale di riconoscimento vocale personalizzato. |
| `{ENDPOINT_URI}` | L'endpoint è necessario per la misurazione e la fatturazione. Per ulteriori informazioni, consultate [Raccolta dei parametri obbligatori.](#gathering-required-parameters) |
| `{API_KEY}` | La chiave API è obbligatoria. Per ulteriori informazioni, consultate [Raccolta dei parametri obbligatori.](#gathering-required-parameters) |

Per eseguire il contenitore *di sintesi* `docker run` vocale personalizzato, eseguire il comando seguente:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue un contenitore *di sintesi vocale personalizzato* dall'immagine del contenitore.
* Alloca 4 core CPU e 4 gigabyte (GB) di memoria.
* Carica il modello *personalizzato Speech-to-Text* dal montaggio dell'input del volume, ad esempio *C:.*
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore.
* Scarica il modello `ModelId` dato il (se non trovato sul montaggio del volume).
* Se il modello personalizzato è `ModelId` stato scaricato in precedenza, viene ignorato.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.

# <a name="text-to-speech"></a>[Sintesi vocale](#tab/tts)

Per eseguire il contenitore *sintesi vocale,* eseguire il comando seguente. `docker run`

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue un contenitore *di sintesi vocale* dall'immagine del contenitore.
* Alloca 2 core CPU e un gigabyte (GB) di memoria.
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.

# <a name="custom-text-to-speech"></a>[Sintesi vocale personalizzata](#tab/ctts)

Il contenitore *di sintesi vocale personalizzato* si basa su un modello vocale personalizzato. Il modello personalizzato deve essere stato [sottoposto](how-to-custom-voice-create-voice.md) a training utilizzando il [portale vocale personalizzato.](https://aka.ms/custom-voice-portal) **L'ID modello** vocale personalizzato è necessario per eseguire il contenitore. È disponibile nella pagina **Formazione** del portale vocale personalizzato. Dal portale vocale personalizzato passare alla pagina **Formazione** e selezionare il modello.
<br>

![Pagina di formazione vocale personalizzata](media/custom-voice/custom-voice-model-training.png)

Ottenere **l'ID modello** da utilizzare `ModelId` come argomento del parametro del comando docker run.
<br>

![Dettagli del modello vocale personalizzato](media/custom-voice/custom-voice-model-details.png)

La tabella seguente `docker run` rappresenta i vari parametri e le relative descrizioni:

| Parametro | Descrizione |
|---------|---------|
| `{VOLUME_MOUNT}` | Il volume del computer host [mount](https://docs.docker.com/storage/volumes/), che viene utilizzato da docker per rendere persistente il modello personalizzato. Ad esempio, *C:, CustomSpeech* in cui si trova *l'unità C* nel computer host. |
| `{MODEL_ID}` | ID **modello** vocale personalizzato dalla pagina **Formazione** del portale vocale personalizzato. |
| `{ENDPOINT_URI}` | L'endpoint è necessario per la misurazione e la fatturazione. Per ulteriori informazioni, consultate [Raccolta dei parametri obbligatori.](#gathering-required-parameters) |
| `{API_KEY}` | La chiave API è obbligatoria. Per ulteriori informazioni, consultate [Raccolta dei parametri obbligatori.](#gathering-required-parameters) |

Per eseguire il contenitore *di sintesi vocale personalizzato,* eseguire il comando seguente: `docker run`

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue un contenitore di *sintesi vocale personalizzato* dall'immagine del contenitore.
* Alloca 2 core CPU e un gigabyte (GB) di memoria.
* Carica il modello *di sintesi vocale personalizzato* dal montaggio dell'input del volume, ad esempio *C:.*
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore.
* Scarica il modello `ModelId` dato il (se non trovato sul montaggio del volume).
* Se il modello personalizzato è `ModelId` stato scaricato in precedenza, viene ignorato.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.

***

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Eseguire query sull'endpoint di stima del contenitore

| Contenitori | SDK Host URL | Protocollo |
|--|--|--|
| Sintesi vocale e sintesi vocale personalizzata | `ws://localhost:5000` | WS |
| Sintesi vocale e sintesi vocale personalizzata | `http://localhost:5000` | HTTP |

Per ulteriori informazioni sull'utilizzo dei protocolli WSS e HTTPS, vedere [Protezione dei contenitori](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

### <a name="text-to-speech-or-custom-text-to-speech"></a>Sintesi vocale o sintesi vocale personalizzata

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Eseguire più contenitori nello stesso host

Se si intende eseguire più contenitori con porte esposte, assicurarsi di eseguire ogni contenitore con una porta esposta diversa. Eseguire ad esempio il primo contenitore sulla porta 5000 e il secondo sulla porta 5001.

Questo contenitore e un contenitore di Servizi cognitivi diverso sono in esecuzione contemporaneamente sull'HOST. Sono inoltre in esecuzione più contenitori dello stesso contenitore di Servizi cognitivi.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrestare il contenitore

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

Quando si avvia o si esegue il contenitore, potrebbero verificarsi problemi. Utilizzare un [montaggio](speech-container-configuration.md#mount-settings) di output e abilitare la registrazione. In questo modo si consentirà al contenitore di generare file di log utili per la risoluzione dei problemi.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fatturazione

I contenitori del riconoscimento vocale inviano informazioni di fatturazione ad Azure usando una risorsa *riconoscimento vocale* nell'account Azure.The Speech containers send billing information to Azure, using a Speech resource on your Azure account.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](speech-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Riepilogo

In questo articolo sono stati illustrati concetti e flusso di lavoro per il download, l'installazione e l'esecuzione di contenitori di riconoscimento vocale. In sintesi:

* Il riconoscimento vocale fornisce quattro contenitori Linux per Docker, incapsulando varie funzionalità:Speech provides four Linux containers for Docker, encapsulating various capabilities:
  * *Sintesi vocale*
  * *Sintesi vocale personalizzata*
  * *Sintesi vocale*
  * *Sintesi vocale personalizzata*
* Container images are downloaded from the container registry in Azure.
* Le immagini dei contenitori vengono eseguite in Docker.
* Se si utilizza l'API REST (solo sintesi vocale) o l'SDK (sintesi vocale o sintesi vocale) si specifica l'URI host del contenitore. 
* È necessario fornire le informazioni di fatturazione quando si crea un'istanza di un contenitore.

> [!IMPORTANT]
>  I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di Servizi cognitivi non inviano i dati dei clienti (ad esempio, l'immagine o il testo analizzato) a Microsoft.

## <a name="next-steps"></a>Passaggi successivi

* Esaminare [i contenitori](speech-container-configuration.md) di configurazione per le impostazioni di configurazioneReview configure containers for configuration settings
* Scopri come usare i contenitori del [servizio di riconoscimento vocale con Kubernetes e Helm](speech-container-howto-on-premises.md)
* Usare più [contenitori di Servizi cognitiviUse more Cognitive Services containers](../cognitive-services-container-support.md)
