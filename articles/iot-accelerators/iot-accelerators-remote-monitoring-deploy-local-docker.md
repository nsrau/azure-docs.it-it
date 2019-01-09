---
title: Distribuire localmente la soluzione di monitoraggio remoto - Docker - Azure | Microsoft Docs
description: Questa guida pratica illustra come distribuire l'acceleratore di soluzioni di monitoraggio remoto in un computer locale tramite Docker a scopo di test e sviluppo.
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: cf3c30d33e618ae3fd9d4ad942c77d211a414e82
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2018
ms.locfileid: "53601152"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>Distribuire localmente l'acceleratore di soluzioni di monitoraggio remoto - Docker

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Questo articolo illustra come distribuire l'acceleratore di soluzioni di monitoraggio remoto in un computer locale a scopo di test e sviluppo. Si apprenderà come distribuire i microservizi in contenitori Docker locali. Una distribuzione di microservizi locale usa i servizi cloud seguenti: servizi IoT Hub, Cosmos DB, Azure Streaming Analytics e Azure Time Series Insights.

Se si intende eseguire l'acceleratore di soluzioni di monitoraggio remoto in un IDE nel computer locale, vedere [Distribuire l'acceleratore di soluzioni di monitoraggio remoto localmente - Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="prerequisites"></a>Prerequisiti

Per distribuire i servizi di Azure usati dall'acceleratore di soluzioni di monitoraggio remoto, è necessaria una sottoscrizione di Azure attiva.

Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Impostazioni del computer

Per completare la distribuzione in locale, è necessario che nel computer di sviluppo locale siano installati gli strumenti seguenti:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [Node.js v8](https://nodejs.org/) - Questo software è un prerequisito per l'interfaccia della riga di comando di PCS usata dagli script per creare le risorse di Azure. Non usare Node.js v10.

> [!NOTE]
> Questi strumenti sono disponibili in numerose piattaforme, tra cui Windows, Linux e iOS.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>Eseguire i microservizi in Docker

Aprire un nuovo prompt dei comandi per assicurarsi di avere accesso alle variabili di ambiente impostate dallo script **start.cmd**. In Windows è possibile verificare l'impostazione delle variabili di ambiente eseguendo il comando seguente:

```cmd
set PCS
```

Il comando mostra tutte le variabili di ambiente impostate dallo script **start.cmd**.

Verificare che Docker sia in esecuzione nel computer locale.

I microservizi in esecuzione nei contenitori Docker locali devono accedere ai servizi cloud di Azure. È possibile testare la connettività Internet dell'ambiente Docker usando il comando seguente per effettuare il ping di un indirizzo Internet dall'interno di un contenitore:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

Per eseguire l'acceleratore di soluzioni, passare alla cartella **services\\scripts\\local** nell'ambiente della riga di comando ed eseguire questo comando:

```cmd/sh
docker-compose up
```

Alla prima esecuzione di questo comando, Docker scarica le immagini dei microservizi dall'hub Docker per creare i contenitori in locale. Nelle esecuzioni successive Docker esegue immediatamente i contenitori.

> [!TIP]
> Microsoft pubblica spesso nuove immagini di Docker con nuove funzionalità. È possibile usare il set di comandi seguente per la pulizia dei contenitori Docker locali e delle immagini corrispondenti prima di eseguire il pull di quelli più recenti:

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

È possibile usare una shell separata per visualizzare i log del contenitore. Per prima cosa trovare l'ID contenitore con il comando `docker ps`. Usare quindi `docker logs {container-id} --tail 1000` per visualizzare le ultime 1000 voci per il contenitore specificato.

### <a name="start-the-stream-analytics-job"></a>Avviare il processo di analisi di flusso

Per avviare il processo di analisi di flusso, seguire questa procedura:

1. Passare al [portale di Azure](https://portal.azure.com).
1. Passare al **gruppo di risorse** creato per la soluzione. Il nome del gruppo di risorse è il nome della soluzione scelto quando è stato eseguito lo script **start.cmd**.
1. Fare clic su **Processo di Analisi di flusso** nell'elenco delle risorse.
1. Nella pagina di **panoramica** del processo di Analisi di flusso fare clic sul pulsante **Avvia**. Fare clic su **Avvia** per avviare il processo.

### <a name="connect-to-the-dashboard"></a>Connettersi al dashboard

Per accedere alla dashboard della soluzione di monitoraggio remoto, passare a [http://localhost:8080](http://localhost:8080) nel browser. È ora possibile usare l'interfaccia utente Web e i microservizi locali.

## <a name="clean-up"></a>Eseguire la pulizia

Per evitare l'addebito di costi non necessari, al termine dei test rimuovere i servizi cloud dalla sottoscrizione di Azure. Per rimuovere i servizi, passare al [portale di Azure](https://ms.portal.azure.com) ed eliminare il gruppo di risorse creato dallo script **start.cmd**.

Usare il comando `docker-compose down --rmi all` per rimuovere le immagini Docker e liberare spazio nel computer locale. È anche possibile eliminare la copia locale del repository per il monitoraggio remoto che è stata creata clonando il codice sorgente da GitHub.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata distribuita la soluzione di monitoraggio remoto, il passaggio successivo consiste nell'[esplorare le funzionalità del dashboard della soluzione](quickstart-remote-monitoring-deploy.md).
