---
title: Installare ed eseguire i contenitori
titlesuffix: Face - Azure Cognitive Services
description: Scaricare, installare ed eseguire i contenitori per viso in questa esercitazione dettagliata.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: article
ms.date: 06/10/2019
ms.author: dapine
ms.openlocfilehash: 4d3f3e64e109d0b1dc010be39b62b4f0bdc0573d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063604"
---
# <a name="install-and-run-face-containers"></a>Installare ed eseguire i contenitori di viso

Azure viso di servizi cognitivi fornisce un contenitore Linux standardizzato per Docker che rileva i visi umani nelle immagini. Vengono inoltre identificati attributi, che includono riquadri, ad esempio oblique e gli occhi, sesso, età e altre caratteristiche del viso stimato macchina. Oltre a rilevamento viso possibile verificare se due visi nella stessa immagine o immagini diverse sono uguali utilizzando un punteggio di confidenza. Viso può anche confrontare visi in un database per vedere se esiste già un viso simili o identico. Anche possibile organizzare i visi simili in gruppi tramite condiviso traits visual.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

È necessario soddisfare i prerequisiti seguenti prima di usare i contenitori l'API viso.

|Obbligatorio|Scopo|
|--|--|
|Motore Docker| Il motore Docker deve essere installato in un [computer host](#the-host-computer). Docker offre pacchetti per la configurazione dell'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> In Windows, Docker anche deve essere configurato per supportare i contenitori Linux.<br><br>|
|Familiarità con Docker | È necessario una conoscenza di base dei concetti relativi a Docker, ad esempio i registri, gli archivi, i contenitori e immagini del contenitore. È anche necessario conoscere basic `docker` comandi.| 
|Azure `Cognitive Services` risorsa |Per usare il contenitore, è necessario disporre di:<br><br>Una risorsa di servizi cognitivi di Azure e la chiave di fatturazione associata e l'URI dell'endpoint fatturazione. Entrambi i valori sono disponibili sul **Overview** e **chiavi** pagine per la risorsa. Sono richieste per avviare il contenitore. Aggiungere il `face/v1.0` routing per l'URI dell'endpoint, come illustrato nell'esempio BILLING_ENDPOINT_URI seguente: <br><br>**{BILLING_KEY}** : chiave della risorsa<br><br>**{BILLING_ENDPOINT_URI}** : esempio di URI dell'endpoint è `https://westus.api.cognitive.microsoft.com/face/v1.0`|

## <a name="request-access-to-the-private-container-registry"></a>Richiedere l'accesso al registro contenitori privato

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

La tabella seguente indica il valore minimo e consigliato per CPU e memoria da allocare per ogni contenitore di API Viso.

| Contenitore | Minima | Consigliato | Transazioni al secondo<br>(Minimo, massimo)|
|-----------|---------|-------------|--|
|Viso | 1 core, 2 GB di memoria | 1 core, 4 GB di memoria |10, 20|

* Ciascun core deve essere almeno a 2,6 GHz o superiore.
* Transazioni al secondo (TPS).

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine del contenitore con il pull di docker

Sono disponibili immagini del contenitore per l'API viso. 

| Contenitore | Repository |
|-----------|------------|
| Viso | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Docker pull per il contenitore di Viso

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Usare il contenitore

Dopo aver creato il contenitore nel [computer host](#the-host-computer), utilizzare la seguente procedura per lavorare con il contenitore.

1. [Eseguire il contenitore](#run-the-container-with-docker-run) con le necessarie impostazioni di fatturazione. Sono disponibili altri [esempi](./face-resource-container-config.md#example-docker-run-commands) del comando `docker run`. 
1. [Eseguire le query sull'endpoint di stima del contenitore](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con docker run

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire uno qualsiasi dei tre contenitori. Il comando Usa i parametri seguenti.

| Placeholder | Value |
|-------------|-------|
|{BILLING_KEY} | Questa chiave viene usata per avviare il contenitore e sono disponibile in Azure `Cognitive Services` **chiavi** pagina. |
|{BILLING_ENDPOINT_URI} | Il valore URI dell'endpoint di fatturazione è disponibile in Azure `Cognitive Services` **Cenni preliminari su** pagina. Un esempio è `https://westus.api.cognitive.microsoft.com/face/v1.0`.|

Aggiungere il `face/v1.0` routing per l'URI dell'endpoint, come illustrato nell'esempio BILLING_ENDPOINT_URI precedente. 

Sostituire questi parametri con valori personalizzati nel seguente `docker run` comando di esempio:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Questo comando:

* Esegue un contenitore di visi dall'immagine del contenitore.
* Consente di allocare un core CPU e 4 GB di memoria.
* Espone la porta TCP 5000 e alloca una pseudo TTY per il contenitore.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host. 

Sono disponibili altri [esempi](./face-resource-container-config.md#example-docker-run-commands) del comando `docker run`. 

> [!IMPORTANT]
> Il `Eula`, `Billing`, e `ApiKey` opzioni devono essere specificate per l'esecuzione del contenitore o non si avvia il contenitore. Per altre informazioni, vedere[Fatturazione](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Eseguire query sull'endpoint di stima del contenitore

Il contenitore fornisce API dell'endpoint di stima di query basate su REST. 

Usare l'host, `https://localhost:5000`, per le API del contenitore.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrestare il contenitore

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>risoluzione dei problemi

Se si esegue il contenitore con un output [montare](./face-resource-container-config.md#mount-settings) e la registrazione è abilitata, il contenitore genera file di log che sono utili per risolvere i problemi che si verificano durante l'avvio o esegue il contenitore. 


## <a name="billing"></a>Fatturazione

I contenitori di viso inviare le informazioni di fatturazione in Azure usando una risorsa API viso nell'account Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Riepilogo

In questo articolo si è appreso i concetti e flusso di lavoro per la procedura scaricare, installare ed eseguire i contenitori l'API viso. In sintesi:

* L'API viso fornisce tre contenitori di Linux per Docker che forniscono l'estrazione di frasi chiave, il rilevamento della lingua e l'analisi del sentiment.
* Le immagini del contenitore vengono scaricate dal registro contenitori di Azure.
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile utilizzare l'API REST o SDK di chiamare le operazioni nei contenitori di viso specificando l'URI del contenitore di host.
* Quando si crea un'istanza di un contenitore, è necessario specificare le informazioni di fatturazione.

> [!IMPORTANT]
> I contenitori di servizi cognitivi non sono concessi in licenza per l'esecuzione senza connettersi ad Azure per il controllo. I clienti è necessario abilitare i contenitori di comunicare le informazioni di fatturazione con il servizio di controllo in qualsiasi momento. I contenitori di Servizi cognitivi non inviano a Microsoft i dati dei clienti, ad esempio l'immagine o il testo analizzato.

## <a name="next-steps"></a>Passaggi successivi

* Per le impostazioni di configurazione, vedere [configurare i contenitori](face-resource-container-config.md).
* Per altre informazioni su come rilevare e identificare visi, vedere [Panoramica di volti](Overview.md).
* Per informazioni sui metodi supportati dal contenitore, vedere la [API viso](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Per usare più contenitori di servizi cognitivi, vedere [i contenitori di servizi cognitivi](../cognitive-services-container-support.md).
