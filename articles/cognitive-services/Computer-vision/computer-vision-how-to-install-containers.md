---
title: Come installare ed eseguire i contenitori - Visione artificiale
titlesuffix: Azure Cognitive Services
description: Come scaricare, installare ed eseguire i contenitori per Visione artificiale in questa esercitazione dettagliata.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 6a6468cd71cf83c627f6dd72e5a1fc5564361d50
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53579856"
---
# <a name="install-and-run-recognize-text-containers"></a>Installare ed eseguire contenitori di riconoscimento del testo

La containerizzazione è un approccio alla distribuzione del software in cui un'applicazione o un servizio viene compresso come immagine del contenitore. La configurazione e le dipendenze dell'applicazione o del servizio sono incluse nell'immagine del contenitore. L'immagine del contenitore può quindi essere distribuita in un host contenitore senza alcuna variazione o con modifiche minime. I contenitori sono isolati fra loro e dal sistema operativo sottostante, con un footprint inferiore a quello di una macchina virtuale. È possibile creare istanze dei contenitori dalle immagini per attività a breve termine e rimuoverle quando non sono più necessarie.

La parte di riconoscimento del testo di Visione artificiale è disponibile anche come un contenitore Docker. Permette di rilevare ed estrarre testo stampato dalle immagini di diversi oggetti con superfici e sfondi diversi, ad esempio ricette, poster e biglietti da visita.  
> [!IMPORTANT]
> Il contenitore di riconoscimento del testo attualmente funziona solo con la lingua inglese.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="preparation"></a>Operazioni preliminari

Prima di usare il contenitore Riconoscimento del testo, è necessario soddisfare i prerequisiti seguenti:

**Motore Docker**: il motore Docker deve essere installato in locale. Docker offre pacchetti per la configurazione dell'ambiente in [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) e [Windows](https://docs.docker.com/docker-for-windows/). In Windows Docker deve essere configurato per supportare i contenitori Linux. I contenitori Docker possono anche essere distribuiti direttamente nel [servizio Azure Kubernetes](../../aks/index.yml), in [Istanze di Azure Container](../../container-instances/index.yml) o in un cluster [Kubernetes](https://kubernetes.io/) distribuito in [Azure Stack](../../azure-stack/index.yml). Per altre informazioni sulla distribuzione di Kubernetes in Azure Stack, vedere [Deploy Kubernetes to Azure Stack](../../azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md) (Distribuzione di Kubernetes in Azure Stack).

Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure.

**Familiarità con Registro Container Microsoft e Docker**: è opportuno avere una conoscenza di base dei concetti relativi a Registro Container Microsoft e Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.  

Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti dei contenitori

Il contenitore Riconoscimento del testo richiede un minimo di 1 core CPU (almeno 2,6 GHz) e 8 GB di memoria allocata, ma sono consigliabili almeno 2 core CPU e 8 GB di memoria allocata.

## <a name="request-access-to-the-private-container-registry"></a>Richiedere l'accesso al registro contenitori privato

È necessario innanzitutto completare e inviare il [modulo di richiesta di contenitori di visione di Servizi cognitivi](https://aka.ms/VisionContainersPreview) per richiedere l'accesso al contenitore Riconoscimento del testo. Il modulo richiede informazioni sull'utente, sull'azienda e sullo scenario utente per cui si userà il contenitore. Dopo l'invio, il team di Servizi cognitivi di Azure esamina il modulo per verificare che siano soddisfatti i criteri di accesso al registro contenitori privato.

> [!IMPORTANT]
> È necessario usare un indirizzo di posta elettronica associato un account Microsoft o Azure Active Directory (Azure AD) nel modulo.

Se la richiesta viene approvata, si riceverà un messaggio di posta elettronica con istruzioni su come ottenere le credenziali e accedere al registro contenitori privato.

## <a name="create-a-computer-vision-resource-on-azure"></a>Creare una risorsa di Visione artificiale in Azure

Se si desidera usare il contenitore Riconoscimento del testo, è necessario creare una risorsa di Visione artificiale in Azure. Dopo aver creato la risorsa, usare la chiave di sottoscrizione e l'URL dell'endpoint della risorsa per creare un'istanza del contenitore. Per altre informazioni sulla creazione di un'istanza del contenitore, vedere [Creare un'istanza di un contenitore da un'immagine del contenitore scaricata](#instantiate-a-container-from-a-downloaded-container-image).

Seguire i passaggi seguenti per creare e recuperare informazioni da una risorsa di Azure:

1. Creare una risorsa di Azure nel portale di Azure.  
   Per usare il contenitore Riconoscimento del testo, è necessario creare innanzitutto una risorsa Visione artificiale corrispondente nel portale di Azure. Per altre informazioni, vedere [Avvio rapido: Creare un account di Servizi cognitivi nel portale di Azure](../cognitive-services-apis-create-account.md).

1. Ottenere l'URL dell'endpoint e la chiave di sottoscrizione per la risorsa di Azure.  
   Una volta creata la risorsa di Azure, sarà necessario usare l'URL dell'endpoint e la chiave di sottoscrizione della risorsa per creare un'istanza del contenitore Riconoscimento del testo corrispondente. È possibile copiare l'URL dell'endpoint e la chiave di sottoscrizione rispettivamente dalle pagine di Avvio rapido e Chiavi della risorsa Visione artificiale nel portale di Azure.

## <a name="log-in-to-the-private-container-registry"></a>Accedere al registro contenitori privato

Esistono diversi modi per eseguire l'autenticazione con il registro contenitori privato per i contenitori di Servizi cognitivi, ma il metodo consigliato dalla riga di comando consiste nell'usare l'[interfaccia della riga di comando di Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Usare il comando [docker login](https://docs.docker.com/engine/reference/commandline/login/), come illustrato nell'esempio seguente, per accedere a `containerpreview.azurecr.io`, il registro contenitori privato per i contenitori di Servizi cognitivi. Sostituire *\<username\>* con il nome utente e *\<password\>* con la password specificata nelle credenziali ricevute dal team di Servizi cognitivi di Azure.

```docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Se le credenziali sono protette in un file di testo, è possibile concatenare il contenuto del file mediante il comando `cat` al comando `docker login`, come mostrato nell'esempio seguente. Sostituire *\<passwordFile\>* con il percorso e il nome del file di testo contenente la password e *\<username\>* con il nome utente specificato nelle credenziali.

```docker
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

## <a name="download-container-images-from-the-private-container-registry"></a>Scaricare le immagini dei contenitori dal registro contenitori privato

L'immagine del contenitore Riconoscimento del testo è disponibile in un registro contenitori Docker privato, denominato `containerpreview.azurecr.io`, in Registro Azure Container. L'immagine del contenitore Riconoscimento del testo deve essere scaricata dal repository per eseguire il contenitore in locale.

Usare il comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare un'immagine del contenitore dal repository. Per scaricare, ad esempio, l'immagine del contenitore Riconoscimento del testo più recente dal repository, usare il comando seguente:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

Per una descrizione completa dei tag disponibili per il contenitore Riconoscimento del testo, vedere [Riconoscimento del testo](https://go.microsoft.com/fwlink/?linkid=2018655) nell'hub Docker.

> [!TIP]
> È possibile usare il comando [docker images](https://docs.docker.com/engine/reference/commandline/images/) per elencare le immagini dei contenitori scaricate. Ad esempio, il comando seguente visualizza l'ID, il repository e il tag di ogni immagine del contenitore scaricata, in formato tabella:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>Creare un'istanza di un contenitore da un'immagine del contenitore scaricata

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per creare un'istanza di un contenitore da un'immagine del contenitore scaricata. Ad esempio, il comando seguente:

* Creare un'istanza di un contenitore da un'immagine del contenitore Riconoscimento del testo
* Alloca due core CPU e 8 GB di memoria
* Espone la porta TCP 5000 e alloca un pseudo terminale (TTY) per il contenitore
* Rimuove automaticamente il contenitore dopo la chiusura

```docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westus.api.cognitive.microsoft.com/vision/v2.0 ApiKey=0123456789
```

Una volta creata un'istanza, sarà possibile chiamare operazioni dal contenitore mediante l'URI dell'host del contenitore. L'URI dell'host seguente rappresenta ad esempio il contenitore Riconoscimento del testo di cui è stata creata un'istanza nell'esempio precedente:

```http
http://localhost:5000/
```

> [!IMPORTANT]
> È possibile accedere alla [specifica OpenAPI](https://swagger.io/docs/specification/about/) (in precedenza nota come specifica Swagger), che descrive le operazioni supportate da un contenitore di cui è stata creata un'istanza, dall'URI relativo di `/swagger` per il contenitore. L'URI seguente consente ad esempio di accedere alla specifica OpenAPI per il contenitore Riconoscimento del testo di cui è stata creata un'istanza nell'esempio precedente:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

È possibile [chiamare le operazioni API REST](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtocallvisionapi) disponibili nel contenitore per il riconoscimento del testo in modo sincrono o asincrono, o usare il [SDK di Visione artificiale di Servizi cognitivi di Azure](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision) per chiamare le operazioni.  
> [!IMPORTANT]
> Se si intende usare la libreria client con il contenitore, è necessario disporre SDK di Visione artificiale di Servizi cognitivi di Azure 3.2.0 o versione successiva.

### <a name="asynchronous-text-recognition"></a>Riconoscimento del testo asincrono

È possibile usare le operazioni `POST /vision/v2.0/recognizeText` e `GET /vision/v2.0/textOperations/*{id}*` in parallelo per riconoscere in modo asincrono il testo stampato in un'immagine, in modo simile a come il servizio Visione artificiale usa le operazioni REST corrispondenti. Il contenitore Riconoscimento del testo riconosce ora solo testo stampato e testo non scritto a mano, pertanto il parametro `mode` che viene in genere specificato per l'operazione del servizio Visione artificiale viene ignorato dal contenitore di Riconoscimento del testo.

### <a name="synchronous-text-recognition"></a>Riconoscimento del testo sincrono

È possibile usare l'operazione `POST /vision/v2.0/recognizeTextDirect` per riconoscere il testo stampato in un'immagine in modo sincrono. Poiché questa operazione è sincrona, il corpo della richiesta per questa operazione è analoga a quella per l'operazione `POST /vision/v2.0/recognizeText`, ma il corpo della risposta per questa operazione è uguale a quello restituito dall'operazione `GET /vision/v2.0/textOperations/*{id}*`.

### <a name="billing"></a>Fatturazione

Il contenitore Riconoscimento del testo invia le informazioni di fatturazione ad Azure usando una risorsa Visione artificiale corrispondente nell'account di Azure. Le opzioni seguenti vengono usate dal contenitore Riconoscimento del testo ai fini di fatturazione:

| Opzione | DESCRIZIONE |
|--------|-------------|
| `ApiKey` | La chiave API della risorsa Visione artificiale usata per tenere traccia delle informazioni di fatturazione.<br/>Il valore di questa opzione deve essere impostato su una chiave API per la risorsa Visione artificiale di Azure sottoposta a provisioning specificata in `Billing`. |
| `Billing` | L'endpoint della risorsa Visione artificiale usata per tenere traccia delle informazioni di fatturazione.<br/>Il valore di questa opzione deve essere impostato sull'URI dell'endpoint di una risorsa Visione artificiale di Azure sottoposta a provisioning.|
| `Eula` | Indica che è stata accettata la licenza per il contenitore.<br/>Il valore di questa opzione deve essere impostato su `accept`. |

> [!IMPORTANT]
> Tutte e tre le opzioni devono essere specificate con valori validi per consentire l'avvio del contenitore.

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](computer-vision-resource-container-config.md).

## <a name="summary"></a>Summary

In questo articolo sono stati descritti i concetti e il flusso di lavoro per scaricare, installare ed eseguire i contenitori Visione artificiale. In sintesi:

* Visione artificiale fornisce uno dei contenitori di Linux per Docker, per rilevare ed estrarre testo stampato.
* Le immagini dei contenitori vengono scaricate da un registro contenitori privato in Azure.
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile usare l'API REST o l'SDK per chiamare le operazioni nei contenitori Visione artificiale specificando l'URI dell'host del contenitore.
* Quando si crea un'istanza di un contenitore, è necessario specificare le informazioni di fatturazione.

> [!IMPORTANT]
> I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di Servizi cognitivi non inviano i dati dei clienti (ad esempio, l'immagine o il testo analizzato) a Microsoft.

## <a name="next-steps"></a>Passaggi successivi

* Rivedere [Configurare i contenitori](computer-vision-resource-container-config.md) per informazioni sulle impostazioni di configurazione.
* Rivedere [Panoramica di Visione artificiale](Home.md) per altre informazioni sul riconoscimento di testo scritto a mano e stampato  
* Fare riferimento all'[API Visione artificiale](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) per informazioni dettagliate sui metodi supportati dal contenitore.
* Fare riferimento alle [domande frequenti](FAQ.md) per risolvere i problemi correlati alla funzionalità Visione artificiale.
