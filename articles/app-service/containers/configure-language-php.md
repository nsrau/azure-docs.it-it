---
title: Configurare le app PHP
description: Scopri come configurare un contenitore PHP predefinito per la tua app. Questo articolo illustra le attività di configurazione più comuni.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9e87466f810dc4ebf767c36ad74c358cbf6069e5
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758884"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Configurare un'app PHP Linux per il servizio app di AzureConfigure a Linux PHP app for Azure App Service

Questa guida illustra come configurare il runtime PHP incorporato per le app Web, i back-end per dispositivi mobili e le app API nel servizio app di Azure.This guide shows you how to configure the built-in PHP runtime for web apps, mobile back ends, and API apps in Azure App Service.

Questa guida fornisce concetti e istruzioni chiave per gli sviluppatori PHP che usano un contenitore Linux incorporato nel servizio app. Se non hai mai usato il servizio app di Azure, segui prima [l'esercitazione PHP](quickstart-php.md) e [PHP con MySQL.](tutorial-php-mysql-app.md)

## <a name="show-php-version"></a>Mostra versione PHP

Per visualizzare la versione CORRENTE di PHP, eseguire il seguente comando in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Per visualizzare tutte le versioni PHP supportate, eseguire il comando seguente in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>Impostare la versione di PHP

Eseguire il comando seguente in [Cloud Shell](https://shell.azure.com) per impostare la versione PHP su 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>Personalizzare l'automazione della compilazione

Se distribuisci l'app usando pacchetti Git o zip con l'automazione della compilazione attivata, l'automazione della compilazione del servizio app passa la sequenza seguente:

1. Eseguire uno script `PRE_BUILD_SCRIPT_PATH`personalizzato se specificato da .
1. Eseguire `php composer.phar install`.
1. Eseguire uno script `POST_BUILD_SCRIPT_PATH`personalizzato se specificato da .

`PRE_BUILD_COMMAND`e `POST_BUILD_COMMAND` sono variabili di ambiente vuote per impostazione predefinita. Per eseguire comandi di `PRE_BUILD_COMMAND`pre-compilazione, definire . Per eseguire i comandi `POST_BUILD_COMMAND`di post-compilazione, definire .

Nell'esempio seguente le due variabili vengono specificate a una serie di comandi, separati da virgole.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Per altre variabili di ambiente per personalizzare l'automazione della compilazione, vedere [Configurazione Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Per altre informazioni su come viene eseguito il servizio app e crea app PHP in Linux, vedere Documentazione di [Oryx: Come vengono rilevate e create le app PHP.](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md)

## <a name="customize-start-up"></a>Personalizzare l’avvio

Per impostazione predefinita, il contenitore PHP incorporato esegue il server Apache. All'avvio, funziona `apache2ctl -D FOREGROUND"`. Se lo si desidera, è possibile eseguire un comando diverso all'avvio, eseguendo il comando seguente in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Accedere alle variabili di ambiente

Nel servizio app è possibile [configurare le impostazioni dell'app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) al di fuori del codice dell'app. È quindi possibile accedervi utilizzando il modello [getenv()](https://secure.php.net/manual/function.getenv.php) standard. Ad esempio, per accedere a un'impostazione dell'app denominata `DB_HOST`, usare il codice seguente:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Modificare la radice del sito

Il framework web di propria scelta può utilizzare una sottodirectory come radice del sito. Ad esempio, [Laravel](https://laravel.com/) `public/` , utilizza la sottodirectory come radice del sito.

L'immagine PHP predefinita per il servizio app usa Apache e non consente di personalizzare la radice del sito per l'app. Per aggirare questa limitazione, aggiungere un file *con estensione htaccess* alla radice del repository con il contenuto seguente:

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
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

I framework Web più diffusi consentono di accedere alle informazioni `X-Forwarded-*` nel modello di app standard. In [CodeIgniter](https://codeigniter.com/)[is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) controlla il valore di `X_FORWARDED_PROTO` per impostazione predefinita.

## <a name="customize-phpini-settings"></a>Personalizzare le impostazioni di php.ini

Se è necessario apportare modifiche all'installazione di PHP, è possibile modificare una qualsiasi delle [direttive php.ini](https://www.php.net/manual/ini.list.php) seguendo questi passaggi.

> [!NOTE]
> Il modo migliore per visualizzare la versione PHP e la configurazione *php.ini* corrente consiste nel chiamare [phpinfo()](https://php.net/manual/function.phpinfo.php) nell'app.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Personalizzare le direttive non PHP_INI_SYSTEM

Per personalizzare le direttive PHP_INI_USER, PHP_INI_PERDIR e PHP_INI_ALL (vedere [direttive php.ini](https://www.php.net/manual/ini.list.php)), aggiungete un file *htaccess* alla directory radice dell'app.

Nel file *con estensione htaccess* aggiungere `php_value <directive-name> <value>` le direttive utilizzando la sintassi . Ad esempio:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Ridistribuire l'app con le modifiche e riavviarla. Se lo si distribuisce con Kudu (ad esempio, utilizzando [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)), viene riavviato automaticamente dopo la distribuzione.

In alternativa all'uso di *.htaccess*, puoi usare [ini_set()](https://www.php.net/manual/function.ini-set.php) nella tua app per personalizzare queste direttive non PHP_INI_SYSTEM.

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Personalizzare le direttive PHP_INI_SYSTEM

Per personalizzare PHP_INI_SYSTEM direttive (vedere [direttive php.ini](https://www.php.net/manual/ini.list.php)), non è possibile utilizzare l'approccio *.htaccess* . Il servizio app fornisce `PHP_INI_SCAN_DIR` un meccanismo separato usando l'impostazione dell'app.

Innanzitutto, eseguire il comando seguente in [Cloud](https://shell.azure.com) `PHP_INI_SCAN_DIR`Shell per aggiungere un'impostazione dell'app denominata :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d`è la directory predefinita in cui è presente *php.ini.* `/home/site/ini`è la directory personalizzata in cui si aggiungerà un file *.ini* personalizzato. Separare i valori `:`con un oggetto .

Passare alla sessione SSH Web con`https://<app-name>.scm.azurewebsites.net/webssh/host`il contenitore Linux ( ).

Creare una `/home/site` directory `ini`in denominato , quindi creare `/home/site/ini` un file *ini* nella directory (ad esempio, *settings.ini)* con le direttive che si desidera personalizzare. Utilizzare la stessa sintassi che si userebbe in un file *php.ini.* 

> [!TIP]
> Nei contenitori Linux incorporati nel servizio app, */home* viene usato come archiviazione condivisa persistente. 
>

Ad esempio, per modificare il valore di [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) eseguire i seguenti comandi:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Per rendere effettive le modifiche, riavvia l'app.

## <a name="enable-php-extensions"></a>Abilitare le estensioni PHP

Le installazioni PHP integrate contengono le estensioni più comunemente utilizzate. È possibile abilitare estensioni aggiuntive nello stesso modo in cui si [personalizzano le direttive php.ini](#customize-php_ini_system-directives).

> [!NOTE]
> Il modo migliore per visualizzare la versione PHP e la configurazione *php.ini* corrente consiste nel chiamare [phpinfo()](https://php.net/manual/function.phpinfo.php) nell'app.
>

Per abilitare le estensioni aggiuntive, eseguire la procedura seguente:

Aggiungi `bin` una directory alla directory radice dell'app e inseriscivi i `.so` file di estensione (ad esempio, *mongodb.so*). Assicurarsi che le estensioni siano compatibili con la versione PHP in Azure e siano compatibili con VC9 e non thread-safe (nts).

Distribuire le modifiche.

Seguire i passaggi descritti in [Personalizzare PHP_INI_SYSTEM direttive](#customize-php_ini_system-directives), aggiungere le estensioni nel file *ini* personalizzato con [l'estensione](https://www.php.net/manual/ini.core.php#ini.extension) o le direttive [zend_extension.](https://www.php.net/manual/ini.core.php#ini.zend-extension)

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Per rendere effettive le modifiche, riavvia l'app.

## <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Aprire una sessione SSH nel browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

Quando un'app PHP funzionante si comporta in modo diverso nel servizio app o presenta errori, provare a eseguire le operazioni seguenti:

- [Accedere al flusso di log](#access-diagnostic-logs).
- Testa l'app localmente in modalità di produzione. Il servizio app esegue le app Node.js in modalità di produzione, pertanto è necessario assicurarsi che il progetto funzioni come previsto in modalità di produzione in locale. Ad esempio:
    - A seconda del *file composer.json*, è`require` possibile `require-dev`che sia possibile installare pacchetti diversi per la modalità di produzione ( vs ).
    - Alcuni framework Web possono distribuire i file statici in modo diverso in modalità di produzione.
    - Alcuni framework Web possono utilizzare script di avvio personalizzati durante l'esecuzione in modalità di produzione.
- Eseguire l'app nel servizio app in modalità di debug. Ad esempio, in [Laravel,](https://meanjs.org/)è possibile configurare l'app per l'output dei messaggi di debug nell'ambiente di produzione [impostando l'impostazione dell'app `APP_DEBUG` su `true` ](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: App PHP con MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Domande frequenti sul Servizio app di Azure in Linux](app-service-linux-faq.md)
