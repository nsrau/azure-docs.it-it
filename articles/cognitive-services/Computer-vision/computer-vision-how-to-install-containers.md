---
title: Come installare ed eseguire i contenitori - Visione artificiale
titlesuffix: Azure Cognitive Services
description: Come scaricare, installare ed eseguire i contenitori per Visione artificiale in questa esercitazione dettagliata.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 01/29/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 1e7f62d35e9850202b7d55c3c3440ff88413931d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55473494"
---
# <a name="install-and-run-recognize-text-containers"></a>Installare ed eseguire contenitori di riconoscimento del testo

La parte di riconoscimento del testo di Visione artificiale è disponibile anche come un contenitore Docker. Permette di rilevare ed estrarre testo stampato dalle immagini di diversi oggetti con superfici e sfondi diversi, ad esempio ricette, poster e biglietti da visita.  
> [!IMPORTANT]
> Il contenitore di riconoscimento del testo attualmente funziona solo con la lingua inglese.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di usare i contenitori Riconoscimento del testo, è necessario soddisfare i prerequisiti seguenti:

|Obbligatoria|Scopo|
|--|--|
|Motore Docker| È necessario il motore Docker installato in un [computer host](#the-host-computer). Docker offre pacchetti per la configurazione dell'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> **In Windows** Docker deve essere configurato anche per supportare i contenitori Linux.<br><br>|
|Familiarità con Docker | È opportuno avere una conoscenza di base dei concetti relativi a Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.| 
|Risorsa di richiesta di riconoscimento del testo |Per usare il contenitore, è necessario disporre di:<br><br>Una risorsa di [_Riconoscimento del testo_](vision-api-how-to-topics/howtosubscribe.md) di Azure per ottenere la chiave di fatturazione associata e l'URI dell'endpoint di fatturazione. Entrambi i valori sono disponibili nelle pagine delle chiavi e della panoramica di Riconoscimento del testo nel portale di Azure e sono necessarie per avviare il contenitore.<br><br>**{BILLING_KEY}** : chiave della risorsa<br><br>**{BILLING_ENDPOINT_URI}** : un esempio di URI dell'endpoint è: `https://westus.api.cognitive.microsoft.com/vision/v2.0`|


## <a name="request-access-to-the-private-container-registry"></a>Richiedere l'accesso al registro contenitori privato

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Computer host

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

La tabella seguente indica i core CPU minimi e consigliati e la memoria da allocare per ogni contenitore di Riconoscimento del testo.

| Contenitore | Minima | Consigliato |
|-----------|---------|-------------|
|Riconoscimento del testo|1 core, 8 GB di memoria, 0,5 TPS|2 core, 8 GB di memoria, 1 TPS|

Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.


## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine del contenitore con `docker pull`

Sono disponibili le immagini del contenitore per il Riconoscimento del testo. 

| Contenitore | Repository |
|-----------|------------|
|Riconoscimento del testo | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Usare il comando [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare un'immagine del contenitore.


### <a name="docker-pull-for-the-recognize-text-container"></a>Docker pull per il contenitore di Riconoscimento del testo

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-rocognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Come usare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usare il contenitore.

1. [Eseguire il contenitore](#run-the-container-with-docker-run), con le impostazioni di fatturazione necessarie. Sono disponibili altri [esempi](computer-vision-resource-container-config.md) del comando `docker run`. 
1. [Eseguire le query sull'endpoint di stima del contenitore](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con `docker run`

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire il contenitore. Il comando usa i parametri seguenti:

| Placeholder | Valore |
|-------------|-------|
|{BILLING_KEY} | Questa chiave viene usata per avviare il contenitore ed è disponibile nella pagina delle chiavi di Riconoscimento del testo del portale di Azure.  |
|{BILLING_ENDPOINT_URI} | Valore dell'URI dell'endpoint di fatturazione.|

Sostituire i parametri con i valori personalizzati nel comando `docker run` di esempio seguente.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Questo comando:

* Esegue un contenitore di riconoscimento dall'immagine del contenitore
* Alloca un core CPU e 4 GB di memoria
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host. 

Sono disponibili altri [esempi](./computer-vision-resource-container-config.md#example-docker-run-commands) del comando `docker run`. 

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Eseguire query sull'endpoint di stima del contenitore

Il contenitore fornisce API dell'endpoint di stima di query basate su REST. 

Usare l'host, https://localhost:5000, per le API del contenitore.

### <a name="asynchronous-text-recognition"></a>Riconoscimento del testo asincrono

È possibile usare le operazioni `POST /vision/v2.0/recognizeText` e `GET /vision/v2.0/textOperations/*{id}*` in parallelo per riconoscere in modo asincrono il testo stampato in un'immagine, in modo simile a come il servizio Visione artificiale usa le operazioni REST corrispondenti. Il contenitore Riconoscimento del testo riconosce ora solo testo stampato e testo non scritto a mano, pertanto il parametro `mode` che viene in genere specificato per l'operazione del servizio Visione artificiale viene ignorato dal contenitore di Riconoscimento del testo.

### <a name="synchronous-text-recognition"></a>Riconoscimento del testo sincrono

È possibile usare l'operazione `POST /vision/v2.0/recognizeTextDirect` per riconoscere il testo stampato in un'immagine in modo sincrono. Poiché questa operazione è sincrona, il corpo della richiesta per questa operazione è analoga a quella per l'operazione `POST /vision/v2.0/recognizeText`, ma il corpo della risposta per questa operazione è uguale a quello restituito dall'operazione `GET /vision/v2.0/textOperations/*{id}*`.

## <a name="stop-the-container"></a>Arrestare il contenitore

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>risoluzione dei problemi

Se si esegue il contenitore con un punto di [montaggio](./computer-vision-resource-container-config.md#mount-settings) di output e la registrazione attivata, il contenitore genera file di log utili per risolvere i problemi che si verificano durante l'avvio o l'esecuzione del contenitore. 

## <a name="containers-api-documentation"></a>Documentazione dell'API del contenitore

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="billing"></a>Fatturazione

I contenitori Riconoscimento del testo inviano le informazioni di fatturazione ad Azure usando una risorsa di _Riconoscimento del testo_ nell'account di Azure. 

I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di Servizi cognitivi non inviano dati dei clienti a Microsoft. 

Il comando `docker run` usa gli argomenti seguenti a scopo di fatturazione:

| Opzione | DESCRIZIONE |
|--------|-------------|
| `ApiKey` | Chiave API della risorsa di _Riconoscimento del testo_ usata per tenere traccia delle informazioni di fatturazione. |
| `Billing` | Endpoint della risorsa di _Riconoscimento del testo_ usata per tenere traccia delle informazioni di fatturazione.|
| `Eula` | Indica che è stata accettata la licenza per il contenitore.<br/>Il valore di questa opzione deve essere impostato su `accept`. |

> [!IMPORTANT]
> Tutte e tre le opzioni devono essere specificate con valori validi per consentire l'avvio del contenitore.

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](./computer-vision-resource-container-config.md).

## <a name="summary"></a>Summary

In questo articolo sono stati descritti i concetti e il flusso di lavoro per scaricare, installare ed eseguire i contenitori di Riconoscimento del testo. In sintesi:

* Riconoscimento del testo fornisce un contenitore Linux per Docker, che incapsula il riconoscimento del testo.
* Le immagini dei contenitori vengono scaricate da Registro Container Microsoft in Azure.
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile usare l'API REST o l'SDK per chiamare le operazioni nei contenitori di Riconoscimento del testo specificando l'URI host del contenitore.
* Quando si crea un'istanza di un contenitore, è necessario specificare le informazioni di fatturazione.

> [!IMPORTANT]
> I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di Servizi cognitivi non inviano a Microsoft i dati dei clienti, ad esempio l'immagine o il testo analizzato.

## <a name="next-steps"></a>Passaggi successivi

* Rivedere [Configurare i contenitori](computer-vision-resource-container-config.md) per informazioni sulle impostazioni di configurazione.
* Rivedere [Panoramica di Visione artificiale](Home.md) per altre informazioni sul riconoscimento di testo scritto a mano e stampato  
* Fare riferimento all'[API Visione artificiale](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) per informazioni dettagliate sui metodi supportati dal contenitore.
* Fare riferimento alle [domande frequenti](FAQ.md) per risolvere i problemi correlati alle funzionalità di Analisi del testo.
* Usare altri [contenitori di Servizi cognitivi](../cognitive-services-container-support.md)
