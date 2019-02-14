---
title: Installare ed eseguire contenitori
titlesuffix: Face - Azure Cognitive Services
description: Come scaricare, installare ed eseguire i contenitori per Face in questa esercitazione dettagliata.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: article
ms.date: 02/11/2019
ms.author: diberry
ms.openlocfilehash: 7a41bfaada64528e90f43064b34c394f9a9b8f8f
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56099089"
---
# <a name="install-and-run-containers"></a>Installare ed eseguire i contenitori

Viso offre un contenitore Linux standardizzato per Docker, denominato Viso, che rileva i visi umani nelle immagini e identifica gli attributi, ad esempio i punti di riferimento del viso (come naso e occhi), il sesso, l'età e altre caratteristiche con previsioni basate sul computer. Oltre al rilevamento, Viso può verificare se due volti nella stessa immagine o in immagini diverse sono uguali mediante un punteggio di attendibilità oppure confrontare i visi in un database per verificare se esistano già visi simili o identici. È anche possibile organizzare i visi simili in gruppi mediante caratteristiche condivise.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di usare i contenitori API Viso, è necessario soddisfare i prerequisiti seguenti:

|Obbligatoria|Scopo|
|--|--|
|Motore Docker| È necessario il motore Docker installato in un [computer host](#the-host-computer). Docker offre pacchetti per la configurazione dell'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> **In Windows** Docker deve essere configurato anche per supportare i contenitori Linux.<br><br>|
|Familiarità con Docker | È opportuno avere una conoscenza di base dei concetti relativi a Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.| 
|Risorsa di API Viso |Per usare il contenitore, è necessario disporre di:<br><br>Una risorsa di _API Viso_ di Azure per ottenere la chiave di fatturazione associata e l'URI dell'endpoint di fatturazione. Entrambi i valori sono disponibili nelle pagine delle chiavi e della panoramica di API Viso nel portale di Azure e sono necessarie per avviare il contenitore.<br><br>**{BILLING_KEY}** : chiave della risorsa<br><br>**{BILLING_ENDPOINT_URI}** : un esempio di URI dell'endpoint è: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|


## <a name="request-access-to-the-private-container-registry"></a>Richiedere l'accesso al registro contenitori privato

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Computer host

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

La tabella seguente indica il valore minimo e consigliato per CPU e memoria da allocare per ogni contenitore di API Viso.

| Contenitore | Minima | Consigliato |
|-----------|---------|-------------|
|Viso | 1 core, 2 GB di memoria | 1 core, 4 GB di memoria |

Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine del contenitore con `docker pull`

Sono disponibili immagini del contenitore per l'API Viso. 

| Contenitore | Repository |
|-----------|------------|
| Viso | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Docker pull per il contenitore di Viso

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="how-to-use-the-container"></a>Come usare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usare il contenitore.

1. [Eseguire il contenitore](#run-the-container-with-docker-run), con le impostazioni di fatturazione necessarie. Sono disponibili altri [esempi](./face-resource-container-config.md#example-docker-run-commands) del comando `docker run`. 
1. [Eseguire le query sull'endpoint di stima del contenitore](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con `docker run`

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire uno qualsiasi dei tre contenitori. Il comando usa i parametri seguenti:

| Placeholder | Valore |
|-------------|-------|
|{BILLING_KEY} | Questa chiave viene usata per avviare il contenitore ed è disponibile nella pagina delle chiavi di API Viso del portale di Azure.  |
|{BILLING_ENDPOINT_URI} | Il valore dell'URI dell'endpoint di fatturazione è disponibile nella pagina della panoramica di API Viso del portale di Azure.|

Sostituire i parametri con i valori personalizzati nel comando `docker run` di esempio seguente.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Questo comando:

* Esegue un contenitore di Viso dall'immagine del contenitore
* Alloca un core CPU e 4 GB di memoria
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host. 

Sono disponibili altri [esempi](./face-resource-container-config.md#example-docker-run-commands) del comando `docker run`. 

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Eseguire query sull'endpoint di stima del contenitore

Il contenitore fornisce API dell'endpoint di stima di query basate su REST. 

Usare l'host, https://localhost:5000, per le API del contenitore.

## <a name="stop-the-container"></a>Arrestare il contenitore

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>risoluzione dei problemi

Se si esegue il contenitore con un punto di [montaggio](./face-resource-container-config.md#mount-settings) di output e la registrazione attivata, il contenitore genera file di log utili per risolvere i problemi che si verificano durante l'avvio o l'esecuzione del contenitore. 

## <a name="containers-api-documentation"></a>Documentazione dell'API del contenitore

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="billing"></a>Fatturazione

I contenitori di API Viso inviano le informazioni di fatturazione ad Azure usando una risorsa di _API Viso_ nell'account di Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](./face-resource-container-config.md).

## <a name="summary"></a>Summary

Questo articolo ha illustrato i concetti e il flusso di lavoro per scaricare, installare ed eseguire i contenitori di API Viso. In sintesi:

* API Viso fornisce tre contenitori Linux per Docker, che incapsulano funzionalità di estrazione di frasi chiave, rilevamento della lingua e analisi del sentiment.
* Le immagini dei contenitori vengono scaricate da Registro Container Microsoft in Azure.
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile usare l'API REST o l'SDK per chiamare le operazioni nei contenitori di API Viso specificando l'URI dell'host del contenitore.
* Quando si crea un'istanza di un contenitore, è necessario specificare le informazioni di fatturazione.

> [!IMPORTANT]
> I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di Servizi cognitivi non inviano i dati dei clienti (ad esempio, l'immagine o il testo analizzato) a Microsoft.

## <a name="next-steps"></a>Passaggi successivi

* Rivedere [Configurare i contenitori](face-resource-container-config.md) per informazioni sulle impostazioni di configurazione.
* Rivedere [Face Overview](Overview.md) (Panoramica di Viso) per altre informazioni sul rilevamento e sull'identificazione di visi  
* Fare riferimento all'[API Viso](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) per informazioni dettagliate sui metodi supportati dal contenitore.
* Fare riferimento alle [domande frequenti](FAQ.md) per risolvere i problemi correlati alla funzionalità Viso.
* Usare altri [contenitori di Servizi cognitivi](../cognitive-services-container-support.md)
