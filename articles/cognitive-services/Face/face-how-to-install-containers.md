---
title: Installare ed eseguire i contenitori
titlesuffix: Face - Azure Cognitive Services
description: Scaricare, installare ed eseguire i contenitori per visi in questa esercitazione della procedura dettagliata.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: f4abf17c774fb75a0314c8890f5f4383058e37fd
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321373"
---
# <a name="install-and-run-face-containers"></a>Installare ed eseguire contenitori viso

Il volto dei servizi cognitivi di Azure fornisce un contenitore Linux standardizzato per Docker che rileva i visi umani nelle immagini. Consente inoltre di identificare gli attributi, che includono i punti di interesse del viso, ad esempio nasi e occhi, sesso, età e altre funzionalità facciali stimate dal computer. Oltre al rilevamento, il volto può verificare se due facce nella stessa immagine o immagini diverse sono uguali usando un punteggio di confidenza. La faccia può inoltre confrontare i visi con un database per verificare se esiste già una faccia simile o identica. Consente anche di organizzare visi simili in gruppi usando tratti visivi condivisi.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di usare i contenitori di API Viso, è necessario soddisfare i prerequisiti seguenti.

|Obbligatoria|Scopo|
|--|--|
|Motore Docker| Il motore Docker deve essere installato in un [computer host](#the-host-computer). Docker offre pacchetti per la configurazione dell'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> In Windows, Docker deve essere configurato anche per supportare i contenitori Linux.<br><br>|
|Familiarità con Docker | È necessaria una conoscenza di base dei concetti di Docker, ad esempio registri, repository, contenitori e immagini del contenitore. È anche necessario conoscere i comandi `docker` di base.| 
|Risorsa Face |Per usare il contenitore, è necessario disporre di:<br><br>Una risorsa **Face** di Azure e la chiave API associata e l'URI dell'endpoint. Entrambi i valori sono disponibili nelle pagine **Panoramica** e **chiavi** per la risorsa. È necessario avviare il contenitore.<br><br>**{API_KEY}** : Una delle due chiavi di risorsa disponibili nella pagina **chiavi**<br><br>**{ENDPOINT_URI}** : Endpoint fornito nella pagina **Panoramica**

## <a name="request-access-to-the-private-container-registry"></a>Richiedere l'accesso al registro contenitori privato

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

La tabella seguente indica il valore minimo e consigliato per CPU e memoria da allocare per ogni contenitore di API Viso.

| Contenitore | Minima | Consigliato | Transazioni al secondo<br>(Minimo, massimo)|
|-----------|---------|-------------|--|
|Viso | 1 core, 2 GB di memoria | 1 core, 4 GB di memoria |10, 20|

* Ogni core deve essere almeno 2,6 GHz o superiore.
* Transazioni al secondo (TPS).

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine del contenitore con docker pull

Sono disponibili le immagini del contenitore per la API Viso. 

| Contenitore | Repository |
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
1. [Eseguire le query sull'endpoint di stima del contenitore](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con Docker Run

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire uno qualsiasi dei tre contenitori. Il comando usa i parametri seguenti.

| Placeholder | Value |
|-------------|-------|
|{API_KEY} | Questa chiave viene usata per avviare il contenitore ed è disponibile nella pagina `Cognitive Services` **chiavi** di Azure. |
|{ENDPOINT_URI} | Il valore dell'URI dell'endpoint di fatturazione è disponibile `Cognitive Services` nella pagina **Panoramica** di Azure. Un esempio è `https://westus.api.cognitive.microsoft.com/face/v1.0`.|

Aggiungere il `face/v1.0` routing all'URI dell'endpoint, come illustrato nell'esempio precedente di ENDPOINT_URI. 

Sostituire questi parametri con i propri valori nell'esempio di `docker run` comando seguente:

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
> È `Eula`necessario `Billing`specificare le `ApiKey` opzioni, e per eseguire il contenitore oppure il contenitore non viene avviato. Per altre informazioni, vedere[Fatturazione](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Eseguire query sull'endpoint di stima del contenitore

Il contenitore fornisce API dell'endpoint di stima di query basate su REST. 

Usare l'host, `https://localhost:5000`, per le API del contenitore.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrestare il contenitore

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>risoluzione dei problemi

Se si esegue il contenitore con un [montaggio](./face-resource-container-config.md#mount-settings) di output e la registrazione è abilitata, il contenitore genera file di log utili per la risoluzione dei problemi che si verificano durante l'avvio o l'esecuzione del contenitore. 


## <a name="billing"></a>Fatturazione

I contenitori di API Viso inviano informazioni di fatturazione ad Azure usando una risorsa API Viso nell'account Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Riepilogo

In questo articolo sono stati illustrati i concetti e il flusso di lavoro per il download, l'installazione e l'esecuzione di contenitori di API Viso. In sintesi:

* Il API Viso offre tre contenitori Linux per Docker che forniscono l'estrazione di frasi chiave, il rilevamento della lingua e l'analisi dei sentimenti.
* Le immagini del contenitore vengono scaricate dalla Container Registry di Azure.
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile usare l'API REST o l'SDK per chiamare le operazioni nei contenitori di API Viso specificando l'URI host del contenitore.
* È necessario specificare le informazioni di fatturazione quando si crea un'istanza di un contenitore.

> [!IMPORTANT]
> I contenitori di servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono abilitare i contenitori per la comunicazione di informazioni di fatturazione con il servizio di misurazione in qualsiasi momento. I contenitori di Servizi cognitivi non inviano a Microsoft i dati dei clienti, ad esempio l'immagine o il testo analizzato.

## <a name="next-steps"></a>Passaggi successivi

* Per le impostazioni di configurazione, vedere [configurare i contenitori](face-resource-container-config.md).
* Per altre informazioni su come rilevare e identificare i visi, vedere [Cenni preliminari sul volto](Overview.md).
* Per informazioni sui metodi supportati dal contenitore, vedere la [API viso](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Per usare più contenitori di servizi cognitivi, vedere [contenitori di servizi cognitivi](../cognitive-services-container-support.md).
