---
title: Creare un'app Web Node.js
description: Distribuzione di Node.js nei servizi app di Azure
author: KarlErickson
ms.author: karler
ms.date: 07/18/2019
ms.topic: quickstart
ms.service: app-service
ms.devlang: javascript
ms.openlocfilehash: ced2977509f16f8dab2abe5546e19b7e05fb2a3d
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68975884"
---
# <a name="create-a-nodejs-app-in-azure"></a>Creare un'app Node.js in Azure

Il Servizio app di Azure offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione. Questo argomento di avvio rapido illustra come distribuire un'app Node.js in Servizio app di Azure.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha un account Azure [iscriversi](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) per ottenere un account gratuito con USD 200 in crediti di Azure per provare qualsiasi combinazione di servizi.

È necessario che sia installato [Visual Studio Code](https://code.visualstudio.com/) insieme a [Node.js e npm](https://nodejs.org/en/download), lo strumento di gestione dei pacchetti Node.js.

È anche necessario installare l'[estensione del servizio app di Azure](vscode:extension/ms-azuretools.vscode-azureappservice), che è possibile usare per creare, gestire e distribuire app Web Linux nella piattaforma distribuita come servizio (PaaS) di Azure.

### <a name="sign-in"></a>Accesso

Una volta installata l'estensione, accedere all'account Azure. Sulla barra delle attività fare clic sul logo di Azure per visualizzare lo strumento di esplorazione dei **servizi app di Azure**. Fare clic su **Accedi ad Azure** e seguire le istruzioni.

![Accedere ad Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>risoluzione dei problemi

Se viene visualizzato l'errore **"Impossibile trovare la sottoscrizione [ID sottoscrizione]"** , è possibile che un proxy impedisca di raggiungere l'API di Azure. Configurare le variabili di ambiente `HTTP_PROXY` e `HTTPS_PROXY` con le informazioni del proxy nel terminale usando `export`.

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Se l'impostazione delle variabili di ambiente non consente di risolvere il problema, contattare il supporto facendo clic sul pulsante **Si è verificato un problema** sotto.

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

Prima di continuare, assicurarsi che tutti i prerequisiti siano installati e configurati.

In VS Code dovrebbero essere visualizzati l'indirizzo di posta elettronica di Azure nella barra di stato e la sottoscrizione nello strumento di esplorazione dei **servizi app di Azure**.

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Creare l'applicazione Node.js

Creare quindi un'applicazione Node.js che possa essere distribuita nel cloud. Questo argomento di avvio rapido usa un generatore di applicazioni per eseguire rapidamente lo scaffolding dell'applicazione da un terminale.

> [!TIP]
> Se è già stata completata l'[esercitazione su Node.js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial), è possibile passare alla sezione [Distribuire il sito Web](#deploy-the-website).

### <a name="install-the-express-generator"></a>Installare il generatore Express

[Express](https://www.expressjs.com) è un framework di uso comune per la creazione e l'esecuzione di applicazioni Node.js. È possibile eseguire lo scaffolding (creazione) di una nuova applicazione Express usando lo strumento [generatore Express](https://expressjs.com/en/starter/generator.html). Il generatore Express viene fornito come modulo npm e installato tramite un comando `npm` dello strumento da riga di comando npm.

```bash
npm install -g express-generator
```

L'opzione `-g` installa il generatore Express globalmente nel computer, in modo che sia possibile eseguirlo da qualsiasi posizione.

### <a name="scaffold-a-new-application"></a>Eseguire lo scaffolding di una nuova applicazione

Eseguire quindi lo scaffolding di una nuova applicazione Express denominata `myExpressApp` eseguendo:

```bash
express myExpressApp --view pug --git
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

Aprire quindi il browser e passare a [http://localhost:3000](http://localhost:3000), dove dovrebbe venire visualizzato un contenuto simile al seguente:

![Esecuzione dell'applicazione Express](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-website"></a>Distribuire il sito Web

In questa sezione si distribuisce il sito Web Node.js usando VS Code e l'estensione del servizio app di Azure. Questo argomento di avvio rapido usa il modello di distribuzione più semplice in cui l'app viene compressa e distribuita in un'app Web di Azure in Linux.

### <a name="deploy-using-azure-app-service"></a>Eseguire la distribuzione usando il servizio app di Azure

Aprire prima di tutto la cartella dell'applicazione in VS Code.

```bash
code .
```

Nello strumento di esplorazione dei **servizi app di Azure** fare clic sull'icona a forma di freccia blu per distribuire l'app in Azure.

![Eseguire la distribuzione nell'app Web](./media/quickstart-nodejs/deploy.png)

> [!TIP]
> È anche possibile eseguire la distribuzione dal **riquadro comandi** (CTRL+MAIUSC+P) digitando "Distribuisci nell'app Web" ed eseguendo il comando **Servizio app di Azure: Distribuisci nell'app Web**.

1. Scegliere la directory attualmente aperta, `myExpressApp`.

2. Scegliere **Crea una nuova app Web**.

3. Digitare un nome univoco a livello globale per l'app Web e premere INVIO. I caratteri validi per il nome dell'app sono "a-z", "0-9" e "-".

4. Scegliere un'opzione per **Versione di Node.js**. Il valore consigliato è LTS.

    Il canale di notifica mostra le risorse di Azure che vengono create per l'app.

Fare clic su **Sì** quando viene richiesto di aggiornare la configurazione per eseguire `npm install` nel server di destinazione. L'app viene quindi distribuita.

![Distribuzione configurata](./media/quickstart-nodejs/server-build.png)

All'avvio della distribuzione, viene richiesto di aggiornare l'area di lavoro in modo che le distribuzioni successive usino automaticamente come destinazione la stessa app Web del servizio app. Scegliere **Sì** per assicurarsi che le modifiche vengano distribuite nell'app corretta.

![Distribuzione configurata](./media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Assicurarsi che l'applicazione sia in ascolto sulla porta fornita dalla variabile di ambiente PORT: `process.env.PORT`.

### <a name="browse-the-website"></a>Esplorare il sito Web

Una volta completata la distribuzione, fare clic su **Esplora il sito Web** nel messaggio per visualizzare il sito Web appena distribuito.

### <a name="troubleshooting"></a>risoluzione dei problemi

Se viene visualizzato l'errore **"Non si dispone delle autorizzazioni necessarie per visualizzare la directory o la pagina"** , è probabile che l'applicazione non sia stata avviata correttamente. Passare alla sezione successiva e visualizzare l'output del log per individuare e correggere l'errore. Se non è possibile risolvere il problema, contattare il supporto facendo clic sul pulsante **Si è verificato un problema** sotto. Microsoft sarà lieta di fornire aiuto.

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="updating-the-website"></a>Aggiornamento del sito Web

È possibile distribuire le modifiche all'app usando lo stesso processo e scegliendo l'app esistente invece di crearne una nuova.

## <a name="viewing-logs"></a>Visualizzazione dei log

In questa sezione viene illustrato come visualizzare i log dal sito Web in esecuzione. Tutte le chiamate di `console.log` nel sito vengono visualizzate nella finestra di output in Visual Studio Code.

Trovare l'app nello strumento di esplorazione dei **servizi app di Azure**, fare clic su di essa con il pulsante destro del mouse e scegliere **Visualizza log in streaming**.

Quando viene richiesto, scegliere di abilitare la registrazione e riavviare l'applicazione. Una volta riavviata l'app, verrà aperta la finestra di output di VS Code con una connessione al flusso di log.

![Comando Visualizza log in streaming](./media/quickstart-nodejs/view-logs.png)

![Abilitare la registrazione e riavviare](./media/quickstart-nodejs/enable-restart.png)

Dopo alcuni secondi verrà visualizzato un messaggio che indica che si è connessi al servizio di streaming di log.

```bash
Connecting to log-streaming service...
2017-12-21 17:33:51.428 INFO  - Container practical-mustache_2 for site practical-mustache initialized successfully.
2017-12-21 17:33:56.500 INFO  - Container logs
```

Aggiornare la pagina alcune volte nel browser per visualizzare l'output di log.

```bash
2017-12-21 17:35:17.774 INFO  - Container logs
2017-12-21T17:35:14.955412230Z GET / 304 141.798 ms - -
2017-12-21T17:35:15.248930479Z GET /stylesheets/style.css 304 3.180 ms - -
2017-12-21T17:35:15.378623115Z GET /favicon.ico 404 53.839 ms - 995
```

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
