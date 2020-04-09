---
title: Come installare ed eseguire contenitori per l'utilizzo di Anomaly Detector API
titleSuffix: Azure Cognitive Services
description: Usare gli algoritmi avanzati dell'API Rilevamento anomalie per identificare le anomalie nei dati di serie temporali.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: fa25d27e99a9516d461a84dde184e2a6412baa0b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875046"
---
# <a name="install-and-run-anomaly-detector-containers-preview"></a>Installare ed eseguire contenitori Anomaly Detector (anteprima)Install and run Anomaly Detector containers (Preview)

Il rilevatore di anomalie dispone delle seguenti funzionalità di funzionalità contenitore:

| Funzione | Funzionalità |
|--|--|
| Rilevatore di anomalie | <li> Rileva le anomalie quando si verificano in tempo reale. <li> Rileva le anomalie in tutto il set di dati come batch. <li> Deduce l'intervallo normale previsto dei dati. <li> Supporta la regolazione della sensibilità di rilevamento delle anomalie per adattarli meglio ai dati. |

Per informazioni dettagliate sulle API, vedere:
* [Ulteriori informazioni sul servizio API Anomaly Detector](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di utilizzare i contenitori Rilevatore anomalie, è necessario soddisfare i prerequisiti seguenti:You must meet the following prerequisites before using Anomaly Detector containers:

|Obbligatoria|Scopo|
|--|--|
|Motore Docker| È necessario il motore Docker installato in un [computer host](#the-host-computer). Docker offre pacchetti che configurano l'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> **In Windows** Docker deve essere configurato anche per supportare i contenitori Linux.<br><br>|
|Familiarità con Docker | È opportuno avere una conoscenza di base dei concetti relativi a Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.| 
|Risorsa rilevatore di anomalie |Per usare questi contenitori, è necessario avere:<br><br>Una risorsa _Azure Anomaly Detector_ per ottenere la chiave API associata e l'URI dell'endpoint. Entrambi i valori sono disponibili nelle pagine Panoramica **rilevatore di anomalie** e chiavi del portale di Azure e sono necessari per avviare il contenitore.<br><br>**API_KEY :Una**delle due chiavi di risorsa disponibili nella pagina **Chiavi**<br><br>**ENDPOINT_URI :** l'endpoint come indicato nella pagina **Panoramica**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-container-registry"></a>Richiedere l'accesso al registro contenitori

È innanzitutto necessario completare e inviare il [modulo di richiesta del contenitore del rilevatore](https://aka.ms/adcontainer) di anomalie per richiedere l'accesso al contenitore.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

Nella tabella seguente vengono descritti i core CPU e la memoria minimi e consigliati da allocare per il contenitore Rilevatore anomalie.

| QPS(Query al secondo) | Minima | Consigliato |
|-----------|---------|-------------|
| 10 QPS | 4 core, 1 GB di memoria | 8 core 2 GB di memoria |
| 20 QPS | 8 core, 2 GB di memoria | 16 core di 4 GB di memoria |

Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine del contenitore con `docker pull`

Utilizzare [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) il comando per scaricare un'immagine contenitore.

| Contenitore | Archivio |
|-----------|------------|
| cognitivo-servizi-anomalia-rivelatore | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Docker tirare per il contenitore Rilevatore Anomaly

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Come usare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usare il contenitore.

1. [Eseguire il contenitore](#run-the-container-with-docker-run), con le impostazioni di fatturazione necessarie. Sono disponibili altri [esempi](anomaly-detector-container-configuration.md#example-docker-run-commands) del comando `docker run`.
1. [Eseguire una query sull'endpoint di stima del contenitore.](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con `docker run`

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire il contenitore. Fare riferimento alla raccolta dei [parametri](#gathering-required-parameters) `{ENDPOINT_URI}` obbligatori `{API_KEY}` per informazioni dettagliate su come ottenere i valori e .

[Sono](anomaly-detector-container-configuration.md#example-docker-run-commands) disponibili `docker run` esempi del comando.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue un contenitore Rilevatore anomalia dall'immagine del contenitore
* Alloca un core CPU e 4 GB di memoria
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host. 

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Esecuzione di più contenitori nello stesso hostRunning multiple containers on the same host

Se si intende eseguire più contenitori con porte esposte, assicurarsi di eseguire ogni contenitore con una porta diversa. Eseguire ad esempio il primo contenitore sulla porta 5000 e il secondo sulla porta 5001.

Sostituire `<container-registry>` e `<container-name>` con i valori dei contenitori usati. Questi non devono trovarsi necessariamente nello stesso contenitore. È possibile fare in modo che il contenitore Rilevatore di anomalie e il contenitore LUIS in esecuzione su HOST siano in esecuzione su HOST oppure che siano in esecuzione più contenitori Rilevatore di anomalie. 

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

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si esegue il contenitore con un punto di [montaggio](anomaly-detector-container-configuration.md#mount-settings) di output e la registrazione attivata, il contenitore genera file di log utili per risolvere i problemi che si verificano durante l'avvio o l'esecuzione del contenitore.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fatturazione

I contenitori Rilevatore anomalie inviano informazioni di fatturazione ad Azure usando una risorsa Rilevatore anomalie nell'account Azure.The Anomaly Detector containers send billing information to Azure, using an _Anomaly Detector_ resource on your Azure account. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](anomaly-detector-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Summary

In questo articolo sono stati illustrati i concetti e il flusso di lavoro per il download, l'installazione e l'esecuzione di contenitori Rilevatore anomalie. In sintesi:

* Rilevatore di anomalie fornisce un contenitore Linux per Docker, incapsulando il rilevamento delle anomalie con batch vs streaming, inferenza dell'intervallo previsto e ottimizzazione della sensibilità.
* Le immagini dei contenitori vengono scaricate da un Registro contenitori di Azure privato dedicato all'anteprima dei contenitori.
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile usare l'API REST o l'SDK per chiamare le operazioni nei contenitori di Anomaly Detector specificando l'URI host del contenitore.
* Quando si crea un'istanza di un contenitore, è necessario specificare le informazioni di fatturazione.

> [!IMPORTANT]
> I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di Servizi cognitivi non inviano i dati dei clienti (ad esempio, i dati della serie temporale analizzati) a Microsoft.

## <a name="next-steps"></a>Passaggi successivi

* Esaminare [Configurare i contenitori](anomaly-detector-container-configuration.md) per le impostazioni di configurazioneReview Configure containers for configuration settings
* [Distribuire un contenitore Anomaly Detector nelle istanze del contenitore di AzureDeploy an Anomaly Detector container to Azure Container Instances](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Ulteriori informazioni sul servizio API Anomaly Detector](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
