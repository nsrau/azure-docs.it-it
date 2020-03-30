---
title: Configurare le app Node.js
description: Scopri come configurare un contenitore Node.js predefinito per la tua app. Questo articolo illustra le attività di configurazione più comuni.
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: fdc5129fc395f99cb4c244414ea952b2776dc4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252727"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Configurare un'app Node.js Linux per il servizio app di AzureConfigure a Linux Node.js app for Azure App Service

Le app Node.js devono essere distribuite con tutte le dipendenze NPM necessarie. Il motore di distribuzione del servizio `npm install --production` app (Kudu) viene eseguito automaticamente quando si distribuisce un [repository Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)o un [pacchetto zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) con i processi di compilazione attivati. Se si distribuiscono i file utilizzando [FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), tuttavia, è necessario caricare manualmente i pacchetti necessari.

Questa guida fornisce concetti chiave e istruzioni per gli sviluppatori Node.js che usano un contenitore Linux incorporato nel servizio app. Se non hai mai usato il servizio app di Azure, segui prima la [guida introduttiva di Node.js](quickstart-nodejs.md) e [l'esercitazione su MongoDB.](tutorial-nodejs-mongodb-app.md)

## <a name="show-nodejs-version"></a>Mostra versione Node.js

Per visualizzare la versione corrente di Node.js, eseguire il comando seguente in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Per visualizzare tutte le versioni di Node.js supportate, eseguire il comando seguente in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Impostare la versione di Node.jsSet Node.js version

Per impostare l'app su una [versione Node.js supportata,](#show-nodejs-version)eseguire il comando seguente in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Questa impostazione specifica la versione di Node.js da utilizzare, sia in fase di esecuzione che durante il ripristino automatico del pacchetto in Kudu.This setting specifies the Node.js version to use, both at runtime and during automated package restore in Kudu.

> [!NOTE]
> È necessario impostare la versione di `package.json`Node.js nel file . Il motore di distribuzione viene eseguito in un contenitore separato che contiene tutte le versioni supportate di Node.js.The deployment engine runs in a separate container that contains all the supported Node.js versions.

## <a name="customize-build-automation"></a>Personalizzare l'automazione della compilazione

Se distribuisci l'app usando pacchetti Git o zip con l'automazione della compilazione attivata, l'automazione della compilazione del servizio app passa la sequenza seguente:

1. Eseguire uno script `PRE_BUILD_SCRIPT_PATH`personalizzato se specificato da .
1. Esegui `npm install` senza flag, che `preinstall` include `postinstall` npm e `devDependencies`script e installa anche .
1. Eseguire `npm run build` se uno script di compilazione è specificato in *package.json*.
1. Eseguire `npm run build:azure` se nel *file package.json*è specificato uno script build:azure .
1. Eseguire uno script `POST_BUILD_SCRIPT_PATH`personalizzato se specificato da .

> [!NOTE]
> Come descritto in [npm docs](https://docs.npmjs.com/misc/scripts), gli script denominati `prebuild` ed `postbuild` eseguiti prima e dopo `build`, rispettivamente, se specificati. `preinstall`e `postinstall` correre rispettivamente prima e dopo `install`,

`PRE_BUILD_COMMAND`e `POST_BUILD_COMMAND` sono variabili di ambiente vuote per impostazione predefinita. Per eseguire comandi di `PRE_BUILD_COMMAND`pre-compilazione, definire . Per eseguire i comandi `POST_BUILD_COMMAND`di post-compilazione, definire .

Nell'esempio seguente le due variabili vengono specificate a una serie di comandi, separati da virgole.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Per altre variabili di ambiente per personalizzare l'automazione della compilazione, vedere [Configurazione Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Per altre informazioni su come viene eseguito il servizio app e crea le app Node.js in Linux, vedere [documentazione di Oryx: Come vengono rilevate e compilate le app Node.js.](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md)

## <a name="configure-nodejs-server"></a>Configurare il server Node.js

I contenitori Node.js sono dotati di [PM2](https://pm2.keymetrics.io/), un gestore di processi di produzione. Puoi configurare l'app per l'avvio con PM2, con NPM o con un comando personalizzato.

- [Esegui comando personalizzato](#run-custom-command)
- [Eseguire l'inizio npm](#run-npm-start)
- [Esegui con PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Esegui comando personalizzato

Il servizio app può avviare l'app usando un comando personalizzato, ad esempio un eseguibile come *run.sh*. Ad esempio, `npm run start:prod`per eseguire , eseguire il comando seguente in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Eseguire l'inizio npm

Per avviare `npm start`l'app usando `start` , assicurati che uno script si trova nel file *package.json.* Ad esempio:

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

Per utilizzare un *file package.json* personalizzato nel progetto, eseguire il comando seguente in [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Esegui con PM2

Il contenitore avvia automaticamente l'app con PM2 quando uno dei file Node.js comuni si trova nel progetto:

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Uno dei seguenti [file PM2:](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) *process.json* e *ecosystem.config.js*

È inoltre possibile configurare un file di avvio personalizzato con le seguenti estensioni:

- Un file *.js*
- Un [file PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) con estensione *.json*, *.config.js*, *.yaml*o *.yml*

Per aggiungere un file di avvio personalizzato, eseguire il comando seguente in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Eseguire il debug in modalità remota

> [!NOTE]
> Il debug remoto è attualmente in anteprima.

È possibile eseguire il debug dell'app Node.js in modalità remota in [Visual Studio Code](https://code.visualstudio.com/) se viene configurata per l'esecuzione con [PM2](#run-with-pm2), tranne quando viene eseguita utilizzando un file con estensione config.js, yml o *yaml*.

Nella maggior parte dei casi, non è necessaria alcuna configurazione aggiuntiva per l'app. Se l'app viene eseguita con un file *process.json* (predefinito o personalizzato), deve avere una `script` proprietà nella radice JSON. Ad esempio:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Per configurare Visual Studio Code per il debug remoto, installare [l'estensione del servizio app.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) Seguire le istruzioni nella pagina dell'estensione e accedere ad Azure nel codice di Visual Studio.Follow the instructions on the extension page and sign in to Azure in Visual Studio Code.

In Esplora di Azure individuare l'app di cui si vuole eseguire il debug, fare clic con il pulsante destro del mouse su di essa e **scegliere Avvia debug remoto**. Fare clic su **Sì** per abilitarla per l'app. Il servizio app avvia automaticamente un proxy di tunneling e connette il debugger. È quindi possibile effettuare richieste all'app e visualizzare la pausa del debugger in corrispondenza dei punti di interruzione.

Al termine del debug, arrestare il debugger selezionando **Disconnetti**. Quando richiesto, fare clic su **Sì** per disattivare il debug remoto. Per disabilitarla in un secondo momento, fare di nuovo clic con il pulsante destro del mouse sull'app in Esplora di Azure e **selezionare Disabilita debug remoto**.

## <a name="access-environment-variables"></a>Accedere alle variabili di ambiente

Nel servizio app è possibile [configurare le impostazioni dell'app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) al di fuori del codice dell'app. È quindi possibile accedervi utilizzando il modello Node.js standard. Ad esempio, per accedere a un'impostazione dell'app denominata `NODE_ENV`, usare il codice seguente:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Eseguire Grunt/Bower/Gulp

Per impostazione predefinita, Kudu viene eseguito quando riconosce la distribuzione di un'app Node.js.By default, Kudu runs `npm install --production` when it recognizes a Node.js app is deployed. Se l'app richiede uno degli strumenti di automazione più diffusi, ad esempio Grunt, Bower o Gulp, devi fornire uno script di [distribuzione personalizzato](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) per eseguirla.

Per consentire al repository di eseguire questi strumenti, è necessario aggiungerli alle dipendenze in *package.json.To* enable your repository to run these tools, you need to add them to the dependencies in package.json. Ad esempio:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Da una finestra del terminale locale, passare alla directory del repository ed eseguire i seguenti comandi:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

La radice del repository dispone ora di due file aggiuntivi: *.deployment* e *deploy.sh*.

Apri *deploy.sh* e `Deployment` trova la sezione, che ha questo aspetto:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Questa sezione termina `npm install --production`con l'esecuzione di . Aggiungere la sezione di codice necessaria per eseguire `Deployment` lo strumento richiesto alla *fine* della sezione:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Vedere un [esempio nell'esempio MEAN.js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), in `npm install` cui lo script di distribuzione esegue anche un comando personalizzato.

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

I framework Web più diffusi consentono di accedere alle informazioni `X-Forwarded-*` nel modello di app standard. In [Express](https://expressjs.com/)è possibile utilizzare [i proxy di trust.](https://expressjs.com/guide/behind-proxies.html) Ad esempio:

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

## <a name="troubleshooting"></a>Risoluzione dei problemi

Quando un'app Node.js funzionante si comporta in modo diverso nel servizio app o presenta errori, prova a eseguire le operazioni seguenti:

- [Accedere al flusso di log](#access-diagnostic-logs).
- Testa l'app localmente in modalità di produzione. Il servizio app esegue le app Node.js in modalità di produzione, pertanto è necessario assicurarsi che il progetto funzioni come previsto in modalità di produzione in locale. Ad esempio:
    - A seconda del *file package.json*, è`dependencies` possibile `devDependencies`che sia possibile installare pacchetti diversi per la modalità di produzione ( vs ).
    - Alcuni framework Web possono distribuire i file statici in modo diverso in modalità di produzione.
    - Alcuni framework Web possono utilizzare script di avvio personalizzati durante l'esecuzione in modalità di produzione.
- Eseguire l'app nel servizio app in modalità di sviluppo. Ad esempio, in [MEAN.js](https://meanjs.org/), puoi impostare l'app sulla modalità di sviluppo in fase di esecuzione [ `NODE_ENV` impostando l'impostazione dell'app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: App Node.js con MongoDBTutorial: Node.js app with MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Domande frequenti sul Servizio app di Azure in Linux](app-service-linux-faq.md)
