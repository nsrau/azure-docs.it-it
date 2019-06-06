---
title: Installare ed eseguire i contenitori - Riconoscimento modulo
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare il contenitore Riconoscimento modulo per analizzare i dati di modulo e tabella.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/31/2019
ms.author: pafarley
ms.openlocfilehash: 1c9e68f643f27f70190b5847225692d554cc5480
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475434"
---
# <a name="install-and-run-form-recognizer-containers"></a>Installare ed eseguire i contenitori Riconoscimento modulo
Riconoscimento modulo applica la tecnologia di Machine Learning per identificare ed estrarre le coppie chiave-valore e le tabelle dai moduli, associando i valori e le voci della tabella e quindi restituendo dati strutturati che includono le relazioni nel file originale. È possibile chiamare il modello Riconoscimento modulo personalizzato usando una semplice API REST per ridurre la complessità e integrarlo facilmente nel processo di automazione del flusso di lavoro o in un'altra applicazione. Sono necessari solo cinque documenti (o un modulo vuoto) per ottenere risultati in modo rapido, accurato e su misura per il contenuto specifico, senza alcun intervento manuale impegnativo né competenze approfondite di data science. Non richiede l'assegnazione di etichette dati né l'annotazione dei dati.

|Funzione|Funzionalità|
|-|-|
|Riconoscimento modulo| <li>Elabora file di tipo PDF, PNG e JPG.<li>Esegue il training di modelli personalizzati con minimo 5 moduli dello stesso layout. <li>Estrae coppie chiave-valore e le informazioni della tabella. <li>Usa la funzionalità RecognizeText dell'API Visione artificiale di Servizi cognitivi per rilevare ed estrarre testo stampato dalle immagini all'interno dei moduli.<li>Non richiede annotazione o assegnazione di etichette.|

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di usare i contenitori Riconoscimento modulo, è necessario soddisfare i prerequisiti seguenti:

|Obbligatoria|Scopo|
|--|--|
|Motore Docker| È necessario il motore Docker installato in un [computer host](#the-host-computer). Docker offre pacchetti per la configurazione dell'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> **In Windows** Docker deve essere configurato anche per supportare i contenitori Linux.<br><br>|
|Familiarità con Docker | È opportuno avere una conoscenza di base dei concetti relativi a Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.|
|Interfaccia della riga di comando di Azure| È necessario installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nell'host.|
|Risorsa API Visione artificiale| Per poter elaborare documenti e immagini digitalizzati, è obbligatoria una **Risorsa Visione artificiale** è obbligatorio. È possibile accedere alla funzione di **riconoscimento del testo** come risorsa di Azure (API REST o SDK) o come [contenitore](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull) `cognitive-services-recognize-text`. Si applicano le normali tariffe di fatturazione. <br><br>È necessario passare la chiave e l'endpoint di fatturazione per la risorsa di Visione artificiale specifica (cloud di Azure o un contenitore di Servizi cognitivi). Usare questa chiave e l'endpoint di fatturazione come {COMPUTER_VISION_API_KEY} e {COMPUTER_VISION_BILLING_ENDPOINT_URI}.<br><br> Se si usa il **contenitore `cognitive-services-recognize-text`** , assicurarsi che:<br><br>* La chiave Visione artificiale per il contenitore Riconoscimento modulo sia quella specificata nel comando `docker run` di Visione artificiale per il contenitore `cognitive-services-recognize-text`.<br>* L'endpoint di fatturazione sia quello del contenitore, ad esempio `https://localhost:5000`. Se si usano insieme i contenitori Visione artificiale e Riconoscimento modulo sullo stesso host, non sarà possibile avviarli entrambi con la porta `5000` predefinita.  |  
|Risorsa Riconoscimento modulo |Per usare questi contenitori, è necessario avere:<br><br>Una risorsa di _Riconoscimento modulo_ di Azure per ottenere la chiave di fatturazione associata e l'URI dell'endpoint di fatturazione. Entrambi i valori sono disponibili nelle pagine delle chiavi e della panoramica di **Riconoscimento modulo** nel portale di Azure e sono necessarie per avviare il contenitore.<br><br>**{BILLING_KEY}** : chiave della risorsa<br><br>**{BILLING_ENDPOINT_URI}** : un esempio di URI dell'endpoint è: `https://westus.api.cognitive.microsoft.com/forms/v1.0`| 

## <a name="request-access-to-the-container-registry"></a>Richiedere l'accesso al registro contenitori

È necessario innanzitutto completare e inviare il [modulo di richiesta dei contenitori di Riconoscimento modulo in Servizi cognitivi](https://aka.ms/FormRecognizerRequestAccess) per richiedere l'accesso al contenitore. Verrà inoltre effettuata l'iscrizione a Visione artificiale. Non è necessario iscriversi al modulo di richiesta di Visione artificiale separatamente. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

La tabella seguente indica i core CPU minimi e consigliati e la memoria da allocare per ogni contenitore di Riconoscimento modulo.

| Contenitore | Minima | Consigliato |
|-----------|---------|-------------|
|cognitive-services-form-recognizer | 2 core, 4 GB di memoria | 4 core, 8 GB di memoria |

* Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.
* TPS - transazioni al secondo

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.

> [!Note]
> I valori minimi e consigliati sono basati sui limiti di Docker e *non* sulle risorse del computer host.

## <a name="get-the-container-image-with-docker-pull-command"></a>Ottenere l'immagine del contenitore con il comando docker pull

Sono disponibili le immagini del contenitore per il Riconoscimento modulo.

| Contenitore | Repository |
|-----------|------------|
| cognitive-services-form-recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |

Se si intende usare il ​​[contenitore](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull) `cognitive-services-recognize-text` invece del servizio Riconoscimento modulo, usare il comando `docker pull` con il nome contenitore corretto: 

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-form-recognizer-container"></a>Docker pull per il contenitore di Riconoscimento modulo

#### <a name="form-recognizer"></a>Riconoscimento modulo

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```

## <a name="how-to-use-the-container"></a>Come usare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usare il contenitore.

1. [Eseguire il contenitore](#run-the-container-with-docker-run), con le impostazioni di fatturazione necessarie, ma non usate. Sono disponibili altri [esempi](form-recognizer-container-configuration.md#example-docker-run-commands) del comando `docker run`.
1. [Eseguire le query sull'endpoint di stima del contenitore](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con `docker run`

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire uno qualsiasi dei tre contenitori. Il comando usa i parametri seguenti:

| Placeholder | Valore |
|-------------|-------|
|{BILLING_KEY} | Questa chiave viene usata per avviare il contenitore ed è disponibile nella pagina delle chiavi di Riconoscimento modulo del portale di Azure.  |
|{BILLING_ENDPOINT_URI} | Il valore dell'URI dell'endpoint di fatturazione è disponibile nella pagina della panoramica di Riconoscimento modulo del portale di Azure.|
|{COMPUTER_VISION_API_KEY}| La chiave è disponibile nella pagina Chiavi dell'API di Visione artificiale del portale di Azure.|
|{COMPUTER_VISION_ENDPOINT_URI}|L'endpoint di fatturazione. Se si usa una risorsa Visione artificiale basata sul cloud, il valore dell'URI è disponibile nella pagina Panoramica dell'API di Visione artificiale del portale di Azure. Se si usa un contenitore `cognitive-services-recognize-text`, usare l'URL dell'endpoint di fatturazione passato al contenitore nel comando `docker run`.|

Sostituire i parametri con i valori personalizzati nel comando `docker run` di esempio seguente.

### <a name="form-recognizer"></a>Riconoscimento modulo

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Questo comando:

* Esegue un contenitore di Riconoscimento modulo dall'immagine del contenitore
* Alloca 2 core CPU e 8 GB di memoria
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.
* Monta un volume /input e /output al contenitore

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Esegui contenitori separati come comandi Docker Run separati

Per la combinazione di Riconoscimento modulo e Riconoscimento del testo ospitata in locale nello stesso host, seguono i due esempi di comandi dell'interfaccia della riga di comando di Docker.

Eseguire il primo contenitore sulla porta 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Eseguire il secondo contenitore sulla porta 5001.


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Ogni contenitore successivo deve essere su una porta diversa. 

### <a name="run-separate-containers-with-docker-compose"></a>Eseguire contenitori separati con Docker Compose

Per la combinazione di Riconoscimento modulo e Riconoscimento del testo ospitata in locale nello stesso host, segue un file YAML Docker Compose di esempio. La `{COMPUTER_VISION_API_KEY}` di Riconoscimento del testo deve essere identica per i contenitori `formrecognizer` e `ocr`. L'`{COMPUTER_VISION_ENDPOINT_URI}` viene usato solo nel contenitore `ocr` perché il contenitore `formrecognizer` usa la porta e il nome `ocr`. 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: {COMPUTER_VISION_API_KEY}  

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{BILLING_ENDPOINT_URI}"
      apikey: {BILLING_KEY}
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"  
```


> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey`, oltre che `FormRecognizer:ComputerVisionApiKey` e `FormRecognizer:ComputerVisionEndpointUri`, per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Eseguire query sull'endpoint di stima del contenitore

|Contenitore|Endpoint|
|--|--|
|form-recognizer|http://localhost:5000


### <a name="form-recognizer"></a>Riconoscimento modulo

Il contenitore fornisce le API endpoint di query basate su websocket, accessibili tramite la [documentazione dell'SDK dei servizi Riconoscimento modulo](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

Per impostazione predefinita, l'SDK di Riconoscimento modulo usa i servizi online. Per usare il contenitore, è necessario modificare il metodo di inizializzazione. Vedere gli esempi seguenti.

#### <a name="for-c"></a>Per C#

Passare dall'uso di questa chiamata di inizializzazione del cloud di Azure:

```C#
var config = FormRecognizerConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

a questa chiamata che usa l'endpoint del contenitore:

```C#
var config = FormRecognizerConfig.FromEndpoint("ws://localhost:5000/formrecognizer/v1.0-preview/custom", "YourSubscriptionKey");
```

#### <a name="for-python"></a>Per Python

Passare dall'uso di questa chiamata di inizializzazione del cloud di Azure

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, region=service_region)
```

a questa chiamata che usa l'endpoint del contenitore:

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Riconoscimento modulo

Il contenitore fornisce le API endpoint REST, disponibili [qui](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel).


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Arrestare il contenitore

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>risoluzione dei problemi

Quando si esegue il contenitore, questo usa **stdout** e **stderr** per generare informazioni utili per risolvere i problemi che si verificano durante l'avvio o l'esecuzione del contenitore.

## <a name="billing"></a>Fatturazione

I contenitori Riconoscimento modulo inviano le informazioni di fatturazione ad Azure usando una risorsa di _Riconoscimento modulo_ nell'account di Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](form-recognizer-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Summary

In questo articolo sono stati descritti i concetti e il flusso di lavoro per scaricare, installare ed eseguire i contenitori di Riconoscimento modulo. In sintesi:

* Riconoscimento modulo fornisce un unico contenitore Linux per Docker.
* Le immagini dei contenitori vengono scaricate da un registro contenitori privato in Azure.
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile usare l'API REST o l'SDK per chiamare le operazioni nei contenitori di Riconoscimento modulo specificando l'URI host del contenitore.
* Quando si crea un'istanza di un contenitore, è necessario specificare le informazioni di fatturazione.

> [!IMPORTANT]
>  I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di Servizi cognitivi non inviano i dati dei clienti (ad esempio, l'immagine o il testo analizzato) a Microsoft.

## <a name="next-steps"></a>Passaggi successivi

* Rivedere [Configurare i contenitori](form-recognizer-container-configuration.md) per informazioni sulle impostazioni di configurazione.
* Usare altri [contenitori di Servizi cognitivi](../cognitive-services-container-support.md)
