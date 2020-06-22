---
title: Configurare le app PHP
description: Informazioni su come configurare un contenitore PHP predefinito per l'app. Questo articolo illustra le attività di configurazione più comuni.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9933205095587d9e8e0d8a5641d213f159512450
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84234947"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Configurare un'app PHP in Linux per Servizio app di Azure

Questa guida descrive come configurare il runtime PHP incorporato per le app Web, i back-end per dispositivi mobili e le app per le API in Servizio app di Azure.

Questa guida illustra i concetti chiave e le istruzioni per gli sviluppatori PHP che usano un contenitore Linux predefinito nel servizio app. Se non si è mai usato Servizio app di Azure, seguire per prima cosa l'[Avvio rapido di PHP](quickstart-php.md) e l'[esercitazione di PHP con MySQL](tutorial-php-mysql-app.md).

## <a name="show-php-version"></a>Visualizzare la versione di PHP

Per visualizzare la versione corrente di PHP, eseguire il comando seguente in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Per visualizzare tutte le versioni di PHP supportate, eseguire il comando seguente in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>Impostare la versione di PHP

Eseguire il comando seguente in [Cloud Shell](https://shell.azure.com) per impostare la versione di PHP su 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

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

## <a name="access-environment-variables"></a>Accedere alle variabili di ambiente

Nel servizio app è possibile [configurare le impostazioni dell'app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) al di fuori del codice dell'app. È quindi possibile accedervi usando il modello standard [getenv()](https://secure.php.net/manual/function.getenv.php). Ad esempio, per accedere a un'impostazione dell'app denominata `DB_HOST`, usare il codice seguente:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Modificare la radice del sito

Il framework Web scelto può usare una sottodirectory come radice del sito. [Laravel](https://laravel.com/), ad esempio, usa la sottodirectory `public/` come radice del sito.

L'immagine PHP predefinita per il servizio app usa Apache e non consente di personalizzare la radice del sito per l'app. Per ovviare a questa limitazione, aggiungere alla radice del repository un file con estensione *htaccess* con il contenuto seguente:

```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} ^/$
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

Se è necessario apportare modifiche all'installazione di PHP, è possibile modificare qualsiasi [direttiva di php.ini](https://www.php.net/manual/ini.list.php) seguendo questa procedura.

> [!NOTE]
> Il modo migliore per visualizzare la versione di PHP e la configurazione corrente di *php.ini* è chiamare [phpinfo()](https://www.php.net/manual/function.phpinfo.php) nell'app.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Personalizzare le direttive non PHP_INI_SYSTEM

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

Ridistribuire l'app con le modifiche e riavviarla. Se la si distribuisce con Kudu (ad esempio, usando [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)), viene automaticamente riavviata dopo la distribuzione.

In alternativa al file con estensione *htaccess*, è possibile usare [ini_set()](https://www.php.net/manual/function.ini-set.php) nell'app per personalizzare queste direttive non PHP_INI_SYSTEM.

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Personalizzare le direttive PHP_INI_SYSTEM

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

## <a name="enable-php-extensions"></a>Abilitare le estensioni di PHP

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

## <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Aprire una sessione SSH nel browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

Quando un'app PHP funzionante si comporta in modo diverso nel servizio app o presenta errori, provare a eseguire le operazioni seguenti:

- [Accedere al flusso di log](#access-diagnostic-logs).
- Testare l'app in locale nella modalità di produzione. Il servizio app esegue le app Node.js in modalità di produzione, quindi è necessario assicurarsi che il progetto funzioni come previsto in modalità di produzione in locale. Ad esempio:
    - A seconda del file *composer.json*, è possibile che siano installati pacchetti diversi per la modalità di produzione (`require` o `require-dev`).
    - Alcuni framework Web possono distribuire i file statici in modo diverso in modalità di produzione.
    - Alcuni framework Web possono usare script di avvio personalizzati durante l'esecuzione in modalità di produzione.
- Eseguire l'app nel servizio app in modalità di debug. In [Laravel](https://meanjs.org/) è ad esempio possibile configurare l'app per l'output di messaggi di debug in fase di produzione [impostando l'impostazione dell'app `APP_DEBUG` su `true`](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: App PHP con MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Domande frequenti sul Servizio app di Azure in Linux](app-service-linux-faq.md)
