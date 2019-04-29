---
title: Distribuire la soluzione di monitoraggio remota in locale (tramite IDE di Visual Studio) - Azure | Microsoft Docs
description: Questa guida pratica illustra come distribuire l'acceleratore di soluzioni di monitoraggio remoto in un computer locale tramite Visual Studio a scopo di test e sviluppo.
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: fdb9b6c09e81ce29d2587fb0faca1aa80d28ea38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450597"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>Distribuire l'acceleratore di soluzioni di monitoraggio remoto localmente - Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Questo articolo illustra come distribuire l'acceleratore di soluzioni di monitoraggio remoto in un computer locale a scopo di test e sviluppo. Si apprenderà come eseguire i microservizi in Visual Studio. Una distribuzione di microservizi locale usa i servizi cloud seguenti: servizi Hub IoT, Cosmos DB, Analisi di flusso di Azure e Azure Time Series Insights nel cloud.

Se si intende eseguire l'acceleratore di soluzioni di monitoraggio remoto in Docker nel computer locale, vedere [Distribuire l'acceleratore di soluzioni di monitoraggio remoto localmente - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Prerequisiti

Per distribuire i servizi di Azure usati dall'acceleratore di soluzioni di monitoraggio remoto, è necessaria una sottoscrizione di Azure attiva.

Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Impostazioni del computer

Per completare la distribuzione in locale, è necessario che nel computer di sviluppo locale siano installati gli strumenti seguenti:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) - Questo software è un prerequisito per l'interfaccia della riga di comando di PCS usata dagli script per creare le risorse di Azure. Non usare Node.js v10.

> [!NOTE]
> Visual Studio è disponibile per Windows e Mac.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Eseguire i microservizi

In questa sezione vengono eseguiti i microservizi di monitoraggio remoto. Eseguire l'interfaccia utente Web in modo nativo, il servizio di simulazione dispositivi in Docker e i microservizi in Visual Studio.

### <a name="run-the-device-simulation-service"></a>Eseguire il servizio di simulazione dispositivi

Aprire un nuova finestra del prompt dei comandi per assicurarsi di avere accesso alle variabili di ambiente impostate dallo script **start.cmd** nella sezione precedente.

Eseguire questo comando per avviare il contenitore Docker per il servizio di simulazione dispositivi. Il servizio consente di simulare i dispositivi per la soluzione di monitoraggio remoto.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Distribuire tutti gli altri microservizi nel computer locale

I passaggi seguenti mostrano come eseguire i microservizi di monitoraggio remoto in Visual Studio 2017:

1. Avviare Visual Studio 2017
1. Aprire la soluzione **remote-monitoring.sln** nella cartella **servizi** nella copia locale del repository.
1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse e quindi scegliere **Proprietà**.
1. Selezionare **Proprietà comuni > Progetto di avvio**.
1. Selezionare **Progetti di avvio multipli** e impostare **Azione** su **Avvio** per i progetti seguenti:
    * WebService (asa-manager\WebService)
    * WebService (auth\WebService)
    * WebService (config\WebService)
    * WebService (device-telemetry\WebService)
    * WebService (iothub-manager\WebService)
    * WebService (storage-adapter\WebService)
1. Fare clic su **OK** per salvare le modifiche.
1. Fare clic su **Debug > Avvia debug** per compilare ed eseguire i servizi Web nel computer locale.

Ogni servizio Web consente di aprire una finestra del Web browser e il prompt dei comandi. Al prompt dei comandi, viene visualizzato un output dal servizio in esecuzione e la finestra del browser consente di monitorare lo stato. Non chiudere il prompt dei comandi o le pagine Web perché questa azione arresta il servizio Web.

### <a name="start-the-stream-analytics-job"></a>Avviare il processo di analisi di flusso

Per avviare il processo di analisi di flusso, seguire questa procedura:

1. Passare al [portale di Azure](https://portal.azure.com).
1. Passare al **gruppo di risorse** creato per la soluzione. Il nome del gruppo di risorse è quello scelto per la soluzione scelto quando è stato eseguito lo script **start.cmd**.
1. Fare clic su **Processo di Analisi di flusso** nell'elenco delle risorse.
1. Nella pagina di **panoramica** del processo di Analisi di flusso fare clic sul pulsante **Avvia**. Fare clic su **Avvia** per avviare il processo.

### <a name="run-the-web-ui"></a>Eseguire l'interfaccia utente Web

In questo passaggio si avvia l'interfaccia utente Web. Aprire un nuova finestra del prompt dei comandi per assicurarsi di avere accesso alle variabili di ambiente impostate dallo script **start.cmd**. Passare alla cartella **webui** nella copia locale del repository ed eseguire questi comandi:

```cmd
npm install
npm start
```

Una volta completato l'avvio, il browser visualizza la pagina **http:\//localhost:3000 / dashboard**. Gli errori in questa pagina sono previsti. Per visualizzare l'applicazione senza errori, completare la procedura seguente.

### <a name="configure-and-run-nginx"></a>Configurare ed eseguire NGINX

Configurare un server proxy inverso per collegare l'applicazione Web e i microservizi in esecuzione nel computer locale:

* Copiare il file **nginx.conf** dalla cartella **webui\scripts\localhost** della copia locale del repository nella directory di installazione **nginx\conf**.
* Eseguire **nginx**.

Per altre informazioni sull'esecuzione **nginx**, vedere [nginx per Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Connettersi al dashboard

Per accedere a dashboard della soluzione di monitoraggio remoto, passare a http:\//localhost:9000 nel browser.

## <a name="clean-up"></a>Eseguire la pulizia

Per evitare l'addebito di costi non necessari, al termine dei test rimuovere i servizi cloud dalla sottoscrizione di Azure. Per rimuovere i servizi, passare al [portale di Azure](https://ms.portal.azure.com) ed eliminare il gruppo di risorse creato dallo script **start.cmd**.

È anche possibile eliminare la copia locale del repository per il monitoraggio remoto che è stata creata clonando il codice sorgente da GitHub.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata distribuita la soluzione di monitoraggio remoto, il passaggio successivo consiste nell'[esplorare le funzionalità del dashboard della soluzione](quickstart-remote-monitoring-deploy.md).
