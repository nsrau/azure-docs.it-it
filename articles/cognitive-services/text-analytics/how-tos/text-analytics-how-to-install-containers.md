---
title: Installare ed eseguire i contenitori
titleSuffix: Text Analytics - Cognitive Services - Azure
description: Come scaricare, installare ed eseguire i contenitori per Analisi del testo in questa esercitazione dettagliata.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 99bdb42d9a0d86d0d2acc4a6272e0c802042e6b5
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634907"
---
# <a name="install-and-run-containers"></a>Installare ed eseguire i contenitori

La containerizzazione è un approccio alla distribuzione del software in cui un'applicazione o un servizio viene compresso come immagine del contenitore. La configurazione e le dipendenze dell'applicazione o del servizio sono incluse nell'immagine del contenitore. L'immagine del contenitore può quindi essere distribuita in un host contenitore senza alcuna variazione o con modifiche minime. I contenitori sono isolati fra loro e dal sistema operativo sottostante, con un footprint inferiore a quello di una macchina virtuale. È possibile creare istanze dei contenitori dalle immagini per attività a breve termine. Tali istanze possono essere rimosse quando non sono più necessarie.

Analisi del testo fornisce il set seguente di contenitori Docker, ognuno dei quali contiene un subset di funzionalità:

| Contenitore| Descrizione |
|----------|-------------|
|Estrazione frasi chiave | Estrae le frasi chiave per identificare i punti principali. Ad esempio, per il testo di input "Il cibo era delizioso e il personale era meraviglioso", l'API restituisce i punti rilevanti del discorso, ovvero "cibo" e "personale meraviglioso". |
|Rilevamento lingua | Per un massimo di 120 lingue, rileva e segnala la lingua in cui è scritto il testo di input. Il contenitore segnala un solo codice lingua per ogni documento incluso nella richiesta. Il codice lingua è associato a un punteggio che indica il livello di attendibilità. |
|Analisi del sentiment | Analizza testo non elaborato per indicazioni su una valutazione positiva o negativa. L'API restituisce un punteggio sentiment compreso tra 0 e 1 per ogni documento, dove 1 è il risultato più positivo. Viene eseguito un training preliminare dei modelli di analisi usando un ampio corpus di testi e tecnologie per l'elaborazione del linguaggio naturale di Microsoft. Per [alcune lingue](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages.md), l'API può analizzare e assegnare punteggi a qualsiasi testo non elaborato fornito dall'utente, restituendo direttamente i risultati all'applicazione chiamante. |

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="preparation"></a>Operazioni preliminari

Per usare i contenitori di Analisi del testo, è necessario soddisfare i prerequisiti seguenti:

**Motore Docker**: il motore Docker deve essere installato localmente. Docker offre pacchetti per la configurazione dell'ambiente in [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) e [Windows](https://docs.docker.com/docker-for-windows/). In Windows Docker deve essere configurato per supportare i contenitori Linux. I contenitori Docker possono anche essere distribuiti direttamente nel [servizio Kubernetes di Azure](/azure/aks/), in [Istanze di contenitore di Azure](/azure/container-instances/) o in un cluster [Kubernetes](https://kubernetes.io/) distribuito in [Azure Stack](/azure/azure-stack/). Per altre informazioni sulla distribuzione di Kubernetes in Azure Stack, vedere [Deploy Kubernetes to Azure Stack](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy) (Distribuzione di Kubernetes in Azure Stack).

Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure.

**Familiarità con Registro contenitori Microsoft e Docker**: è opportuno avere una conoscenza di base dei concetti relativi a Registro contenitori di Microsoft e Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.  

Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).

### <a name="server-requirements-and-recommendations"></a>Indicazioni e requisiti del server

La tabella seguente indica i core CPU minimi e consigliati, per una velocità di 2,6 gigahertz (GHz) o superiore, e la memoria, espressa in gigabyte (GB), da allocare per ogni contenitore di Analisi del testo.

| Contenitore | Minimo | Consigliato |
|-----------|---------|-------------|
|Estrazione frasi chiave | 1 core, 2 GB di memoria | 1 core, 4 GB di memoria |
|Rilevamento lingua | 1 core, 2 GB di memoria | 1 core, 4 GB di memoria |
|Analisi del sentiment | 1 core, 8 GB di memoria | 1 core, 8 GB di memoria |

## <a name="download-container-images-from-microsoft-container-registry"></a>Scaricare le immagini dei contenitori da Registro contenitori di Microsoft

Le immagini dei contenitori per Analisi del testo sono disponibili in Registro contenitori di Microsoft. La tabella seguente elenca i repository disponibili in Registro contenitori di Microsoft per i contenitori di Analisi del testo. Ogni repository contiene un'immagine del contenitore, che deve essere scaricata per eseguire il contenitore in locale.

| Contenitore | Repository |
|-----------|------------|
|Estrazione frasi chiave | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Rilevamento lingua | `mcr.microsoft.com/azure-cognitive-services/language` |
|Analisi del sentiment | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Usare il comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare un'immagine del contenitore da un repository. Ad esempio, per scaricare dal repository l'immagine del contenitore Estrazione frasi chiave più recente, usare il comando seguente:

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

Per una descrizione completa dei tag disponibili per i contenitori di Analisi del testo, vedere i contenitori seguenti nell'hub Docker:

* [Estrazione frasi chiave](https://go.microsoft.com/fwlink/?linkid=2018757)
* [Rilevamento lingua](https://go.microsoft.com/fwlink/?linkid=2018759)
* [Analisi del sentiment](https://go.microsoft.com/fwlink/?linkid=2018654)

> [!TIP]
> È possibile usare il comando [docker images](https://docs.docker.com/engine/reference/commandline/images/) per visualizzare l'elenco delle immagini dei contenitori scaricate. Ad esempio, il comando seguente visualizza l'ID, il repository e il tag di ogni immagine del contenitore scaricata, in formato tabella:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>Creare un'istanza di un contenitore da un'immagine del contenitore scaricata

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per creare un'istanza di un contenitore da un'immagine del contenitore scaricata. Ad esempio, il comando seguente:

* Crea un'istanza di un contenitore da un'immagine del contenitore Analisi del sentiment
* Alloca un core CPU e 8 GB di memoria
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore
* Rimuove automaticamente il contenitore dopo la chiusura

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789

```

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per creare un'istanza del contenitore, altrimenti il contenitore non verrà avviato.  Per altre informazioni, vedere[Fatturazione](#billing).

Dopo aver creato l'istanza, sarà possibile chiamare le operazioni dal contenitore tramite l'URI host del contenitore. L'URI host seguente rappresenta ad esempio il contenitore Analisi del sentiment di cui è stata creata un'istanza nell'esempio precedente:

```http
http://localhost:5000/
```

> [!TIP]
> È possibile accedere alla [specifica OpenAPI](https://swagger.io/docs/specification/about/) (in precedenza nota come specifica Swagger), che descrive le operazioni supportate da un contenitore di cui è stata creata un'istanza, dall'URI relativo `/swagger` per il contenitore. L'URI seguente consente ad esempio di accedere alla specifica OpenAPI per il contenitore Analisi del sentiment di cui è stata creata un'istanza nell'esempio precedente:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

È possibile [chiamare le operazioni API REST](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-call-api) disponibili nel contenitore o usare la libreria client [Azure Cognitive Services Text Analytics SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics) per chiamare tali operazioni.  
> [!IMPORTANT]
> Se si vuole usare la libreria client con il contenitore, è necessario avere installato Azure Cognitive Services Text Analytics SDK versione 2.1.0 o successiva.

L'unica differenza tra la chiamata di una determinata operazione dal contenitore e la stessa chiamata da un servizio corrispondente in Azure consiste nel fatto che verrà usato l'URI host del contenitore anziché l'URI host di un'area di Azure. Se ad esempio si vuole usare un'istanza di Analisi del testo in esecuzione nell'area di Azure Stati Uniti occidentali, verrà chiamata l'operazione API REST seguente:

```http
POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
```

Se si vuole usare un contenitore Estrazione frasi chiave in esecuzione nel computer locale con la configurazione predefinita, verrà chiamata l'operazione API REST seguente:

```http
POST http://localhost:5000/text/analytics/v2.0/keyPhrases
```

### <a name="billing"></a>Fatturazione

I contenitori Analisi del testo inviano le informazioni di fatturazione ad Azure usando una corrispondente risorsa di Analisi del testo nell'account di Azure. Ai fini della fatturazione, vengono usate dai contenitori Analisi del testo le opzioni della riga di comando seguenti:

| Opzione | Descrizione |
|--------|-------------|
| `ApiKey` | Chiave API della risorsa di Analisi del testo usata per tenere traccia delle informazioni di fatturazione.<br/>Il valore di questa opzione deve essere impostato su una chiave API per la risorsa di Analisi del testo sottoposta a provisioning specificata in `Billing`. |
| `Billing` | Endpoint della risorsa di Analisi del testo usata per tenere traccia delle informazioni di fatturazione.<br/>Il valore di questa opzione deve essere impostato sull'URI dell'endpoint di una risorsa di Analisi del testo sottoposta a provisioning.|
| `Eula` | Indica che è stata accettata la licenza per il contenitore.<br/>Il valore di questa opzione deve essere impostato su `accept`. |

> [!IMPORTANT]
> Tutte e tre le opzioni devono essere specificate con valori validi per consentire l'avvio del contenitore.

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](../text-analytics-resource-container-config.md).

## <a name="summary"></a>Riepilogo

In questo articolo sono stati descritti i concetti e il flusso di lavoro per scaricare, installare ed eseguire i contenitori di Analisi del testo. In sintesi:

* Analisi del testo fornisce tre contenitori Linux per Docker, che incapsulano funzionalità di estrazione di frasi chiave, rilevamento della lingua e analisi del sentiment.
* Le immagini dei contenitori vengono scaricate da un registro contenitori privato in Azure.
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile usare l'API REST o l'SDK per chiamare le operazioni nei contenitori di Analisi del testo specificando l'URI host del contenitore.
* Quando si crea un'istanza di un contenitore, è necessario specificare le informazioni di fatturazione.
* ** I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza connessione ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di Servizi cognitivi non inviano i dati dei clienti (ad esempio, l'immagine o il testo analizzato) a Microsoft.  

## <a name="next-steps"></a>Passaggi successivi

* Rivedere [Configurare i contenitori](../text-analytics-resource-container-config.md) per informazioni sulle impostazioni di configurazione.
* Rivedere [Informazioni su Analisi del testo](../overview.md) per altre informazioni su estrazione di frasi chiave, rilevamento della lingua e analisi del sentiment.  
* Fare riferimento all'[API Analisi del testo](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) per informazioni dettagliate sui metodi supportati dal contenitore.
* Fare riferimento alle [domande frequenti](../text-analytics-resource-faq.md) per risolvere i problemi correlati alle funzionalità di Analisi del testo.