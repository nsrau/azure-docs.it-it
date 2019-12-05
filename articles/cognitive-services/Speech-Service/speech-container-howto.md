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
ms.date: 12/04/2019
ms.author: dapine
ms.openlocfilehash: d5ecc104c7845a1881cbcdecfbccb75148f6e070
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815366"
---
# <a name="install-and-run-speech-service-containers-preview"></a>Installare ed eseguire i contenitori dei servizi vocali (anteprima)

I contenitori consentono di eseguire alcune delle API del servizio vocale nell'ambiente in uso. I contenitori sono ottimi per requisiti specifici di sicurezza e governance dei dati. In questo articolo si apprenderà come scaricare, installare ed eseguire un contenitore di riconoscimento vocale.

I contenitori di sintesi vocale consentono ai clienti di creare un'architettura di applicazione vocale ottimizzata per le funzionalità cloud affidabili e la località perimetrale. Sono disponibili quattro contenitori diversi. I due contenitori standard sono **sintesi vocale** e sintesi **vocale**. I due contenitori personalizzati sono da **riconoscimento vocale personalizzato a testo** e **da sintesi vocale personalizzata**.

> [!IMPORTANT]
> Tutti i contenitori di riconoscimento vocale sono attualmente offerti come parte di un' [anteprima pubblica "gestita"](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio). Verrà creato un annuncio quando i contenitori di riconoscimento vocale sono in stato di disponibilità generale.

| Funzione | database elastico | Più recente |
|--|--|--|
| Riconoscimento vocale | Trascrive registrazioni audio continue in tempo reale o batch in testo con risultati intermedi. | 2.0.0 |
| Da Riconoscimento vocale personalizzato a testo | Usando un modello personalizzato dal [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech), le registrazioni audio continue in tempo reale o batch vengono trascritte in testo con risultati intermedi. | 2.0.0 |
| Sintesi vocale | Converte il testo in sintesi vocale naturale con input di testo normale o linguaggio di markup sintesi vocale (SSML). | 1.3.0 |
| Sintesi vocale personalizzata | Usando un modello personalizzato dal [portale vocale personalizzato](https://aka.ms/custom-voice-portal), converte il testo in un discorso di suono naturale con input di testo normale o SSML (Speech Synthesis Markup Language). | 1.3.0 |

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti seguenti prima di usare i contenitori di sintesi vocale:

| Obbligatoria | Finalità |
|--|--|
| Motore Docker | È necessario il motore Docker installato in un [computer host](#the-host-computer). Docker offre pacchetti per la configurazione dell'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> **In Windows** Docker deve essere configurato anche per supportare i contenitori Linux.<br><br> |
| Familiarità con Docker | È opportuno avere una conoscenza di base dei concetti relativi a Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base. |
| Risorsa vocale | Per usare questi contenitori, è necessario avere:<br><br>Una risorsa _vocale_ di Azure per ottenere la chiave API e l'URI dell'endpoint associati. Entrambi i valori sono disponibili nelle pagine relative alla panoramica e alle chiavi del **discorso** del portale di Azure. Sono entrambi necessari per avviare il contenitore.<br><br>**{API_KEY}** : una delle due chiavi di risorsa disponibili nella pagina **chiavi**<br><br>**{ENDPOINT_URI}** : endpoint fornito nella pagina **Panoramica** |

## <a name="request-access-to-the-container-registry"></a>Richiedere l'accesso al registro contenitori

Compilare e inviare il [modulo di richiesta di contenitori di sintesi vocale di servizi cognitivi](https://aka.ms/speechcontainerspreview/) per richiedere l'accesso al contenitore. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Supporto Advanced Vector Extension

L'**host** è il computer che esegue il contenitore Docker. L'host *deve supportare* [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). È possibile verificare il supporto di AVX2 negli host Linux con il comando seguente:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> Il computer host è *necessario* per supportare AVX2. Il contenitore *non* funzionerà correttamente senza supporto per AVX2.

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

La tabella seguente descrive l'allocazione minima e consigliata delle risorse per ogni contenitore vocale.

# <a name="speech-to-texttabstt"></a>[Riconoscimento vocale](#tab/stt)

| Contenitore | Minima | Consigliato |
|-----------|---------|-------------|
| Riconoscimento vocale | 2 Core, 2 GB di memoria | 4 core, 4 GB di memoria |

# <a name="custom-speech-to-texttabcstt"></a>[Da Riconoscimento vocale personalizzato a testo](#tab/cstt)

| Contenitore | Minima | Consigliato |
|-----------|---------|-------------|
| Da Riconoscimento vocale personalizzato a testo | 2 Core, 2 GB di memoria | 4 core, 4 GB di memoria |

# <a name="text-to-speechtabtts"></a>[Sintesi vocale](#tab/tts)

| Contenitore | Minima | Consigliato |
|-----------|---------|-------------|
| Sintesi vocale | 1 core, 2 GB di memoria | 2 Core, 3 GB di memoria |

# <a name="custom-text-to-speechtabctts"></a>[Sintesi vocale personalizzata](#tab/ctts)

| Contenitore | Minima | Consigliato |
|-----------|---------|-------------|
| Sintesi vocale personalizzata | 1 core, 2 GB di memoria | 2 Core, 3 GB di memoria |

***

* Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.

> [!NOTE]
> Il valore minimo e consigliato sono basati sui limiti di Docker, *non* sulle risorse del computer host. Ad esempio, i contenitori di sintesi vocale mappano parti di un modello di linguaggio di grandi dimensioni ed è *consigliabile* che l'intero file si trovi in memoria, che è un 4-6 GB aggiuntivo. Inoltre, la prima esecuzione di uno dei due contenitori potrebbe richiedere più tempo, perché i modelli vengono inseriti in una pagina di memoria.

## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine del contenitore con `docker pull`

Le immagini del contenitore per la sintesi vocale sono disponibili nelle Container Registry seguenti.

# <a name="speech-to-texttabstt"></a>[Riconoscimento vocale](#tab/stt)

| Contenitore | Repository |
|-----------|------------|
| Riconoscimento vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-texttabcstt"></a>[Da Riconoscimento vocale personalizzato a testo](#tab/cstt)

| Contenitore | Repository |
|-----------|------------|
| Da Riconoscimento vocale personalizzato a testo | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speechtabtts"></a>[Sintesi vocale](#tab/tts)

| Contenitore | Repository |
|-----------|------------|
| Sintesi vocale | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speechtabctts"></a>[Sintesi vocale personalizzata](#tab/ctts)

| Contenitore | Repository |
|-----------|------------|
| Sintesi vocale personalizzata | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Pull Docker per i contenitori di riconoscimento vocale

# <a name="speech-to-texttabstt"></a>[Riconoscimento vocale](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Pull di Docker per il contenitore di riconoscimento vocale

Usare il comando [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare un'immagine del contenitore dal registro di anteprima del contenitore.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> Il tag `latest` estrae le impostazioni locali del `en-US`. Per altre impostazioni locali, vedere [impostazioni locali di riconoscimento vocale](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Impostazioni locali per sintesi vocale

Tutti i tag, ad eccezione di `latest`, sono nel formato seguente e fanno distinzione tra maiuscole e minuscole:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

Il seguente tag è un esempio del formato:

```
2.0.0-amd64-en-us-preview
```

Per tutte le impostazioni locali supportate del contenitore di **riconoscimento vocale** , vedere [tag dell'immagine da voce a testo](../containers/container-image-tags.md#speech-to-text).

# <a name="custom-speech-to-texttabcstt"></a>[Da Riconoscimento vocale personalizzato a testo](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Pull di Docker per il contenitore da Riconoscimento vocale personalizzato a testo

Usare il comando [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare un'immagine del contenitore dal registro di anteprima del contenitore.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> Il `locale` e il `voice` per i contenitori di riconoscimento vocale personalizzati sono determinati dal modello personalizzato inserito dal contenitore.

# <a name="text-to-speechtabtts"></a>[Sintesi vocale](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Pull di Docker per il contenitore di sintesi vocale

Usare il comando [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare un'immagine del contenitore dal registro di anteprima del contenitore.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> Il tag `latest` estrae le impostazioni locali `en-US` e `jessarus` voce. Per altre impostazioni locali, vedere [impostazioni locali da sintesi vocale](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Impostazioni locali per sintesi vocale

Tutti i tag, ad eccezione di `latest`, sono nel formato seguente e fanno distinzione tra maiuscole e minuscole:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

Il seguente tag è un esempio del formato:

```
1.3.0-amd64-en-us-jessarus-preview
```

Per tutte le impostazioni locali supportate e le voci corrispondenti del contenitore di **sintesi vocale** , vedere [tag di immagine da testo a voce](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> Quando si crea un POST HTTP *di tipo text-to-Speech standard* , il messaggio [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md) richiede un elemento `voice` con un attributo `name`. Il valore corrisponde alle impostazioni locali del contenitore e alla voce corrispondenti, nota anche come ["nome breve"](language-support.md#standard-voices). Ad esempio, il tag `latest` avrà un nome vocale di `en-US-JessaRUS`.

# <a name="custom-text-to-speechtabctts"></a>[Sintesi vocale personalizzata](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Pull Docker per il contenitore di sintesi vocale personalizzato

Usare il comando [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare un'immagine del contenitore dal registro di anteprima del contenitore.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> Il `locale` e il `voice` per i contenitori di riconoscimento vocale personalizzati sono determinati dal modello personalizzato inserito dal contenitore.

***

## <a name="how-to-use-the-container"></a>Come usare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usare il contenitore.

1. [Eseguire il contenitore](#run-the-container-with-docker-run), con le impostazioni di fatturazione necessarie. Sono disponibili altri [esempi](speech-container-configuration.md#example-docker-run-commands) del comando `docker run`.
1. [Eseguire le query sull'endpoint di stima del contenitore](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con `docker run`

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire il contenitore. Per informazioni dettagliate su come ottenere i valori `{Endpoint_URI}` e `{API_Key}`, vedere [raccolta dei parametri obbligatori](#gathering-required-parameters) . Sono disponibili anche [esempi](speech-container-configuration.md#example-docker-run-commands) aggiuntivi del comando `docker run`.

# <a name="speech-to-texttabstt"></a>[Riconoscimento vocale](#tab/stt)

Per eseguire il contenitore *sintesi vocale* , eseguire il comando `docker run` seguente.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue un contenitore *di riconoscimento vocale* dall'immagine del contenitore.
* Alloca 4 core CPU e 4 gigabyte (GB) di memoria.
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.

# <a name="custom-speech-to-texttabcstt"></a>[Da Riconoscimento vocale personalizzato a testo](#tab/cstt)

Il contenitore *da riconoscimento vocale personalizzato a testo* si basa su un modello di riconoscimento vocale personalizzato. È necessario eseguire il [Training](how-to-custom-speech-train-model.md) del modello personalizzato usando il [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech). Per eseguire il contenitore è necessario l' **ID del modello** di riconoscimento vocale personalizzato. Si trova nella pagina **Training** del portale di riconoscimento vocale personalizzato. Dal portale di riconoscimento vocale personalizzato passare alla pagina **Training** e selezionare il modello.
<br>

![Pagina di formazione vocale personalizzata](media/custom-speech/custom-speech-model-training.png)

Ottenere l' **ID modello** da usare come argomento per il parametro `ModelId` del comando di `docker run`.
<br>

![Dettagli del modello di riconoscimento vocale personalizzato](media/custom-speech/custom-speech-model-details.png)

La tabella seguente rappresenta i vari parametri di `docker run` e le relative descrizioni:

| Parametro | Description |
|---------|---------|
| `{VOLUME_MOUNT}` | [Montaggio del volume](https://docs.docker.com/storage/volumes/)del computer host, usato da Docker per salvare in modo permanente il modello personalizzato. Ad esempio *C:\CustomSpeech* , in cui l' *unità C* si trova nel computer host. |
| `{MODEL_ID}` | ID del **modello** di riconoscimento vocale personalizzato dalla pagina **Training** del portale di riconoscimento vocale personalizzato. |
| `{ENDPOINT_URI}` | L'endpoint è necessario per la misurazione e la fatturazione. Per ulteriori informazioni, vedere [raccolta dei parametri obbligatori](#gathering-required-parameters). |
| `{API_KEY}` | La chiave API è obbligatoria. Per ulteriori informazioni, vedere [raccolta dei parametri obbligatori](#gathering-required-parameters). |

Per eseguire il contenitore *da riconoscimento vocale personalizzato a testo* , eseguire il comando `docker run` seguente:

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

* Esegue un contenitore da *riconoscimento vocale personalizzato a testo* dall'immagine del contenitore.
* Alloca 4 core CPU e 4 gigabyte (GB) di memoria.
* Carica il modello da *riconoscimento vocale personalizzato a testo* dal montaggio di input del volume, ad esempio *C:\CustomSpeech*.
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore.
* Scarica il modello in base al `ModelId` (se non è stato trovato nel montaggio del volume).
* Se il modello personalizzato è stato scaricato in precedenza, il `ModelId` viene ignorato.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.

# <a name="text-to-speechtabtts"></a>[Sintesi vocale](#tab/tts)

Per eseguire il contenitore *sintesi vocale* , eseguire il comando `docker run` seguente.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue un contenitore da *sintesi vocale* dall'immagine del contenitore.
* Alloca 2 core CPU e un gigabyte (GB) di memoria.
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.

# <a name="custom-text-to-speechtabctts"></a>[Sintesi vocale personalizzata](#tab/ctts)

Il contenitore *sintesi vocale personalizzato* si basa su un modello Voice personalizzato. È necessario eseguire il [Training](how-to-custom-voice-create-voice.md) del modello personalizzato tramite il [portale vocale personalizzato](https://aka.ms/custom-voice-portal). Per eseguire il contenitore è necessario l' **ID del modello** Voice personalizzato. Si trova nella pagina **Training** del portale Voice personalizzato. Dal portale vocale personalizzato passare alla pagina **Training** e selezionare il modello.
<br>

![Pagina training vocale personalizzata](media/custom-voice/custom-voice-model-training.png)

Ottenere l' **ID modello** da usare come argomento per il parametro `ModelId` del comando Docker Run.
<br>

![Dettagli del modello Voice personalizzato](media/custom-voice/custom-voice-model-details.png)

La tabella seguente rappresenta i vari parametri di `docker run` e le relative descrizioni:

| Parametro | Description |
|---------|---------|
| `{VOLUME_MOUNT}` | [Montaggio del volume](https://docs.docker.com/storage/volumes/)del computer host, usato da Docker per salvare in modo permanente il modello personalizzato. Ad esempio *C:\CustomSpeech* , in cui l' *unità C* si trova nel computer host. |
| `{MODEL_ID}` | ID del **modello** di riconoscimento vocale personalizzato dalla pagina **Training** del portale vocale personalizzato. |
| `{ENDPOINT_URI}` | L'endpoint è necessario per la misurazione e la fatturazione. Per ulteriori informazioni, vedere [raccolta dei parametri obbligatori](#gathering-required-parameters). |
| `{API_KEY}` | La chiave API è obbligatoria. Per ulteriori informazioni, vedere [raccolta dei parametri obbligatori](#gathering-required-parameters). |

Per eseguire il contenitore *di sintesi vocale personalizzato* , eseguire il comando `docker run` seguente:

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

* Esegue un contenitore *di sintesi vocale personalizzato* dall'immagine del contenitore.
* Alloca 2 core CPU e un gigabyte (GB) di memoria.
* Carica il modello *di sintesi vocale personalizzato* dal montaggio di input del volume, ad esempio *C:\CustomVoice*.
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore.
* Scarica il modello in base al `ModelId` (se non è stato trovato nel montaggio del volume).
* Se il modello personalizzato è stato scaricato in precedenza, il `ModelId` viene ignorato.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.

***

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore e avviarlo; altrimenti il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Eseguire query sull'endpoint di stima del contenitore

| Contenitori | URL host SDK | Protocol |
|--|--|--|
| Riconoscimento vocale e da Riconoscimento vocale personalizzato a testo | `ws://localhost:5000` | WS |
| Sintesi vocale e sintesi vocale personalizzata | `http://localhost:5000` | http |

Per ulteriori informazioni sull'utilizzo di protocolli WSS e HTTPS, vedere [sicurezza dei contenitori](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

### <a name="text-to-speech-or-custom-text-to-speech"></a>Sintesi vocale o sintesi vocale personalizzata

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Eseguire più contenitori nello stesso host

Se si intende eseguire più contenitori con porte esposte, assicurarsi di eseguire ogni contenitore con una porta esposta diversa. Eseguire ad esempio il primo contenitore sulla porta 5000 e il secondo sulla porta 5001.

Questo contenitore e un contenitore di Servizi cognitivi diverso sono in esecuzione contemporaneamente sull'HOST. Sono inoltre in esecuzione più contenitori dello stesso contenitore di Servizi cognitivi.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrestare il contenitore

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>risoluzione dei problemi

All'avvio o all'esecuzione del contenitore possono verificarsi problemi. Usare un [montaggio](speech-container-configuration.md#mount-settings) di output e abilitare la registrazione. Questa operazione consentirà al contenitore di generare file di log utili per la risoluzione dei problemi.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fatturazione

I contenitori di riconoscimento vocale inviano informazioni di fatturazione ad Azure, usando una risorsa *vocale* nell'account Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](speech-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Summary

In questo articolo sono stati appresi concetti e flussi di lavoro per il download, l'installazione e l'esecuzione di contenitori di sintesi vocale. In sintesi:

* Il riconoscimento vocale fornisce quattro contenitori Linux per Docker, incapsulando varie funzionalità:
  * *Riconoscimento vocale*
  * *Da Riconoscimento vocale personalizzato a testo*
  * *Sintesi vocale*
  * *Sintesi vocale personalizzata*
* Le immagini del contenitore vengono scaricate dal registro contenitori in Azure.
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile usare l'API REST o l'SDK per chiamare le operazioni nei contenitori di sintesi vocale specificando l'URI host del contenitore.
* È necessario fornire informazioni di fatturazione quando si crea un'istanza di un contenitore.

> [!IMPORTANT]
>  I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di Servizi cognitivi non inviano i dati dei clienti (ad esempio, l'immagine o il testo analizzato) a Microsoft.

## <a name="next-steps"></a>Passaggi successivi

* Esaminare [configurare i contenitori](speech-container-configuration.md) per le impostazioni di configurazione
* Informazioni su come [usare i contenitori di servizi vocali con Kubernetes e Helm](speech-container-howto-on-premises.md)
* Usare più [contenitori di servizi cognitivi](../cognitive-services-container-support.md)
