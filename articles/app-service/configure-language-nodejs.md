---
title: Configurare app Node.js
description: Informazioni su come configurare un'app Node.js nelle istanze di Windows native o in un contenitore Linux predefinito in app Azure servizio. Questo articolo illustra le attività di configurazione più comuni.
ms.custom: devx-track-js
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 48b111966d58af80b6c34fa17231034f4f0cc213
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91311836"
---
# <a name="configure-a-nodejs-app-for-azure-app-service"></a>Configurare un'app Node.js per il servizio app Azure

Node.js le app devono essere distribuite con tutte le dipendenze NPM richieste. Il motore di distribuzione del servizio app viene eseguito automaticamente `npm install --production` quando si distribuisce un [repository git](deploy-local-git.md)o un [pacchetto zip](deploy-zip.md) con l'automazione della compilazione abilitata. Se si distribuiscono i file tramite [FTP/S](deploy-ftp.md), tuttavia, è necessario caricare manualmente i pacchetti necessari.

Questa guida fornisce i concetti chiave e le istruzioni per Node.js gli sviluppatori che eseguono la distribuzione nel servizio app. Se non si è mai usato app Azure servizio, seguire prima l'esercitazione [Node.js avvio rapido](quickstart-nodejs.md) e [Node.js con MongoDB](tutorial-nodejs-mongodb-app.md) .

## <a name="show-nodejs-version"></a>Mostra versione Node.js

::: zone pivot="platform-windows"  

Per visualizzare la versione corrente di Node.js, eseguire il comando seguente nel [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings list --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

Per visualizzare tutte le versioni di Node.js supportate, eseguire il comando seguente nel [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep node
```

::: zone-end

::: zone pivot="platform-linux"

Per visualizzare la versione corrente di Node.js, eseguire il comando seguente nel [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Per visualizzare tutte le versioni di Node.js supportate, eseguire il comando seguente nel [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

::: zone-end

## <a name="set-nodejs-version"></a>Imposta versione Node.js

::: zone pivot="platform-windows"  

Per impostare l'app su una [versione di Node.js supportata](#show-nodejs-version), eseguire il comando seguente nel [cloud Shell](https://shell.azure.com) per impostare `WEBSITE_NODE_DEFAULT_VERSION` su una versione supportata:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

Questa impostazione specifica la versione Node.js da usare, sia in fase di esecuzione che durante il ripristino automatico dei pacchetti durante l'automazione della compilazione del servizio app.

> [!NOTE]
> È necessario impostare la versione del Node.js nel progetto `package.json` . Il motore di distribuzione viene eseguito in un processo separato che contiene tutte le versioni di Node.js supportate.

::: zone-end

::: zone pivot="platform-linux"

Per impostare l'app su una [versione di Node.js supportata](#show-nodejs-version), eseguire il comando seguente nel [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Questa impostazione specifica la versione Node.js da usare, sia in fase di esecuzione che durante il ripristino automatico dei pacchetti in Kudu.

> [!NOTE]
> È necessario impostare la versione del Node.js nel progetto `package.json` . Il motore di distribuzione viene eseguito in un contenitore separato che contiene tutte le versioni di Node.js supportate.

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>Personalizzare l'automazione della compilazione

Se si distribuisce l'app usando pacchetti GIT o ZIP con l'automazione della compilazione attivata, l'automazione della compilazione del servizio app esegue la sequenza seguente:

1. Esegue lo script personalizzato se specificato da `PRE_BUILD_SCRIPT_PATH`.
1. Eseguire `npm install` senza flag, che include NPM `preinstall` e `postinstall` script e installa anche `devDependencies` .
1. Eseguire `npm run build` se nel *package.js*viene specificato uno script di compilazione.
1. Eseguire `npm run build:azure` se una compilazione: lo script di Azure è specificato nella *package.js*.
1. Esegue lo script personalizzato se specificato da `POST_BUILD_SCRIPT_PATH`.

> [!NOTE]
> Come descritto in [NPM docs](https://docs.npmjs.com/misc/scripts), gli script denominati `prebuild` e `postbuild` vengono eseguiti rispettivamente prima e dopo `build` , se specificati. `preinstall` ed `postinstall` eseguono rispettivamente prima e dopo `install` .

`PRE_BUILD_COMMAND` e `POST_BUILD_COMMAND` sono variabili di ambiente vuote per impostazione predefinita. Per eseguire comandi pre-compilazione, definire `PRE_BUILD_COMMAND`. Per eseguire comandi post-compilazione, definire `POST_BUILD_COMMAND`.

Nell'esempio seguente vengono specificate le due variabili, separate da virgole, per una serie di comandi.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Per altre variabili di ambiente per personalizzare l'automazione della compilazione, vedere [Configurazione Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Per altre informazioni sull'esecuzione del servizio app e sulla compilazione di app Node.js in Linux, vedere [la documentazione di Oryx: come vengono rilevate e compilate le app Node.js](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md).

## <a name="configure-nodejs-server"></a>Configurare Node.js server

I contenitori di Node.js sono dotati di [PM2](https://pm2.keymetrics.io/), un gestore di processi di produzione. È possibile configurare l'app per iniziare a usare PM2 o con NPM oppure con un comando personalizzato.

- [Esegui comando personalizzato](#run-custom-command)
- [Esegui NPM Start](#run-npm-start)
- [Eseguire con PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Esegui comando personalizzato

Il servizio app può avviare l'app usando un comando personalizzato, ad esempio un eseguibile come *Run.sh*. Per eseguire ad esempio `npm run start:prod` , eseguire il comando seguente nel [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Esegui NPM Start

Per avviare l'app usando `npm start` , è sufficiente assicurarsi che uno `start` script si trovi nel *package.js* file. Ad esempio:

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

Per usare un *package.js* personalizzato nel progetto, eseguire il comando seguente nel [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Eseguire con PM2

Il contenitore avvia automaticamente l'app con PM2 quando si trova uno dei file di Node.js comuni nel progetto:

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Uno dei [file PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)seguenti: *process.jsin* e *ecosystem.config.js*

È anche possibile configurare un file di avvio personalizzato con le estensioni seguenti:

- Un file con *estensione js*
- Un [file PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) con estensione *JSON*, *.config.js*, *YAML*o *yml*

Per aggiungere un file di avvio personalizzato, eseguire il comando seguente nel [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Eseguire il debug in modalità remota

> [!NOTE]
> Il debug remoto è attualmente in fase di anteprima.

È possibile eseguire il debug dell'app Node.js in modalità remota [Visual Studio Code](https://code.visualstudio.com/) se viene configurata per l' [esecuzione con PM2](#run-with-pm2), tranne quando viene eseguita con * .config.js, *. yml o *. YAML*.

Nella maggior parte dei casi, non è necessaria alcuna configurazione aggiuntiva per l'app. Se l'app viene eseguita con un *process.jssu* file (impostazione predefinita o personalizzata), deve avere una `script` proprietà nella radice JSON. Ad esempio:

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

::: zone-end

## <a name="access-environment-variables"></a>Accedere alle variabili di ambiente

Nel servizio app è possibile [configurare le impostazioni dell'app](configure-common.md) al di fuori del codice dell'app. Quindi, è possibile accedervi usando il modello di Node.js standard. Ad esempio, per accedere a un'impostazione dell'app denominata `NODE_ENV`, usare il codice seguente:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Esegui grugnito/Bower/Gulp

Per impostazione predefinita, l'automazione della compilazione del servizio app viene eseguita `npm install --production` quando riconosce un'app Node.js viene distribuita tramite git o la distribuzione zip con l'automazione della compilazione abilitata. Se l'app richiede uno degli strumenti di automazione più diffusi, ad esempio grugnito, Bower o Gulp, è necessario fornire uno [script di distribuzione personalizzato](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) per eseguirlo.

Per abilitare il repository per l'esecuzione di questi strumenti, è necessario aggiungerli alle dipendenze in *package.js.* Ad esempio:

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

Aprire *deploy.sh* e trovare la `Deployment` sezione, che ha un aspetto simile al seguente:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Questa sezione termina con l'esecuzione di `npm install --production` . Aggiungere la sezione di codice è necessario eseguire lo strumento necessario *alla fine* della `Deployment` sezione:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Vedere un [esempio nell'esempio MEAN.js, in](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)cui lo script di distribuzione esegue anche un `npm install` comando personalizzato.

### <a name="bower"></a>Bower

Questo frammento di codice viene eseguito `bower install` .

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Questo frammento di codice viene eseguito `gulp imagemin` .

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Questo frammento di codice viene eseguito `grunt` .

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

::: zone pivot="platform-windows"  

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

## <a name="troubleshooting"></a>Risoluzione dei problemi

Quando un'app Node.js funzionante si comporta in modo diverso nel servizio app o presenta errori, provare a eseguire le operazioni seguenti:

- [Accedere al flusso di log](#access-diagnostic-logs).
- Testare l'app in locale nella modalità di produzione. Il servizio app esegue le app Node.js in modalità di produzione, quindi è necessario assicurarsi che il progetto funzioni come previsto in modalità di produzione in locale. Ad esempio:
    - A seconda del *package.jsin*, è possibile installare pacchetti diversi per la modalità di produzione ( `dependencies` rispetto a `devDependencies` ).
    - Alcuni framework Web possono distribuire i file statici in modo diverso in modalità di produzione.
    - Alcuni framework Web possono usare script di avvio personalizzati durante l'esecuzione in modalità di produzione.
- Eseguire l'app nel servizio app in modalità di sviluppo. Ad esempio, in [MEAN.js](https://meanjs.org/), è possibile impostare l'app sulla modalità di sviluppo in fase di esecuzione impostando [l' `NODE_ENV` impostazione dell'app](configure-common.md).

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: App Node.js con MongoDB](tutorial-nodejs-mongodb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [Domande frequenti sul Servizio app di Azure in Linux](faq-app-service-linux.md)

::: zone-end

