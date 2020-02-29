---
title: Configurare app node. js
description: Informazioni su come configurare un contenitore node. js predefinito per l'app. Questo articolo illustra le attività di configurazione più comuni.
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 45d7d141bc2ab85ab33be455fc3da5570b0e7f51
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920026"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Configurare un'app node. js Linux per il servizio app Azure

Le app node. js devono essere distribuite con tutte le dipendenze NPM richieste. Il motore di distribuzione del servizio app (kudu) viene eseguito automaticamente `npm install --production` quando si distribuisce un [repository git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)o un [pacchetto zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) con processi di compilazione attivati. Se si distribuiscono i file tramite [FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), tuttavia, è necessario caricare manualmente i pacchetti necessari.

Questa guida fornisce i concetti chiave e le istruzioni per gli sviluppatori node. js che usano un contenitore Linux incorporato nel servizio app. Se non si è mai usato app Azure servizio, seguire prima la [Guida introduttiva a node. js](quickstart-nodejs.md) e [node. js con MongoDB](tutorial-nodejs-mongodb-app.md) .

## <a name="show-nodejs-version"></a>Mostra versione di node. js

Per visualizzare la versione corrente di node. js, eseguire il comando seguente nel [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Per visualizzare tutte le versioni supportate di node. js, eseguire il comando seguente nel [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Impostare la versione di node. js

Per impostare l'app su una [versione supportata di node. js](#show-nodejs-version), eseguire il comando seguente nella [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Questa impostazione specifica la versione di node. js da usare, sia in fase di esecuzione che durante il ripristino automatico dei pacchetti in Kudu.

> [!NOTE]
> È necessario impostare la versione di node. js nel `package.json`del progetto. Il motore di distribuzione viene eseguito in un contenitore separato che contiene tutte le versioni di node. js supportate.

## <a name="customize-build-automation"></a>Personalizzare l'automazione della compilazione

Se si distribuisce l'app usando i pacchetti git o zip con l'automazione della compilazione attivata, l'automazione della compilazione del servizio app esegue la sequenza seguente:

1. Eseguire uno script personalizzato se specificato da `PRE_BUILD_SCRIPT_PATH`.
1. Eseguire `npm install` senza flag, che include gli script NPM `preinstall` e `postinstall` e installa `devDependencies`.
1. Eseguire `npm run build` se nel file *Package. JSON*viene specificato uno script di compilazione.
1. Eseguire `npm run build:azure` se nel file *Package. JSON*è specificata una build: script di Azure.
1. Eseguire uno script personalizzato se specificato da `POST_BUILD_SCRIPT_PATH`.

> [!NOTE]
> Come descritto in [NPM docs](https://docs.npmjs.com/misc/scripts), gli script denominati `prebuild` e `postbuild` eseguire prima e dopo `build`, rispettivamente, se specificato. `preinstall` e `postinstall` eseguire rispettivamente prima e dopo `install`.

`PRE_BUILD_COMMAND` e `POST_BUILD_COMMAND` sono variabili di ambiente vuote per impostazione predefinita. Per eseguire i comandi di pre-compilazione, definire `PRE_BUILD_COMMAND`. Per eseguire i comandi post-compilazione, definire `POST_BUILD_COMMAND`.

Nell'esempio seguente vengono specificate le due variabili a una serie di comandi, separate da virgole.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Per altre variabili di ambiente per personalizzare l'automazione della compilazione, vedere [configurazione di Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Per altre informazioni sull'esecuzione del servizio app e sulla compilazione di app node. js in Linux, vedere [la documentazione di Oryx: come vengono rilevate e compilate le app node. js](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md).

## <a name="configure-nodejs-server"></a>Configurare il server node. js

I contenitori node. js sono dotati di [PM2](https://pm2.keymetrics.io/), un gestore di processi di produzione. È possibile configurare l'app per iniziare a usare PM2 o con NPM oppure con un comando personalizzato.

- [Esegui comando personalizzato](#run-custom-command)
- [Esegui NPM Start](#run-npm-start)
- [Eseguire con PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Esegui comando personalizzato

Il servizio app può avviare l'app usando un comando personalizzato, ad esempio un eseguibile come *Run.sh*. Ad esempio, per eseguire `npm run start:prod`, eseguire il comando seguente nel [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Esegui NPM Start

Per avviare l'app con `npm start`, è sufficiente assicurarsi che nel file *Package. JSON* sia presente uno script di `start`. Ad esempio:

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

Per usare un file *Package. JSON* personalizzato nel progetto, eseguire il comando seguente nel [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Eseguire con PM2

Il contenitore avvia automaticamente l'app con PM2 quando si trova uno dei file node. js comuni nel progetto:

- *bin/www*
- *Server. js*
- *app.js*
- *index.js*
- *hostingstart. js*
- Uno dei [file PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)seguenti: *Process. JSON* e *Ecosystem. config. js*

È anche possibile configurare un file di avvio personalizzato con le estensioni seguenti:

- Un file con *estensione js*
- Un [file PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) con estensione *JSON*, *config. js*, *YAML*o *yml*

Per aggiungere un file di avvio personalizzato, eseguire il comando seguente nel [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Eseguire il debug in modalità remota

> [!NOTE]
> Il debug remoto è attualmente in fase di anteprima.

È possibile eseguire il debug dell'app node. js in modalità remota in [Visual Studio Code](https://code.visualstudio.com/) se viene configurata per l' [esecuzione con PM2](#run-with-pm2), tranne quando viene eseguita con *. config. js, *. yml o *. YAML*.

Nella maggior parte dei casi, non è necessaria alcuna configurazione aggiuntiva per l'app. Se l'app viene eseguita con un file *Process. JSON* (predefinito o personalizzato), deve avere una proprietà `script` nella radice JSON. Ad esempio:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Per configurare Visual Studio Code per il debug remoto, installare l' [estensione del servizio app](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Seguire le istruzioni nella pagina di estensione e accedere ad Azure in Visual Studio Code.

In Azure Explorer trovare l'app di cui si vuole eseguire il debug, fare clic con il pulsante destro del mouse su di essa e scegliere **Avvia debug remoto**. Fare clic su **Sì** per abilitarla per l'app. Il servizio app avvia un proxy di tunneling per l'utente e connette il debugger. È quindi possibile effettuare richieste all'app e vedere il debugger che sospende i punti di interruzione.

Al termine del debug, arrestare il debugger selezionando **Disconnetti**. Quando richiesto, fare clic su **Sì** per disabilitare il debug remoto. Per disabilitarlo in un secondo momento, fare di nuovo clic con il pulsante destro del mouse sull'app in Azure Explorer e scegliere **Disabilita debug remoto**.

## <a name="access-environment-variables"></a>Accedere alle variabili di ambiente

Nel servizio app è possibile [configurare le impostazioni dell'app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) al di fuori del codice dell'app. Sarà quindi possibile accedervi usando il modello node. js standard. Ad esempio, per accedere a un'impostazione dell'app denominata `NODE_ENV`, usare il codice seguente:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Esegui grugnito/Bower/Gulp

Per impostazione predefinita, Kudu viene eseguito `npm install --production` quando viene riconosciuta un'app node. js distribuita. Se l'app richiede uno degli strumenti di automazione più diffusi, ad esempio grugnito, Bower o Gulp, è necessario fornire uno [script di distribuzione personalizzato](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) per eseguirlo.

Per abilitare il repository per l'esecuzione di questi strumenti, è necessario aggiungerli alle dipendenze in *Package. JSON.* Ad esempio:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Da una finestra del terminale locale passare alla directory radice del repository ed eseguire i comandi seguenti:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

La radice del repository dispone ora di due file aggiuntivi: *. Deployment* e *deploy.sh*.

Aprire *deploy.sh* e trovare la sezione `Deployment`, che ha un aspetto simile al seguente:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Questa sezione termina con l'esecuzione `npm install --production`. Aggiungere la sezione di codice è necessario eseguire lo strumento necessario *alla fine* della sezione `Deployment`:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Vedere un [esempio nell'esempio mean. js, in](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)cui lo script di distribuzione esegue anche un comando `npm install` personalizzato.

### <a name="bower"></a>Bower

Questo frammento di codice esegue `bower install`.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Questo frammento di codice esegue `gulp imagemin`.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Questo frammento di codice esegue `grunt`.

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

I framework Web più diffusi consentono di accedere alle informazioni `X-Forwarded-*` nel modello di app standard. In [Express](https://expressjs.com/)è possibile usare i [proxy di attendibilità](https://expressjs.com/guide/behind-proxies.html). Ad esempio:

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

Quando un'app node. js funzionante si comporta in modo diverso nel servizio app o presenta errori, provare a eseguire le operazioni seguenti:

- [Accedere al flusso di log](#access-diagnostic-logs).
- Testare l'app localmente in modalità di produzione. Il servizio app esegue le app node. js in modalità di produzione, quindi è necessario assicurarsi che il progetto funzioni come previsto in modalità di produzione localmente. Ad esempio:
    - A seconda del file *Package. JSON*, è possibile installare pacchetti diversi per la modalità di produzione (`dependencies` rispetto a `devDependencies`).
    - Alcuni framework Web possono distribuire i file statici in modo diverso in modalità di produzione.
    - Alcuni framework Web possono usare script di avvio personalizzati durante l'esecuzione in modalità di produzione.
- Eseguire l'app nel servizio app in modalità di sviluppo. In [mean. js](https://meanjs.org/), ad esempio, è possibile impostare l'app sulla modalità di sviluppo in fase di esecuzione impostando [l'impostazione dell'app `NODE_ENV`](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: app node. js con MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Domande frequenti sul Servizio app di Azure in Linux](app-service-linux-faq.md)
