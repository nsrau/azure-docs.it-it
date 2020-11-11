---
title: "Avvio rapido: Creare un'app Web Node.js"
description: Distribuire la prima app Node.js Hello World nel servizio app di Azure in pochi minuti.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
ms.openlocfilehash: b42abfda863ed38eb3c29004630f905edef075c4
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359103"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Creare un'app Web Node.js in Azure

::: zone pivot="platform-windows"  

Per iniziare a usare il servizio app di Azure, creare un'app Node.js/Express locale con Visual Studio Code e quindi distribuire l'app nel cloud. Poiché si usa un livello di servizio app gratuito, non verrà addebitato alcun costo per il completamento di questa guida di avvio rapido.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- <a href="https://git-scm.com/" target="_blank">Installare Git</a>
- [Node.js e npm](https://nodejs.org). Eseguire il comando `node --version` per verificare che Node.js sia installato.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Estensione Servizio app di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) per Visual Studio Code.

## <a name="clone-and-run-a-local-nodejs-application"></a>Clonare ed eseguire un'applicazione Node.js locale

1. Nel computer locale aprire un terminale e clonare il repository di esempio:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Passare alla cartella della nuova app:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Avviare l'app per testarla in locale:

    ```bash
    npm start
    ```
    
1. Aprire il browser e passare a `http://localhost:1337`. Nel browser verrà visualizzato il testo "Hello World!".

1. Premere **CTRL**+**C** nel terminale per arrestare il server.

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>Distribuire l'app in Azure

In questa sezione si distribuisce l'app Node.js in Azure usando VS Code e l'estensione Servizio app di Azure.

1. Nel terminale assicurarsi di trovarsi nella cartella *nodejs-docs-hello-world* , quindi avviare Visual Studio Code con il comando seguente:

    ```bash
    code .
    ```

1. Sulla barra delle attività di VS Code fare clic sul logo di Azure per visualizzare Esplora risorse del **SERVIZIO APP DI AZURE**. Selezionare **Accedi ad Azure** e seguire le istruzioni. Se si verificano errori, vedere [Risoluzione dei problemi di accesso di Azure](#troubleshooting-azure-sign-in) sotto. Una volta effettuato l'accesso, lo strumento di esplorazione visualizzerà il nome della sottoscrizione di Azure.

    ![Accedere ad Azure](media/quickstart-nodejs/sign-in.png)

1. In Esplora risorse del **SERVIZIO APP DI AZURE** di VS Code selezionare l'icona della freccia verso l'alto blu per distribuire l'app in Azure. È anche possibile richiamare lo stesso comando dal **riquadro comandi** ( **CTRL**+**MAIUSC**+**P** ) digitando 'Distribuisci nell'app Web' e scegliendo **Servizio app di Azure: Distribuisci nell'app Web**.

    :::image type="content" source="media/quickstart-nodejs/deploy.png" alt-text="Screenshot del servizio app di Azure in VS Code con l'icona della freccia blu selezionata.":::
        
1. Scegliere la cartella *nodejs-docs-hello-world*.

1. Scegliere un'opzione di creazione in base al sistema operativo in cui eseguire la distribuzione:

    - Linux: Scegliere **Crea una nuova app Web**
    - Windows: Scegliere **Crea una nuova app Web... Impostazioni avanzate**

1. Digitare un nome univoco a livello globale per l'app Web e premere **INVIO**. Il nome deve essere univoco in tutte le risorse di Azure e usare solo caratteri alfanumerici (' A-Z ',' a-z ' è 0-9') e trattini ('-').

1. Se la destinazione è Linux, selezionare una versione di Node.js quando richiesto. È consigliabile usare una versione **LTS**.

1. Se la destinazione è Windows, seguire le istruzioni aggiuntive:
    1. Selezionare **Creare un nuovo gruppo di risorse** , quindi immettere un nome per il gruppo di risorse, ad esempio `AppServiceQS-rg`.
    1. Selezionare **Windows** come sistema operativo.
    1. Selezionare **Crea nuovo piano di servizio app** , quindi immettere un nome per il piano, ad esempio `AppServiceQS-plan`, quindi selezionare il piano tariffario **F1 Gratuito**.
    1. Scegliere **Ignora per adesso** quando viene richiesto riguardo ad Application Insights.
    1. Scegliere un'area vicina al proprio ambiente o alle risorse a cui si vuole accedere.

1. Dopo aver risposto a tutte le richieste, VS Code mostra le risorse di Azure che vengono create per l'app nella finestra popup di notifica.

    Per la distribuzione in Linux, selezionare **Sì** quando viene richiesto di aggiornare la configurazione per eseguire `npm install` nel server Linux di destinazione.

    ![Richiesta di aggiornamento della configurazione nel server Linux di destinazione](media/quickstart-nodejs/server-build.png)

1. Quando richiesto, selezionare **Sì** per **Distribuisci sempre l'area di lavoro "nodejs-docs-hello-world" in (nome app)"** . Selezionando **Sì** si indica VS Code di impostare automaticamente come destinazione la stessa app Web del servizio app con le distribuzioni successive.

1. Per la distribuzione in Linux, al termine della distribuzione, selezionare **Esplora sito Web** nel messaggio per visualizzare l'app Web appena distribuita. Nel browser verrà visualizzato il testo "Hello World!"

1. Per la distribuzione in Windows, è prima necessario impostare il numero di versione di Node.js per l'app Web:

    1. In VS Code espandere il nodo del nuovo servizio app, quindi fare clic con il pulsante destro del mouse su **Impostazioni applicazione** e scegliere **Aggiungi nuova impostazione** :

        ![Comando Aggiungi impostazione app](media/quickstart-nodejs/add-setting.png)

    1. Immettere `WEBSITE_NODE_DEFAULT_VERSION` per la chiave dell'impostazione.
    1. Immettere `10.15.2` per il valore dell'impostazione.
    1. Fare clic con il pulsante destro del mouse sul nodo relativo al servizio app e scegliere **Riavvia**

        ![Comando Riavvia servizio app](media/quickstart-nodejs/restart.png)

    1. Fare clic con il pulsante destro del mouse sul nodo relativo al servizio app e scegliere **Esplora sito Web**.

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Risoluzione dei problemi di accesso ad Azure

Se viene visualizzato l'errore **"La sottoscrizione con il nome [ID sottoscrizione] non è stata trovata"** quando si esegue l'accesso ad Azure, è possibile che un proxy impedisca di raggiungere l'API di Azure. Configurare le variabili di ambiente `HTTP_PROXY` e `HTTPS_PROXY` con le informazioni del proxy nel terminale usando `export`.

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Se l'impostazione delle variabili di ambiente non consente di risolvere il problema, contattare il supporto tecnico selezionando il pulsante **Si è verificato un problema** precedente.

### <a name="update-the-app"></a>Aggiornare l'app

Per distribuire le modifiche nell'app, è possibile apportare le modifiche in VS Code, salvare i file e quindi usare lo stesso processo illustrato in precedenza scegliendo l'app esistente invece di crearne una nuova.

## <a name="viewing-logs"></a>Visualizzazione dei log

È possibile visualizzare l'output del log (chiamate a `console.log`) dall'app direttamente nella finestra di output di VS Code.

1. In Esplora risorse del **SERVIZIO APP DI AZURE** fare clic con il pulsante destro del mouse sul nodo dell'app e scegliere **Start Streaming Logs** (Avvia streaming dei log).

    ![Avvia streaming dei log](media/quickstart-nodejs/view-logs.png)

1. Quando viene richiesto, scegliere di abilitare la registrazione e riavviare l'applicazione. Una volta riavviata l'app, verrà aperta la finestra di output di VS Code con una connessione al flusso di log. 

    :::image type="content" source="media/quickstart-nodejs/enable-restart.png" alt-text="Screenshot della richiesta di Visual Studio Code di abilitare la registrazione e riavviare l'applicazione con il pulsante Sì selezionato.":::

1. Dopo alcuni secondi, nella finestra di output verrà visualizzato un messaggio che indica che si è connessi al servizio di streaming dei log. È possibile generare altre attività di output aggiornando la pagina nel browser.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Passaggi successivi

L'argomento avvio rapido è stato completato.

> [!div class="nextstepaction"]
> [Esercitazione: App Node.js con MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Configurare l'app Node.js](configure-language-nodejs.md)

Vedere le altre estensioni di Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Strumenti di Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Strumenti dell'interfaccia della riga di comando di Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

In alternativa, è possibile ottenere tutte queste soluzioni installando il pacchetto di estensioni [Node per Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack).
::: zone-end

::: zone pivot="platform-linux"  
## <a name="prerequisites"></a>Prerequisiti

Se non si ha un account Azure [iscriversi](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) per ottenere un account gratuito con USD 200 in crediti di Azure per provare qualsiasi combinazione di servizi.

È necessario che sia installato [Visual Studio Code](https://code.visualstudio.com/) insieme a [Node.js e npm](https://nodejs.org/en/download), lo strumento di gestione dei pacchetti Node.js.

È anche necessario installare l'[estensione del servizio app di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice), che è possibile usare per creare, gestire e distribuire app Web Linux nella piattaforma distribuita come servizio (PaaS) di Azure.

### <a name="sign-in"></a>Accesso

Una volta installata l'estensione, accedere all'account Azure. Sulla barra delle attività fare clic sul logo di Azure per visualizzare l'area **SERVIZIO APP DI AZURE**. Selezionare **Accedi ad Azure** e seguire le istruzioni.

![Accedere ad Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Risoluzione dei problemi

Se viene visualizzato l'errore **"Impossibile trovare la sottoscrizione [ID sottoscrizione]"** , è possibile che un proxy impedisca di raggiungere l'API di Azure. Configurare le variabili di ambiente `HTTP_PROXY` e `HTTPS_PROXY` con le informazioni del proxy nel terminale usando `export`.

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Se l'impostazione delle variabili di ambiente non consente di risolvere il problema, contattare il supporto selezionando **Si è verificato un problema** qui sotto.

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

Prima di continuare, assicurarsi che tutti i prerequisiti siano installati e configurati.

In VS Code dovrebbero essere visualizzati l'indirizzo di posta elettronica di Azure nella barra di stato e la sottoscrizione nello strumento di esplorazione dei **servizi app di Azure**.

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Creare l'applicazione Node.js

Creare quindi un'applicazione Node.js che possa essere distribuita nel cloud. Questo argomento di avvio rapido usa un generatore di applicazioni per eseguire rapidamente lo scaffolding dell'applicazione da un terminale.

> [!TIP]
> Se è già stata completata l'[esercitazione su Node.js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial), è possibile passare alla sezione sulla [distribuzione in Azure](#deploy-to-azure).

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Eseguire lo scaffolding di una nuova applicazione con il generatore Express

[Express](https://www.expressjs.com) è un framework di uso comune per la creazione e l'esecuzione di applicazioni Node.js. È possibile eseguire lo scaffolding (creazione) di una nuova applicazione Express usando lo strumento [generatore Express](https://expressjs.com/en/starter/generator.html). Il generatore Express viene fornito come modulo npm e può essere eseguito direttamente (senza installazione) tramite un comando `npx` dello strumento da riga di comando npm.

```bash
npx express-generator myExpressApp --view pug --git
```

I parametri `--view pug --git` indicano al generatore di usare il motore di modelli [pug](https://pugjs.org/api/getting-started.html) (noto in precedenza come `jade`) e creare un file `.gitignore`.

Per installare tutte le dipendenze dell'applicazione, passare alla nuova cartella ed eseguire `npm install`.

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Eseguire l'applicazione

Assicurarsi quindi che l'applicazione venga eseguita correttamente. Dal terminale avviare l'applicazione usando il comando `npm start` per avviare il server.

```bash
npm start
```

Aprire quindi il browser e passare a `http://localhost:3000`, dove verrà visualizzato un contenuto simile al seguente:

![Esecuzione dell'applicazione Express](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Distribuisci in Azure

In questa sezione si distribuisce l'app Node.js usando VS Code e l'estensione Servizio app di Azure. Questo argomento di avvio rapido usa il modello di distribuzione più semplice in cui l'app viene compressa e distribuita in un'app Web di Azure in Linux.

### <a name="deploy-using-azure-app-service"></a>Eseguire la distribuzione usando il servizio app di Azure

Aprire prima di tutto la cartella dell'applicazione in VS Code.

```bash
code .
```

Nell'area **SERVIZIO APP DI AZURE** selezionare l'icona della freccia verso l'alto blu per distribuire l'app in Azure.

:::image type="content" source="./media/quickstart-nodejs/deploy.png" alt-text="Screenshot del menu del servizio app di Azure in Visual Studio Code con la freccia blu per la distribuzione selezionata.":::

> [!TIP]
> È anche possibile eseguire la distribuzione dal **riquadro comandi** (CTRL+MAIUSC+P) digitando "Distribuisci nell'app Web" ed eseguendo il comando **Servizio app di Azure: Distribuisci nell'app Web**.

1. Scegliere la directory attualmente aperta, `myExpressApp`.

1. Scegliere **Crea una nuova app Web** , che esegue la distribuzione in Servizio app di Azure in Linux per impostazione predefinita.

1. Digitare un nome univoco a livello globale per l'app Web e premere INVIO. I caratteri validi per il nome dell'app sono "a-z", "0-9" e "-".

1. Scegliere un'opzione per **Versione di Node.js**. Il valore consigliato è LTS.

    Il canale di notifica mostra le risorse di Azure che vengono create per l'app.

1. Selezionare **Sì** quando viene chiesto di aggiornare la configurazione per eseguire `npm install` nel server di destinazione. L'app viene quindi distribuita.

    :::image type="content" source="./media/quickstart-nodejs/server-build.png" alt-text="Screenshot della richiesta di aggiornare la configurazione nel server di destinazione con il pulsante Sì selezionato.":::

1. All'avvio della distribuzione, viene richiesto di aggiornare l'area di lavoro in modo che le distribuzioni successive usino automaticamente come destinazione la stessa app Web del servizio app. Scegliere **Sì** per assicurarsi che le modifiche vengano distribuite nell'app corretta.

    :::image type="content" source="./media/quickstart-nodejs/save-configuration.png" alt-text="Screenshot della richiesta di aggiornare l'area di lavoro con il pulsante Sì selezionato.":::

> [!TIP]
> Assicurarsi che l'applicazione sia in ascolto sulla porta fornita dalla variabile di ambiente PORT: `process.env.PORT`.

### <a name="browse-the-app-in-azure"></a>Esplorare l'app in Azure

Una volta completata la distribuzione, selezionare **Esplora sito Web** nel messaggio per visualizzare l'app Web appena distribuita.

### <a name="troubleshooting"></a>Risoluzione dei problemi

Se viene visualizzato l'errore **"Non si dispone delle autorizzazioni necessarie per visualizzare la directory o la pagina"** , è probabile che l'applicazione non sia stata avviata correttamente. Passare alla sezione successiva e visualizzare l'output del log per individuare e correggere l'errore. Se non è possibile risolvere il problema, contattare il supporto facendo clic sul pulsante **Si è verificato un problema** qui sotto. Microsoft sarà lieta di fornire aiuto.

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>Aggiornare l'app

È possibile distribuire le modifiche all'app usando lo stesso processo e scegliendo l'app esistente invece di crearne una nuova.

## <a name="viewing-logs"></a>Visualizzazione dei log

In questa sezione si è appreso come visualizzare i log dell'app del Servizio app in esecuzione. Tutte le chiamate a `console.log` nell'app vengono visualizzate nella finestra di output in Visual Studio Code.

Trovare l'app nello strumento di esplorazione dei **servizi app di Azure** , fare clic su di essa con il pulsante destro del mouse e scegliere **Visualizza log in streaming**.

Verrà aperta la finestra di output di VS Code con una connessione al flusso di log.

![Comando Visualizza log in streaming](./media/quickstart-nodejs/view-logs.png)

:::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="Screenshot della richiesta di VS Code di abilitare la registrazione dei file e riavviare l'app Web, con il pulsante Sì selezionato.":::

Dopo alcuni secondi verrà visualizzato un messaggio che indica che si è connessi al servizio di streaming di log. Aggiornare la pagina alcune volte per visualizzare altre attività.

<pre>
2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
</pre>

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Passaggi successivi

L'argomento avvio rapido è stato completato.

Vedere ora le altre estensioni di Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Strumenti di Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Strumenti dell'interfaccia della riga di comando di Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

In alternativa, è possibile ottenere tutte queste soluzioni installando il pacchetto di estensioni [Node per Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack).


::: zone-end
