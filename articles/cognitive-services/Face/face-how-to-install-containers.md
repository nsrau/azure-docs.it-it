---
title: Install and run containers - FACE API
titleSuffix: Azure Cognitive Services
description: This article shows you how to download, install, and run containers for Face in this walkthrough tutorial.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 574f6bead9cac384c72d2d0cd35353eb571a9490
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327049"
---
# <a name="install-and-run-face-containers-preview"></a>Install and run Face containers (Preview)

Azure Cognitive Services Face provides a standardized Linux container for Docker that detects human faces in images. It also identifies attributes, which include face landmarks such as noses and eyes, gender, age, and other machine-predicted facial features. In addition to detection, Face can check if two faces in the same image or different images are the same by using a confidence score. Face also can compare faces against a database to see if a similar-looking or identical face already exists. It also can organize similar faces into groups by using shared visual traits.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

You must meet the following prerequisites before you use the Face API containers.

|Obbligatoria|Finalità|
|--|--|
|Motore Docker| The Docker Engine must be installed on a [host computer](#the-host-computer). Docker offre pacchetti per la configurazione dell'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> On Windows, Docker also must be configured to support Linux containers.<br><br>|
|Familiarità con Docker | You need a basic understanding of Docker concepts, such as registries, repositories, containers, and container images. You also need knowledge of basic `docker` commands.| 
|Face resource |To use the container, you must have:<br><br>An Azure **Face** resource and the associated API key and the endpoint URI. Both values are available on the **Overview** and **Keys** pages for the resource. They're required to start the container.<br><br>**{API_KEY}** : One of the two available resource keys on the **Keys** page<br><br>**{ENDPOINT_URI}** : The endpoint as provided on the **Overview** page

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>Richiedere l'accesso al registro contenitori privato

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

La tabella seguente indica il valore minimo e consigliato per CPU e memoria da allocare per ogni contenitore di API Viso.

| Contenitore | Minima | Consigliato | Transazioni al secondo<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|Viso | 1 core, 2-GB memory | 1 core, 4-GB memory |10, 20|

* Each core must be at least 2.6 GHz or faster.
* Transactions per second (TPS).

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Get the container image with docker pull

Container images for the Face API are available. 

| Contenitore | Repository |
|-----------|------------|
| Viso | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Docker pull per il contenitore di Viso

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Use the container

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usarlo.

1. [Run the container](#run-the-container-with-docker-run) with the required billing settings. Sono disponibili altri [esempi](./face-resource-container-config.md#example-docker-run-commands) del comando `docker run`. 
1. [Eseguire le query sull'endpoint di stima del contenitore](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Run the container with docker run

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire il contenitore. Refer to [gathering required parameters](#gathering-required-parameters) for details on how to get the `{ENDPOINT_URI}` and `{API_KEY}` values.

[Examples](face-resource-container-config.md#example-docker-run-commands) of the `docker run` command are available.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Runs a face container from the container image.
* Allocates one CPU core and 4 GB of memory.
* Exposes TCP port 5000 and allocates a pseudo TTY for the container.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host. 

Sono disponibili altri [esempi](./face-resource-container-config.md#example-docker-run-commands) del comando `docker run`. 

> [!IMPORTANT]
> The `Eula`, `Billing`, and `ApiKey` options must be specified to run the container or the container won't start. Per altre informazioni, vedere[Fatturazione](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Eseguire query sull'endpoint di stima del contenitore

Il contenitore fornisce API dell'endpoint di stima di query basate su REST. 

Usare l'host, `http://localhost:5000`, per le API del contenitore.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrestare il contenitore

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>risoluzione dei problemi

If you run the container with an output [mount](./face-resource-container-config.md#mount-settings) and logging is enabled, the container generates log files that are helpful to troubleshoot issues that happen while you start or run the container.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fatturazione

The Face API containers send billing information to Azure by using a Face API resource on your Azure account. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Summary

In this article, you learned concepts and workflow for how to download, install, and run Face API containers. In sintesi:

* Container images are downloaded from the Azure Container Registry.
* Le immagini dei contenitori vengono eseguite in Docker.
* You can use either the REST API or the SDK to call operations in Face API containers by specifying the host URI of the container.
* You must specify billing information when you instantiate a container.

> [!IMPORTANT]
> Cognitive Services containers aren't licensed to run without being connected to Azure for metering. Customers must enable the containers to communicate billing information with the metering service at all times. I contenitori di Servizi cognitivi non inviano a Microsoft i dati dei clienti, ad esempio l'immagine o il testo analizzato.

## <a name="next-steps"></a>Passaggi successivi

* For configuration settings, see [Configure containers](face-resource-container-config.md).
* To learn more about how to detect and identify faces, see [Face overview](Overview.md).
* For information about the methods supported by the container, see the [Face API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* To use more Cognitive Services containers, see [Cognitive Services containers](../cognitive-services-container-support.md).
