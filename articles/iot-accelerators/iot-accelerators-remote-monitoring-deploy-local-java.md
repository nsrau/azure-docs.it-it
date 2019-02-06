---
title: Distribuire la soluzione di monitoraggio remoto in locale (tramite IDE di IntelliJ) - Azure | Microsoft Docs
description: Questa guida pratica illustra come distribuire l'acceleratore di soluzioni di monitoraggio remoto in un computer locale tramite IntelliJ a scopo di test e sviluppo.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: e1918d527afbe5aad647d84ab82889099f5f22b9
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2019
ms.locfileid: "55105801"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Distribuire localmente l'acceleratore di soluzioni di monitoraggio remoto - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Questo articolo illustra come distribuire l'acceleratore di soluzioni di monitoraggio remoto in un computer locale a scopo di test e sviluppo. Si apprenderà come eseguire i microservizi in IntelliJ. Una distribuzione di microservizi locale usa i servizi cloud seguenti: servizi Hub IoT, Cosmos DB, Analisi di flusso di Azure e Azure Time Series Insights nel cloud.

Se si intende eseguire l'acceleratore di soluzioni di monitoraggio remoto in Docker nel computer locale, vedere [Distribuire l'acceleratore di soluzioni di monitoraggio remoto localmente - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Prerequisiti

Per distribuire i servizi di Azure usati dall'acceleratore di soluzioni di monitoraggio remoto, è necessaria una sottoscrizione di Azure attiva.

Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Impostazioni del computer

Per completare la distribuzione in locale, è necessario che nel computer di sviluppo locale siano installati gli strumenti seguenti:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ Community Edition](https://www.jetbrains.com/idea/download/)
* [Plug-in IntelliJ Scala](https://plugins.jetbrains.com/plugin/1347-scala)
* [Plug-in IntelliJ SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [Plug-in IntelliJ SBT Executor](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) - Questo software è un prerequisito per l'interfaccia della riga di comando di PCS usata dagli script per creare le risorse di Azure. Non usare Node.js v10.

> [!NOTE]
> L'IDE IntelliJ è disponibile per Windows e Mac.

[!INCLUDE [iot-accelerators-local-setup-java](../../includes/iot-accelerators-local-setup-java.md)]

## <a name="run-the-microservices"></a>Eseguire i microservizi

In questa sezione vengono eseguiti i microservizi di monitoraggio remoto. Eseguire l'interfaccia utente Web in modo nativo, il servizio di simulazione dispositivi, di autenticazione e di gestione di Analisi di flusso di Azure in Docker e i microservizi in IntelliJ.

### <a name="run-the-device-simulation-service"></a>Eseguire il servizio di simulazione dispositivi

Aprire un nuova finestra del prompt dei comandi per assicurarsi di avere accesso alle variabili di ambiente impostate dallo script **start.cmd** nella sezione precedente.

Eseguire questo comando per avviare il contenitore Docker per il servizio di simulazione dispositivi. Il servizio consente di simulare i dispositivi per la soluzione di monitoraggio remoto.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Eseguire il servizio di autenticazione

Aprire una nuova finestra del prompt dei comandi ed eseguire il comando seguente per avviare il contenitore Docker per il servizio di autenticazione. Il servizio consente di gestire gli utenti autorizzati ad accedere alle soluzioni IoT di Azure.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-asa-manager-service"></a>Eseguire il servizio di gestione di Analisi di flusso di Azure

Aprire una nuova finestra del prompt dei comandi ed eseguire il comando seguente per avviare il contenitore Docker per il servizio di gestione di Analisi di flusso di Azure. Il servizio consente la gestione dei processi di Analisi di flusso di Azure, tra cui l'impostazione della configurazione, l'avvio, l'arresto e il monitoraggio dello stato.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Distribuire tutti gli altri microservizi nel computer locale

La procedura seguente mostra come eseguire i microservizi di monitoraggio remoto in IntelliJ:

#### <a name="import-project"></a>Importare il progetto

1. Avviare l'IDE di IntelliJ
1. Selezionare **Import Project** (Importa progetto) e scegliere **azure-iot-pcs-remote-monitoring-java\services\build.sbt**

#### <a name="create-run-configurations"></a>Creare configurazioni di esecuzione

1. Selezionare **Run > Edit Configurations** (Esegui > Modifica configurazioni)
1. Selezionare **Add New Configuration > sbt task** (Aggiungi nuova configurazione > Attività sbt) 
1. Immettere **Name** (Nome) e immettere run per **Tasks** (Attività) 
1. Selezionare la **Working Directory** (Directory di lavoro) in base al servizio da eseguire
1. Fare clic su **Apply > Ok** (Applica > OK) per salvare le modifiche.
1. Creare le configurazioni di esecuzione per i servizi seguenti:
    * WebService (services\config)
    * WebService (services\device-telemetry)
    * WebService (services\iothub-manager)
    * WebService (services\storage-adapter)

L'immagine seguente mostra un esempio di aggiunta della configurazione per un servizio:

[![Add-Configuration](./media/deploy-locally-intelliJ/run-configurations.png)](./media/deploy-locally-intelliJ/run-configurations.png#lightbox)


#### <a name="create-compound-configuration"></a>Creare una configurazione composta

1. Per eseguire tutti i servizi insieme, selezionare **Add new Configuration > Compound** (Aggiungi nuova configurazione > Composta)
1. Immettere un nome in **Name** e **aggiungere le attività sbt**
1. Fare clic su **Apply > Ok** (Applica > OK) per salvare le modifiche.

L'immagine seguente mostra un esempio di aggiunta di tutte le attività sbt a una singola configurazione:


[![Add-All-Services](./media/deploy-locally-intelliJ/all-services.png)](./media/deploy-locally-intelliJ/all-services.png#lightbox)



1. Fare clic su **Run** (Esegui) per compilare ed eseguire i servizi Web nel computer locale.

Ogni servizio Web consente di aprire una finestra del Web browser e il prompt dei comandi. Al prompt dei comandi, viene visualizzato un output dal servizio in esecuzione e la finestra del browser consente di monitorare lo stato. Non chiudere il prompt dei comandi o le pagine Web perché questa azione arresta il servizio Web.


Per accedere allo stato dei servizi, è possibile passare agli URL seguenti:
* Gestione dell'hub IoT [http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* Telemetria dei dispositivi [http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* config [http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* storage-adapter [http://localhost:9022/v1/status](http://localhost:9022/v1/status)


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

Una volta completato l'avvio, il browser visualizza la pagina **http://localhost:3000/dashboard**. Gli errori in questa pagina sono previsti. Per visualizzare l'applicazione senza errori, completare la procedura seguente.

### <a name="configure-and-run-nginx"></a>Configurare ed eseguire NGINX

Configurare un server proxy inverso per collegare l'applicazione Web e i microservizi in esecuzione nel computer locale:

* Copiare il file **nginx.conf** dalla cartella **webui\scripts\localhost** della copia locale del repository nella directory di installazione **nginx\conf**.
* Eseguire **nginx**.

Per altre informazioni sull'esecuzione **nginx**, vedere [nginx per Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Connettersi al dashboard

Per accedere alla dashboard della soluzione di monitoraggio remoto, passare a [http://localhost:9000](http://localhost:9000) nel browser.

## <a name="clean-up"></a>Eseguire la pulizia

Per evitare l'addebito di costi non necessari, al termine dei test rimuovere i servizi cloud dalla sottoscrizione di Azure. Per rimuovere i servizi, passare al [portale di Azure](https://ms.portal.azure.com) ed eliminare il gruppo di risorse creato dallo script **start.cmd**.

È anche possibile eliminare la copia locale del repository per il monitoraggio remoto che è stata creata clonando il codice sorgente da GitHub.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata distribuita la soluzione di monitoraggio remoto, il passaggio successivo consiste nell'[esplorare le funzionalità del dashboard della soluzione](quickstart-remote-monitoring-deploy.md).
