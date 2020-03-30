---
title: Distribuire la soluzione di monitoraggio remoto in locale - IntelliJ IDE - Azure Documenti Microsoft
description: Questa guida alle procedure illustra come distribuire l'acceleratore di soluzioni di monitoraggio remoto nel computer locale usando IntelliJ per il test e lo sviluppo.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 779ee1e057d74b11c5e0ba58dc2fd32b803f1e0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888821"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Distribuire localmente l'acceleratore di soluzioni di monitoraggio remoto - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Questo articolo illustra come distribuire l'acceleratore di soluzioni di monitoraggio remoto in un computer locale a scopo di test e sviluppo. Si apprenderà come eseguire i microservizi in IntelliJ. Una distribuzione di microservizi locali utilizzerà i servizi cloud seguenti: Hub IoT, Azure Cosmos DB, Azure Streaming Analytics e Azure Time Series Insights.A local microservices deployment will use the following cloud hub: IoT Hub, Azure Cosmos DB, Azure Streaming Analytics, and Azure Time Series Insights.

Se si intende eseguire l'acceleratore di soluzioni di monitoraggio remoto in Docker nel computer locale, vedere [Distribuire l'acceleratore di soluzioni di monitoraggio remoto localmente - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Prerequisiti

Per distribuire i servizi di Azure usati dall'acceleratore di soluzioni di monitoraggio remoto, è necessaria una sottoscrizione di Azure attiva.

Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere Versione di valutazione gratuita di Azure .For [details,](https://azure.microsoft.com/pricing/free-trial/)see Azure Free Trial .

### <a name="machine-setup"></a>Impostazioni del computer

Per completare la distribuzione in locale, è necessario che nel computer di sviluppo locale siano installati gli strumenti seguenti:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ Community Edition](https://www.jetbrains.com/idea/download/)
* [Plug-in IntelliJ Scala](https://plugins.jetbrains.com/plugin/1347-scala)
* [Plug-in IntelliJ SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [Plug-in Esecutore IntelliJ SBT](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/)

Node.js v8 è un prerequisito per l'interfaccia della riga di comando di PCS che gli script usano per creare risorse di Azure.Node.js v8 is a prerequisite for the PCS CLI that the scripts use to create Azure resources. Non usare Node.js v10.

> [!NOTE]
> L'IDE IntelliJ è disponibile per Windows e Mac.

## <a name="download-the-source-code"></a>Scaricare il codice sorgente

I repository di codice sorgente di monitoraggio remoto includono il codice sorgente e i file di configurazione Docker necessari per eseguire le immagini Docker dei microservizi.

Per clonare e creare una versione locale del repository, utilizzare l'ambiente della riga di comando per passare a una cartella appropriata nel computer locale. Quindi eseguire uno dei seguenti set di comandi per clonare il repository Java:

* Per scaricare la versione più recente delle implementazioni dei microservizi Java, eseguire il comando seguente:

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* Per recuperare i sottomoduli più recenti, eseguire i seguenti comandi:

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> Questi comandi scaricano il codice sorgente per tutti i microservizi oltre agli script usati per eseguire i microservizi in locale. Non è necessario il codice sorgente per eseguire i microservizi in Docker.You don't need the source code to run the microservices in Docker. Ma il codice sorgente è utile se in seguito si prevede di modificare l'acceleratore di soluzioni e testare le modifiche in locale.

## <a name="deploy-the-azure-services"></a>Distribuire i servizi di Azure

Nonostante questo articolo illustri come eseguire i microservizi in locale, questi dipendono dai servizi di Azure eseguiti nel cloud. Usare lo script seguente per distribuire i servizi di Azure. Gli esempi di script presuppongono che si sta utilizzando il repository Java su un computer Windows. Se si lavora in un altro ambiente, modificare in modo appropriato i percorsi, le estensioni di file e i separatori di percorso.

### <a name="create-new-azure-resources"></a>Creare nuove risorse di Azure

Se non sono ancora state create le risorse di Azure necessarie, seguire questa procedura:

1. Nell'ambiente della riga di comando, passare alla cartella di avvio di **,services, scripts, local, start** nella copia clonata del repository.

1. Eseguire i comandi seguenti per installare lo strumento dell'interfaccia della riga di comando **pcs** e accedere all'account di Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Eseguire lo script **start.cmd**. Lo script richiede le informazioni seguenti:

   * Nome della soluzione.
   * Sottoscrizione di Azure da usare.
   * Località del data center di Azure da usare.

   Lo script crea un gruppo di risorse in Azure con il nome della soluzione. Questo gruppo di risorse contiene le risorse di Azure utilizzate dall'acceleratore di soluzioni. È possibile eliminare questo gruppo di risorse dopo che le risorse corrispondenti non sono più necessarie.

   Lo script aggiunge anche un set di variabili di ambiente al computer locale. Ogni nome di variabile ha il prefisso **PCS**. Queste variabili di ambiente forniscono dettagli che consentono al monitoraggio remoto di leggere i valori di configurazione da una risorsa dell'insieme di credenziali delle chiavi di Azure.These environment variables provide details that let Remote Monitoring read its configuration values from an Azure Key Vault resource.

   > [!TIP]
   > Al termine dello script, le variabili di ambiente vengono salvate in un file denominato ** \<nome\>\\\\\<soluzione\>.env**della cartella inizio . È possibile utilizzarli per future distribuzioni dell'acceleratore di soluzioni. Si noti che tutte le variabili di ambiente impostate nel computer locale sostituiscono i valori nel file **.env locale\\\\\\** degli script dei servizi quando si esegue **docker-compose**.

1. Chiudere l'ambiente della riga di comando.

### <a name="use-existing-azure-resources"></a>Usare le risorse di Azure esistenti

Se sono già state create le risorse di Azure necessarie, impostare le variabili di ambiente corrispondenti nel computer locale:If you've already created the required Azure resources, set the corresponding environment variables on your local machine:
* **PCS_KEYVAULT_NAME**: il nome della risorsa Key Vault.
* **PCS_AAD_APPID:** ID applicazione di Azure Active Directory (Azure AD).
* **PCS_AAD_APPSECRET:** segreto dell'applicazione Azure AD.

I valori di configurazione verranno letti da questa risorsa dell'insieme di credenziali delle chiavi. Queste variabili di ambiente possono essere salvate nel ** \<\>\\file\\\<\>.pcs nome soluzione** della cartella inizio dalla distribuzione. Tutte le variabili di ambiente impostate nel computer locale ignorano i valori nel file **services\\scripts\\local\\.env** quando si esegue **docker-compose**.

Parte della configurazione necessaria per il microservizio viene archiviata in un'istanza dell'insieme di credenziali delle chiavi creata durante la distribuzione iniziale. Le variabili corrispondenti nell'insieme di credenziali delle chiavi devono essere modificate in base alle esigenze.

## <a name="run-the-microservices"></a>Eseguire i microservizi

In questa sezione vengono eseguiti i microservizi di monitoraggio remoto. Eseguire:

* L'interfaccia utente Web in modo nativo.
* I servizi Azure IoT Device Simulation, Auth e Azure Stream Analytics Manager in Docker.
* Microservizi in IntelliJ.

### <a name="run-the-device-simulation-service"></a>Eseguire il servizio Device Simulation

Aprire una nuova finestra del prompt dei comandi. Verificare di avere accesso alle variabili di ambiente impostate dallo script **start.cmd** nella sezione precedente.

Eseguire il comando seguente per aprire il contenitore Docker per il servizio Device Simulation. Il servizio simula i dispositivi per la soluzione di monitoraggio remoto.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Eseguire il servizio di autenticazione

Aprire una nuova finestra del prompt dei comandi e quindi eseguire il comando seguente per aprire il contenitore Docker per il servizio di autenticazione. Usando questo servizio, è possibile gestire gli utenti autorizzati ad accedere alle soluzioni IoT di Azure.By using this service, you can manage the users who are authorized to access Azure IoT solutions.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>Eseguire il servizio Gestione analisi di flussoRun the Stream Analytics Manager service

Aprire una nuova finestra del prompt dei comandi e quindi eseguire il comando seguente per aprire il contenitore Docker per il servizio Gestione Analisi di flusso. Con questo servizio, puoi gestire i processi di Analisi di flusso. Tale gestione include l'impostazione della configurazione del processo e l'avvio, l'arresto e il monitoraggio dello stato del processo.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>Distribuire tutti gli altri microservizi nel computer localeDeploy all other microservices on your local machine

La procedura seguente illustra come eseguire i microservizi di monitoraggio remoto in IntelliJ.

#### <a name="import-a-project"></a>Importare un progetto

1. Aprire l'IDE IntelliJ.
1. Selezionare **Importa progetto**.
1. Scegliere **azure-iot-pcs-remote-monitoring-java**

#### <a name="create-run-configurations"></a>Creare configurazioni di esecuzione

1. Selezionare **Esegui** > **configurazioni**di modifica .
1. Selezionare Aggiungi nuova**attività sbt** **di configurazione** > .
1. Immettere **Nome**, quindi **Attività** come **esecuzione**.
1. Selezionare la **directory di lavoro** in base al servizio che si desidera eseguire.
1. Selezionare **Applica** > **OK** per salvare le scelte.
1. Creare configurazioni di esecuzione per i servizi Web seguenti:
    * WebService (services\config)
    * WebService (services\device-telemetry)
    * WebService (services\iothub-manager)
    * WebService (services\storage-adapter)

Ad esempio, l'immagine seguente mostra come aggiungere una configurazione per un servizio:As an example, the following image shows how to add a configuration for a service:

[![Screenshot della finestra Configurazioni esecuzione/debug dell'IDE di IntelliJ, che mostra l'opzione storageAdapter evidenziata nell'elenco delle attività sbt nel riquadro sinistro e le voci nelle caselle Nome, Attività, Directory di lavoro e Parametri VM nel riquadro destro.](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>Creare una configurazione compostaCreate a compound configuration

1. Per eseguire tutti i servizi insieme, selezionare **Aggiungi nuovo componente** > **di**configurazione .
1. Immettere **Nome**, quindi selezionare **Aggiungi attività sbt**.
1. Selezionare **Applica** > **OK** per salvare le scelte.

Ad esempio, l'immagine seguente mostra come aggiungere tutte le attività sbt a una singola configurazione:As an example, the following image shows how to add all sbt tasks to a single configuration:

[![Screenshot della finestra Configurazioni esecuzione/debug dell'IDE intelli,IntelliJ, con l'opzione AllServices evidenziata nell'elenco Composto nel riquadro sinistro e l'opzione sbt Task 'deviceTelemetry' evidenziata nel riquadro di destra.](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Selezionare **Esegui** per compilare ed eseguire i servizi Web nel computer locale.

Ogni servizio Web apre una finestra del prompt dei comandi e una finestra del browser Web. Al prompt dei comandi viene visualizzato l'output del servizio in esecuzione. La finestra del browser consente di monitorare lo stato. Non chiudere le finestre o le pagine Web del prompt dei comandi, poiché queste azioni arrestano il servizio Web.

Per accedere allo stato dei servizi, visitare i seguenti URL:

* Gestione Hub IoT:[http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* Telemetria del dispositivo:Device Telemetry:[http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* Conﬁg:[http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* adattatore di archiviazione:[http://localhost:9022/v1/status](http://localhost:9022/v1/status)

### <a name="start-the-stream-analytics-job"></a>Avviare il processo di analisi di flusso

Per avviare il processo di analisi di flusso, seguire questa procedura:

1. Passare al [portale di Azure](https://portal.azure.com).
1. Passare al **gruppo di risorse** creato per la soluzione. Il nome del gruppo di risorse è il nome della soluzione scelto quando è stato eseguito lo script **start.cmd**.
1. Selezionare il **processo Analisi di flusso** nell'elenco delle risorse.
1. Nella pagina **Panoramica** processo di Analisi di flusso selezionare il pulsante **Avvia** e quindi selezionare **Avvia** per avviare il processo.

### <a name="run-the-web-ui"></a>Eseguire l'interfaccia utente Web

In questo passaggio si avvia l'interfaccia utente Web. Aprire una nuova finestra del prompt dei comandi. Verificare di avere accesso alle variabili di ambiente impostate dallo script **start.cmd.** Passare alla cartella **webui** nella copia locale del repository e quindi eseguire i comandi seguenti:

```cmd
npm install
npm start
```

Una volta completato il comando **start,** il [http://localhost:3000/dashboard](http://localhost:3000/dashboard)browser visualizza la pagina all'indirizzo . Gli errori in questa pagina sono previsti. La procedura seguente illustra come visualizzare l'applicazione senza errori.

### <a name="configure-and-run-nginx"></a>Configurare ed eseguire Nginx

Configurare un server proxy inverso che colleghi l'applicazione Web ai microservizi in esecuzione nel computer locale:

1. Copiare il file **nginx.conf** dalla cartella **webui,scripts,localhost** nella copia locale del repository, nella directory di installazione **di nginx.conf.**
1. Esegui Nginx.

Per ulteriori informazioni sull'esecuzione di Nginx, vedere [nginx per Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Connettersi al dashboard

Per accedere al dashboard della http://localhost:9000 soluzione di monitoraggio remoto, passare a nel browser.

## <a name="clean-up"></a>Eseguire la pulizia

Per evitare addebiti non necessari, rimuovere i servizi cloud dalla sottoscrizione di Azure al termine del test. Per rimuovere i servizi, passare al [portale di Azure](https://ms.portal.azure.com)ed eliminare il gruppo di risorse creato dallo script **start.cmd.**

You can also delete the local copy of the Remote Monitoring repository that was created when you cloned the source code from GitHub.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata distribuita la soluzione di monitoraggio remoto, il passaggio successivo consiste nell'[esplorare le funzionalità del dashboard della soluzione](quickstart-remote-monitoring-deploy.md).
