---
title: Come installare ed eseguire i contenitori
titlesuffix: Face - Cognitive Services - Azure
description: Come scaricare, installare ed eseguire i contenitori per Face in questa esercitazione dettagliata.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 27a4bccfbac73c7c8c902a59fdd4cafe0c420c31
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634897"
---
# <a name="install-and-run-containers"></a>Installare ed eseguire i contenitori

La containerizzazione è un approccio alla distribuzione del software in cui un'applicazione o un servizio viene compresso come un'immagine del contenitore. La configurazione e le dipendenze per l'applicazione o il servizio sono incluse nell'immagine del contenitore. L'immagine del contenitore può quindi essere distribuita in un host contenitore senza alcuna variazione o con modifiche minime. I contenitori sono isolati fra loro e il sistema operativo sottostante, con un footprint ridotto rispetto a una macchina virtuale. È possibile creare istanze dei contenitori dalle immagini per attività a breve termine e rimuoverle quando non sono più necessarie.

Viso offre un contenitore Linux standardizzato per Docker, denominato Viso, che rileva i visi umani nelle immagini e identifica gli attributi, ad esempio i punti di riferimento del viso (come naso e occhi), il sesso, l'età e altre caratteristiche con previsioni basate sul computer. Oltre al rilevamento, Viso può verificare se due volti nella stessa immagine o in immagini diverse sono uguali mediante un punteggio di attendibilità oppure confrontare i visi in un database per verificare se esistano già visi simili o identici. È anche possibile organizzare i visi simili in gruppi mediante caratteristiche condivise.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="preparation"></a>Operazioni preliminari

Prima di usare il contenitore Viso, è necessario soddisfare i prerequisiti seguenti:

**Motore Docker**: il motore Docker deve essere installato localmente. Docker offre pacchetti per la configurazione dell'ambiente in [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) e [Windows](https://docs.docker.com/docker-for-windows/). In Windows Docker deve essere configurato per supportare i contenitori Linux. I contenitori Docker possono anche essere distribuiti direttamente nel [servizio Kubernetes di Azure](/azure/aks/), in [istanze di contenitore di Azure](/azure/container-instances/) o in un cluster [Kubernetes](https://kubernetes.io/) distribuito in [Azure Stack](/azure/azure-stack/). Per altre informazioni sulla distribuzione di Kubernetes in Azure Stack, vedere [Deploy Kubernetes to Azure Stack](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy) (Distribuzione di Kubernetes in Azure Stack).

Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure.

**Familiarità con Registro contenitori Microsoft e Docker**: è opportuno avere una conoscenza di base dei concetti relativi a Registro contenitori Microsoft e Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.  

Per una panoramica sui concetti fondamentali relativi a Docker e ai contenitori, vedere [Panoramica Docker](https://docs.docker.com/engine/docker-overview/).

### <a name="server-requirements-and-recommendations"></a>Indicazioni e requisiti del server

Il contenitore Viso richiede un minimo di 1 core CPU (almeno 2,6 GHz) e 4 GB di memoria allocata, ma sono consigliabili almeno 2 core CPU e 6 GB di memoria allocata.

## <a name="request-access-to-the-private-container-registry"></a>Richiedere l'accesso al registro contenitori privato

È necessario innanzitutto completare e inviare il [modulo di richiesta di contenitori di visione di Servizi cognitivi](https://aka.ms/VisionContainersPreview) per richiedere l'accesso al contenitore Viso. Il modulo richiede informazioni sull'utente, sull'azienda e sullo scenario utente per cui si userà il contenitore. Dopo l'invio, il team di Servizi cognitivi di Azure esamina il modulo per verificare che siano soddisfatti i criteri di accesso al registro contenitori privato.

> [!IMPORTANT]
> È necessario usare un indirizzo di posta elettronica associato un account Microsoft o Azure Active Directory (Azure AD) nel modulo.

Se la richiesta viene approvata, si riceverà un messaggio di posta elettronica con istruzioni su come ottenere le credenziali e accedere al registro contenitori privato.

## <a name="create-a-face-resource-on-azure"></a>Creare una risorsa Viso in Azure

È necessario creare una risorsa Viso in Azure se si intende usare il contenitore Viso. Dopo aver creato la risorsa, usare la chiave di sottoscrizione e l'URL dell'endpoint della risorsa per creare un'istanza del contenitore. Per altre informazioni sulla creazione di un'istanza del contenitore, vedere [Creare un'istanza di un contenitore da un'immagine del contenitore scaricata](#instantiate-a-container-from-a-downloaded-container-image).

Seguire i passaggi seguenti per creare e recuperare informazioni da una risorsa Viso:

1. Creare una risorsa Viso nel portale di Azure.  
   Per usare il contenitore Viso, è necessario creare innanzitutto una risorsa Viso corrispondente nel portale di Azure. Per altre informazioni, vedere [Guida introduttiva: Creare un account Servizi cognitivi nel portale di Azure](../cognitive-services-apis-create-account.md).

   > [!IMPORTANT]
   > La risorsa Viso deve usare il piano tariffario F0.

1. Ottenere l'URL dell'endpoint e la chiave di sottoscrizione per la risorsa di Azure.  
   Una volta creata la risorsa di Azure, sarà necessario usare l'URL dell'endpoint e la chiave di sottoscrizione della risorsa per creare un'istanza del contenitore Viso corrispondente. È possibile copiare l'URL dell'endpoint e la chiave di sottoscrizione rispettivamente dalle pagine di Avvio rapido e Chiavi della risorsa Viso nel portale di Azure.

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

L'immagine del contenitore Viso è disponibile in un registro contenitori Docker privato, denominato `containerpreview.azurecr.io`, nel Registro contenitori di Azure. L'immagine del contenitore Viso deve essere scaricata dal repository per eseguire il contenitore in locale.

Usare il comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare un'immagine del contenitore dal repository. Per scaricare, ad esempio, l'immagine del contenitore Viso più recente dal repository, usare il comando seguente:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

Per una descrizione completa dei tag disponibili per il contenitore Viso, vedere [Riconoscimento del testo](https://go.microsoft.com/fwlink/?linkid=2018655) nell'hub Docker.

> [!TIP]
> È possibile usare il comando [docker images](https://docs.docker.com/engine/reference/commandline/images/) per elencare le immagini dei contenitori scaricate. Il comando seguente elenca ad esempio l'ID, il repository e il tag di ogni immagine del contenitore scaricata, formattata come tabella:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>Creare un'istanza di un contenitore da un'immagine del contenitore scaricata

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per creare un'istanza di un contenitore da un'immagine del contenitore scaricata. Ad esempio, il comando seguente:

* Crea un'istanza di un contenitore da un'immagine del contenitore Viso
* Alloca due core CPU e 6 GB di memoria
* Espone la porta TCP 5000 e alloca un pseudo terminale (TTY) per il contenitore
* Rimuove automaticamente il contenitore dopo la chiusura

```Docker
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789
```

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per creare un'istanza del contenitore e avviarlo.  Per altre informazioni, vedere[Fatturazione](#billing).

Una volta creata un'istanza, sarà possibile chiamare operazioni dal contenitore mediante l'URI dell'host del contenitore. L'URI dell'host seguente rappresenta ad esempio il contenitore Viso di cui è stata creata un'istanza nell'esempio precedente:

```http
http://localhost:5000/
```

> [!TIP]
> È possibile accedere alla [specifica OpenAPI](https://swagger.io/docs/specification/about/) (in precedenza nota come specifica Swagger), che descrive le operazioni supportate da un contenitore di cui è stata creata un'istanza, dall'URI relativo di `/swagger` per il contenitore. L'URI seguente consente ad esempio di accedere alla specifica OpenAPI per il contenitore Viso di cui è stata creata un'istanza nell'esempio precedente:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

È possibile [chiamare le operazioni API REST](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/howtodetectfacesinimage) disponibili nel contenitore o usare la [libreria client di Viso di Servizi cognitivi di Azure](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/) per chiamare le operazioni.  
> [!IMPORTANT]
> Se si intende usare la libreria client con il contenitore, è necessario disporre della libreria client di Viso di Servizi cognitivi di Azure 2.0 o versione successiva.

L'unica differenza tra la chiamata di un'operazione specifica dal contenitore o da un servizio corrispondente in Azure consiste nel fatto che si userà l'URI dell'host del contenitore, anziché l'URI dell'host di un'area di Azure. Se ad esempio si intende usare un'istanza di Viso in esecuzione nell'area di Azure Stati Uniti occidentali per rilevare i visi, chiamare l'operazione API REST seguente:

```http
POST https://westus.api.cognitive.microsoft.com/face/v1.0/detect
```

Se si intende usare un contenitore Viso in esecuzione nel computer locale nella configurazione predefinita per rilevare i visi, chiamare l'operazione API REST seguente:

```http
POST http://localhost:5000/face/v1.0/detect
```

### <a name="billing"></a>Fatturazione

Il contenitore Viso invia le informazioni di fatturazione ad Azure usando una risorsa Viso corrispondente nell'account di Azure. Le opzioni della riga di comando seguenti vengono usate dal contenitore Viso ai fini di fatturazione:

| Opzione | DESCRIZIONE |
|--------|-------------|
| `ApiKey` | Chiave API della risorsa Viso usata per tenere traccia delle informazioni di fatturazione.<br/>Il valore di questa opzione deve essere impostato su una chiave API per la risorsa Viso di Azure sottoposta a provisioning specificata in `Billing`. |
| `Billing` | Endpoint della risorsa Viso usata per tenere traccia delle informazioni di fatturazione.<br/>Il valore di questa opzione deve essere impostato sull'URI dell'endpoint di una risorsa Viso di Azure sottoposta a provisioning.|
| `Eula` | Indica che è stata accettata la licenza per il contenitore.<br/>Il valore di questa opzione deve essere impostato su `accept`. |

> [!IMPORTANT]
> Tutte le tre opzioni devono essere specificate con valori validi per consentire l'avvio del contenitore.

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](face-resource-container-config.md).

## <a name="summary"></a>Summary

In questo articolo sono stati descritti i concetti e il flusso di lavoro per scaricare, installare ed eseguire i contenitori Viso. In sintesi:

* Viso fornisce un contenitore Linux per Docker, denominato Viso, per rilevare i visi o identificarli mediante un database di persone.
* Le immagini dei contenitori vengono scaricate da un registro contenitori privato in Azure.
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile usare l'API REST o l'SDK per chiamare le operazioni nei contenitori Viso specificando l'URI dell'host del contenitore.
* Quando si crea un'istanza di un contenitore, è necessario specificare le informazioni di fatturazione.
* ** I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza connessione ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di Servizi cognitivi non inviano i dati dei clienti (ad esempio, l'immagine o il testo analizzato) a Microsoft.  

## <a name="next-steps"></a>Passaggi successivi

* Rivedere [Configurare i contenitori](face-resource-container-config.md) per le impostazioni di configurazione
* Rivedere [Face Overview](Overview.md) (Panoramica di Viso) per altre informazioni sul rilevamento e sull'identificazione di visi  
* Fare riferimento all'[API Viso](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) per informazioni dettagliate sui metodi supportati dal contenitore.
* Fare riferimento alle [domande frequenti](FAQ.md) per risolvere i problemi correlati alla funzionalità Viso.
