---
title: "Guida introduttiva: Creare un'app Web Node.js"
description: Distribuire la prima app Node.js Hello World nel servizio app di Azure in pochi minuti. A questo scopo si usa Visual Studio Code, uno dei molti strumenti disponibili per eseguire distribuzioni nel servizio app.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 03/04/2020
ms.custom: mvc, devcenter, seodec18
ms.openlocfilehash: df1e0809c0122eae593543cd193a6477b03d0938
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690785"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Creare un'app Web Node.js in Azure 

Per iniziare a usare il servizio app di Azure, creare un'app Node.js/Express locale con Visual Studio Code e quindi distribuire l'app nel cloud. Poiché si usa un livello di servizio app gratuito, non verrà addebitato alcun costo per il completamento di questa guida di avvio rapido.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
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

1. Nel terminale assicurarsi di trovarsi nella cartella *nodejs-docs-hello-world*, quindi avviare Visual Studio Code con il comando seguente:

    ```bash
    code .
    ```

1. Sulla barra delle attività di VS Code fare clic sul logo di Azure per visualizzare Esplora risorse del **SERVIZIO APP DI AZURE**. Selezionare **Accedi ad Azure** e seguire le istruzioni. Se si verificano errori, vedere [Risoluzione dei problemi di accesso di Azure](#troubleshooting-azure-sign-in) sotto. Una volta effettuato l'accesso, lo strumento di esplorazione visualizzerà il nome della sottoscrizione di Azure.

    ![Accedere ad Azure](containers/media/quickstart-nodejs/sign-in.png)

1. In Esplora risorse del **SERVIZIO APP DI AZURE** di VS Code selezionare l'icona della freccia verso l'alto blu per distribuire l'app in Azure. È anche possibile richiamare lo stesso comando dal **riquadro comandi** (**CTRL**+**MAIUSC**+**P**) digitando 'Distribuisci nell'app Web' e scegliendo **Servizio app di Azure: Distribuisci nell'app Web**.

    ![Eseguire la distribuzione nell'app Web](containers/media/quickstart-nodejs/deploy.png)
        
1. Scegliere la cartella *nodejs-docs-hello-world*.

1. Scegliere un'opzione di creazione in base al sistema operativo in cui eseguire la distribuzione:

    - Linux: Scegliere **Crea una nuova app Web**
    - Windows: Scegliere **Crea una nuova app Web... Impostazioni avanzate**

1. Digitare un nome univoco a livello globale per l'app Web e premere **INVIO**. Il nome deve essere univoco in tutte le risorse di Azure e usare solo caratteri alfanumerici (' A-Z ',' a-z ' è 0-9') e trattini ('-').

1. Se la destinazione è Linux, selezionare una versione di Node.js quando richiesto. È consigliabile usare una versione **LTS**.

1. Se la destinazione è Windows, seguire le istruzioni aggiuntive:
    1. Selezionare **Creare un nuovo gruppo di risorse**, quindi immettere un nome per il gruppo di risorse, ad esempio `AppServiceQS-rg`.
    1. Selezionare **Windows** come sistema operativo.
    1. Selezionare **Crea nuovo piano di servizio app**, quindi immettere un nome per il piano, ad esempio `AppServiceQS-plan`, quindi selezionare il piano tariffario **F1 Gratuito**.
    1. Scegliere **Ignora per adesso** quando viene richiesto riguardo ad Application Insights.
    1. Scegliere un'area vicina al proprio ambiente o alle risorse a cui si vuole accedere.

1. Dopo aver risposto a tutte le richieste, VS Code mostra le risorse di Azure che vengono create per l'app nella finestra popup di notifica.

    Per la distribuzione in Linux, selezionare **Sì** quando viene richiesto di aggiornare la configurazione per eseguire `npm install` nel server Linux di destinazione.

    ![Richiesta di aggiornamento della configurazione nel server Linux di destinazione](containers/media/quickstart-nodejs/server-build.png)

1. Quando richiesto, selezionare **Sì** per **Distribuisci sempre l'area di lavoro "nodejs-docs-hello-world" in (nome app)"** . Selezionando **Sì** si indica VS Code di impostare automaticamente come destinazione la stessa app Web del servizio app con le distribuzioni successive.

1. Per la distribuzione in Linux, al termine della distribuzione, selezionare **Esplora sito Web** nel messaggio per visualizzare l'app Web appena distribuita. Nel browser verrà visualizzato il testo "Hello World!"

1. Per la distribuzione in Windows, è prima necessario impostare il numero di versione di Node.js per l'app Web:

    1. In VS Code espandere il nodo del nuovo servizio app, quindi fare clic con il pulsante destro del mouse su **Impostazioni applicazione** e scegliere **Aggiungi nuova impostazione**:

        ![Comando Aggiungi impostazione app](containers/media/quickstart-nodejs/add-setting.png)

    1. Immettere `WEBSITE_NODE_DEFAULT_VERSION` per la chiave dell'impostazione.
    1. Immettere `10.15.2` per il valore dell'impostazione.
    1. Fare clic con il pulsante destro del mouse sul nodo relativo al servizio app e scegliere **Riavvia**

        ![Comando Riavvia servizio app](containers/media/quickstart-nodejs/restart.png)

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

    ![Avvia streaming dei log](containers/media/quickstart-nodejs/view-logs.png)

1. Quando viene richiesto, scegliere di abilitare la registrazione e riavviare l'applicazione. Una volta riavviata l'app, verrà aperta la finestra di output di VS Code con una connessione al flusso di log. 

    ![Abilitare la registrazione e riavviare](containers/media/quickstart-nodejs/enable-restart.png)

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

Vedere ora le altre estensioni di Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Strumenti di Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Strumenti dell'interfaccia della riga di comando di Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

In alternativa, è possibile ottenere tutte queste soluzioni installando il pacchetto di estensioni [Node per Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack).
