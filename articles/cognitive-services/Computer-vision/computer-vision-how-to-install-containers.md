---
title: Installare i contenitori Docker di lettura OCR da Visione artificiale
titleSuffix: Azure Cognitive Services
description: Usare i contenitori Docker di lettura OCR da Visione artificiale per estrarre il testo da immagini e documenti in locale.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: aahi
ms.custom: seodec18, cog-serv-seo-aug-2020
keywords: locale, OCR, Docker, contenitore
ms.openlocfilehash: b89d02107365872471f1dd5a7df07902b08f2031
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "96006908"
---
# <a name="install-read-ocr-docker-containers-preview"></a>Installare i contenitori Docker per l'OCR di lettura (anteprima) 

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

I contenitori consentono di eseguire le API di Visione artificiale nell'ambiente in uso. I contenitori sono ottimi per requisiti specifici di sicurezza e governance dei dati. In questo articolo si apprenderà come scaricare, installare ed eseguire Visione artificiale contenitori.

Il contenitore OCR *Read* consente di estrarre testo stampato e scritto a mano da immagini e documenti con supporto per formati di file JPEG, PNG, BMP, PDF e TIFF. Per ulteriori informazioni, vedere la [documentazione relativa all'API di lettura](concept-recognizing-text.md#read-api).

## <a name="read-32-preview-container"></a>Leggere 3,2-contenitore di anteprima

> [!NOTE]
> Il contenitore Read 3,0-Preview è stato deprecato. 

Il contenitore Read 3,2-Preview fornisce:
* Nuovi modelli per una maggiore accuratezza.
* Supporto per più lingue nello stesso documento
* Supporto per: olandese, inglese, francese, tedesco, italiano, portoghese e spagnolo.
* Una singola operazione per documenti e immagini.
* Supporto per documenti e immagini di dimensioni maggiori.
* Punteggi di confidenza da 0 a 1.
* Supporto per documenti con testo stampato e scritto a mano
* Supporto per il cinese semplificato e il giapponese.
* punteggi di confidenza ed etichette per testo stampato e scritto a mano. 
* Possibilità di estrarre il testo solo da una o più pagine selezionate in un documento.

Se si usa attualmente la lettura di contenitori 2,0, vedere la [Guida alla migrazione](read-container-migration-guide.md) per informazioni sulle modifiche apportate alle nuove versioni.

## <a name="prerequisites"></a>Prerequisiti

Prima di usare i contenitori, è necessario soddisfare i prerequisiti seguenti:

|Necessario|Scopo|
|--|--|
|Motore Docker| È necessario il motore Docker installato in un [computer host](#the-host-computer). Docker offre pacchetti che configurano l'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> **In Windows** Docker deve essere configurato anche per supportare i contenitori Linux.<br><br>|
|Familiarità con Docker | È opportuno avere una conoscenza di base dei concetti relativi a Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.| 
|Risorsa Visione artificiale |Per usare il contenitore, è necessario disporre di:<br><br>Una risorsa **visione artificiale** di Azure e la chiave API associata l'URI dell'endpoint. Entrambi i valori sono disponibili nelle pagine Panoramica e chiavi per la risorsa e sono necessari per avviare il contenitore.<br><br>**{API_KEY}**: una delle due chiavi di risorsa disponibili nella pagina **chiavi**<br><br>**{ENDPOINT_URI}**: endpoint fornito nella pagina **Panoramica**|

Se non si ha una sottoscrizione di Azure, prima di iniziare creare un [account gratuito](https://azure.microsoft.com/free/cognitive-services/).

## <a name="request-approval-to-run-the-container"></a>Richiedi l'approvazione per eseguire il contenitore

Compilare e inviare il [modulo di richiesta](https://aka.ms/csgate) per richiedere l'approvazione per l'esecuzione del contenitore. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Supporto Advanced Vector Extension

Il computer **host** è il computer in cui è in esecuzione il contenitore docker. L'host *deve supportare* [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). È possibile verificare il supporto di AVX2 negli host Linux con il comando seguente:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

> [!WARNING]
> Il computer host è *necessario* per supportare AVX2. Il contenitore *non* funzionerà correttamente senza supporto per AVX2.

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine del contenitore con `docker pull`

Sono disponibili le immagini del contenitore per la lettura.

| Contenitore | Container Registry/repository/nome dell'immagine |
|-----------|------------|
| Leggi 2,0-Anteprima | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| Leggi 3,2-anteprima | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.1` |

Usare il [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando per scaricare un'immagine del contenitore.

### <a name="docker-pull-for-the-read-container"></a>Pull Docker per il contenitore di lettura

# <a name="version-32-preview"></a>[Versione 3,2-Preview](#tab/version-3-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.1
```

# <a name="version-20-preview"></a>[Versione 2,0-Preview](#tab/version-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Come usare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usare il contenitore.

1. [Eseguire il contenitore](#run-the-container-with-docker-run), con le impostazioni di fatturazione necessarie. Sono disponibili altri [esempi](computer-vision-resource-container-config.md) del comando `docker run`. 
1. [Eseguire una query sull'endpoint di stima del contenitore](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con `docker run`

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire il contenitore. Per informazioni dettagliate su come ottenere i valori e, vedere [raccolta dei parametri obbligatori](#gathering-required-parameters) `{ENDPOINT_URI}` `{API_KEY}` .

[Examples](computer-vision-resource-container-config.md#example-docker-run-commands) `docker run` Sono disponibili esempi di comando.

# <a name="version-32-preview"></a>[Versione 3,2-Preview](#tab/version-3-2)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.1 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue il contenitore di lettura dall'immagine del contenitore.
* Alloca 8 core CPU e 18 gigabyte (GB) di memoria.
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.

# <a name="version-20-preview"></a>[Versione 2,0-Preview](#tab/version-2)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue il contenitore di lettura dall'immagine del contenitore.
* Alloca 8 core CPU e 16 gigabyte (GB) di memoria.
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.

---


Sono disponibili altri [esempi](./computer-vision-resource-container-config.md#example-docker-run-commands) del comando `docker run`. 

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](#billing).

Se è necessaria una velocità effettiva più elevata, ad esempio durante l'elaborazione di file a più pagine, è consigliabile distribuire più contenitori [in un cluster Kubernetes](deploy-computer-vision-on-premises.md)usando [archiviazione di Azure](../../storage/common/storage-account-create.md) e la [coda di Azure](../../storage/queues/storage-queues-introduction.md).

Se si usa archiviazione di Azure per archiviare le immagini per l'elaborazione, è possibile creare una [stringa di connessione](../../storage/common/storage-configure-connection-string.md) da usare quando si chiama il contenitore.

Per trovare la stringa di connessione:

1. Passare ad **account di archiviazione** nell'portale di Azure e trovare l'account.
2. Fare clic su **chiavi di accesso** nell'elenco di navigazione a sinistra.
3. La stringa di connessione sarà posizionata sotto la **stringa di connessione**

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Eseguire una query sull'endpoint di stima del contenitore

Il contenitore fornisce le API dell'endpoint di stima della query basata su REST. 

# <a name="version-32-preview"></a>[Versione 3,2-Preview](#tab/version-3-2)

Usare l'host `http://localhost:5000` per le API del contenitore. È possibile visualizzare il percorso di spavalderia in: `http://localhost:5000/swagger/vision-v3.2-preview-read/swagger.json` .

# <a name="version-20-preview"></a>[Versione 2,0-Preview](#tab/version-2)

Usare l'host `http://localhost:5000` per le API del contenitore. È possibile visualizzare il percorso di spavalderia in: `http://localhost:5000/swagger/vision-v2.0-preview-read/swagger.json` .

---

### <a name="asynchronous-read"></a>Lettura asincrona


# <a name="version-32-preview"></a>[Versione 3,2-Preview](#tab/version-3-2)

È possibile usare le `POST /vision/v3.2/read/analyze` `GET /vision/v3.2/read/operations/{operationId}` operazioni e in Concert per leggere in modo asincrono un'immagine, in modo analogo a come il servizio visione artificiale usa le operazioni REST corrispondenti. Il metodo POST asincrono restituirà un oggetto `operationId` che viene usato come identificatore per la richiesta HTTP Get.


Dall'interfaccia utente di spavalderia selezionare il `asyncBatchAnalyze` per espanderlo nel browser. Quindi selezionare **try it out**  >  **Choose file**. In questo esempio si userà l'immagine seguente:

![tabulazioni e spazi](media/tabs-vs-spaces.png)

Quando il POST asincrono viene eseguito correttamente, restituisce un codice di stato **HTTP 202** . Come parte della risposta, è presente un' `operation-location` intestazione che contiene l'endpoint di risultato per la richiesta.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.2/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`È l'URL completo a cui è possibile accedere tramite HTTP Get. Ecco la risposta JSON dall'esecuzione dell' `operation-location` URL dall'immagine precedente:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-09-02T10:30:14Z",
  "lastUpdatedDateTime": "2020-09-02T10:30:15Z",
  "analyzeResult": {
    "version": "3.2.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.12,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "language": "",
        "lines": [
          {
            "boundingBox": [58, 42, 314, 59, 311, 123, 56, 121],
            "text": "Tabs vs",
            "appearance": {
              "style": "handwriting",
              "styleConfidence": 0.999
            },
            "words": [
              {
                "boundingBox": [85, 45, 242, 62, 241, 122, 83, 123],
                "text": "Tabs",
                "confidence": 0.981
              },
              {
                "boundingBox": [258, 64, 314, 72, 314, 123, 256, 123],
                "text": "vs",
                "confidence": 0.958
              }
            ]
          },
          {
            "boundingBox": [286, 171, 415, 165, 417, 197, 287, 201],
            "text": "paces",
            "appearance": {
              "style": "print",
              "styleConfidence": 0.603
            },
            "words": [
              {
                "boundingBox": [303, 175, 415, 167, 415, 198, 306, 199],
                "text": "paces",
                "confidence": 0.918
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-20-preview"></a>[Versione 2,0-Preview](#tab/version-2)

È possibile usare le `POST /vision/v2.0/read/core/asyncBatchAnalyze` `GET /vision/v2.0/read/operations/{operationId}` operazioni e in Concert per leggere in modo asincrono un'immagine, in modo analogo a come il servizio visione artificiale usa le operazioni REST corrispondenti. Il metodo POST asincrono restituirà un oggetto `operationId` che viene usato come identificatore per la richiesta HTTP Get.

Dall'interfaccia utente di spavalderia selezionare il `asyncBatchAnalyze` per espanderlo nel browser. Quindi selezionare **try it out**  >  **Choose file**. In questo esempio si userà l'immagine seguente:

![tabulazioni e spazi](media/tabs-vs-spaces.png)

Quando il POST asincrono viene eseguito correttamente, restituisce un codice di stato **HTTP 202** . Come parte della risposta, è presente un' `operation-location` intestazione che contiene l'endpoint di risultato per la richiesta.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`È l'URL completo a cui è possibile accedere tramite HTTP Get. Ecco la risposta JSON dall'esecuzione dell' `operation-location` URL dall'immagine precedente:

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

---

> [!IMPORTANT]
> Se si distribuiscono più contenitori di lettura dietro un servizio di bilanciamento del carico, ad esempio in Docker Compose o Kubernetes, è necessario disporre di una cache esterna. Poiché il contenitore di elaborazione e il contenitore di richieste GET potrebbero non essere uguali, una cache esterna archivia i risultati e li condivide tra i contenitori. Per informazioni dettagliate sulle impostazioni della cache, vedere [configurare visione artificiale contenitori Docker](./computer-vision-resource-container-config.md).

### <a name="synchronous-read"></a>Lettura sincrona

Per leggere in modo sincrono un'immagine, è possibile usare l'operazione seguente. 

# <a name="version-32-preview"></a>[Versione 3,2-Preview](#tab/version-3-2)

`POST /vision/v3.2/read/syncAnalyze` 

# <a name="version-20-preview"></a>[Versione 2,0-Preview](#tab/version-2)

`POST /vision/v2.0/read/core/Analyze`

---

Quando l'immagine viene letta completamente, solo l'API restituisce una risposta JSON. L'unica eccezione è rappresentata dal caso in cui si verifichi un errore. Quando si verifica un errore, viene restituito il codice JSON seguente:

```json
{
    "status": "Failed"
}
```

L'oggetto di risposta JSON ha lo stesso oggetto grafico della versione asincrona. Se si è un utente JavaScript e si vuole usare l'indipendenza dai tipi, provare a usare TypeScript per eseguire il cast della risposta JSON.

Per un esempio di utilizzo, vedere il <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">sandbox typescript qui <span class="docon docon-navigate-external x-hidden-focus"></span></a> e selezionare **Run (Esegui** ) per visualizzarne la semplicità d'uso.

## <a name="stop-the-container"></a>Arrestare il contenitore

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si esegue il contenitore con un punto di [montaggio](./computer-vision-resource-container-config.md#mount-settings) di output e la registrazione attivata, il contenitore genera file di log utili per risolvere i problemi che si verificano durante l'avvio o l'esecuzione del contenitore.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fatturazione

I contenitori di servizi cognitivi inviano informazioni di fatturazione ad Azure, usando la risorsa corrispondente nell'account Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Riepilogo

In questo articolo sono stati descritti i concetti e il flusso di lavoro per scaricare, installare ed eseguire i contenitori Visione artificiale. In sintesi:

* Visione artificiale fornisce un contenitore Linux per Docker, incapsulando Read.
* Le immagini del contenitore vengono scaricate dal registro contenitori "anteprima contenitore" in Azure.
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile usare l'API REST o l'SDK per chiamare le operazioni nei contenitori di lettura specificando l'URI host del contenitore.
* Quando si crea un'istanza di un contenitore, è necessario specificare le informazioni di fatturazione.

> [!IMPORTANT]
> I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di Servizi cognitivi non inviano a Microsoft i dati dei clienti, ad esempio l'immagine o il testo analizzato.

## <a name="next-steps"></a>Passaggi successivi

* Rivedere [Configurare i contenitori](computer-vision-resource-container-config.md) per informazioni sulle impostazioni di configurazione.
* Rivedere [Panoramica di Visione artificiale](overview.md) per altre informazioni sul riconoscimento di testo scritto a mano e stampato
* Fare riferimento all'[API Visione artificiale](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) per informazioni dettagliate sui metodi supportati dal contenitore.
* Fare riferimento alle [domande frequenti](FAQ.md) per risolvere i problemi correlati alle funzionalità di Analisi del testo.
* Usare altri [contenitori di Servizi cognitivi](../cognitive-services-container-support.md)