---
title: Distribuire la soluzione di monitoraggio remoto localmente-IDE IntelliJ-Azure | Microsoft Docs
description: Questa guida illustra come distribuire Remote Monitoring Solution Accelerator nel computer locale usando IntelliJ per il test e lo sviluppo.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 779ee1e057d74b11c5e0ba58dc2fd32b803f1e0e
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888821"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Distribuire localmente l'acceleratore di soluzioni di monitoraggio remoto - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Questo articolo illustra come distribuire l'acceleratore di soluzioni di monitoraggio remoto in un computer locale a scopo di test e sviluppo. Si apprenderà come eseguire i microservizi in IntelliJ. Una distribuzione locale di microservizi utilizzerà i servizi cloud seguenti: hub Internet delle cose, Azure Cosmos DB, analisi di flusso di Azure e Azure Time Series Insights.

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
* [Plug-in scala IntelliJ](https://plugins.jetbrains.com/plugin/1347-scala)
* [Plug-in IntelliJ SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [Plug-in Executor IntelliJ SBT](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node. js V8](https://nodejs.org/)

Node. js V8 è un prerequisito per l'interfaccia della riga di comando per PC usata dagli script per creare risorse di Azure. Non usare Node.js v10.

> [!NOTE]
> L'IDE IntelliJ è disponibile per Windows e Mac.

## <a name="download-the-source-code"></a>Scaricare il codice sorgente

I repository di codice sorgente di monitoraggio remoto includono il codice sorgente e i file di configurazione Docker necessari per eseguire le immagini Docker dei microservizi.

Per clonare e creare una versione locale del repository, usare l'ambiente della riga di comando per passare a una cartella appropriata nel computer locale. Eseguire quindi uno dei seguenti set di comandi per clonare il repository Java:

* Per scaricare la versione più recente delle implementazioni di microservizi Java, eseguire il comando seguente:

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* Per recuperare i moduli secondari più recenti, eseguire i comandi seguenti:

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> Questi comandi scaricano il codice sorgente per tutti i microservizi oltre agli script usati per eseguire i microservizi in locale. Non è necessario il codice sorgente per eseguire i microservizi in docker. Il codice sorgente è tuttavia utile se si prevede di modificare l'acceleratore di soluzione e testare le modifiche in locale.

## <a name="deploy-the-azure-services"></a>Distribuire i servizi di Azure

Nonostante questo articolo illustri come eseguire i microservizi in locale, questi dipendono dai servizi di Azure eseguiti nel cloud. Usare lo script seguente per distribuire i servizi di Azure. Gli esempi di script presuppongono che si stia usando il repository Java in un computer Windows. Se si lavora in un altro ambiente, modificare in modo appropriato i percorsi, le estensioni di file e i separatori di percorso.

### <a name="create-new-azure-resources"></a>Creare nuove risorse di Azure

Se non sono ancora state create le risorse di Azure necessarie, seguire questa procedura:

1. Nell'ambiente della riga di comando passare alla cartella **\services\scripts\local\launch** nella copia clonata del repository.

1. Eseguire i comandi seguenti per installare lo strumento dell'interfaccia della riga di comando **pcs** e accedere all'account di Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Eseguire lo script **start.cmd**. Lo script richiede le informazioni seguenti:

   * Nome della soluzione.
   * Sottoscrizione di Azure da usare.
   * Località del data center di Azure da usare.

   Lo script crea un gruppo di risorse in Azure con il nome della soluzione. Questo gruppo di risorse contiene le risorse di Azure utilizzate dall'acceleratore di soluzioni. È possibile eliminare questo gruppo di risorse dopo che non sono più necessarie le risorse corrispondenti.

   Lo script aggiunge anche un set di variabili di ambiente nel computer locale. Il prefisso dei **PC**è il nome di ogni variabile. Queste variabili di ambiente forniscono dettagli che consentono al monitoraggio remoto di leggere i valori di configurazione da una risorsa Azure Key Vault.

   > [!TIP]
   > Al termine dell'esecuzione dello script, le variabili di ambiente vengono salvate in un file denominato **\<cartella home\>\\. pcs\\\<nome della soluzione\>. env**. È possibile usarli per distribuzioni future di Solution Accelerator. Si noti che tutte le variabili di ambiente impostate nel computer locale sostituiscono i valori negli **script di\\dei servizi\\file\\. env locale** quando si esegue **Docker-compose**.

1. Chiudere l'ambiente della riga di comando.

### <a name="use-existing-azure-resources"></a>Usare le risorse di Azure esistenti

Se sono già state create le risorse di Azure necessarie, impostare le variabili di ambiente corrispondenti nel computer locale:
* **PCS_KEYVAULT_NAME**: il nome della risorsa key Vault.
* **PCS_AAD_APPID**: l'ID applicazione Azure Active Directory (Azure ad).
* **PCS_AAD_APPSECRET**: il segreto dell'applicazione Azure ad.

I valori di configurazione verranno letti da questa risorsa Key Vault. Queste variabili di ambiente possono essere salvate nel **\<cartella home\>\\. pcs\\\<nome della soluzione\>file con estensione ENV** dalla distribuzione. Tutte le variabili di ambiente impostate nel computer locale ignorano i valori nel file **services\\scripts\\local\\.env** quando si esegue **docker-compose**.

Parte della configurazione richiesta dal microservizio è archiviata in un'istanza di Key Vault creata durante la distribuzione iniziale. Le variabili corrispondenti nell'insieme di credenziali delle chiavi devono essere modificate in base alle esigenze.

## <a name="run-the-microservices"></a>Eseguire i microservizi

In questa sezione vengono eseguiti i microservizi di monitoraggio remoto. Eseguire:

* Interfaccia utente Web in modo nativo.
* I servizi di simulazione, autenticazione e Azure Stream Analytics Manager di Azure in docker.
* I microservizi in IntelliJ.

### <a name="run-the-device-simulation-service"></a>Eseguire il servizio di simulazione del dispositivo

Aprire una nuova finestra del prompt dei comandi. Verificare di avere accesso alle variabili di ambiente impostate dallo script **Start. cmd** nella sezione precedente.

Eseguire il comando seguente per aprire il contenitore Docker per il servizio simulazione dispositivi. Il servizio simula i dispositivi per la soluzione di monitoraggio remoto.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Eseguire il servizio di autenticazione

Aprire una nuova finestra del prompt dei comandi, quindi eseguire il comando seguente per aprire il contenitore Docker per il servizio di autenticazione. Con questo servizio, è possibile gestire gli utenti autorizzati ad accedere alle soluzioni Azure.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>Eseguire il servizio Stream Analytics Manager

Aprire una nuova finestra del prompt dei comandi, quindi eseguire il comando seguente per aprire il contenitore Docker per il servizio di gestione di analisi di flusso. Con questo servizio è possibile gestire i processi di analisi di flusso. Tale gestione include l'impostazione della configurazione del processo e l'avvio, l'arresto e il monitoraggio dello stato del processo.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>Distribuire tutti gli altri microservizi nel computer locale

I passaggi seguenti illustrano come eseguire i microservizi di monitoraggio remoto in IntelliJ.

#### <a name="import-a-project"></a>Importare un progetto

1. Aprire l'IDE di IntelliJ.
1. Selezionare **Importa progetto**.
1. Scegliere **Azure-IOT-PCS-Remote-Monitoring-java\services\build.SBT**.

#### <a name="create-run-configurations"></a>Creare configurazioni di esecuzione

1. Selezionare **esegui** > **modificare le configurazioni**.
1. Selezionare **Aggiungi nuova configurazione** > **attività SBT**.
1. Immettere **nome**, quindi immettere **attività** come **Esegui**.
1. Selezionare la **directory di lavoro** in base al servizio che si desidera eseguire.
1. Selezionare **applica** > **OK** per salvare le scelte.
1. Creare configurazioni di esecuzione per i servizi Web seguenti:
    * WebService (services\config)
    * WebService (services\device-telemetry)
    * WebService (services\iothub-manager)
    * WebService (services\storage-adapter)

Nell'immagine seguente, ad esempio, viene illustrato come aggiungere una configurazione per un servizio:

[![screenshot della finestra di configurazione dell'esecuzione/debug dell'IDE IntelliJ, che mostra l'opzione storageAdapter evidenziata nell'elenco attività SBT nel riquadro sinistro e voci nelle caselle nome, attività, directory di lavoro e parametri VM nel riquadro destro.](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>Creare una configurazione composta

1. Per eseguire tutti i servizi insieme, selezionare **Aggiungi nuova configurazione** > **composto**.
1. Immettere **nome**, quindi selezionare **Aggiungi attività SBT**.
1. Selezionare **applica** > **OK** per salvare le scelte.

Nell'immagine seguente, ad esempio, viene illustrato come aggiungere tutte le attività SBT a una singola configurazione:

[![screenshot della finestra di configurazione dell'esecuzione/debug dell'IDE IntelliJ, che mostra l'opzione AllServices evidenziata nell'elenco composto nel riquadro sinistro e l'opzione "deviceTelemetry" dell'attività SBT evidenziata nel riquadro di destra.](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Selezionare **Esegui** per compilare ed eseguire i servizi Web nel computer locale.

Ogni servizio Web apre una finestra del prompt dei comandi e una finestra del browser Web. Al prompt dei comandi, viene visualizzato l'output del servizio in esecuzione. La finestra del browser consente di monitorare lo stato. Non chiudere le finestre del prompt dei comandi o le pagine Web, perché queste azioni arrestano il servizio Web.

Per accedere allo stato dei servizi, passare agli URL seguenti:

* Gestione Hub Internet: [http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* Telemetria del dispositivo: [http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* configurazione: [http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* scheda di memoria: [http://localhost:9022/v1/status](http://localhost:9022/v1/status)

### <a name="start-the-stream-analytics-job"></a>Avviare il processo di analisi di flusso

Per avviare il processo di analisi di flusso, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare al **gruppo di risorse** creato per la soluzione. Il nome del gruppo di risorse è il nome della soluzione scelto quando è stato eseguito lo script **start.cmd**.
1. Selezionare il **processo di analisi di flusso** nell'elenco di risorse.
1. Nella pagina **Panoramica** del processo di analisi di flusso selezionare il pulsante **Start** , quindi selezionare **Avvia** per avviare il processo.

### <a name="run-the-web-ui"></a>Eseguire l'interfaccia utente Web

In questo passaggio si avvia l'interfaccia utente Web. Aprire una nuova finestra del prompt dei comandi. Verificare di avere accesso alle variabili di ambiente impostate dallo script **Start. cmd** . Passare alla cartella **WebUI** nella copia locale del repository, quindi eseguire i comandi seguenti:

```cmd
npm install
npm start
```

Al termine del comando **Start** , il browser Visualizza la pagina all'indirizzo [http://localhost:3000/dashboard](http://localhost:3000/dashboard). Gli errori in questa pagina sono previsti. Per visualizzare l'applicazione senza errori, attenersi alla procedura riportata di seguito.

### <a name="configure-and-run-nginx"></a>Configurare ed eseguire nginx

Configurare un server proxy inverso che collega l'applicazione Web ai microservizi in esecuzione nel computer locale:

1. Copiare il file **nginx. conf** dalla cartella **webui\scripts\localhost** nella copia locale del repository alla directory di installazione di **nginx\conf** .
1. Eseguire nginx.

Per ulteriori informazioni sull'esecuzione di nginx, vedere [Nginx per Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Connettersi al dashboard

Per accedere al dashboard della soluzione di monitoraggio remoto, passare a http://localhost:9000 nel browser.

## <a name="clean-up"></a>Eseguire la pulizia

Per evitare addebiti superflui, rimuovere i servizi cloud dalla sottoscrizione di Azure al termine del test. Per rimuovere i servizi, passare al [portale di Azure](https://ms.portal.azure.com)ed eliminare il gruppo di risorse creato dallo script **Start. cmd** .

È anche possibile eliminare la copia locale del repository di monitoraggio remoto creato durante la clonazione del codice sorgente da GitHub.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata distribuita la soluzione di monitoraggio remoto, il passaggio successivo consiste nell'[esplorare le funzionalità del dashboard della soluzione](quickstart-remote-monitoring-deploy.md).
