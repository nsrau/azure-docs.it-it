---
title: Configurare le app PHP
description: Informazioni su come configurare un contenitore PHP predefinito per l'app. Questo articolo illustra le attività di configurazione più comuni.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: a3de4769193d95a3ef483924c4d65c4fa1cc9f8d
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671845"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Configurare un'app PHP Linux per il servizio app Azure

Questa guida illustra come configurare il runtime PHP predefinito per le app Web, i back-end per dispositivi mobili e le app per le API nel servizio app Azure.

Questa guida fornisce i concetti chiave e le istruzioni per gli sviluppatori PHP che usano un contenitore Linux incorporato nel servizio app. Se non si è mai usato app Azure servizio, seguire prima l'esercitazione [introduttiva php](quickstart-php.md) e [php con MySQL](tutorial-php-mysql-app.md) .

## <a name="show-php-version"></a>Mostra versione PHP

Per visualizzare la versione corrente di PHP, eseguire il comando seguente nella [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Per visualizzare tutte le versioni di PHP supportate, eseguire il comando seguente nella [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>Impostare la versione di PHP

Eseguire il comando seguente nella [cloud Shell](https://shell.azure.com) per impostare la versione di PHP su 7,2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="run-composer"></a>Esegui Composer

Per impostazione predefinita, Kudu non esegue [Composer](https://getcomposer.org/). Per abilitare l'automazione Composer durante la distribuzione di Kudu, è necessario fornire uno [script di distribuzione personalizzato](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

Da una finestra del terminale locale passare alla directory radice del repository. Seguire i [passaggi di installazione da riga di comando](https://getcomposer.org/download/) per scaricare *Composer. phar*.

Eseguire i comandi seguenti:

```bash
npm install kuduscript -g
kuduscript --php --scriptType bash --suppressPrompt
```

La radice del repository dispone ora di due nuovi file oltre a *Composer. phar*: *. Deployment* e *deploy.sh*. Questi file funzionano sia per le versioni Windows e Linux del servizio app.

Aprire *deploy.sh* e trovare la sezione `Deployment`. Sostituire l'intera sezione con il codice seguente:

```bash
##################################################################################################################################
# Deployment
# ----------

echo PHP deployment

# 1. KuduSync
if [[ "$IN_PLACE_DEPLOYMENT" -ne "1" ]]; then
  "$KUDU_SYNC_CMD" -v 50 -f "$DEPLOYMENT_SOURCE" -t "$DEPLOYMENT_TARGET" -n "$NEXT_MANIFEST_PATH" -p "$PREVIOUS_MANIFEST_PATH" -i ".git;.hg;.deployment;deploy.sh"
  exitWithMessageOnError "Kudu Sync failed"
fi

# 3. Initialize Composer Config
initializeDeploymentConfig

# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
##################################################################################################################################
```

Eseguire il commit di tutte le modifiche e distribuire di nuovo il codice. Composer dovrebbe ora essere eseguito come parte dell'automazione della distribuzione.

## <a name="customize-start-up"></a>Personalizzare l’avvio

Per impostazione predefinita, il contenitore PHP predefinito esegue il server Apache. All'avvio, viene eseguito `apache2ctl -D FOREGROUND"`. Se si desidera, è possibile eseguire un comando diverso all'avvio, eseguendo il comando seguente nella [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Accedere alle variabili di ambiente

Nel servizio app è possibile [configurare le impostazioni dell'app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) al di fuori del codice dell'app. Quindi, è possibile accedervi usando il modello [getenv ()](https://secure.php.net/manual/function.getenv.php) standard. Ad esempio, per accedere a un'impostazione dell'app denominata `DB_HOST`, usare il codice seguente:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Modifica radice sito

Il framework Web di propria scelta può utilizzare una sottodirectory come radice del sito. Ad esempio, [Laravel](https://laravel.com/)usa la sottodirectory `public/` come radice del sito.

L'immagine PHP predefinita per il servizio app usa Apache e non consente di personalizzare la radice del sito per l'app. Per ovviare a questa limitazione, aggiungere un file con *estensione htaccess* alla radice del repository con il contenuto seguente:

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^.*$ /public/$1 [NC,L,QSA]
</IfModule>
```

Se si preferisce non usare la riscrittura con *.htaccess*, si può distribuire l'applicazione Laravel con un'[immagine Docker personalizzata](quickstart-docker-go.md).

## <a name="detect-https-session"></a>Rilevare una sessione HTTPS

Nel servizio app la [terminazione SSL](https://wikipedia.org/wiki/TLS_termination_proxy) si verifica nei servizi di bilanciamento del carico di rete, pertanto tutte le richieste HTTPS raggiungano l'app come richieste HTTP non crittografate. Se la logica dell'app deve controllare se le richieste degli utenti sono crittografate o meno, esaminare l'intestazione `X-Forwarded-Proto`.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

I framework Web più diffusi consentono di accedere alle informazioni `X-Forwarded-*` nel modello di app standard. In [CodeIgniter](https://codeigniter.com/) [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) controlla il valore di `X_FORWARDED_PROTO` per impostazione predefinita.

## <a name="customize-phpini-settings"></a>Personalizzare le impostazioni di php. ini

Se è necessario apportare modifiche all'installazione di PHP, è possibile modificare una qualsiasi delle [direttive php. ini](https://www.php.net/manual/ini.list.php) attenendosi alla seguente procedura.

> [!NOTE]
> Il modo migliore per visualizzare la versione di PHP e la configurazione corrente di *php. ini* consiste nel chiamare [phpinfo ()](https://php.net/manual/function.phpinfo.php) nell'app.
>

### <a name="Customize-non-PHP_INI_SYSTEM directives"></a>Personalizza-direttive non PHP_INI_SYSTEM

Per personalizzare le direttive PHP_INI_USER, PHP_INI_PERDIR e PHP_INI_ALL (vedere [direttive php. ini](https://www.php.net/manual/ini.list.php)), aggiungere un file con *estensione htaccess* alla directory radice dell'app.

Nel file con *estensione htaccess* aggiungere le direttive usando la sintassi del `php_value <directive-name> <value>`. ad esempio:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Ridistribuire l'app con le modifiche e riavviarla. Se la si distribuisce con Kudu, ad esempio usando [git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), viene riavviata automaticamente dopo la distribuzione.

In alternativa all'uso di *. htaccess*, è possibile usare [ini_set ()](https://www.php.net/manual/function.ini-set.php) nell'app per personalizzare queste direttive non PHP_INI_SYSTEM.

### <a name="customize-php_ini_system-directives"></a>Personalizzare le direttive PHP_INI_SYSTEM

Per personalizzare PHP_INI_SYSTEM direttive (vedere le [direttive php. ini](https://www.php.net/manual/ini.list.php)), non è possibile usare l'approccio *. htaccess* . Il servizio app fornisce un meccanismo separato con l'impostazione dell'app `PHP_INI_SCAN_DIR`.

Per prima cosa, eseguire il comando seguente nella [cloud Shell](https://shell.azure.com) per aggiungere un'impostazione dell'app denominata `PHP_INI_SCAN_DIR`:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` è la directory predefinita in cui esiste *php. ini* . `/home/site/ini` è la directory personalizzata in cui verrà aggiunto un file con *estensione ini* personalizzato. I valori vengono separati con un `:`.

Passare alla sessione SSH Web con il contenitore Linux (`https://<app-name>.scm.azurewebsites.net/webssh/host`).

Creare una directory in `/home/site` denominata `ini`, quindi creare un file *ini* nella directory `/home/site/ini` (ad esempio *Settings. ini)* con le direttive che si desidera personalizzare. Usare la stessa sintassi usata in un file *php. ini* . 

> [!TIP]
> Nei contenitori Linux predefiniti nel servizio app, */Home* viene usato come risorsa di archiviazione condivisa salvata in modo permanente. 
>

Per modificare, ad esempio, il valore di [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) eseguire i comandi seguenti:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Per rendere effettive le modifiche, riavviare l'app.

## <a name="enable-php-extensions"></a>Abilita estensioni PHP

Le installazioni PHP predefinite contengono le estensioni usate più di frequente. È possibile abilitare estensioni aggiuntive nello stesso modo in cui si [personalizzano le direttive php. ini](#customize-php_ini_system-directives).

> [!NOTE]
> Il modo migliore per visualizzare la versione di PHP e la configurazione corrente di *php. ini* consiste nel chiamare [phpinfo ()](https://php.net/manual/function.phpinfo.php) nell'app.
>

Per abilitare le estensioni aggiuntive, eseguire la procedura seguente:

Aggiungere una directory `bin` alla directory radice dell'app e inserirvi i file di estensione `.so` (ad esempio, *MongoDB.so*). Verificare che le estensioni siano compatibili con la versione di PHP in Azure e che siano compatibili con VC9 e non thread-safe (NTS).

Distribuire le modifiche.

Seguire i passaggi descritti in [personalizzare le direttive PHP_INI_SYSTEM](#customize-php_ini_system-directives)e aggiungere le estensioni al file *ini* personalizzato con le direttive [Extension](https://www.php.net/manual/ini.core.php#ini.extension) o [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) .

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Per rendere effettive le modifiche, riavviare l'app.

## <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Aprire una sessione SSH nel browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>risoluzione dei problemi

Quando un'app PHP funzionante si comporta in modo diverso nel servizio app o presenta degli errori, provare a eseguire le operazioni seguenti:

- [Accedere al flusso di log](#access-diagnostic-logs).
- Testare l'app localmente in modalità di produzione. Il servizio app esegue le app node. js in modalità di produzione, quindi è necessario assicurarsi che il progetto funzioni come previsto in modalità di produzione localmente. ad esempio:
    - A seconda del file *Composer. JSON*, è possibile installare pacchetti diversi per la modalità di produzione (`require` e `require-dev`).
    - Alcuni framework Web possono distribuire i file statici in modo diverso in modalità di produzione.
    - Alcuni framework Web possono usare script di avvio personalizzati durante l'esecuzione in modalità di produzione.
- Eseguire l'app nel servizio app in modalità di debug. Ad esempio, in [Laravel](https://meanjs.org/)è possibile configurare l'app per l'output dei messaggi di debug in produzione impostando [l'impostazione dell'app `APP_DEBUG` su `true`](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

### <a name="robots933456"></a>robots933456

È possibile che venga visualizzato il messaggio seguente nei log del contenitore:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Questo messaggio può essere ignorato in modo sicuro. `/robots933456.txt` è un percorso URL fittizio utilizzato dal servizio app per verificare se il contenitore è in grado di soddisfare le richieste. Una risposta 404 indica semplicemente che il percorso non esiste, ma consente al servizio app di capire che il contenitore è integro e pronto per rispondere alle richieste.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: app PHP con MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Domande frequenti sul Servizio app di Azure in Linux](app-service-linux-faq.md)
