---
title: Installare ed eseguire contenitori - Faccia
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come scaricare, installare ed eseguire contenitori per Face in questa procedura dettagliata.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: e467b195ab1e2124286bfef74d7d1b71a4d99dd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76165990"
---
# <a name="install-and-run-face-containers-preview"></a>Installare ed eseguire contenitori Face (anteprima)Install and run Face containers (Preview)

Azure Cognitive Services Face offre un contenitore Linux standardizzato per Docker che rileva i volti umani nelle immagini. Identifica anche gli attributi, che includono punti di riferimento del viso come nasi e occhi, sesso, età e altre caratteristiche facciali previste dalla macchina. Oltre al rilevamento, Face può verificare se due volti nella stessa immagine o immagini diverse sono uguali utilizzando un punteggio di confidenza. Face può anche confrontare i volti con un database per vedere se esiste già una faccia simile o identica. Può anche organizzare volti simili in gruppi utilizzando tratti visivi condivisi.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di utilizzare i contenitori del servizio Face, è necessario soddisfare i prerequisiti seguenti.

|Obbligatoria|Scopo|
|--|--|
|Motore Docker| Il motore Docker deve essere installato in un [computer host.](#the-host-computer) Docker offre pacchetti che configurano l'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica sui concetti fondamentali relativi a Docker e al contenitore, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica su Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> In Windows, Docker deve anche essere configurato per supportare i contenitori Linux.On Windows, Docker also must be configured to support Linux containers.<br><br>|
|Familiarità con Docker | È necessaria una conoscenza di base dei concetti di Docker, ad esempio registri, archivi, contenitori e immagini di contenitori. È inoltre necessaria `docker` una conoscenza dei comandi di base.| 
|Risorsa viso |Per utilizzare il contenitore, è necessario disporre di:To use the container, you must have:<br><br>Una risorsa Azure **Face,** la chiave API associata e l'URI dell'endpoint. Entrambi i valori sono disponibili nelle pagine **Panoramica** e **Chiavi** per la risorsa. Sono tenuti ad avviare il contenitore.<br><br>**API_KEY :Una**delle due chiavi di risorsa disponibili nella pagina **Chiavi**<br><br>**ENDPOINT_URI :** l'endpoint come indicato nella pagina **Panoramica**

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>Richiedere l'accesso al registro contenitori privato

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

Nella tabella seguente vengono descritti i core CPU minimo e consigliato e la memoria da allocare per ogni contenitore del servizio Face.

| Contenitore | Minima | Consigliato | Transazioni al secondo<br>(Minimo, massimo)|
|-----------|---------|-------------|--|
|Viso | 1 core, 2 GB di memoria | 1 core, 4 GB di memoria |10, 20|

* Ogni core deve essere almeno 2,6 GHz o superiore.
* Transazioni al secondo (TPS).

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine contenitore con docker pullGet the container image with docker pull

Sono disponibili immagini contenitore per il servizio Face. 

| Contenitore | Archivio |
|-----------|------------|
| Viso | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Docker pull per il contenitore di Viso

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Utilizzare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usarlo.

1. [Eseguire il contenitore](#run-the-container-with-docker-run) con le impostazioni di fatturazione necessarie. Sono disponibili altri [esempi](./face-resource-container-config.md#example-docker-run-commands) del comando `docker run`. 
1. [Eseguire una query sull'endpoint di stima del contenitore.](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con l'esecuzione dockerRun the container with docker run

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire il contenitore. Fare riferimento alla raccolta dei [parametri](#gathering-required-parameters) `{ENDPOINT_URI}` obbligatori `{API_KEY}` per informazioni dettagliate su come ottenere i valori e .

[Sono](face-resource-container-config.md#example-docker-run-commands) disponibili `docker run` esempi del comando.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue un contenitore di volti dall'immagine del contenitore.
* Alloca un core CPU e 4 GB di memoria.
* Espone la porta TCP 5000 e alloca uno pseudo TTY per il contenitore.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host. 

Sono disponibili altri [esempi](./face-resource-container-config.md#example-docker-run-commands) del comando `docker run`. 

> [!IMPORTANT]
> Le `Eula` `Billing`opzioni `ApiKey` , e devono essere specificate per eseguire il contenitore, altrimenti il contenitore non verrà avviato. Per altre informazioni, vedere[Fatturazione](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Eseguire query sull'endpoint di stima del contenitore

Il contenitore fornisce API dell'endpoint di stima di query basate su REST. 

Usare l'host, `http://localhost:5000`, per le API del contenitore.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrestare il contenitore

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si esegue il contenitore con un [montaggio](./face-resource-container-config.md#mount-settings) di output e la registrazione è abilitata, il contenitore genera file di log utili per risolvere i problemi che si verificano durante l'avvio o l'esecuzione del contenitore.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fatturazione

I contenitori del servizio Face inviano le informazioni di fatturazione ad Azure usando una risorsa Face nel tuo account Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Riepilogo

In questo articolo sono stati illustrati i concetti e il flusso di lavoro su come scaricare, installare ed eseguire contenitori di servizi Face. In sintesi:

* Container images are downloaded from the Azure Container Registry.
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile usare l'API REST o l'SDK per chiamare le operazioni nei contenitori del servizio Face specificando l'URI host del contenitore.
* È necessario specificare le informazioni di fatturazione quando si crea un'istanza di un contenitore.

> [!IMPORTANT]
> I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono abilitare i contenitori per comunicare sempre le informazioni di fatturazione con il servizio di misurazione. I contenitori di Servizi cognitivi non inviano a Microsoft i dati dei clienti, ad esempio l'immagine o il testo analizzato.

## <a name="next-steps"></a>Passaggi successivi

* Per le impostazioni di configurazione, vedere [Configurare i contenitori](face-resource-container-config.md).
* Per ulteriori informazioni su come rilevare e identificare i volti, consultate [Panoramica dei volti.](Overview.md)
* Per informazioni sui metodi supportati dal contenitore, vedere [l'API Face](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Per usare più contenitori di Servizi cognitivi, vedere Contenitori di [Servizi cognitivi](../cognitive-services-container-support.md).
