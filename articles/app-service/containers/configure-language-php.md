---
title: Configurare le app PHP - servizio App di Azure | Microsoft Docs
description: Informazioni su come configurare le app PHP a lavorare in servizio App di Azure
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
ms.openlocfilehash: 11d0648ee5090f02cb96c2d42a8d90cc3ea0ed28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60853304"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Configurare un'app PHP di Linux per servizio App di Azure

Questa guida illustra come configurare il runtime PHP incorporato per le app web, back-end per dispositivi mobili e App per le API in servizio App di Azure.

Questa guida fornisce i concetti chiave e le istruzioni per gli sviluppatori PHP che usano un contenitore Linux incorporato nel servizio App. Se non si è mai usato il servizio App di Azure, seguire le [Guida introduttiva PHP](quickstart-php.md) e [PHP con MySQL esercitazione](tutorial-php-mysql-app.md) prima.

## <a name="show-php-version"></a>Consente di visualizzare la versione PHP

Per visualizzare la versione corrente di PHP, eseguire il comando seguente [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Per visualizzare tutte le versioni PHP, eseguire il comando seguente [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>Impostare la versione di PHP

Eseguire il comando seguente [Cloud Shell](https://shell.azure.com) per impostare la versione PHP 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="run-composer"></a>Eseguire Composer

Per impostazione predefinita, non viene eseguito Kudu [Composer](https://getcomposer.org/). Per abilitare l'automazione Composer durante la distribuzione di Kudu, è necessario fornire un [script di distribuzione personalizzato](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

Da una finestra del terminale locale passare directory radice del repository. Seguire le [passaggi di installazione da riga di comando](https://getcomposer.org/download/) per scaricare *Phar*.

Eseguire i comandi seguenti:

```bash
npm install kuduscript -g
kuduscript --php --scriptType bash --suppressPrompt
```

Radice del repository ha ora due nuovi file oltre a *Phar*: *con estensione Deployment* e *deploy.sh*. Questi file funzionano sia per Windows e Linux versioni del servizio App.

Aprire *deploy.sh* e individuare il `Deployment` sezione. Sostituire l'intera sezione con il codice seguente:

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

Eseguire il commit di tutte le modifiche e distribuire di nuovo il codice. Composer dovrebbe ora essere in esecuzione come parte di automazione della distribuzione.

## <a name="customize-start-up"></a>Personalizzare l’avvio

Per impostazione predefinita, il contenitore PHP predefinito esegue il server Apache. All'avvio, viene eseguito `apache2ctl -D FOREGROUND"`. Se si desidera, è possibile eseguire un comando diverso all'avvio, eseguendo il comando seguente nel [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Accedere alle variabili di ambiente

Nel servizio app è possibile [configurare le impostazioni dell'app](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) al di fuori del codice dell'app. Quindi è possibile accedervi usando lo standard [getenv ()](https://secure.php.net/manual/function.getenv.php) pattern. Ad esempio, per accedere a un'impostazione dell'app denominata `DB_HOST`, usare il codice seguente:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Directory radice del sito di modifica

Il framework web di propria scelta potrebbe usare una sottodirectory come radice del sito. Ad esempio, [Laravel](https://laravel.com/), viene utilizzato il `public/` sottodirectory come radice del sito.

L'immagine PHP predefinita per il servizio App Usa Apache e non è possibile personalizzare la radice del sito per l'app. Per aggirare questa limitazione, aggiungere un' *. htaccess* file alla radice del repository con il contenuto seguente:

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

## <a name="customize-phpini-settings"></a>Personalizzare le impostazioni di PHP. ini

Se è necessario apportare modifiche all'installazione di PHP, è possibile modificare qualsiasi il [PHP. ini](http://www.php.net/manual/ini.list.php) seguendo questa procedura.

> [!NOTE]
> Il modo migliore per visualizzare la versione di PHP e l'oggetto corrente *PHP. ini* configurazione consiste nel chiamare [phpinfo](https://php.net/manual/function.phpinfo.php) nell'app.
>

### <a name="customize-non-phpinisystem-directives"></a>Personalizzare le direttive non PHP_INI_SYSTEM

Per personalizzare le direttive PHP_INI_USER PHP_INI_PERDIR e PHP_INI_ALL (vedere [PHP. ini](http://www.php.net/manual/ini.list.php)), aggiungere un' *. htaccess* file nella directory radice dell'app.

Nel *. htaccess* del file, aggiungere le direttive utilizzando il `php_value <directive-name> <value>` sintassi. Ad esempio: 

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Ridistribuire l'app con le modifiche e riavviarlo. Se lo si distribuisce con Kudu (ad esempio, usando [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)), viene automaticamente riavviato dopo la distribuzione.

Come alternativa all'uso *. htaccess*, è possibile usare [ini_set ()](http://www.php.net/manual/function.ini-set.php) nell'app per personalizzare queste direttive non PHP_INI_SYSTEM.

### <a name="customize-phpinisystem-directives"></a>Personalizzare le direttive PHP_INI_SYSTEM

Per personalizzare le direttive PHP_INI_SYSTEM (vedere [PHP. ini](http://www.php.net/manual/ini.list.php)), non è possibile utilizzare il *. htaccess* approccio. Il servizio App fornisce un meccanismo separato utilizzando il `PHP_INI_SCAN_DIR` impostazione dell'app.

In primo luogo, eseguire il comando seguente [Cloud Shell](https://shell.azure.com) per aggiungere un'app denominata `PHP_INI_SCAN_DIR`:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` la directory predefinita in cui *PHP. ini* esiste. `/home/site/ini` è la directory personalizzata in cui si aggiungerà una classe personalizzata *ini* file. È necessario separare i valori con un `:`.

Passare alla sessione SSH con il contenitore Linux web (`https://cephalin-container.scm.azurewebsites.net/webssh/host`).

Creare una directory nel `/home/site` chiamato `ini`, quindi creare un *ini* del file nel `/home/site/ini` directory (ad esempio, *Settings)* con le direttive che si desidera personalizzare. Usare la stessa sintassi si userebbe in un *PHP. ini* file. 

> [!TIP]
> Nei contenitori Linux incorporati nel servizio App */Home* viene usato come archiviazione condivisa persistente. 
>

Ad esempio, per modificare il valore della [expose_php](http://php.net/manual/ini.core.php#ini.expose-php) eseguire i comandi seguenti:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Per rendere effettive le modifiche, riavviare l'app.

## <a name="enable-php-extensions"></a>Abilitare le estensioni PHP

Le installazioni di PHP predefinite contengono le estensioni usate più comunemente. È possibile abilitare altre estensioni nello stesso modo in cui si [personalizzare PHP. ini](#customize-php_ini_system-directives).

> [!NOTE]
> Il modo migliore per visualizzare la versione di PHP e l'oggetto corrente *PHP. ini* configurazione consiste nel chiamare [phpinfo](https://php.net/manual/function.phpinfo.php) nell'app.
>

Per abilitare le estensioni aggiuntive, eseguire la procedura seguente:

Aggiungere un `bin` directory nella directory radice dell'app e put il `.so` i file di estensione in esso (ad esempio, *mongodb.so*). Assicurarsi che le estensioni siano compatibili con la versione di PHP in Azure e con vc9 e compatibile con non thread-safe (nts).

Distribuire le modifiche.

Seguire i passaggi descritti in [direttive personalizzare PHP_INI_SYSTEM](#customize-php_ini_system-directives), aggiungere le estensioni personalizzate *ini* del file con il [estensione](https://www.php.net/manual/ini.core.php#ini.extension) o [zend_extension ](https://www.php.net/manual/ini.core.php#ini.zend-extension) direttive.

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

Quando un'app PHP in funzione si comporta in modo diverso nel servizio App o presenta errori, procedere come segue:

- [Accedere al flusso di log](#access-diagnostic-logs).
- Testare l'app in locale in modalità di produzione. Servizio App le app Node. js viene eseguito in modalità di produzione, pertanto è necessario assicurarsi che il progetto funziona come previsto in modalità di produzione in locale. Ad esempio: 
    - A seconda del *Composer. JSON*, pacchetti diversi possono essere installati per la modalità di produzione (`require` confronto `require-dev`).
    - Alcuni Framework web possono distribuire file statici in modo diverso in modalità di produzione.
    - Alcuni Framework web può usare gli script di avvio personalizzate durante l'esecuzione in modalità di produzione.
- Eseguire l'app nel servizio App in modalità di debug. Ad esempio, nella [Laravel](http://meanjs.org/), è possibile configurare l'app per ottenere l'output dei messaggi di debug in produzione tramite [impostazione il `APP_DEBUG` impostazione dell'app per `true` ](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="robots933456"></a>robots933456

Si può vedere il messaggio seguente nei log del contenitore:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

È possibile ignorare questo messaggio. `/robots933456.txt` è un percorso URL fittizio che il servizio App Usa per verificare se il contenitore è in grado di servire le richieste. Una risposta 404 indica semplicemente che il percorso non esiste, ma consente al servizio App di sapere che il contenitore sia integro e pronto per rispondere alle richieste.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: App PHP con MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Domande frequenti sul Servizio app di Azure in Linux](app-service-linux-faq.md)