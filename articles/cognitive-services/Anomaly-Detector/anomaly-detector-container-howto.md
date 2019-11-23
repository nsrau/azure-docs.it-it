---
title: How to install and run containers for using Anomaly Detector API
titleSuffix: Azure Cognitive Services
description: Usare gli algoritmi avanzati dell'API Rilevamento anomalie per identificare le anomalie nei dati di serie temporali.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 45abd904ea95cf8e68583ba5630a485af59479ec
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327265"
---
# <a name="install-and-run-anomaly-detector-containers-preview"></a>Install and run Anomaly Detector containers (Preview)

The Anomaly Detector has the following container feature functionality:

| Funzione | database elastico |
|--|--|
| Anomaly detector | <li> Detects anomalies as they occur in real-time. <li> Detects anomalies throughout your data set as a batch. <li> Infers the expected normal range of your data. <li> Supports anomaly detection sensitivity adjustment to better fit your data. |

For detailed information about the APIs, please see:
* [Learn more about Anomaly Detector API service](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

You must meet the following prerequisites before using Anomaly Detector containers:

|Obbligatoria|Finalità|
|--|--|
|Motore Docker| È necessario il motore Docker installato in un [computer host](#the-host-computer). Docker offre pacchetti per la configurazione dell'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> **In Windows** Docker deve essere configurato anche per supportare i contenitori Linux.<br><br>|
|Familiarità con Docker | È opportuno avere una conoscenza di base dei concetti relativi a Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.| 
|Anomaly Detector resource |Per usare questi contenitori, è necessario avere:<br><br>An Azure _Anomaly Detector_ resource to get the associated API key and endpoint URI. Both values are available on the Azure portal's **Anomaly Detector** Overview and Keys pages and are required to start the container.<br><br>**{API_KEY}** : One of the two available resource keys on the **Keys** page<br><br>**{ENDPOINT_URI}** : The endpoint as provided on the **Overview** page|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-container-registry"></a>Richiedere l'accesso al registro contenitori

You must first complete and submit the [Anomaly Detector Container Request form](https://aka.ms/adcontainer) to request access to the container.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

The following table describes the minimum and recommended CPU cores and memory to allocate for Anomaly Detector container.

| QPS(Queries per second) | Minima | Consigliato |
|-----------|---------|-------------|
| 10 QPS | 4 core, 1-GB memory | 8 core 2-GB memory |
| 20 QPS | 8 core, 2-GB memory | 16 core 4-GB memory |

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

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Docker pull for the Anomaly Detector container

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Come usare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usare il contenitore.

1. [Eseguire il contenitore](#run-the-container-with-docker-run), con le impostazioni di fatturazione necessarie. Sono disponibili altri [esempi](anomaly-detector-container-configuration.md#example-docker-run-commands) del comando `docker run`.
1. [Eseguire le query sull'endpoint di stima del contenitore](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con `docker run`

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire il contenitore. Refer to [gathering required parameters](#gathering-required-parameters) for details on how to get the `{ENDPOINT_URI}` and `{API_KEY}` values.

[Examples](anomaly-detector-container-configuration.md#example-docker-run-commands) of the `docker run` command are available.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Runs an Anomaly Detector container from the container image
* Alloca un core CPU e 4 GB di memoria
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host. 

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore e avviarlo; altrimenti il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Running multiple containers on the same host

Se si intende eseguire più contenitori con porte esposte, assicurarsi di eseguire ogni contenitore con una porta diversa. Eseguire ad esempio il primo contenitore sulla porta 5000 e il secondo sulla porta 5001.

Sostituire `<container-registry>` e `<container-name>` con i valori dei contenitori usati. Questi non devono trovarsi necessariamente nello stesso contenitore. You can have the Anomaly Detector container and the LUIS container running on the HOST together or you can have multiple Anomaly Detector containers running. 

Eseguire il primo contenitore sulla porta 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Eseguire il secondo contenitore sulla porta 5001.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Ogni contenitore successivo deve essere su una porta diversa. 

## <a name="query-the-containers-prediction-endpoint"></a>Eseguire query sull'endpoint di stima del contenitore

Il contenitore fornisce API dell'endpoint di stima di query basate su REST. 

Usare l'host, http://localhost:5000, per le API del contenitore.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrestare il contenitore

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>risoluzione dei problemi

Se si esegue il contenitore con un punto di [montaggio](anomaly-detector-container-configuration.md#mount-settings) di output e la registrazione attivata, il contenitore genera file di log utili per risolvere i problemi che si verificano durante l'avvio o l'esecuzione del contenitore.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fatturazione

The Anomaly Detector containers send billing information to Azure, using an _Anomaly Detector_ resource on your Azure account. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](anomaly-detector-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Summary

In this article, you learned concepts and workflow for downloading, installing, and running Anomaly Detector containers. In sintesi:

* Anomaly Detector provides one Linux container for Docker, encapsulating anomaly detection with batch vs streaming, expected range inference, and sensitivity tuning.
* Container images are downloaded from a private Azure Container Registry dedicated for containers preview.
* Le immagini dei contenitori vengono eseguite in Docker.
* You can use either the REST API or SDK to call operations in Anomaly Detector containers by specifying the host URI of the container.
* Quando si crea un'istanza di un contenitore, è necessario specificare le informazioni di fatturazione.

> [!IMPORTANT]
> I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. Cognitive Services containers do not send customer data (e.g., the time series data that is being analyzed) to Microsoft.

## <a name="next-steps"></a>Passaggi successivi

* Rivedere [Configurare i contenitori](anomaly-detector-container-configuration.md) per informazioni sulle impostazioni di configurazione.
* [Deploy an Anomaly Detector container to Azure Container Instances](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Learn more about Anomaly Detector API service](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
