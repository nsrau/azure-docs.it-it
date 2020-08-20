---
title: Configurare le app PHP
description: Informazioni su come configurare un'app PHP nelle istanze di Windows native o in un contenitore PHP predefinito nel servizio app Azure. Questo articolo illustra le attività di configurazione più comuni.
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 440815d7d24cde9708c214bf407a2dd9206a1706
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642045"
---
# <a name="configure-a-php-app-for-azure-app-service"></a>Configurare un'app PHP per il servizio app Azure

Questa guida illustra come configurare le app Web PHP, i back-end per dispositivi mobili e le app per le API nel servizio app Azure.

Questa guida fornisce i concetti chiave e le istruzioni per gli sviluppatori PHP che distribuiscono le app nel servizio app. Se non si è mai usato Servizio app di Azure, seguire per prima cosa l'[Avvio rapido di PHP](quickstart-php.md) e l'[esercitazione di PHP con MySQL](tutorial-php-mysql-app.md).

## <a name="show-php-version"></a>Visualizzare la versione di PHP

::: zone pivot="platform-windows"  

Per visualizzare la versione corrente di PHP, eseguire il comando seguente in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

Per visualizzare tutte le versioni di PHP supportate, eseguire il comando seguente in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep php
```

::: zone-end

::: zone pivot="platform-linux"

Per visualizzare la versione corrente di PHP, eseguire il comando seguente in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Per visualizzare tutte le versioni di PHP supportate, eseguire il comando seguente in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

::: zone-end

## <a name="set-php-version"></a>Impostare la versione di PHP

::: zone pivot="platform-windows"  

Eseguire il comando seguente nella [cloud Shell](https://shell.azure.com) per impostare la versione di PHP su 7,4:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --php-version 7.4
```

::: zone-end

::: zone pivot="platform-linux"

Eseguire il comando seguente in [Cloud Shell](https://shell.azure.com) per impostare la versione di PHP su 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

::: zone-end

::: zone pivot="platform-windows"  

## <a name="run-composer"></a>Esegui Composer

Se si vuole che il servizio app esegua [Composer](https://getcomposer.org/) in fase di distribuzione, il modo più semplice consiste nell'includere il compositore nel repository.

Da una finestra del terminale locale passare alla directory radice del repository e seguire le istruzioni in [scaricare Composer](https://getcomposer.org/download/) per scaricare *Composer. phar* nella directory radice.

Eseguire i comandi seguenti (è necessario che [NPM](https://www.npmjs.com/get-npm) sia installato):

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

Aggiungere la sezione di codice è necessario eseguire lo strumento necessario *alla fine* della `Deployment` sezione:

```bash
# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
```

Eseguire il commit di tutte le modifiche e distribuire il codice usando git o la distribuzione zip con l'automazione della compilazione abilitata. Composer dovrebbe ora essere eseguito come parte dell'automazione della distribuzione.

## <a name="run-gruntbowergulp"></a>Esegui grugnito/Bower/Gulp

Se si vuole che il servizio app esegua strumenti di automazione diffusi in fase di distribuzione, ad esempio grugnito, Bower o Gulp, è necessario fornire uno [script di distribuzione personalizzato](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script). Il servizio app esegue questo script quando si esegue la distribuzione con git o con la [distribuzione zip](deploy-zip.md) con l'automazione della compilazione abilitata. 

Per abilitare il repository per l'esecuzione di questi strumenti, è necessario aggiungerli alle dipendenze in *package.js.* Ad esempio:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Da una finestra del terminale locale passare alla directory radice del repository ed eseguire i comandi seguenti (è necessario che [NPM](https://www.npmjs.com/get-npm) sia installato):

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

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>Personalizzare l'automazione della compilazione

Se si distribuisce l'app usando pacchetti GIT o ZIP con l'automazione della compilazione attivata, l'automazione della compilazione del servizio app esegue la sequenza seguente:

1. Esegue lo script personalizzato se specificato da `PRE_BUILD_SCRIPT_PATH`.
1. Eseguire `php composer.phar install`.
1. Esegue lo script personalizzato se specificato da `POST_BUILD_SCRIPT_PATH`.

`PRE_BUILD_COMMAND` e `POST_BUILD_COMMAND` sono variabili di ambiente vuote per impostazione predefinita. Per eseguire comandi pre-compilazione, definire `PRE_BUILD_COMMAND`. Per eseguire comandi post-compilazione, definire `POST_BUILD_COMMAND`.

Nell'esempio seguente vengono specificate le due variabili, separate da virgole, per una serie di comandi.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Per altre variabili di ambiente per personalizzare l'automazione della compilazione, vedere [Configurazione Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Per altre informazioni sull'esecuzione del servizio app e sulla compilazione di app PHP in Linux, vedere la [documentazione di Oryx relativa al rilevamento e alla compilazione di app PHP](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md).

## <a name="customize-start-up"></a>Personalizzare l’avvio

Per impostazione predefinita, il contenitore PHP predefinito esegue il server Apache. All'avvio, esegue `apache2ctl -D FOREGROUND"`. Se si preferisce, è possibile eseguire un comando diverso all'avvio, eseguendo il comando seguente in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

::: zone-end

## <a name="access-environment-variables"></a>Accedere alle variabili di ambiente

Nel servizio app è possibile [configurare le impostazioni dell'app](configure-common.md#configure-app-settings) al di fuori del codice dell'app. È quindi possibile accedervi usando il modello standard [getenv()](https://secure.php.net/manual/function.getenv.php). Ad esempio, per accedere a un'impostazione dell'app denominata `DB_HOST`, usare il codice seguente:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Modificare la radice del sito

::: zone pivot="platform-windows"  

Il framework Web scelto può usare una sottodirectory come radice del sito. Ad esempio, [Laravel](https://laravel.com/)usa la directory *public/* subdirectory come radice del sito.

Per personalizzare la radice del sito, impostare il percorso dell'applicazione virtuale per l'app usando il [`az resource update`](/cli/azure/resource#az-resource-update) comando. L'esempio seguente imposta la radice del sito sulla sottodirectory *public/* sottodirectory nel repository. 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Per impostazione predefinita, il servizio app di Azure fa in modo che il percorso virtuale dell'applicazione radice ( _/_ ) punti alla directory radice dei file dell'applicazione distribuiti (_sites\wwwroot_).

::: zone-end

::: zone pivot="platform-linux"

Il framework Web scelto può usare una sottodirectory come radice del sito. [Laravel](https://laravel.com/), ad esempio, usa la sottodirectory `public/` come radice del sito.

L'immagine PHP predefinita per il servizio app usa Apache e non consente di personalizzare la radice del sito per l'app. Per ovviare a questa limitazione, aggiungere alla radice del repository un file con estensione *htaccess* con il contenuto seguente:

```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} ^/$
    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

Se si preferisce non usare la riscrittura con *.htaccess*, si può distribuire l'applicazione Laravel con un'[immagine Docker personalizzata](quickstart-custom-container.md).

::: zone-end

## <a name="detect-https-session"></a>Rilevare una sessione HTTPS

Nel servizio app la [terminazione SSL](https://wikipedia.org/wiki/TLS_termination_proxy) si verifica nei servizi di bilanciamento del carico di rete, pertanto tutte le richieste HTTPS raggiungano l'app come richieste HTTP non crittografate. Se la logica dell'app deve controllare se le richieste degli utenti sono crittografate o meno, esaminare l'intestazione `X-Forwarded-Proto`.

```php
if (isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https') {
// Do something when HTTPS is used
}
```

I framework Web più diffusi consentono di accedere alle informazioni `X-Forwarded-*` nel modello di app standard. In [CodeIgniter](https://codeigniter.com/)[is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) controlla il valore di `X_FORWARDED_PROTO` per impostazione predefinita.

## <a name="customize-phpini-settings"></a>Personalizzare le impostazioni di php.ini

Se è necessario apportare modifiche all'installazione di PHP, è possibile modificare qualsiasi [direttiva di php.ini](https://www.php.net/manual/ini.list.php) seguendo questa procedura.

> [!NOTE]
> Il modo migliore per visualizzare la versione di PHP e la configurazione corrente di *php.ini* è chiamare [phpinfo()](https://php.net/manual/function.phpinfo.php) nell'app.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Personalizzare le direttive non PHP_INI_SYSTEM

::: zone pivot="platform-windows"  

Per personalizzare le direttive PHP_INI_USER, PHP_INI_PERDIR e PHP_INI_ALL (vedere [ direttivephp.ini](https://www.php.net/manual/ini.list.php)), aggiungere un `.user.ini` file alla directory radice dell'app.

Aggiungere le impostazioni di configurazione al file  `.user.ini` usando la stessa sintassi che si userebbe in un file `php.ini`. Ad esempio, se si desidera attivare l'impostazione `display_errors` e impostare `upload_max_filesize` su 10 M, il file `.user.ini` conterrà il testo seguente:

```
 ; Example Settings
 display_errors=On
 upload_max_filesize=10M

 ; Write errors to d:\home\LogFiles\php_errors.log
 ; log_errors=On
```

Ridistribuire l'app con le modifiche e riavviarla.

In alternativa all'uso di un `.user.ini` file, è possibile usare [ini_set ()](https://www.php.net/manual/function.ini-set.php) nell'app per personalizzare le direttive non PHP_INI_SYSTEM.

::: zone-end

::: zone pivot="platform-linux"

Per personalizzare le direttive PHP_INI_USER, PHP_INI_PERDIR e PHP_INI_ALL (vedere le [direttive di php.ini](https://www.php.net/manual/ini.list.php)), aggiungere alla directory radice dell'app un file con estensione *htaccess*.

Nel file con estensione *htaccess* aggiungere le direttive usando la sintassi `php_value <directive-name> <value>`. Ad esempio:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Ridistribuire l'app con le modifiche e riavviarla. Se la si distribuisce con Kudu (ad esempio, usando [Git](deploy-local-git.md)), viene automaticamente riavviata dopo la distribuzione.

In alternativa al file con estensione *htaccess*, è possibile usare [ini_set()](https://www.php.net/manual/function.ini-set.php) nell'app per personalizzare queste direttive non PHP_INI_SYSTEM.

::: zone-end

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Personalizzare le direttive PHP_INI_SYSTEM

::: zone pivot="platform-windows"  

Per personalizzare le direttive PHP_INI_SYSTEM (vedere le [direttive di php.ini](https://www.php.net/manual/ini.list.php)), non è possibile usare l'approccio basato sul file con estensione *htaccess*. Il servizio app fornisce un meccanismo separato che usa l'impostazione dell'app `PHP_INI_SCAN_DIR`.

Eseguire prima di tutto il comando seguente in [Cloud Shell](https://shell.azure.com) per aggiungere un'impostazione dell'app denominata `PHP_INI_SCAN_DIR`:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="d:\home\site\ini"
```

Passare alla console Kudu ( `https://<app-name>.scm.azurewebsites.net/DebugConsole` ) e passare a `d:\home\site` .

In `d:\home\site` creare una directory denominata `ini`, quindi creare un file *INI* nella directory `d:\home\site\ini` (ad esempio, *settings.ini*) con le direttive che si vogliono personalizzare. Usare la stessa sintassi che si userebbe in un file *php.ini*. 

Ad esempio, per modificare il valore di [expose_php](https://php.net/manual/ini.core.php#ini.expose-php), eseguire i comandi seguenti:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Per rendere effettive le modifiche apportate, riavviare l'app.

::: zone-end

::: zone pivot="platform-linux"

Per personalizzare le direttive PHP_INI_SYSTEM (vedere le [direttive di php.ini](https://www.php.net/manual/ini.list.php)), non è possibile usare l'approccio basato sul file con estensione *htaccess*. Il servizio app fornisce un meccanismo separato che usa l'impostazione dell'app `PHP_INI_SCAN_DIR`.

Eseguire prima di tutto il comando seguente in [Cloud Shell](https://shell.azure.com) per aggiungere un'impostazione dell'app denominata `PHP_INI_SCAN_DIR`:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` è la directory predefinita in cui si trova *php.ini*. `/home/site/ini` è la directory personalizzata in cui si aggiungerà un file *INI* personalizzato. Separare i valori con `:`.

Passare alla sessione SSH Web con il contenitore Linux (`https://<app-name>.scm.azurewebsites.net/webssh/host`).

In `/home/site` creare una directory denominata `ini`, quindi creare un file *INI* nella directory `/home/site/ini` (ad esempio, *settings.ini*) con le direttive che si vogliono personalizzare. Usare la stessa sintassi che si userebbe in un file *php.ini*. 

> [!TIP]
> Nei contenitori Linux predefiniti nel servizio app, */home* viene usata come risorsa di archiviazione condivisa persistente. 
>

Ad esempio, per modificare il valore di [expose_php](https://php.net/manual/ini.core.php#ini.expose-php), eseguire i comandi seguenti:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Per rendere effettive le modifiche apportate, riavviare l'app.

::: zone-end

## <a name="enable-php-extensions"></a>Abilitare le estensioni di PHP

::: zone pivot="platform-windows"  

Le installazioni di PHP predefinite contengono le estensioni usate più di frequente. È possibile abilitare altre estensioni nello stesso modo in cui si [personalizzano le direttive di php.ini](#customize-php_ini_system-directives).

> [!NOTE]
> Il modo migliore per visualizzare la versione di PHP e la configurazione corrente di *php.ini* è chiamare [phpinfo()](https://php.net/manual/function.phpinfo.php) nell'app.
>

Per abilitare le estensioni aggiuntive, eseguire la procedura seguente:

Aggiungere una directory `bin` alla directory radice dell'app e inserirvi i file con estensione `.so` (ad esempio, *mongodb.so*). Verificare che le estensioni siano compatibili con la versione di PHP in Azure e con VC9, ma non con thread-safe (nts).

Distribuire le modifiche.

Seguire la procedura descritta in [Personalizzare le direttive PHP_INI_SYSTEM](#customize-php_ini_system-directives), aggiungere le estensioni nel file *INI* personalizzato con le direttive [extension](https://www.php.net/manual/ini.core.php#ini.extension) o [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension).

```
extension=d:\home\site\wwwroot\bin\mongodb.so
zend_extension=d:\home\site\wwwroot\bin\xdebug.so
```

Per rendere effettive le modifiche apportate, riavviare l'app.

::: zone-end

::: zone pivot="platform-linux"

Le installazioni di PHP predefinite contengono le estensioni usate più di frequente. È possibile abilitare altre estensioni nello stesso modo in cui si [personalizzano le direttive di php.ini](#customize-php_ini_system-directives).

> [!NOTE]
> Il modo migliore per visualizzare la versione di PHP e la configurazione corrente di *php.ini* è chiamare [phpinfo()](https://php.net/manual/function.phpinfo.php) nell'app.
>

Per abilitare le estensioni aggiuntive, eseguire la procedura seguente:

Aggiungere una directory `bin` alla directory radice dell'app e inserirvi i file con estensione `.so` (ad esempio, *mongodb.so*). Verificare che le estensioni siano compatibili con la versione di PHP in Azure e con VC9, ma non con thread-safe (nts).

Distribuire le modifiche.

Seguire la procedura descritta in [Personalizzare le direttive PHP_INI_SYSTEM](#customize-php_ini_system-directives), aggiungere le estensioni nel file *INI* personalizzato con le direttive [extension](https://www.php.net/manual/ini.core.php#ini.extension) o [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension).

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Per rendere effettive le modifiche apportate, riavviare l'app.

::: zone-end

## <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

::: zone pivot="platform-windows"  

Usare l'utilità standard [error_log ()](https://php.net/manual/function.error-log.php) per visualizzare i log di diagnostica nel servizio app Azure.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

## <a name="troubleshooting"></a>Risoluzione dei problemi

Quando un'app PHP funzionante si comporta in modo diverso nel servizio app o presenta errori, provare a eseguire le operazioni seguenti:

- [Accedere al flusso di log](#access-diagnostic-logs).
- Testare l'app in locale nella modalità di produzione. Il servizio app esegue l'app in modalità di produzione, quindi è necessario assicurarsi che il progetto funzioni come previsto in modalità di produzione localmente. Ad esempio:
    - A seconda del file *composer.json*, è possibile che siano installati pacchetti diversi per la modalità di produzione (`require` o `require-dev`).
    - Alcuni framework Web possono distribuire i file statici in modo diverso in modalità di produzione.
    - Alcuni framework Web possono usare script di avvio personalizzati durante l'esecuzione in modalità di produzione.
- Eseguire l'app nel servizio app in modalità di debug. In [Laravel](https://meanjs.org/) è ad esempio possibile configurare l'app per l'output di messaggi di debug in fase di produzione [impostando l'impostazione dell'app `APP_DEBUG` su `true`](configure-common.md#configure-app-settings).

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: App PHP con MySQL](tutorial-php-mysql-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [Domande frequenti sul Servizio app di Azure in Linux](faq-app-service-linux.md)

::: zone-end

