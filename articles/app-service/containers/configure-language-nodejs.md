---
title: Configurare le app Node. js - servizio App di Azure | Microsoft Docs
description: Informazioni su come configurare le app Node. js per lavorare in servizio App di Azure
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 43dc76e6d1e1ec2a6167f1d3e3cc7b8780f843db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850240"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Configurare un'app Node. js di Linux per servizio App di Azure

App Node. js deve essere distribuita con tutte le dipendenze NPM necessari. Il motore di distribuzione di servizio App (Kudu) viene eseguita automaticamente `npm install --production` automaticamente quando si distribuisce un [repository Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), o un' [pacchetto Zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) con processi di compilazione attivati. Se si distribuiscono i file usando [FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), tuttavia, è necessario caricare manualmente i pacchetti necessari.

Questa guida fornisce i concetti chiave e le istruzioni per gli sviluppatori di Node. js che usano un contenitore Linux incorporato nel servizio App. Se non si è mai usato il servizio App di Azure, seguire le [Guida introduttiva di Node. js](quickstart-nodejs.md) e [Node. js con MongoDB esercitazione](tutorial-nodejs-mongodb-app.md) prima.

## <a name="show-nodejs-version"></a>Versione di Node. js Show

Per visualizzare la versione corrente di Node. js, eseguire il comando seguente [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Per visualizzare tutte le versioni di Node. js supportate, eseguire il comando seguente [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Impostare la versione di Node. js

Per impostare l'app su un [Node. js versione supportata](#show-nodejs-version), eseguire il comando seguente [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Questa impostazione specifica la versione di Node. js da usare, entrambi in fase di esecuzione e durante il ripristino dei pacchetti automatizzato in Kudu.

> [!NOTE]
> È necessario impostare la versione di Node. js del progetto `package.json`. Il motore di distribuzione viene eseguito in un contenitore separato che contiene tutte le versioni supportate di Node. js.

## <a name="configure-nodejs-server"></a>Configurare il server Node. js

I contenitori di Node. js sono dotate [PM2](http://pm2.keymetrics.io/), un gestore di processi di produzione. È possibile configurare l'app per l'avvio con PM2, o con NPM o con un comando personalizzato.

- [Eseguire un comando personalizzato](#run-custom-command)
- [Eseguire npm start](#run-npm-start)
- [Esegui con PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Eseguire un comando personalizzato

Servizio App puoi avviare l'app usando un comando personalizzato, ad esempio, ad esempio un file eseguibile *run.sh*. Ad esempio, per eseguire `npm run start:prod`, eseguire il comando seguente [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Eseguire npm start

Per avviare l'app usando `npm start`, assicurarsi di un `start` script è nel *package. JSON* file. Ad esempio: 

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

Usare una classe personalizzata *package. JSON* nel progetto, eseguire il comando seguente [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Esegui con PM2

Il contenitore avvia automaticamente l'app con PM2 quando uno dei file di Node. js comuni viene trovato nel progetto:

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Uno dei seguenti [file PM2](http://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *Process* e *ecosystem.config.js*

È anche possibile configurare un file di avvio personalizzata con le estensioni seguenti:

- Oggetto *js* file
- Oggetto [file PM2](http://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) con l'estensione *JSON*, *. config. js*, *yaml*, o *yml*

Per aggiungere un file di avvio personalizzate, eseguire il comando seguente [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Eseguire il debug in modalità remota

> [!NOTE]
> Debug remoto è attualmente in anteprima.

È possibile eseguire il debug di app Node. js in modalità remota in [Visual Studio Code](https://code.visualstudio.com/) se si configura per [eseguito con PM2](#run-with-pm2), tranne quando si esegue usando un *. config. js, *.yml, o *yaml*.

Nella maggior parte dei casi, non è necessario per l'app alcuna configurazione aggiuntiva. Se l'app viene eseguita con un *Process* file (predefinito o personalizzato), deve avere un `script` proprietà nella radice del JSON. Ad esempio: 

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Per configurare Visual Studio Code per il debug remoto, installare il [estensione del servizio App](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Seguire le istruzioni nella pagina dell'estensione e accedere ad Azure in Visual Studio Code.

In Azure explorer, trovare l'app si desidera eseguire il debug, pulsante destro del mouse e selezionare **avviare il debug remoto**. Fare clic su **Sì** abilitarlo per l'app. Servizio App viene avviato un proxy di tunnel per si e collega il debugger. È quindi possibile effettuare richieste all'app e visualizzare il debugger di interruzione in corrispondenza di punti di interruzione.

Una volta terminato il debug, arrestare il debugger selezionando **Disconnect**. Quando richiesto, è necessario fare clic su **Sì** per disabilitare il debug remoto. Per disabilitare in un secondo momento, fare doppio clic dell'app nuovamente nell'explorer di Azure e selezionare **disabilitare il debug remoto**.

## <a name="access-environment-variables"></a>Accedere alle variabili di ambiente

Nel servizio app è possibile [configurare le impostazioni dell'app](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) al di fuori del codice dell'app. Quindi è possibile accedervi usando il modello standard di Node. js. Ad esempio, per accedere a un'impostazione dell'app denominata `NODE_ENV`, usare il codice seguente:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Eseguire Grunt/Bower/Gulp

Per impostazione predefinita, viene eseguito Kudu `npm install --production` quando riconosce viene distribuita un'app Node. js. Se l'app richiede uno qualsiasi degli strumenti di automazione più diffusi, ad esempio Grunt, Bower o Gulp, è necessario fornire un [script di distribuzione personalizzato](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) per eseguirlo.

Per abilitare il repository eseguire questi strumenti, è necessario aggiungerle alle dipendenze in *package. JSON.* Ad esempio: 

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Da una finestra del terminale locale passare alla radice del repository ed eseguire i comandi seguenti:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Radice del repository include ora due file aggiuntivi: *con estensione Deployment* e *deploy.sh*.

Aprire *deploy.sh* e individuare il `Deployment` sezione, che ha un aspetto simile al seguente:

```bash
##################################################################################################################################
# Deployment
# ----------
```

In questa sezione termina con esecuzione `npm install --production`. Aggiungere la sezione di codice è necessario eseguire lo strumento necessario *alla fine* del `Deployment` sezione:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Vedere un' [riportato nell'esempio di Mean. js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), in cui lo script di distribuzione viene eseguito anche un oggetto personalizzato `npm install` comando.

### <a name="bower"></a>Bower

Questo frammento di codice viene eseguito `bower install`.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Questo frammento di codice viene eseguito `gulp imagemin`.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Questo frammento di codice viene eseguito `grunt`.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>Rilevare una sessione HTTPS

Nel servizio app la [terminazione SSL](https://wikipedia.org/wiki/TLS_termination_proxy) si verifica nei servizi di bilanciamento del carico di rete, pertanto tutte le richieste HTTPS raggiungano l'app come richieste HTTP non crittografate. Se la logica dell'app deve controllare se le richieste degli utenti sono crittografate o meno, esaminare l'intestazione `X-Forwarded-Proto`.

I framework Web più diffusi consentono di accedere alle informazioni `X-Forwarded-*` nel modello di app standard. Nelle [Express](https://expressjs.com/), è possibile usare [trust proxy](http://expressjs.com/guide/behind-proxies.html). Ad esempio: 

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Aprire una sessione SSH nel browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>risoluzione dei problemi

Quando un'app Node. js funzionante nel servizio App, si comporta in modo diverso o presenta errori, procedere come segue:

- [Accedere al flusso di log](#access-diagnostic-logs).
- Testare l'app in locale in modalità di produzione. Servizio App le app Node. js viene eseguito in modalità di produzione, pertanto è necessario assicurarsi che il progetto funziona come previsto in modalità di produzione in locale. Ad esempio: 
    - A seconda del *package. JSON*, pacchetti diversi possono essere installati per la modalità di produzione (`dependencies` confronto `devDependencies`).
    - Alcuni Framework web possono distribuire file statici in modo diverso in modalità di produzione.
    - Alcuni Framework web può usare gli script di avvio personalizzate durante l'esecuzione in modalità di produzione.
- Eseguire l'app nel servizio App in modalità di sviluppo. Ad esempio, nella [Mean. js](http://meanjs.org/), è possibile impostare l'app per la modalità di sviluppo in fase di esecuzione dal [impostazione il `NODE_ENV` impostazione app](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: App Node.js con MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Domande frequenti sul Servizio app di Azure in Linux](app-service-linux-faq.md)