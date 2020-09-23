---
title: Installare ed eseguire contenitori-Analisi del testo
titleSuffix: Azure Cognitive Services
description: Come scaricare, installare ed eseguire i contenitori per Analisi del testo in questa esercitazione dettagliata.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: a83fd67a9ad5e5a36555fcbaed65b1d76936c33d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906057"
---
# <a name="install-and-run-text-analytics-containers"></a>Installare ed eseguire i contenitori di Analisi del testo

> [!NOTE]
> * Il contenitore per Analisi del sentiment V3 è ora disponibile a livello generale. I contenitori per l'estrazione di frasi chiave e rilevamento della lingua sono disponibili in anteprima pubblica non gestita.
> * Il collegamento di entità e NER non sono attualmente disponibili come contenitore.
> * Per accedere alla Analisi del testo per il contenitore di integrità è necessario un [modulo di richiesta](https://aka.ms/cognitivegate). Attualmente, non verrà addebitato l'utilizzo.
> * I percorsi delle immagini del contenitore potrebbero essere stati modificati di recente. Leggere questo articolo per visualizzare il percorso aggiornato per questo contenitore.

I contenitori consentono di eseguire le API di analisi del testo nell'ambiente in uso e sono perfette per i requisiti di sicurezza e governance dei dati specifici. I contenitori di Analisi del testo forniscono l'elaborazione avanzata in linguaggio naturale su testo non elaborato e includono tre funzioni principali: analisi dei sentimenti, estrazione di frasi chiave e rilevamento della lingua. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/cognitive-services/) prima di iniziare.

> [!IMPORTANT]
> L'account gratuito è limitato a 5.000 transazioni al mese e solo i <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">piani <span class="docon docon-navigate-external x-hidden-focus"></span> tariffari</a> **gratuiti** e **standard** sono validi per i contenitori. Per ulteriori informazioni sulle frequenze delle richieste di transazioni, vedere [limiti dei dati](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview#data-limits).

## <a name="prerequisites"></a>Prerequisiti

Per eseguire uno dei contenitori di Analisi del testo, è necessario disporre degli ambienti host e del computer host.

## <a name="preparation"></a>Preparazione

Per usare i contenitori di Analisi del testo, è necessario soddisfare i prerequisiti seguenti:

|Necessario|Scopo|
|--|--|
|Motore Docker| È necessario il motore Docker installato in un [computer host](#the-host-computer). Docker offre pacchetti che configurano l'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> **In Windows** Docker deve essere configurato anche per supportare i contenitori Linux.<br><br>|
|Familiarità con Docker | È opportuno avere una conoscenza di base dei concetti relativi a Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.| 
|Risorsa Analisi del testo |Per usare il contenitore, è necessario disporre di:<br><br>Una risorsa di Azure [analisi del testo](../../cognitive-services-apis-create-account.md) per ottenere la chiave API e l'URI dell'endpoint associati. Entrambi i valori sono disponibili nelle pagine delle chiavi e della panoramica di Analisi del testo nel portale di Azure e sono necessarie per avviare il contenitore.<br><br>**{API_KEY}**: una delle due chiavi di risorsa disponibili nella pagina **chiavi**<br><br>**{ENDPOINT_URI}**: endpoint fornito nella pagina **Panoramica**|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

Nella tabella seguente vengono descritte le specifiche minime e consigliate per i contenitori di Analisi del testo. Sono necessari almeno 2 gigabyte (GB) di memoria e ogni core CPU deve essere almeno 2,6 gigahertz (GHz) o superiore. Sono elencate anche le transazioni consentite per sezione (TPS).

|  | Specifiche host minime | Specifiche host consigliate | Numero minimo di transazioni | Numero massimo di transazioni|
|---|---------|-------------|--|--|
| **Rilevamento della lingua, estrazione di frasi chiave**   | 1 core, 2 GB di memoria | 1 core, 4 GB di memoria |15 | 30|
| **Analisi del sentiment v3**   | 1 core, 2 GB di memoria | 4 core, 8 GB di memoria |15 | 30|
| **Analisi del testo per l'integrità-1 documento/richiesta**   |  4 core, 10 GB di memoria | 6 core, memoria 12 GB |15 | 30|
| **Analisi del testo per l'integrità-10 documenti/richiesta**   |  6 core, 16GB di memoria | 8 core, memoria 20 GB |15 | 30|

Core CPU e memoria corrispondono alle `--cpus` Impostazioni e `--memory` , che vengono usate come parte del `docker run` comando.

## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine del contenitore con `docker pull`

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

Le immagini del contenitore per Analisi del testo sono disponibili in Microsoft Container Registry.

# <a name="sentiment-analysis-v3"></a>[Analisi del sentiment v3](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Estrazione frasi chiave (anteprima)](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection-preview"></a>[Rilevamento lingua (anteprima)](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[Analisi del testo per l'integrità (anteprima)](#tab/healthcare)

[!INCLUDE [docker-pull-health-container](../includes/docker-pull-health-container.md)]

***

## <a name="how-to-use-the-container"></a>Come usare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usare il contenitore.

1. [Eseguire il contenitore](#run-the-container-with-docker-run), con le impostazioni di fatturazione necessarie.
1. [Eseguire una query sull'endpoint di stima del contenitore](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con `docker run`

Usare il comando [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire i contenitori. Il contenitore continuerà a essere eseguito fino a quando non lo si arresta.

> [!IMPORTANT]
> * I comandi di Docker nelle sezioni seguenti usano la barra rovesciata, `\`, come carattere di continuazione di riga. Sostituirla o rimuoverla in base ai requisiti del sistema operativo host. 
> * È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](#billing).
> * Il contenitore analisi del sentimento V3 è ora disponibile a livello generale, che restituisce le [etichette dei sentimenti](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) nella risposta. I contenitori di estrazione di frasi chiave e rilevamento della lingua usano V2 dell'API e sono in anteprima.

# <a name="sentiment-analysis-v3"></a>[Analisi del sentiment v3](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Estrazione frasi chiave (anteprima)](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection-preview"></a>[Rilevamento lingua (anteprima)](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[Analisi del testo per l'integrità (anteprima)](#tab/healthcare)

[!INCLUDE [docker-run-health-container](../includes/docker-run-health-container.md)]

***

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Eseguire una query sull'endpoint di stima del contenitore

Il contenitore fornisce le API dell'endpoint di stima della query basata su REST.

Usare l'host `http://localhost:5000` per le API del contenitore.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrestare il contenitore

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si esegue il contenitore con un punto di [montaggio](../text-analytics-resource-container-config.md#mount-settings) di output e la registrazione attivata, il contenitore genera file di log utili per risolvere i problemi che si verificano durante l'avvio o l'esecuzione del contenitore.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fatturazione

I contenitori Analisi del testo inviano le informazioni di fatturazione ad Azure usando una risorsa di _Analisi del testo_ nell'account di Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](../text-analytics-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Riepilogo

In questo articolo sono stati descritti i concetti e il flusso di lavoro per scaricare, installare ed eseguire i contenitori di Analisi del testo. In sintesi:

* Analisi del testo offre tre contenitori Linux per Docker, incapsulando varie funzionalità:
   * *Analisi del sentiment*
   * *Estrazione frasi chiave (anteprima)* 
   * *Rilevamento lingua (anteprima)*
   * *Analisi del testo per l'integrità (anteprima)*
* Le immagini del contenitore vengono scaricate dal repository del contenitore Microsoft Container Registry (o di anteprima).
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile usare l'API REST o l'SDK per chiamare le operazioni nei contenitori di Analisi del testo specificando l'URI host del contenitore.
* Quando si crea un'istanza di un contenitore, è necessario specificare le informazioni di fatturazione.

> [!IMPORTANT]
> I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di servizi cognitivi non inviano i dati dei clienti (ad esempio, il testo analizzato) a Microsoft.

## <a name="next-steps"></a>Passaggi successivi

* Esaminare [configurare i contenitori](../text-analytics-resource-container-config.md) per le impostazioni di configurazione
* Fare riferimento alle [domande frequenti](../text-analytics-resource-faq.md) per risolvere i problemi correlati alla funzionalità.
