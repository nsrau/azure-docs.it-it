---
title: Installare ed eseguire i contenitori - rilevatore di anomalie
titleSuffix: Azure Cognitive Services
description: Usare algoritmi avanzati dell'API di rilevamento delle anomalie per identificare le anomalie nei dati delle serie temporali.
services: cognitive-services
author: IEvangelist
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 06/10/2019
ms.author: dapine
ms.openlocfilehash: 0369e27e7a260c50fc4de690dd3261421356799a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073336"
---
# <a name="install-and-run-anomaly-detector-containers"></a>Installare ed eseguire i contenitori di rilevatore di anomalie

Il rilevatore di anomalie ha contenitore seguente: 

|Funzione|Funzionalità|
|-|-|
|Rilevamento anomalie| <li> Rileva le anomalie appena si verificano in tempo reale. <li> Rileva le anomalie in tutto il set di dati come batch. <li> Deduce normale intervallo di dati previsto. <li> Regolazione del tra maiuscole e minuscole rilevamento anomalie supporta per una migliore adatta i dati. |

Per informazioni dettagliate sulle API, vedere:
* [Altre informazioni sul servizio API rilevatore di anomalie](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

È necessario soddisfare i prerequisiti seguenti prima di usare i contenitori di rilevatore di anomalie:

|Obbligatorio|Scopo|
|--|--|
|Motore Docker| È necessario il motore Docker installato in un [computer host](#the-host-computer). Docker offre pacchetti per la configurazione dell'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> **In Windows** Docker deve essere configurato anche per supportare i contenitori Linux.<br><br>|
|Familiarità con Docker | È opportuno avere una conoscenza di base dei concetti relativi a Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.| 
|Risorsa di rilevamento anomalie |Per usare questi contenitori, è necessario avere:<br><br>Un' _rilevatore di anomalie_ risorse di Azure per ottenere la chiave di fatturazione associata e l'URI dell'endpoint di fatturazione. Entrambi i valori sono disponibili nelle pagine di panoramica di rilevatore di anomalie e le chiavi del portale di Azure e sono necessari per avviare il contenitore.<br><br>**{BILLING_KEY}** : chiave della risorsa<br><br>**{BILLING_ENDPOINT_URI}** : un esempio di URI dell'endpoint è: `https://westus2.api.cognitive.microsoft.com`|

## <a name="request-access-to-the-container-registry"></a>Richiedere l'accesso al registro contenitori

È necessario innanzitutto completare e inviare il [modulo di richiesta contenitore rilevatore di anomalie](https://aka.ms/adcontainer) per richiedere l'accesso al contenitore.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

La tabella seguente descrive i minimi e consigliati di core CPU e memoria da allocare per il contenitore di rilevatore di anomalie.

| Query al secondo (query al secondo) | Minima | Consigliato |
|-----------|---------|-------------|
| 10 QUERY AL SECONDO | 4 core, 1GB di memoria | 8 core 2GB di memoria |
| 20 QUERY AL SECONDO | 8 core, 2GB di memoria | 16 core 4GB di memoria |

Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine del contenitore con `docker pull`

Usare il comando [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare un'immagine del contenitore.

| Contenitore | Repository |
|-----------|------------|
| cognitive-services-anomaly-detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-anomaly-detector-container"></a>Pull docker per il contenitore di rilevatore di anomalie

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Come usare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usare il contenitore.

1. [Eseguire il contenitore](#run-the-container-with-docker-run), con le impostazioni di fatturazione necessarie. Sono disponibili altri [esempi](anomaly-detector-container-configuration.md#example-docker-run-commands) del comando `docker run`. 
1. [Eseguire le query sull'endpoint di stima del contenitore](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con `docker run`

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire uno qualsiasi dei tre contenitori. Il comando usa i parametri seguenti:

| Placeholder | Value |
|-------------|-------|
|{BILLING_KEY} | Questa chiave viene usata per avviare il contenitore e è disponibile nella pagina chiavi di rilevamento delle anomalie del portale di Azure.  |
|{BILLING_ENDPOINT_URI} | Il valore URI dell'endpoint di fatturazione è disponibile nella pagina di panoramica di rilevatore di anomalie del portale di Azure.|

Sostituire i parametri con i valori personalizzati nel comando `docker run` di esempio seguente.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Questo comando:

* Esegue un contenitore di rilevatore di anomalie dall'immagine del contenitore
* Alloca un core CPU e 4 GB di memoria
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host. 

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Esecuzione di più contenitori nello stesso host

Se si intende eseguire più contenitori con porte esposte, assicurarsi di eseguire ogni contenitore con una porta diversa. Eseguire ad esempio il primo contenitore sulla porta 5000 e il secondo sulla porta 5001.

Sostituire `<container-registry>` e `<container-name>` con i valori dei contenitori usati. Questi non devono trovarsi necessariamente nello stesso contenitore. È possibile che il contenitore di rilevatore di anomalie e il contenitore di LUIS in esecuzione contemporaneamente nell'HOST oppure è possibile avere più contenitori di rilevatore di anomalie in esecuzione. 

Eseguire il primo contenitore sulla porta 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Eseguire il secondo contenitore sulla porta 5001.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Ogni contenitore successivo deve essere su una porta diversa. 

## <a name="query-the-containers-prediction-endpoint"></a>Eseguire query sull'endpoint di stima del contenitore

Il contenitore fornisce API dell'endpoint di stima di query basate su REST. 

Usare l'host, https://localhost:5000, per le API del contenitore.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrestare il contenitore

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>risoluzione dei problemi

Se si esegue il contenitore con un punto di [montaggio](anomaly-detector-container-configuration.md#mount-settings) di output e la registrazione attivata, il contenitore genera file di log utili per risolvere i problemi che si verificano durante l'avvio o l'esecuzione del contenitore. 

## <a name="billing"></a>Fatturazione

L'invio di contenitori rilevatore di anomalie, fatturazione di Azure, usando un _rilevatore di anomalie_ risorse nell'account Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](anomaly-detector-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Riepilogo

In questo articolo si è appreso i concetti e flusso di lavoro per scaricare, installare ed eseguire i contenitori di rilevatore di anomalie. In sintesi:

* Rilevatore di anomalie fornisce un contenitore Linux per Docker, che incapsula il rilevamento delle anomalie con Visual Studio batch, streaming, l'inferenza di intervallo previsto e l'ottimizzazione tra maiuscole e minuscole.
* Le immagini del contenitore vengono scaricate da un registro contenitori di Azure privato dedicato per l'anteprima dei contenitori.
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile utilizzare l'API REST o SDK di chiamare le operazioni nei contenitori di rilevatore di anomalie, specificando l'URI del contenitore di host.
* Quando si crea un'istanza di un contenitore, è necessario specificare le informazioni di fatturazione.

> [!IMPORTANT]
> I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di servizi cognitivi non inviare i dati dei clienti (ad esempio, la serie temporale che vengano viene analizzate) a Microsoft.

## <a name="next-steps"></a>Passaggi successivi

* Rivedere [Configurare i contenitori](anomaly-detector-container-configuration.md) per informazioni sulle impostazioni di configurazione.
* [Altre informazioni sul servizio API rilevatore di anomalie](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
