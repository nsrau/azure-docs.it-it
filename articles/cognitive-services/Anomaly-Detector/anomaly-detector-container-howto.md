---
title: Come installare ed eseguire i contenitori per l'uso dell'API del rilevatore di anomalie
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
# <a name="install-and-run-anomaly-detector-containers-preview"></a>Installare ed eseguire contenitori di rilevatori di anomalie (anteprima)

Il rilevatore di anomalie presenta le funzionalità del contenitore seguenti:

| Funzione | Funzionalità |
|--|--|
| Rilevamento anomalie | <li> Rileva le anomalie che si verificano in tempo reale. <li> Rileva le anomalie nel set di dati come batch. <li> Deduce l'intervallo normale dei dati previsto. <li> Supporta la regolazione della sensibilità del rilevamento anomalie per adattarsi meglio ai dati. |

Per informazioni dettagliate sulle API, vedere:
* [Altre informazioni sul servizio API del rilevatore di anomalie](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti

Prima di usare i contenitori dei rilevatori di anomalie, è necessario soddisfare i prerequisiti seguenti:

|obbligatori|Scopo|
|--|--|
|Motore Docker| È necessario il motore Docker installato in un [computer host](#the-host-computer). Docker offre pacchetti per la configurazione dell'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> **In Windows** Docker deve essere configurato anche per supportare i contenitori Linux.<br><br>|
|Familiarità con Docker | È opportuno avere una conoscenza di base dei concetti relativi a Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.| 
|Risorsa rilevamento anomalie |Per usare questi contenitori, è necessario avere:<br><br>Risorsa di _Rilevamento anomalie_ di Azure per ottenere la chiave API e l'URI dell'endpoint associati. Entrambi i valori sono disponibili nelle pagine relative alla panoramica del **rilevatore di anomalie** del portale di Azure e alle pagine delle chiavi e sono necessarie per avviare il contenitore.<br><br>**{API_KEY}** : una delle due chiavi di risorsa disponibili nella pagina **chiavi**<br><br>**{ENDPOINT_URI}** : endpoint fornito nella pagina **Panoramica**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-container-registry"></a>Richiedere l'accesso al registro contenitori

È necessario prima completare e inviare il [modulo di richiesta del contenitore del rilevatore di anomalie](https://aka.ms/adcontainer) per richiedere l'accesso al contenitore.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

La tabella seguente descrive i core CPU minimi e consigliati e la memoria da allocare per il contenitore del rilevatore di anomalie.

| QUERY al secondo (query al secondo) | Minima | Consigliato |
|-----------|---------|-------------|
| 10 QUERY AL SECONDO | 4 core, 1 GB di memoria | 8 Core 2-GB di memoria |
| 20 QUERY AL SECONDO | 8 core, 2 GB di memoria | 16 core da 4 GB di memoria |

Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine del contenitore con `docker pull`

Usare il comando [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare un'immagine del contenitore.

| Contenitore: | Repository |
|-----------|------------|
| cognitive-services-anomaly-detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Pull di Docker per il contenitore del rilevatore di anomalie

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Come usare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usare il contenitore.

1. [Eseguire il contenitore](#run-the-container-with-docker-run), con le impostazioni di fatturazione necessarie. Sono disponibili altri [esempi](anomaly-detector-container-configuration.md#example-docker-run-commands) del comando `docker run`.
1. [Eseguire le query sull'endpoint di stima del contenitore](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con `docker run`

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire il contenitore. Per informazioni dettagliate su come ottenere i valori `{ENDPOINT_URI}` e `{API_KEY}`, vedere [raccolta dei parametri obbligatori](#gathering-required-parameters) .

Sono disponibili [esempi](anomaly-detector-container-configuration.md#example-docker-run-commands) del comando `docker run`.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue un contenitore di rilevatori di anomalie dall'immagine del contenitore
* Alloca un core CPU e 4 GB di memoria
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host. 

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Esecuzione di più contenitori nello stesso host

Se si intende eseguire più contenitori con porte esposte, assicurarsi di eseguire ogni contenitore con una porta diversa. Eseguire ad esempio il primo contenitore sulla porta 5000 e il secondo sulla porta 5001.

Sostituire `<container-registry>` e `<container-name>` con i valori dei contenitori usati. Questi non devono trovarsi necessariamente nello stesso contenitore. È possibile fare in modo che il contenitore dei rilevatori di anomalie e il contenitore LUIS siano in esecuzione nell'HOST oppure che siano in esecuzione più contenitori di rilevamento anomalie. 

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

I contenitori di anomalie Detector inviano informazioni di fatturazione ad Azure, usando una risorsa del _rilevatore di anomalie_ nell'account Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](anomaly-detector-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>summary

In questo articolo sono stati illustrati i concetti e il flusso di lavoro per il download, l'installazione e l'esecuzione di contenitori di rilevamento anomalie. In sintesi:

* Il rilevatore di anomalie fornisce un contenitore Linux per Docker, incapsulando il rilevamento delle anomalie con batch e streaming, l'inferenza prevista e l'ottimizzazione della sensibilità.
* Le immagini del contenitore vengono scaricate da un'anteprima privata di Azure Container Registry dedicata per i contenitori.
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile usare l'API REST o l'SDK per chiamare le operazioni nei contenitori dei rilevatori di anomalie specificando l'URI host del contenitore.
* Quando si crea un'istanza di un contenitore, è necessario specificare le informazioni di fatturazione.

> [!IMPORTANT]
> I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di servizi cognitivi non inviano i dati dei clienti (ad esempio, i dati delle serie temporali analizzati) a Microsoft.

## <a name="next-steps"></a>Passaggi successivi

* Rivedere [Configurare i contenitori](anomaly-detector-container-configuration.md) per informazioni sulle impostazioni di configurazione.
* [Distribuire un contenitore di rilevatori di anomalie in istanze di contenitore di Azure](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Altre informazioni sul servizio API del rilevatore di anomalie](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
