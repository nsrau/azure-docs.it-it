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
ms.openlocfilehash: 2b55fea69fe1affb6cab5d360f1e8355c3bb720d
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "66015442"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Distribuire localmente l'acceleratore di soluzioni di monitoraggio remoto - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Questo articolo illustra come distribuire l'acceleratore di soluzioni di monitoraggio remoto in un computer locale a scopo di test e sviluppo. Si apprenderà come eseguire i microservizi in IntelliJ. Una distribuzione di microservizi locale usa i servizi cloud seguenti: servizi Hub IoT, Cosmos DB, Analisi di flusso di Azure e Azure Time Series Insights nel cloud.

Se si intende eseguire l'acceleratore di soluzioni di monitoraggio remoto in Docker nel computer locale, vedere [Distribuire l'acceleratore di soluzioni di monitoraggio remoto localmente - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Prerequisiti

Per distribuire i servizi di Azure usati dall'acceleratore di soluzioni di monitoraggio remoto, è necessaria una sottoscrizione di Azure attiva.

Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

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

## <a name="download-the-source-code"></a>Scaricare il codice sorgente

I repository di codice sorgente di monitoraggio remoto includono il codice sorgente e i file di configurazione Docker necessari per eseguire le immagini Docker dei microservizi.

Per clonare e creare una versione locale del repository, usare l'ambiente della riga di comando per passare a una cartella adatta nel computer locale. Eseguire quindi uno dei set di comandi seguenti per clonare il repository Java:

Per scaricare la versione più recente delle implementazioni di microservizi Java, eseguire:


```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-java
git submodule foreach git pull origin master
```

> [!NOTE]
> Questi comandi scaricano il codice sorgente per tutti i microservizi oltre agli script usati per eseguire i microservizi in locale. Nonostante non sia necessario per eseguire i microservizi in Docker, il codice sorgente è utile se si intende modificare in un secondo momento l'acceleratore di soluzione e testare le modifiche in locale.

## <a name="deploy-the-azure-services"></a>Distribuire i servizi di Azure

Nonostante questo articolo illustri come eseguire i microservizi in locale, questi dipendono dai servizi di Azure eseguiti nel cloud. Usare lo script seguente per distribuire i servizi di Azure. Gli esempi di script seguenti presuppongono che si usi il repository Java in un computer Windows. Se si lavora in un altro ambiente, modificare in modo appropriato i percorsi, le estensioni di file e i separatori di percorso.

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

     Lo script crea un gruppo di risorse in Azure con il nome della soluzione. Questo gruppo di risorse contiene le risorse di Azure usate dall'acceleratore di soluzione. È possibile eliminare il gruppo di risorse nel momento in cui le risorse corrispondenti non sono più necessarie.

     Lo script aggiunge anche un set di variabili di ambiente con un prefisso **PCS** nel computer locale. Queste variabili di ambiente forniscono i dettagli per il monitoraggio remoto sia in grado di leggere da una risorsa di Azure Key Vault. Questa risorsa insieme di credenziali delle chiavi è in cui il monitoraggio remoto verranno letti i valori di configurazione da.

     > [!TIP]
     > Al completamento dello script, le variabili di ambiente vengono salvate in un file denominato **\<home directory\>\\.pcs\\\<nome soluzione\>.env**. Sarà possibile usarle per distribuzioni future di acceleratori di soluzione. Tutte le variabili di ambiente impostate nel computer locale ignorano i valori nel file **services\\scripts\\local\\.env** quando si esegue **docker-compose**.

1. Uscire dall'ambiente della riga di comando.

### <a name="use-existing-azure-resources"></a>Usare le risorse di Azure esistenti

Se sono già state create le risorse di Azure necessarie, creare le corrispondenti variabili di ambiente nel computer locale.
Impostare le variabili di ambiente per le operazioni seguenti:
* **PCS_KEYVAULT_NAME** -nome della risorsa di Azure Key Vault
* **PCS_AAD_APPID** -ID applicazione di AAD
* **PCS_AAD_APPSECRET** -segreto applicazione di AAD

I valori di configurazione vengono letti da questa risorsa di Azure Key Vault. Queste variabili di ambiente possono essere salvate nel  **\<cartella principale\>\\.pcs\\\<Nome soluzione\>env** file dalla distribuzione. Tutte le variabili di ambiente impostate nel computer locale ignorano i valori nel file **services\\scripts\\local\\.env** quando si esegue **docker-compose**.

Alcune configurazioni necessarie per il microservizio è archiviato in un'istanza di **Key Vault** creato nella distribuzione iniziale. Le variabili corrispondenti in Key Vault devono essere modificate in base alle esigenze.

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

Ad esempio, la figura seguente viene illustrato come aggiungere la configurazione per un servizio:

[![Add-Configuration](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)


#### <a name="create-compound-configuration"></a>Creare una configurazione composta

1. Per eseguire tutti i servizi insieme, selezionare **Add new Configuration > Compound** (Aggiungi nuova configurazione > Composta)
1. Immettere un nome in **Name** e **aggiungere le attività sbt**
1. Fare clic su **Apply > Ok** (Applica > OK) per salvare le modifiche.

Ad esempio, l'immagine seguente mostra l'aggiunta di tutte le attività sbt a singola configurazione:

[![Add-All-Services](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Fare clic su **Run** (Esegui) per compilare ed eseguire i servizi Web nel computer locale.

Ogni servizio Web consente di aprire una finestra del Web browser e il prompt dei comandi. Al prompt dei comandi, viene visualizzato un output dal servizio in esecuzione e la finestra del browser consente di monitorare lo stato. Non chiudere il prompt dei comandi o le pagine Web perché questa azione arresta il servizio Web.


Per accedere allo stato dei servizi, è possibile passare agli URL seguenti:
* Gestione dell'hub IoT [http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* Telemetria dei dispositivi [http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* config [http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* storage-adapter [http://localhost:9022/v1/status](http://localhost:9022/v1/status)


### <a name="start-the-stream-analytics-job"></a>Avviare il processo di analisi di flusso

Per avviare il processo di analisi di flusso, seguire questa procedura:

1. Passare al [portale di Azure](https://portal.azure.com).
1. Passare al **gruppo di risorse** creato per la soluzione. Il nome del gruppo di risorse è il nome della soluzione scelto quando è stato eseguito lo script **start.cmd**.
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
