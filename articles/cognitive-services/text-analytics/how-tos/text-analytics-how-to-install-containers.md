---
title: Installare ed eseguire i contenitori
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Come scaricare, installare ed eseguire i contenitori per Analisi del testo in questa esercitazione dettagliata.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/22/2019
ms.author: diberry
ms.openlocfilehash: 39c1a212bee552b436ef21d208f4a20086578325
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437850"
---
# <a name="install-and-run-text-analytics-containers"></a>Installare ed eseguire i contenitori di Analisi del testo

I contenitori di Analisi del testo forniscono l'elaborazione avanzata in linguaggio naturale su testo non elaborato e includono tre funzioni principali: analisi del sentiment, estrazione frasi chiave e rilevamento lingua. Il collegamento di entità non è attualmente supportato in un contenitore. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire uno dei contenitori Analitica di testo, è necessario disporre di ambienti host computer e il contenitore.

## <a name="preparation"></a>Operazioni preliminari

Per usare i contenitori di Analisi del testo, è necessario soddisfare i prerequisiti seguenti:

|Obbligatorio|Scopo|
|--|--|
|Motore Docker| È necessario il motore Docker installato in un [computer host](#the-host-computer). Docker offre pacchetti per la configurazione dell'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> **In Windows** Docker deve essere configurato anche per supportare i contenitori Linux.<br><br>|
|Familiarità con Docker | È opportuno avere una conoscenza di base dei concetti relativi a Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.| 
|Risorsa Analisi del testo |Per usare il contenitore, è necessario disporre di:<br><br>Una risorsa di [ _Analisi del testo_ ](text-analytics-how-to-access-key.md) di Azure per ottenere la chiave di fatturazione associata e l'URI dell'endpoint di fatturazione. Entrambi i valori sono disponibili nelle pagine delle chiavi e della panoramica di Analisi del testo nel portale di Azure e sono necessarie per avviare il contenitore.<br><br>**{BILLING_KEY}** : chiave della risorsa<br><br>**{BILLING_ENDPOINT_URI}** : un esempio di URI dell'endpoint è: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|

### <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

La tabella seguente indica i core CPU minimi e consigliati, per una velocità di 2,6 gigahertz (GHz) o superiore, e la memoria, espressa in gigabyte (GB), da allocare per ogni contenitore di Analisi del testo.

| Contenitore | Minima | Consigliato | TPS<br>(Minimum, Maximum)|
|-----------|---------|-------------|--|
|Estrazione frasi chiave | 1 core, 2 GB di memoria | 1 core, 4 GB di memoria |15, 30|
|Rilevamento lingua | 1 core, 2 GB di memoria | 1 core, 4 GB di memoria |15, 30|
|Analisi del sentiment | 1 core, 2 GB di memoria | 1 core, 4 GB di memoria |15, 30|

* Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.
* Programmi di transazione - transazioni al secondo

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine del contenitore con `docker pull`

Le immagini dei contenitori per Analisi del testo sono disponibili in Registro contenitori di Microsoft. 

| Contenitore | Repository |
|-----------|------------|
|Estrazione frasi chiave | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Rilevamento lingua | `mcr.microsoft.com/azure-cognitive-services/language` |
|Analisi del sentiment | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Usare la [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) comando per scaricare un'immagine del contenitore da registro contenitori di Microsoft.

Per una descrizione completa dei tag disponibili per i contenitori di Analisi del testo, vedere i contenitori seguenti nell'hub Docker:

* [Estrazione frasi chiave](https://go.microsoft.com/fwlink/?linkid=2018757)
* [Rilevamento lingua](https://go.microsoft.com/fwlink/?linkid=2018759)
* [Analisi del sentiment](https://go.microsoft.com/fwlink/?linkid=2018654)

Usare il comando [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare un'immagine del contenitore.


### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker pull per il contenitore di estrazione della frase chiave

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

### <a name="docker-pull-for-the-language-detection-container"></a>Docker pull per il contenitore di rilevamento della lingua

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```

### <a name="docker-pull-for-the-sentiment-container"></a>Docker pull per il contenitore del sentiment

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]


## <a name="how-to-use-the-container"></a>Come usare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usare il contenitore.

1. [Eseguire il contenitore](#run-the-container-with-docker-run), con le impostazioni di fatturazione necessarie. Sono disponibili altri [esempi](../text-analytics-resource-container-config.md#example-docker-run-commands) del comando `docker run`. 
1. [Eseguire le query sull'endpoint di stima del contenitore](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con `docker run`

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire uno qualsiasi dei tre contenitori. Il comando usa i parametri seguenti:

| Placeholder | Valore |
|-------------|-------|
|{BILLING_KEY} | Questa chiave viene usata per avviare il contenitore ed è disponibile nella pagina delle chiavi di Analisi del testo del portale di Azure.  |
|{BILLING_ENDPOINT_URI} | Il valore dell'URI dell'endpoint di fatturazione è disponibile nella pagina della panoramica di Analisi del testo del portale di Azure.|

Sostituire i parametri con i valori personalizzati nel comando `docker run` di esempio seguente.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Questo comando:

* Esegue un contenitore della frase chiave dall'immagine del contenitore
* Alloca un core CPU e 4 GB di memoria
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host. 

Sono disponibili altri [esempi](../text-analytics-resource-container-config.md#example-docker-run-commands) del comando `docker run`. 

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Eseguire query sull'endpoint di stima del contenitore

Il contenitore fornisce API dell'endpoint di stima di query basate su REST. 

Usare l'host, `https://localhost:5000`, per le API del contenitore.

## <a name="stop-the-container"></a>Arrestare il contenitore

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>risoluzione dei problemi

Se si esegue il contenitore con un punto di [montaggio](../text-analytics-resource-container-config.md#mount-settings) di output e la registrazione attivata, il contenitore genera file di log utili per risolvere i problemi che si verificano durante l'avvio o l'esecuzione del contenitore. 

## <a name="containers-api-documentation"></a>Documentazione dell'API del contenitore

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="billing"></a>Fatturazione

I contenitori Analisi del testo inviano le informazioni di fatturazione ad Azure usando una risorsa di _Analisi del testo_ nell'account di Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](../text-analytics-resource-container-config.md).

## <a name="summary"></a>Summary

In questo articolo sono stati descritti i concetti e il flusso di lavoro per scaricare, installare ed eseguire i contenitori di Analisi del testo. In sintesi:

* Analisi del testo fornisce tre contenitori Linux per Docker, che incapsulano funzionalità di estrazione di frasi chiave, rilevamento della lingua e analisi del sentiment.
* Le immagini dei contenitori vengono scaricate da Registro Container Microsoft in Azure.
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile usare l'API REST o l'SDK per chiamare le operazioni nei contenitori di Analisi del testo specificando l'URI host del contenitore.
* Quando si crea un'istanza di un contenitore, è necessario specificare le informazioni di fatturazione.

> [!IMPORTANT]
> I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di Servizi cognitivi non inviano i dati dei clienti (ad esempio, l'immagine o il testo analizzato) a Microsoft.

## <a name="next-steps"></a>Passaggi successivi

* Rivedere [Configurare i contenitori](../text-analytics-resource-container-config.md) per informazioni sulle impostazioni di configurazione.
* Fare riferimento alle [domande frequenti](../text-analytics-resource-faq.md) per risolvere i problemi correlati alla funzionalità.

