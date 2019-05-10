---
title: Installare ed eseguire i contenitori - Personalizza esperienze
titleSuffix: Azure Cognitive Services
description: Come scaricare, installare ed eseguire i contenitori per Personalizza esperienze.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/09/2019
ms.author: edjez
ms.openlocfilehash: a197531a7c78823271c0a5fa5413b76746f63a9a
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507022"
---
# <a name="install-and-run-personalizer-containers"></a>Installare ed eseguire i contenitori di Personalizza esperienze

Il servizio Personalizza esperienze ha i contenitori seguenti: 

|Funzione|Funzionalità|
|-|-|
|Personalizza esperienze|Determinare l'azione più efficace dal contesto corrente del contenuto e dell'utente.|

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di usare i contenitori del servizio Personalizza esperienze è necessario soddisfare i prerequisiti seguenti:

|Obbligatoria|Scopo|
|--|--|
|Motore Docker| È necessario il motore Docker installato in un [computer host](#the-host-computer). Docker offre pacchetti per la configurazione dell'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> **In Windows** Docker deve essere configurato anche per supportare i contenitori Linux.<br><br>|
|Familiarità con Docker | È opportuno avere una conoscenza di base dei concetti relativi a Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.| 
|Risorsa del servizio Personalizza esperienze |Per usare questi contenitori, è necessario avere:<br><br>Una risorsa del _servizio Personalizza esperienze_ di Azure per ottenere la chiave di fatturazione associata e l'URI dell'endpoint di fatturazione. Entrambi i valori sono disponibili nelle pagine delle chiavi e della panoramica del servizio Personalizza esperienze nel portale di Azure e sono necessari per avviare il contenitore.<br><br>**{BILLING_KEY}** : chiave della risorsa<br><br>**{BILLING_ENDPOINT_URI}** : un esempio di URI dell'endpoint è: `https://westus.api.cognitive.microsoft.com/`|

### <a name="the-host-computer"></a>Computer host

L'**host** è il computer che esegue il contenitore Docker. Può essere un computer nell'ambiente locale o un servizio di hosting Docker in Azure, tra cui:

* [Servizio Azure Kubernetes](https://docs.microsoft.com/aks/index.yml)
* [Istanze di Azure Container](https://docs.microsoft.com/container-instances/index.yml)
* Cluster [Kubernetes](https://kubernetes.io/) distribuito in [Azure Stack](https://docs.microsoft.com/azure-stack/index.yml). Per altre informazioni, vedere [Deploy Kubernetes to Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md) (Distribuire Kubernetes in Azure Stack).

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

La tabella seguente indica i core CPU minimi e consigliati e la memoria da allocare per ogni contenitore del servizio Personalizza esperienze.

| Contenitore | Minima | Consigliato |
|-----------|---------|-------------|
|Personalizza esperienze | 1 core, 4 GB di memoria | 2 core, 8 GB di memoria |

Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.

Il contenitore deve potersi connettere ad Hub eventi di Azure per inoltrare le informazioni nelle chiamate di classificazione e assegnazione di punteggi al server di Personalizza esperienze in Azure. Deve anche potersi connettere all'API Personalizza esperienze per caricare la configurazione richiesta e i modelli di Machine Learning più recenti.

## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine del contenitore con `docker pull`

Sono disponibili immagini del contenitore per il servizio Personalizza esperienze. 

| Contenitore | Repository |
|-----------|------------|
| Personalizza esperienze | `mcr.microsoft.com/azure-cognitive-services/personalizer:latest` |

> [!TIP]
> È possibile usare il comando [docker images](https://docs.docker.com/engine/reference/commandline/images/) per visualizzare l'elenco delle immagini dei contenitori scaricate. Ad esempio, il comando seguente visualizza l'ID, il repository e il tag di ogni immagine del contenitore scaricata, in formato tabella:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID            REPOSITORY                                                                TAG
>  ebbee78a6baa        mcr.microsoft.com/azure-cognitive-services/personalizer                 latest
>  ``` 

### <a name="docker-pull-for-the-personalizer-service-container"></a>Docker pull per il contenitore del servizio Personalizza esperienze

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/personalizer:latest
```

## <a name="how-the-container-works"></a>Funzionamento del contenitore

Il contenitore di Personalizza esperienze rappresenta la parte client del servizio Personalizza esperienze. Quando il contenitore viene eseguito, ottiene i modelli e le impostazioni di configurazione dal servizio Personalizza esperienze nel cloud di Azure. Il servizio contenitore usa queste informazioni per rispondere alle richieste di **classificazione** e **assegnazione di punteggi**. Il contenitore invia queste richieste anche alla risorsa Personalizza esperienze nel cloud di Azure. Queste informazioni vengono quindi usate per eseguire il training del modello di cicli di Personalizza esperienze, in base all'impostazione corrente per la frequenza di aggiornamento del modello. Il modello aggiornato viene rinviato al contenitore. 

![Nell'ambiente locale, l'architettura locale per il client del contenitore di Personalizza esperienze](./media/personalizer-container-howto/personalizer-container-architecture.png)

## <a name="how-to-use-the-container"></a>Come usare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usare il contenitore.

1. [Eseguire il contenitore](#run-the-container-with-docker-run), con le impostazioni di fatturazione necessarie. Sono disponibili altri [esempi](personalizer-container-configuration.md#example-docker-run-commands) del comando `docker run`. 
1. [Eseguire le query sull'endpoint di stima del contenitore](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con `docker run`

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire uno qualsiasi dei tre contenitori. Il comando usa i parametri seguenti:

| Placeholder | Valore |
|-------------|-------|
|{BILLING_KEY} | Questa chiave viene usata per avviare il contenitore ed è disponibile nella pagina delle chiavi del servizio Personalizza esperienze del portale di Azure.  |
|{BILLING_ENDPOINT_URI} | Il valore dell'URI dell'endpoint di fatturazione è disponibile nella pagina della panoramica del servizio Personalizza esperienze del portale di Azure.|

Sostituire i parametri con i valori personalizzati nel comando `docker run` di esempio seguente.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/personalizer\
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Questo comando:

* Esegue un contenitore del servizio Personalizza esperienze dall'immagine del contenitore
* Alloca un core CPU e 4 GB di memoria
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host. 

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](#billing).

### <a name="run-multiple-containers-on-the-same-host"></a>Eseguire più contenitori nello stesso host

Se si intende eseguire più contenitori con porte esposte, assicurarsi di eseguire ogni contenitore con una porta diversa. Eseguire ad esempio il primo contenitore sulla porta 5000 e il secondo sulla porta 5001.

Sostituire `<container-registry>` e `<container-name>` con i valori dei contenitori usati. Questi non devono trovarsi necessariamente nello stesso contenitore. È possibile avere il contenitore di Personalizza esperienze e il contenitore LUIS in esecuzione insieme nell'HOST oppure è possibile avere più contenitori di Personalizza esperienze in esecuzione. 

Eseguire il primo contenitore sulla porta 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Eseguire il secondo contenitore sulla porta 5001.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Ogni contenitore successivo deve essere su una porta diversa. 

## <a name="query-the-containers-prediction-endpoint"></a>Eseguire query sull'endpoint di stima del contenitore

Il contenitore fornisce API dell'endpoint di stima di query basate su REST. 

Usare l'host, https://localhost:5000, per le API del contenitore.

## <a name="stop-the-container"></a>Arrestare il contenitore

Per arrestare il contenitore, nell'ambiente della riga di comando in cui è in esecuzione il contenitore premere **CTRL+C**.

## <a name="troubleshoot"></a>Risolvere problemi

Se si esegue il contenitore con un punto di [montaggio](personalizer-container-configuration.md#mount-settings) di output e la registrazione attivata, il contenitore genera file di log utili per risolvere i problemi che si verificano durante l'avvio o l'esecuzione del contenitore. 

## <a name="container-api-documentation"></a>Documentazione dell'API del contenitore

Il contenitore fornisce documentazione completa per gli endpoint, oltre a una funzionalità `Try it now`. Questa funzionalità consente di immettere le impostazioni personalizzate in un modulo HTML basato sul Web ed eseguire la query senza dover scrivere codice. Dopo che la query restituisce il risultato, viene fornito un comando CURL di esempio per illustrare il formato richiesto per il corpo e le intestazioni HTTP. 

> [!TIP]
> Leggere la [specifica OpenAPI](https://swagger.io/docs/specification/about/), che descrive le operazioni API supportate dal contenitore, dall'URI `/swagger` relativo. Ad esempio: 
>
>  ```http
>  http://localhost:5000/swagger
>  ```

## <a name="billing"></a>Fatturazione

I contenitori del servizio Personalizza esperienze inviano le informazioni di fatturazione ad Azure usando una risorsa _Personalizza esperienze_ nell'account di Azure. 

I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione.  

Il comando `docker run` usa gli argomenti seguenti a scopo di fatturazione:

| Opzione | DESCRIZIONE |
|--------|-------------|
| `ApiKey` | La chiave API della risorsa del servizio _Personalizza esperienze_ usata per tenere traccia delle informazioni di fatturazione. |
| `Billing` | L'endpoint della risorsa del servizio _Personalizza esperienze_ usata per tenere traccia delle informazioni di fatturazione.|
| `Eula` | Indica che è stata accettata la licenza per il contenitore.<br/>Il valore di questa opzione deve essere impostato su `accept`. |

> [!IMPORTANT]
> Tutte e tre le opzioni devono essere specificate con valori validi per consentire l'avvio del contenitore.

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](personalizer-container-configuration.md).

## <a name="summary"></a>Summary

In questo articolo sono stati descritti i concetti e il flusso di lavoro per scaricare, installare ed eseguire i contenitori del servizio Personalizza esperienze. In sintesi:

* Il servizio Personalizza esperienze fornisce contenitori Linux per Docker, incapsulando la personalizzazione.
* Le immagini dei contenitori vengono scaricate da Registro Container Microsoft in Azure.
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile usare l'API REST o l'SDK per chiamare le operazioni nei contenitori del servizio Personalizza esperienze specificando l'URI dell'host del contenitore.
* Quando si crea un'istanza di un contenitore, è necessario specificare le informazioni di fatturazione.

> [!IMPORTANT]
> I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di Personalizza esperienze invieranno i dati delle richieste anche al servizio corrispondente in Azure ai fini del training online e riceveranno periodicamente modelli aggiornati da Azure.

## <a name="next-steps"></a>Passaggi successivi

[Come configurare il contenitore Docker per il comando `Docker Run`](personalizer-container-configuration.md)