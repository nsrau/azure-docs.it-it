---
title: Installare ed eseguire contenitori Docker per il API Viso
titleSuffix: Azure Cognitive Services
description: Usare il contenitore Docker per il API Viso per rilevare e identificare i visi umani nelle immagini.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: aahi
keywords: locale, Docker, contenitore, identificazione
ms.openlocfilehash: 0f6807f771510f85c5a20cfb2a160cfe1e8726a3
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91461513"
---
# <a name="install-and-run-face-containers-preview"></a>Installare ed eseguire i contenitori viso (anteprima)

> [!IMPORTANT]
> È stato raggiunto il limite per gli utenti del contenitore Viso. Attualmente non vengono accettate nuove applicazioni per il contenitore Viso.

Servizi cognitivi di Azure API Viso fornisce un contenitore Docker Linux che rileva e analizza i visi umani nelle immagini. Consente inoltre di identificare gli attributi, che includono i punti di interesse del viso, ad esempio nasi e occhi, sesso, età e altre funzionalità facciali stimate dal computer. Oltre al rilevamento, il volto può verificare se due facce nella stessa immagine o immagini diverse sono uguali usando un punteggio di confidenza. La faccia può inoltre confrontare i visi con un database per verificare se esiste già una faccia simile o identica. Consente anche di organizzare visi simili in gruppi usando tratti visivi condivisi.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/cognitive-services/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

È necessario soddisfare i prerequisiti seguenti prima di usare i contenitori del servizio Face.

|Obbligatoria|Scopo|
|--|--|
|Motore Docker| Il motore Docker deve essere installato in un [computer host](#the-host-computer). Docker offre pacchetti che configurano l'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> In Windows, Docker deve essere configurato anche per supportare i contenitori Linux.<br><br>|
|Familiarità con Docker | È necessaria una conoscenza di base dei concetti di Docker, ad esempio registri, repository, contenitori e immagini del contenitore. È anche necessario conoscere i comandi di base `docker` .| 
|Risorsa Face |Per usare il contenitore, è necessario disporre di:<br><br>Una risorsa **Face** di Azure e la chiave API associata e l'URI dell'endpoint. Entrambi i valori sono disponibili nelle pagine **Panoramica** e **chiavi** per la risorsa. È necessario avviare il contenitore.<br><br>**{API_KEY}**: una delle due chiavi di risorsa disponibili nella pagina **chiavi**<br><br>**{ENDPOINT_URI}**: endpoint fornito nella pagina **Panoramica**

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

La tabella seguente descrive i core CPU minimi e consigliati e la memoria da allocare per ogni contenitore del servizio viso.

| Contenitore | Minima | Consigliato | Transazioni al secondo<br>(Minimo, massimo)|
|-----------|---------|-------------|--|
|Viso | 1 core, 2 GB di memoria | 1 core, 4 GB di memoria |10, 20|

* Ogni core deve essere almeno 2,6 GHz o superiore.
* Transazioni al secondo (TPS).

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine del contenitore con docker pull

Sono disponibili le immagini del contenitore per il servizio Face. 

| Contenitore | Archivio |
|-----------|------------|
| Viso | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Docker pull per il contenitore di Viso

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Usare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usarlo.

1. [Eseguire il contenitore](#run-the-container-with-docker-run) con le impostazioni di fatturazione richieste. Sono disponibili altri [esempi](./face-resource-container-config.md#example-docker-run-commands) del comando `docker run`. 
1. [Eseguire una query sull'endpoint di stima del contenitore](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con Docker Run

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire il contenitore. Per informazioni dettagliate su come ottenere i valori e, vedere [raccolta dei parametri obbligatori](#gathering-required-parameters) `{ENDPOINT_URI}` `{API_KEY}` .

[Examples](face-resource-container-config.md#example-docker-run-commands) `docker run` Sono disponibili esempi di comando.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue un contenitore viso dall'immagine del contenitore.
* Alloca un core CPU e 4 GB di memoria.
* Espone la porta TCP 5000 e alloca uno pseudo TTY per il contenitore.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host. 

Sono disponibili altri [esempi](./face-resource-container-config.md#example-docker-run-commands) del comando `docker run`. 

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` È necessario specificare le opzioni, e per eseguire il contenitore oppure il contenitore non viene avviato. Per altre informazioni, vedere[Fatturazione](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Eseguire una query sull'endpoint di stima del contenitore

Il contenitore fornisce le API dell'endpoint di stima della query basata su REST. 

Usare l'host `http://localhost:5000` per le API del contenitore.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrestare il contenitore

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si esegue il contenitore con un [montaggio](./face-resource-container-config.md#mount-settings) di output e la registrazione è abilitata, il contenitore genera file di log utili per la risoluzione dei problemi che si verificano durante l'avvio o l'esecuzione del contenitore.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fatturazione

I contenitori di servizi volti inviano informazioni di fatturazione ad Azure usando una risorsa Face nell'account Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Summary

In questo articolo sono stati appresi concetti e flussi di lavoro per il download, l'installazione e l'esecuzione di contenitori di servizi viso. In sintesi:

* Le immagini del contenitore vengono scaricate dalla Container Registry di Azure.
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile usare l'API REST o l'SDK per chiamare le operazioni nei contenitori dei servizi volti specificando l'URI host del contenitore.
* È necessario specificare le informazioni di fatturazione quando si crea un'istanza di un contenitore.

> [!IMPORTANT]
> I contenitori di servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono abilitare i contenitori per la comunicazione di informazioni di fatturazione con il servizio di misurazione in qualsiasi momento. I contenitori di Servizi cognitivi non inviano a Microsoft i dati dei clienti, ad esempio l'immagine o il testo analizzato.

## <a name="next-steps"></a>Passaggi successivi

* Per le impostazioni di configurazione, vedere [configurare i contenitori](face-resource-container-config.md).
* Per altre informazioni su come rilevare e identificare i visi, vedere [Cenni preliminari sul volto](Overview.md).
* Per informazioni sui metodi supportati dal contenitore, vedere la [API viso](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Per usare più contenitori di servizi cognitivi, vedere [contenitori di servizi cognitivi](../cognitive-services-container-support.md).
