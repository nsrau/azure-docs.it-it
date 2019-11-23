---
title: How to install and run container for Form Recognizer
titleSuffix: Azure Cognitive Services
description: This article will explain how to use the Azure Cognitive Services Form Recognizer container to parse form and table data.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 21582a5a17a3c6f67182173bfe08d80c48765f7d
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325847"
---
# <a name="install-and-run-form-recognizer-containers-preview"></a>Install and run Form Recognizer containers (Preview)

Riconoscimento modulo di Azure applica la tecnologia di Machine Learning per identificare ed estrarre coppie chiave-valore e tabelle dai moduli, associando i valori e le voci della tabella alle coppie chiave-valore e quindi restituendo dati strutturati che includono le relazioni nel file originale. 

Per ridurre la complessità e integrare facilmente un modello di Riconoscimento modulo personalizzato nel processo di automazione dl flusso di lavoro o in un'altra applicazione, è possibile chiamare il modello usando una semplice API REST. Only five form documents (or one empty form and two filled-in forms) are needed, so you can get results quickly, accurately, and tailored to your specific content. senza alcun intervento manuale impegnativo né competenze approfondite di data science. E non è neanche necessario assegnare etichette o annotare dati.

|Funzione|database elastico|
|-|-|
|Riconoscimento modulo| <li>Elabora file PDF, PNG e JPG<li>Trains custom models with a minimum of five forms of the same layout <li>Estrae coppie chiave-valore e informazioni della tabella <li>Usa la funzionalità di riconoscimento del testo dell'API Visione artificiale di Servizi cognitivi per rilevare ed estrarre testo stampato dalle immagini all'interno dei moduli<li>Non richiede annotazioni o assegnazioni di etichette|

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di usare i contenitori di Riconoscimento modulo, è necessario soddisfare i prerequisiti seguenti:

|Obbligatoria|Finalità|
|--|--|
|Motore Docker| È necessario il motore Docker installato in un [computer host](#the-host-computer). Docker offre pacchetti per la configurazione dell'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> In Windows, è anche necessario configurare Docker per supportare i contenitori Linux.<br><br>|
|Familiarità con Docker | È opportuno avere una conoscenza di base dei concetti relativi a Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.|
|Interfaccia della riga di comando di Azure| Installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nell'host.|
|Risorsa API Visione artificiale| Per elaborare documenti e immagini digitalizzati, è necessaria una risorsa di Visione artificiale. È possibile accedere alla funzionalità di riconoscimento del testo come risorsa di Azure (API REST o SDK) o come [contenitore](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull) *cognitive-services-recognize-text*. Si applicano le normali tariffe di fatturazione. <br><br>Pass in both the API key and endpoints for your Computer Vision resource (Azure cloud or Cognitive Services container). Use this API key and the endpoint as **{COMPUTER_VISION_API_KEY}** and **{COMPUTER_VISION_ENDPOINT_URI}** .<br><br> Se si usa il contenitore *cognitive-services-recognize-text*, soddisfare questi requisiti:<br><br>La chiave di Visione artificiale per il contenitore Riconoscimento modulo deve essere quella specificata nel comando `docker run` di Visione artificiale per il contenitore *cognitive-services-recognize-text*.<br>L'endpoint di fatturazione deve essere quello del contenitore, ad esempio `http://localhost:5000`. Se si usano insieme i contenitori di Visione artificiale e Riconoscimento modulo nello stesso host, non sarà possibile avviarli entrambi con la porta *5000* predefinita. |
|Risorsa Riconoscimento modulo |Per usare questi contenitori, è necessario avere:<br><br>An Azure **Form Recognizer** resource to get the associated API key and endpoint URI. Both values are available on the Azure portal **Form Recognizer** Overview and Keys pages, and both values are required to start the container.<br><br>**{FORM_RECOGNIZER_API_KEY}** : One of the two available resource keys on the Keys page<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}** : The endpoint as provided on the Overview page|

## <a name="gathering-required-parameters"></a>Gathering required parameters

There are three primary parameters for all Cognitive Services' containers that are required. The end-user license agreement (EULA) must be present with a value of `accept`. Additionally, both an Endpoint URL and API Key are needed.

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>Endpoint URI `{COMPUTER_VISION_ENDPOINT_URI}` and `{FORM_RECOGNIZER_ENDPOINT_URI}`

The **Endpoint** URI value is available on the Azure portal *Overview* page of the corresponding Cognitive Service resource. Navigate to the *Overview* page, hover over the Endpoint, and a `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> icon will appear. Copy and use where needed.

![Gather the endpoint uri for later use](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>Keys `{COMPUTER_VISION_API_KEY}` and `{FORM_RECOGNIZER_API_KEY}`

This key is used to start the container, and is available on the Azure portal's Keys page of the corresponding Cognitive Service resource. Navigate to the *Keys* page, and click on the `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> icon.

![Get one of the two keys for later use](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> These subscription keys are used to access your Cognitive Service API. Do not share your keys. Store them securely, for example, using Azure Key Vault. We also recommend regenerating these keys regularly. Only one key is necessary to make an API call. When regenerating the first key, you can use the second key for continued access to the service.

## <a name="request-access-to-the-container-registry"></a>Richiedere l'accesso al registro contenitori

È necessario prima di tutto completare e inviare il [modulo di richiesta dei contenitori di Riconoscimento modulo in Servizi cognitivi](https://aka.ms/FormRecognizerRequestAccess) per richiedere l'accesso al contenitore. In questo modo verrà ance effettuata l'iscrizione a Visione artificiale. Non è necessario iscriversi con il modulo di richiesta di Visione artificiale separatamente. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

La tabella seguente indica i core di CPU minimi e consigliati e la memoria da allocare per ogni contenitore di Riconoscimento modulo:

| Contenitore | Minima | Consigliato |
|-----------|---------|-------------|
| Riconoscimento modulo | 2 core, 4-GB memory | 4 core, 8-GB memory |
| Riconoscimento del testo | 1 core, 8-GB memory | 2 cores, 8-GB memory |

* Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.
* Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.

> [!Note]
> I valori minimi e consigliati sono basati sui limiti di Docker e *non* sulle risorse del computer host.

## <a name="get-the-container-images-with-the-docker-pull-command"></a>Get the container images with the docker pull command

Container images for both the **Form Recognizer** and **Recognize Text** offerings are available in the following container registry:

| Contenitore | Fully qualified image name |
|-----------|------------|
| Riconoscimento modulo | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |
| Riconoscimento del testo | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

You will need both containers, please note that the **Recognizer Text** container is [detailed outside of this article.](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull)

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>Docker pull per il contenitore di Riconoscimento modulo

#### <a name="form-recognizer"></a>Riconoscimento modulo

Per ottenere il contenitore di Riconoscimento modulo, usare il comando seguente:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```
### <a name="docker-pull-for-the-recognize-text-container"></a>Docker pull per il contenitore di Riconoscimento del testo

#### <a name="recognize-text"></a>Riconoscimento del testo

To get the Recognize Text container, use the following command:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

## <a name="how-to-use-the-container"></a>Come usare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usarlo.

1. [Eseguire il contenitore](#run-the-container-by-using-the-docker-run-command), con le impostazioni di fatturazione necessarie. Sono disponibili altri [esempi](form-recognizer-container-configuration.md#example-docker-run-commands) del comando `docker run`.
1. [Eseguire le query sull'endpoint di stima del contenitore](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>Eseguire il contenitore con il comando docker run

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire il contenitore. Refer to [gathering required parameters](#gathering-required-parameters) for details on how to get the `{COMPUTER_VISION_ENDPOINT_URI}`, `{COMPUTER_VISION_API_KEY}`, `{FORM_RECOGNIZER_ENDPOINT_URI}` and `{FORM_RECOGNIZER_API_KEY}` values.

[Examples](form-recognizer-container-configuration.md#example-docker-run-commands) of the `docker run` command are available.

### <a name="form-recognizer"></a>Riconoscimento modulo

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Questo comando:

* Esegue un contenitore di Riconoscimento modulo dall'immagine del contenitore.
* Alloca 2 core di CPU e 8 GB di memoria.
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.
* Monta un volume /input e /output nel contenitore.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Esegui contenitori separati come comandi Docker Run separati

Per la combinazione di Riconoscimento modulo e Riconoscimento del testo ospitata in locale nello stesso host, usare i due esempi di comandi seguenti dell'interfaccia della riga di comando di Docker:

Eseguire il primo contenitore sulla porta 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY}
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

Per la combinazione di Riconoscimento modulo e Riconoscimento del testo ospitata in locale nello stesso host, vedere l'esempio seguente di file YAML di Docker Compose. La `{COMPUTER_VISION_API_KEY}` di Riconoscimento del testo deve essere identica per i contenitori `formrecognizer` e `ocr`. `{COMPUTER_VISION_ENDPOINT_URI}` viene usato solo nel contenitore `ocr`, perché il contenitore `formrecognizer` usa la porta e il nome `ocr`. 

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
      apikey: "{COMPUTER_VISION_API_KEY}"

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
      billing: "{FORM_RECOGNIZER_ENDPOINT_URI}"
      apikey: "{FORM_RECOGNIZER_API_KEY}"
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
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey`, oltre che `FormRecognizer:ComputerVisionApiKey` e `FormRecognizer:ComputerVisionEndpointUri`, per eseguire il contenitore. In caso contrario, il contenitore non si avvia. Per altre informazioni, vedere[Fatturazione](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Eseguire query sull'endpoint di stima del contenitore

|Contenitore|Endpoint|
|--|--|
|form-recognizer|http://localhost:5000

### <a name="form-recognizer"></a>Riconoscimento modulo

Il contenitore fornisce le API endpoint di query basate su websocket, accessibili tramite la [documentazione dell'SDK dei servizi Riconoscimento modulo](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

Per impostazione predefinita, l'SDK di Riconoscimento modulo usa i servizi online. Per usare il contenitore, è necessario modificare il metodo di inizializzazione. Vedere gli esempi seguenti.

#### <a name="for-c"></a>Per C#

Passare dall'uso di questa chiamata di inizializzazione del cloud di Azure:

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
a questa chiamata che usa l'endpoint del contenitore:

```csharp
var config =
    FormRecognizerConfig.FromEndpoint(
        "ws://localhost:5000/formrecognizer/v1.0-preview/custom",
        "YourSubscriptionKey");
```

#### <a name="for-python"></a>Per Python

Passare dall'uso di questa chiamata di inizializzazione del cloud di Azure:

```python
formrecognizer_config =
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key, region=service_region)
```

a questa chiamata che usa l'endpoint del contenitore:

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Riconoscimento modulo

Il contenitore fornisce le API endpoint REST, disponibili nella pagina dell'[API Riconoscimento modulo](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel).


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Arrestare il contenitore

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>risoluzione dei problemi

Se si esegue il contenitore con un punto di [montaggio](form-recognizer-container-configuration.md#mount-settings) di output e la registrazione attivata, il contenitore genera file di log utili per risolvere i problemi che si verificano durante l'avvio o l'esecuzione del contenitore.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fatturazione

I contenitori di Riconoscimento modulo inviano le informazioni di fatturazione ad Azure usando una risorsa di _Riconoscimento modulo_ nell'account Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](form-recognizer-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Summary

In questo articolo sono stati descritti i concetti e il flusso di lavoro per scaricare, installare ed eseguire i contenitori di Riconoscimento modulo. In sintesi:

* Riconoscimento modulo fornisce un unico contenitore Linux per Docker.
* Le immagini dei contenitori vengono scaricate da un registro contenitori privato in Azure.
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile usare l'API REST o l'SDK REST per chiamare le operazioni nei contenitori di Riconoscimento modulo specificando l'URI host del contenitore.
* Quando si crea un'istanza di un contenitore, è necessario specificare le informazioni di fatturazione.

> [!IMPORTANT]
>  I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di Servizi cognitivi non inviano a Microsoft i dati dei clienti, ad esempio l'immagine o il testo analizzato.

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Configurare i contenitori](form-recognizer-container-configuration.md) per informazioni sulle impostazioni di configurazione.
* Usare altri [contenitori di Servizi cognitivi](../cognitive-services-container-support.md).
