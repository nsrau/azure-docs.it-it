---
title: Come installare ed eseguire il contenitore per il riconoscimento del modulo
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare il contenitore Riconoscimento modulo per analizzare i dati di moduli e tabelle.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: c58f8ae0605f99952507a5f308316f784b3d8de1
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105136"
---
# <a name="install-and-run-form-recognizer-containers"></a>Installare ed eseguire i contenitori Riconoscimento modulo

Riconoscimento modulo di Azure applica la tecnologia di Machine Learning per identificare ed estrarre coppie chiave-valore e tabelle dai moduli, associando i valori e le voci della tabella alle coppie chiave-valore e quindi restituendo dati strutturati che includono le relazioni nel file originale. 

Per ridurre la complessità e integrare facilmente un modello di Riconoscimento modulo personalizzato nel processo di automazione dl flusso di lavoro o in un'altra applicazione, è possibile chiamare il modello usando una semplice API REST. Sono necessari solo cinque documenti modulo (o un modulo vuoto e due moduli compilati), in modo che sia possibile ottenere risultati rapidamente, accuratamente e personalizzati in base al contenuto specifico. senza alcun intervento manuale impegnativo né competenze approfondite di data science. E non è neanche necessario assegnare etichette o annotare dati.

|Funzione|Funzionalità|
|-|-|
|Riconoscimento modulo| <li>Elabora file PDF, PNG e JPG<li>Addestra i modelli personalizzati con un minimo di cinque forme dello stesso layout <li>Estrae coppie chiave-valore e informazioni della tabella <li>Usa la funzionalità di riconoscimento del testo dell'API Visione artificiale di Servizi cognitivi per rilevare ed estrarre testo stampato dalle immagini all'interno dei moduli<li>Non richiede annotazioni o assegnazioni di etichette|

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di usare i contenitori di Riconoscimento modulo, è necessario soddisfare i prerequisiti seguenti:

|Obbligatoria|Scopo|
|--|--|
|Motore Docker| È necessario il motore Docker installato in un [computer host](#the-host-computer). Docker offre pacchetti per la configurazione dell'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> In Windows, è anche necessario configurare Docker per supportare i contenitori Linux.<br><br>|
|Familiarità con Docker | È opportuno avere una conoscenza di base dei concetti relativi a Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.|
|Interfaccia della riga di comando di Azure| Installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nell'host.|
|Risorsa API Visione artificiale| Per elaborare documenti e immagini digitalizzati, è necessaria una risorsa di Visione artificiale. È possibile accedere alla funzionalità di riconoscimento del testo come risorsa di Azure (API REST o SDK) o come [contenitore](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull) *cognitive-services-recognize-text*. Si applicano le normali tariffe di fatturazione. <br><br>Passare sia la chiave API sia gli endpoint per la risorsa Visione artificiale (cloud di Azure o contenitore di servizi cognitivi). Usare questa chiave API e l'endpoint come **{COMPUTER_VISION_API_KEY}** e **{COMPUTER_VISION_ENDPOINT_URI}** .<br><br> Se si usa il contenitore *cognitive-services-recognize-text*, soddisfare questi requisiti:<br><br>La chiave di Visione artificiale per il contenitore Riconoscimento modulo deve essere quella specificata nel comando `docker run` di Visione artificiale per il contenitore *cognitive-services-recognize-text*.<br>L'endpoint di fatturazione deve essere quello del contenitore, ad esempio `http://localhost:5000`. Se si usano insieme i contenitori di Visione artificiale e Riconoscimento modulo nello stesso host, non sarà possibile avviarli entrambi con la porta *5000* predefinita. |
|Risorsa Riconoscimento modulo |Per usare questi contenitori, è necessario avere:<br><br>Una risorsa di riconoscimento di Azure **form** per ottenere la chiave API e l'URI dell'endpoint associati. Entrambi i valori sono disponibili nella panoramica del **riconoscimento del modulo** portale di Azure e nelle pagine delle chiavi ed entrambi i valori sono necessari per avviare il contenitore.<br><br>**{FORM_RECOGNIZER_API_KEY}** : Una delle due chiavi di risorsa disponibili nella pagina chiavi<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}** : Endpoint fornito nella pagina Panoramica|

## <a name="gathering-required-parameters"></a>Raccolta dei parametri obbligatori

Sono disponibili tre parametri primari per tutti i contenitori di servizi cognitivi richiesti. Il contratto di licenza con l'utente finale deve essere presente con un valore pari `accept`a. Sono inoltre necessari un URL dell'endpoint e una chiave API.

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>URI `{COMPUTER_VISION_ENDPOINT_URI}` dell'endpoint e`{FORM_RECOGNIZER_ENDPOINT_URI}`

Il valore dell'URI dell' **endpoint** è disponibile nella pagina *Panoramica* portale di Azure della risorsa del servizio cognitiva corrispondente. Passare alla pagina *Panoramica* , posizionare il puntatore del mouse sull'endpoint e `Copy to clipboard` verrà visualizzata un' <span class="docon docon-edit-copy x-hidden-focus"></span> icona. Copiare e usare se necessario.

![Raccogliere l'URI dell'endpoint per un uso successivo](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>Chiavi `{COMPUTER_VISION_API_KEY}` e`{FORM_RECOGNIZER_API_KEY}`

Questa chiave viene usata per avviare il contenitore ed è disponibile nella pagina chiavi del portale di Azure della risorsa del servizio cognitiva corrispondente. Passare alla pagina *chiavi* e fare clic sull' `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> icona.

![Ottenere una delle due chiavi per un uso successivo](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> Queste chiavi di sottoscrizione vengono usate per accedere all'API di servizi cognitivi. Non condividere le chiavi. Archiviarli in modo sicuro, ad esempio usando Azure Key Vault. Si consiglia inoltre di rigenerare regolarmente queste chiavi. È necessaria una sola chiave per effettuare una chiamata API. Quando si rigenera la prima chiave, è possibile usare la seconda chiave per l'accesso continuo al servizio.

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
| Riconoscimento modulo | 2 Core, 4 GB di memoria | 4 core, 8 GB di memoria |
| Riconoscimento del testo | 1 core, 8 GB di memoria | 2 Core, 8 GB di memoria |

* Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.
* Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.

> [!Note]
> I valori minimi e consigliati sono basati sui limiti di Docker e *non* sulle risorse del computer host.

## <a name="get-the-container-images-with-the-docker-pull-command"></a>Ottenere le immagini del contenitore con il comando docker pull

Le immagini del contenitore per il **riconoscimento dei moduli** e le offerte di **riconoscimento del testo** sono disponibili nel registro contenitori seguente:

| Contenitore | Nome completo dell'immagine |
|-----------|------------|
| Riconoscimento modulo | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |
| Riconoscimento del testo | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Sono necessari entrambi i contenitori. si noti che il contenitore di **testo del riconoscimento** è [dettagliato al di fuori di questo articolo.](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull)

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>Docker pull per il contenitore di Riconoscimento modulo

#### <a name="form-recognizer"></a>Riconoscimento modulo

Per ottenere il contenitore di Riconoscimento modulo, usare il comando seguente:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```
### <a name="docker-pull-for-the-recognize-text-container"></a>Docker pull per il contenitore di Riconoscimento del testo

#### <a name="recognize-text"></a>Riconoscimento del testo

Per ottenere il contenitore di riconoscimento del testo, usare il comando seguente:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

## <a name="how-to-use-the-container"></a>Come usare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usarlo.

1. [Eseguire il contenitore](#run-the-container-by-using-the-docker-run-command), con le impostazioni di fatturazione necessarie. Sono disponibili altri [esempi](form-recognizer-container-configuration.md#example-docker-run-commands) del comando `docker run`.
1. [Eseguire le query sull'endpoint di stima del contenitore](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>Eseguire il contenitore con il comando docker run

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire il contenitore. Per informazioni dettagliate su come ottenere i `{COMPUTER_VISION_ENDPOINT_URI}`valori, `{FORM_RECOGNIZER_ENDPOINT_URI}` e `{FORM_RECOGNIZER_API_KEY}` , `{COMPUTER_VISION_API_KEY}`vedere raccolta dei [parametri obbligatori](#gathering-required-parameters) .

Sono disponibili [esempi](form-recognizer-container-configuration.md#example-docker-run-commands) di comando `docker run`.

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

## <a name="troubleshooting"></a>Risoluzione dei problemi

Quando viene eseguito, il contenitore usa **stdout** e **stderr** per generare informazioni utili per risolvere i problemi che si verificano durante l'avvio o l'esecuzione del contenitore.

## <a name="billing"></a>Fatturazione

I contenitori di Riconoscimento modulo inviano le informazioni di fatturazione ad Azure usando una risorsa di _Riconoscimento modulo_ nell'account Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](form-recognizer-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Riepilogo

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
