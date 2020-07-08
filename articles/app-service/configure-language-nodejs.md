---
title: Configurare app Windows Node.js
description: Informazioni su come configurare un'app Node.js nelle istanze di Windows native del servizio app. Questo articolo illustra le attività di configurazione più comuni.
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 9f4ccdd04b8d57784f452dc28fa4507fb7ea94c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907924"
---
# <a name="configure-a-windows-nodejs-app-for-azure-app-service"></a>Configurare un'app Node.js Windows per il servizio app Azure

Node.js le app devono essere distribuite con tutte le dipendenze NPM richieste. Il motore di distribuzione del servizio app viene eseguito automaticamente `npm install --production` quando si distribuisce un [repository git](deploy-local-git.md)o un [pacchetto zip](deploy-zip.md) con l'automazione della compilazione abilitata. Se si distribuiscono i file tramite [FTP/S](deploy-ftp.md), tuttavia, è necessario caricare manualmente i pacchetti necessari. Per informazioni sulle app Linux, vedere [configurare un'app php Linux per il servizio app Azure](containers/configure-language-nodejs.md).

Questa guida fornisce i concetti chiave e le istruzioni per Node.js gli sviluppatori che eseguono la distribuzione nel servizio app. Se non si è mai usato app Azure servizio, seguire prima l'esercitazione [Node.js avvio rapido](app-service-web-get-started-nodejs.md) e [Node.js con MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md) .

## <a name="show-nodejs-version"></a>Mostra versione Node.js

Per visualizzare la versione corrente di Node.js, eseguire il comando seguente nel [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

Per visualizzare tutte le versioni di Node.js supportate, eseguire il comando seguente nel [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep node
```

## <a name="set-nodejs-version"></a>Imposta versione Node.js

Per impostare l'app su una [versione di Node.js supportata](#show-nodejs-version), eseguire il comando seguente nel [cloud Shell](https://shell.azure.com) per impostare `WEBSITE_NODE_DEFAULT_VERSION` su una versione supportata:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

Questa impostazione specifica la versione Node.js da usare, sia in fase di esecuzione che durante il ripristino automatico dei pacchetti durante l'automazione della compilazione del servizio app.

> [!NOTE]
> È necessario impostare la versione del Node.js nel progetto `package.json` . Il motore di distribuzione viene eseguito in un processo separato che contiene tutte le versioni di Node.js supportate.

## <a name="access-environment-variables"></a>Accedere alle variabili di ambiente

Nel servizio app è possibile [configurare le impostazioni dell'app](configure-common.md) al di fuori del codice dell'app. Quindi, è possibile accedervi usando il modello di Node.js standard. Ad esempio, per accedere a un'impostazione dell'app denominata `NODE_ENV`, usare il codice seguente:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Esegui grugnito/Bower/Gulp

Per impostazione predefinita, l'automazione della compilazione del servizio app viene eseguita `npm install --production` quando riconosce un'app Node.js viene distribuita tramite git (o la distribuzione zip con l'automazione della compilazione abilitata). Se l'app richiede uno degli strumenti di automazione più diffusi, ad esempio grugnito, Bower o Gulp, è necessario fornire uno [script di distribuzione personalizzato](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) per eseguirlo.

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

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

Quando un'app Node.js funzionante si comporta in modo diverso nel servizio app o presenta errori, provare a eseguire le operazioni seguenti:

- [Accedere al flusso di log](#access-diagnostic-logs).
- Testare l'app in locale nella modalità di produzione. Il servizio app esegue le app Node.js in modalità di produzione, quindi è necessario assicurarsi che il progetto funzioni come previsto in modalità di produzione in locale. Ad esempio:
    - A seconda del *package.jsin*, è possibile installare pacchetti diversi per la modalità di produzione ( `dependencies` rispetto a `devDependencies` ).
    - Alcuni framework Web possono distribuire i file statici in modo diverso in modalità di produzione.
    - Alcuni framework Web possono usare script di avvio personalizzati durante l'esecuzione in modalità di produzione.
- Eseguire l'app nel servizio app in modalità di sviluppo. Ad esempio, in [MEAN.js](https://meanjs.org/), è possibile impostare l'app sulla modalità di sviluppo in fase di esecuzione impostando [l' `NODE_ENV` impostazione dell'app](configure-common.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: App Node.js con MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)

