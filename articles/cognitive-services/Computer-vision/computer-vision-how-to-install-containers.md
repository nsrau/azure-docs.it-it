---
title: Come installare ed eseguire i contenitori - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Come scaricare, installare ed eseguire i contenitori per Visione artificiale in questa esercitazione dettagliata.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 5f36c429041a8182551d1f077f0a1229f520e8c1
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879344"
---
# <a name="install-and-run-read-containers-preview"></a>Installare ed eseguire contenitori di lettura (anteprima)Install and run Read containers (Preview)

I contenitori consentono di eseguire le API di Visione artificiale nel proprio ambiente. I contenitori sono ideali per requisiti specifici di sicurezza e governance dei dati. In questo articolo verrà illustrato come scaricare, installare ed eseguire un contenitore Visione artificiale.

Un singolo contenitore Docker, *Read*, è disponibile per Visione artificiale. Il contenitore *Lettura* consente di rilevare ed estrarre *testo stampato* da immagini di vari oggetti con superfici e sfondi diversi, ad esempio ricevute, poster e biglietti da visita. Inoltre, il contenitore *Lettura* rileva *il testo scritto a mano* nelle immagini e fornisce il supporto per file PDF, TIFF e multipagina. Per altre informazioni, vedere la documentazione relativa all'API [di lettura.](concept-recognizing-text.md#read-api)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di utilizzare i contenitori, è necessario soddisfare i prerequisiti seguenti:

|Obbligatoria|Scopo|
|--|--|
|Motore Docker| È necessario il motore Docker installato in un [computer host](#the-host-computer). Docker offre pacchetti che configurano l'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> **In Windows** Docker deve essere configurato anche per supportare i contenitori Linux.<br><br>|
|Familiarità con Docker | È opportuno avere una conoscenza di base dei concetti relativi a Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.| 
|Risorsa Visione artificiale |Per usare il contenitore, è necessario disporre di:<br><br>Una risorsa **di Azure Computer Vision** e la chiave API associata l'URI dell'endpoint. Entrambi i valori sono disponibili nelle pagine Panoramica e Chiavi per la risorsa e sono necessari per avviare il contenitore.<br><br>**API_KEY :Una**delle due chiavi di risorsa disponibili nella pagina **Chiavi**<br><br>**ENDPOINT_URI :** l'endpoint come indicato nella pagina **Panoramica**|

## <a name="request-access-to-the-private-container-registry"></a>Richiedere l'accesso al registro contenitori privato

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Supporto per Advanced Vector Extension

Il computer **host** è il computer che esegue il contenitore docker. L'host *deve supportare* [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). È possibile verificare il supporto AVX2 su host Linux con il seguente comando:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> Il computer host è *necessario* per supportare AVX2. Il contenitore *non funzionerà* correttamente senza il supporto DI AVX2.

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine del contenitore con `docker pull`

Sono disponibili immagini contenitore per la lettura.

| Contenitore | Registro contenitori / Repository / Nome immagine |
|-----------|------------|
| Lettura | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

Utilizzare [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) il comando per scaricare un'immagine contenitore.

### <a name="docker-pull-for-the-read-container"></a>Pull Docker per il contenitore Read

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Come usare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usare il contenitore.

1. [Eseguire il contenitore](#run-the-container-with-docker-run), con le impostazioni di fatturazione necessarie. Sono disponibili altri [esempi](computer-vision-resource-container-config.md) del comando `docker run`. 
1. [Eseguire una query sull'endpoint di stima del contenitore.](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con `docker run`

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire il contenitore. Fare riferimento alla raccolta dei [parametri](#gathering-required-parameters) `{ENDPOINT_URI}` obbligatori `{API_KEY}` per informazioni dettagliate su come ottenere i valori e .

[Sono](computer-vision-resource-container-config.md#example-docker-run-commands) disponibili `docker run` esempi del comando.

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-read \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue il contenitore Read dall'immagine del contenitore.
* Alloca 8 core CPU e 16 gigabyte (GB) di memoria.
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.

Sono disponibili altri [esempi](./computer-vision-resource-container-config.md#example-docker-run-commands) del comando `docker run`. 

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Eseguire query sull'endpoint di stima del contenitore

Il contenitore fornisce API dell'endpoint di stima di query basate su REST. 

Usare l'host, `http://localhost:5000`, per le API del contenitore.

### <a name="asynchronous-read"></a>Lettura asincrona

È possibile `POST /vision/v2.0/read/core/asyncBatchAnalyze` usare `GET /vision/v2.0/read/operations/{operationId}` le operazioni e in concerto per leggere in modo asincrono un'immagine, in modo simile a come il servizio Visione artificiale utilizza le operazioni REST corrispondenti. Il metodo POST asincrono restituirà un `operationId` oggetto che viene utilizzato come identificatore per la richiesta HTTP GET.

Nell'interfaccia utente swagger, selezionare il `asyncBatchAnalyze` per espanderlo nel browser. Quindi selezionare **Prova** > **Scegli file**. In questo esempio verrà usata l'immagine seguente:In this example, we'll use the following image:

![tabulazioni vs spazi](media/tabs-vs-spaces.png)

Quando l'esecuzione post asincrona è stata eseguita correttamente, restituisce un codice di stato **HTTP 202.When** the asynchronous POST has run successfully, it returns an HTTP 202 status code. Come parte della risposta, `operation-location` è presente un'intestazione che contiene l'endpoint del risultato per la richiesta.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

L'URL `operation-location` è completo ed è accessibile tramite un HTTP GET. Ecco la risposta JSON dall'esecuzione dell'URL dall'immagine precedente:Here is the JSON response from executing the `operation-location` URL from the preceding image:

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="synchronous-read"></a>Lettura sincrona

È possibile `POST /vision/v2.0/read/core/Analyze` utilizzare l'operazione per leggere in modo sincrono un'immagine. Quando l'immagine viene letta nella sua interezza, l'API restituisce una risposta JSON. L'unica eccezione è se si verifica un errore. Quando si verifica un errore viene restituito il seguente JSON:

```json
{
    status: "Failed"
}
```

L'oggetto risposta JSON ha lo stesso oggetto grafico della versione asincrona. Se sei un utente JavaScript e vuoi l'indipendenza dai tipi, i `AnalyzeResult` tipi seguenti potrebbero essere usati per eseguire il cast della risposta JSON come oggetto.

```typescript
export interface AnalyzeResult {
    status: Status;
    recognitionResults?: RecognitionResult[] | null;
}

export enum Status {
    NotStarted = 0,
    Running = 1,
    Failed = 2,
    Succeeded = 3
}

export enum Unit {
    Pixel = 0,
    Inch = 1
}

export interface RecognitionResult {
    page?: number | null;
    clockwiseOrientation?: number | null;
    width?: number | null;
    height?: number | null;
    unit?: Unit | null;
    lines?: Line[] | null;
}

export interface Line {
    boundingBox?: number[] | null;
    text: string;
    words?: Word[] | null;
}

export enum Confidence {
    High = 0,
    Low = 1
}

export interface Word {
  boundingBox?: number[] | null;
  text: string;
  confidence?: Confidence | null;
}
```

Per un esempio di caso d'uso, vedere la <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">sandbox TypeScript qui <span class="docon docon-navigate-external x-hidden-focus"></span> </a> e selezionare **Esegui** per visualizzarne la facilità d'uso.

## <a name="stop-the-container"></a>Arrestare il contenitore

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si esegue il contenitore con un punto di [montaggio](./computer-vision-resource-container-config.md#mount-settings) di output e la registrazione attivata, il contenitore genera file di log utili per risolvere i problemi che si verificano durante l'avvio o l'esecuzione del contenitore.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fatturazione

I contenitori di Servizi cognitivi inviano le informazioni di fatturazione ad Azure usando la risorsa corrispondente nell'account Azure.The Cognitive Services containers send billing information to Azure, using the corresponding resource on your Azure account.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Summary

In questo articolo sono stati descritti i concetti e il flusso di lavoro per scaricare, installare ed eseguire i contenitori Visione artificiale. In sintesi:

* Visione artificiale fornisce un contenitore Linux per Docker, incapsulando Read.
* Container images are downloaded from the "Container Preview" container registry in Azure.
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile usare l'API REST o l'SDK per chiamare le operazioni nei contenitori di lettura specificando l'URI host del contenitore.
* Quando si crea un'istanza di un contenitore, è necessario specificare le informazioni di fatturazione.

> [!IMPORTANT]
> I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di Servizi cognitivi non inviano a Microsoft i dati dei clienti, ad esempio l'immagine o il testo analizzato.

## <a name="next-steps"></a>Passaggi successivi

* Esaminare [Configurare i contenitori](computer-vision-resource-container-config.md) per le impostazioni di configurazioneReview Configure containers for configuration settings
* Rivedere [Panoramica di Visione artificiale](Home.md) per altre informazioni sul riconoscimento di testo scritto a mano e stampato
* Fare riferimento all'[API Visione artificiale](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) per informazioni dettagliate sui metodi supportati dal contenitore.
* Fare riferimento alle [domande frequenti](FAQ.md) per risolvere i problemi correlati alle funzionalità di Analisi del testo.
* Usare più [contenitori di Servizi cognitiviUse more Cognitive Services Containers](../cognitive-services-container-support.md)
