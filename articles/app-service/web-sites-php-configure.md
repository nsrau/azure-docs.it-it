---
title: Configurare PHP nelle app Web del Servizio app di Azure | Microsoft Doc
description: Informazioni su come configurare l'installazione predefinita di PHP o aggiungere un'installazione personalizzata di PHP in Servizio app di Azure."
services: app-service
documentationcenter: php
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: 0467707a46709674d3f5de3346ad242af5c9dcb8
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="configure-php-in-azure-app-service-web-apps"></a>Configurazione di PHP nelle app Web di Servizio app di Azure
## <a name="introduction"></a>Introduzione
In questa guida verrà descritto come configurare il runtime PHP incorporato in App Web per [Servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714), fornire un PHP personalizzato runtime e abilitare le estensioni. Per usare Servizio app di Azure, effettuare l'iscrizione per ricevere la versione di [valutazione gratuita]. Per ottenere il massimo da questa guida, è necessario innanzitutto creare un'app Web PHP in Servizio app.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="how-to-change-the-built-in-php-version"></a>Procedura: modificare la versione PHP incorporata
Per impostazione predefinita, PHP 5.6 è installato e immediatamente disponibile per l'uso quando si crea un'app Web del servizio app di Azure. Il modo migliore per visualizzare la revisione della versione disponibile, la sua configurazione predefinita e le estensioni abilitate consiste nel distribuire uno script che chiama la funzione [phpinfo ()] .

Sono anche disponibili le versioni PHP 5.6 e PHP 7.0, che però non sono abilitate per impostazione predefinita. Per aggiornare la versione di PHP, seguire uno dei metodi seguenti:

### <a name="azure-portal"></a>Portale di Azure
1. Passare all'app Web nel [Portale di Azure](https://portal.azure.com) e fare clic sul pulsante **Impostazioni** .
   
    ![Impostazioni app Web][settings-button]
2. Dal pannello **Impostazioni** selezionare **Impostazioni dell'applicazione** e scegliere la nuova versione di PHP.
   
    ![Impostazioni dell'applicazione][application-settings]
3. Fare clic sul pulsante **Salva** all'inizio del pannello **Impostazioni app Web**.
   
    ![Salvare le impostazioni di configurazione][save-button]

### <a name="azure-powershell-windows"></a>Azure PowerShell (solo Windows).
1. Aprire Azure PowerShell e accedere al proprio account:
   
        PS C:\> Login-AzureRmAccount
2. Impostare la versione PHP per l'app Web.
   
        PS C:\> Set-AzureWebsite -PhpVersion {5.5 | 5.6 | 7.0} -Name {app-name}
3. La versione di PHP è ora impostata. È possibile verificare queste impostazioni:
   
        PS C:\> Get-AzureWebsite -Name {app-name} | findstr PhpVersion

### <a name="azure-command-line-interface-linux-mac-windows"></a>Interfaccia della riga di comando di Azure (Linux, Mac, Windows)
Per usare l'interfaccia della riga di comando di Azure, è necessario che **Node.js** sia installato nel computer.

1. Aprire il terminale e accedere al proprio account.
   
        azure login
2. Impostare la versione PHP per l'app Web.
   
        azure site set --php-version {5.5 | 5.6 | 7.0} {app-name}

3. La versione di PHP è ora impostata. È possibile verificare queste impostazioni:
   
        azure site show {app-name}

> [!NOTE] 
> I comandi dell'[interfaccia della riga di comando di Azure 2.0](https://github.com/Azure/azure-cli) equivalenti a quanto riportato sopra sono i seguenti:
>
>

    az login
    az appservice web config update --php-version {5.5 | 5.6 | 7.0} -g {resource-group-name} -n {app-name}
    az appservice web config show -g {resource-group-name} -n {app-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Modificare la configurazione PHP incorporata
Per qualsiasi runtime PHP incorporato, è possibile modificare le opzioni di configurazione eseguendo la procedura seguente. (per informazioni sulle direttive solo a livello di sistema, vedere la [Lista delle direttive php.ini]).

### <a name="changing-phpiniuser-phpiniperdir-phpiniall-configuration-settings"></a>Modifica delle impostazioni di configurazione PHP\_INI\_USER, PHP\_INI\_PERDIR, PHP\_INI\_ALL
1. Aggiungere un file [.user.ini] alla directory radice in uso.
2. Aggiungere le impostazioni di configurazione al file  `.user.ini` usando la stessa sintassi che si userebbe in un file `php.ini`. Ad esempio, se si desidera attivare l'impostazione `display_errors` e configurare l'impostazione `upload_max_filesize` su 10M, il file `.user.ini` conterrà il testo seguente:
   
        ; Example Settings
        display_errors=On
        upload_max_filesize=10M
   
        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
3. Distribuire l'app Web.
4. Riavviare l'app Web . Il riavvio è necessario in quanto la frequenza di lettura dei file `.user.ini` a parte di PHP è governata dall'impostazione `user_ini.cache_ttl` configurata a livello di sistema, che per impostazione predefinita corrisponde a 300 secondi (5 minuti). Il riavvio del sito forza PHP a leggere le nuove impostazioni nel file `.user.ini` .

In alternativa a un file `.user.ini` è possibile usare la funzione [ini_set()] negli script per impostare le opzioni di configurazione che non sono direttive a livello di sistema.

### <a name="changing-phpinisystem-configuration-settings"></a>Modifica delle impostazioni di configurazione PHP\_INI\_SYSTEM
1. Aggiungere un'impostazione dell'applicazione all'applicazione Web con la chiave `PHP_INI_SCAN_DIR` e valore `d:\home\site\ini`
2. Creare un file `settings.ini` usando la console Kudu (http://&lt;site-name&gt;.scm.azurewebsite.net) nella directory `d:\home\site\ini`.
3. Aggiungere le impostazioni di configurazione al file `settings.ini` utilizzando la stessa sintassi che si userebbe in un file php.ini. Ad esempio, se si desidera fare riferimento il `curl.cainfo` impostazione su un `*.crt` file e impostare su 512K 'wincache.maxfilesize' impostazione del `settings.ini` file conterrebbe il testo:
   
        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
4. Riavviare l'applicazione Web per caricare le modifiche.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Abilitare le estensioni nel runtime PHP predefinito
Come indicato nella sezione precedente, il modo migliore per visualizzare la versione PHP disponibile, la sua configurazione predefinita e le estensioni abilitate consiste nel distribuire uno script che chiama la funzione [phpinfo ()]. Per abilitare le estensioni aggiuntive, eseguire la procedura seguente.

### <a name="configure-via-ini-settings"></a>Configurare tramite le impostazioni del file ini
1. Aggiungere una `ext` directory alla `d:\home\site` directory.
2. Inserire i file con estensione `.dll` nella directory `ext` (ad esempio `php_xdebug.dll`). Verificare che le estensioni siano compatibili con la versione predefinita di PHP e con VC9 e non thread-safe (nts).
3. Aggiungere un'impostazione dell'applicazione all'applicazione Web con la chiave `PHP_INI_SCAN_DIR` e valore `d:\home\site\ini`
4. Creare un `ini` del file in `d:\home\site\ini` chiamato `extensions.ini`.
5. Aggiungere le impostazioni di configurazione al file `extensions.ini` utilizzando la stessa sintassi che si userebbe in un file php.ini. Ad esempio, se si desidera abilitare le estensioni di MongoDB e XDebug il `extensions.ini` file conterrebbe il testo:
   
        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
6. Riavviare l'applicazione Web per caricare le modifiche.

### <a name="configure-via-app-setting"></a>Configurare tramite impostazione dell'applicazione
1. Aggiungere una `bin` directory alla directory radice.
2. Inserire i file con estensione `.dll` nella directory `bin` (ad esempio `php_xdebug.dll`). Verificare che le estensioni siano compatibili con la versione predefinita di PHP e con VC9 e non thread-safe (nts).
3. Distribuire l'app Web.
4. Passare all'app Web nel Portale di Azure e fare clic sul pulsante **Impostazioni** .
   
    ![Impostazioni app Web][settings-button]
5. Dal pannello **Impostazioni** selezionare **Impostazioni dell'applicazione** e scorrere fino alla sezione **Impostazioni app**.
6. Nella sezione **Impostazioni app** creare una chiave **PHP_EXTENSIONS**. Il valore di questa chiave deve corrispondere a un percorso relativo alla radice del sito Web: **bin\your-ext-file**.
   
    ![Abilitare le estensioni nelle impostazioni app][php-extensions]
7. Fare clic sul pulsante **Salva** all'inizio del pannello **Impostazioni app Web**.
   
    ![Salvare le impostazioni di configurazione][save-button]

Con l'uso di una chiave **PHP_ZENDEXTENSIONS** sono supportate anche le estensioni Zend. Per abilitare più estensioni, includere un elenco separato da virgole di `.dll` file per il valore dell'impostazione dell'app.

## <a name="how-to-use-a-custom-php-runtime"></a>Usare un runtime PHP personalizzato
Invece del runtime PHP predefinito, App Web di Servizio Web può utilizzare un runtime PHP fornito dall'utente per l'esecuzione degli script PHP. Quest'ultimo può essere configurato da un file `php.ini` analogamente fornito dall'utente. Per usare un runtime PHP personalizzato con App Web, attenersi alla procedura seguente.

1. Ottenere una versione compatibile con VC9 o VC11 e non-thread-safe di PHP per Windows. Versioni recenti di PHP per Windows sono disponibili qui: [http://windows.php.net/download/]. Versioni precedenti sono disponibili nell'archivio qui: [http://windows.php.net/downloads/releases/archives/].
2. Modificare il file `php.ini` per il proprio runtime. Si noti che qualsiasi impostazione di configurazione che non sia una direttiva solo a livello di sistema verrà ignorata da App Web (per informazioni sulle direttive solo a livello di sistema, vedere la [Lista delle direttive php.ini]).
3. Facoltativamente, aggiungere le estensioni al proprio runtime PHP e abilitarle nel file `php.ini` .
4. Aggiungere una directory `bin` alla propria directory radice e inserirvi la directory contenente il proprio runtime PHP (ad esempio, `bin\php`).
5. Distribuire l'app Web.
6. Passare all'app Web nel Portale di Azure e fare clic sul pulsante **Impostazioni** .
   
    ![Impostazioni app Web][settings-button]
7. Dal pannello **Impostazioni** selezionare **Impostazioni applicazione** e scorrere fino alla sezione **Mapping del gestore**. Aggiungere `*.php` al campo Estensione e aggiungere il percorso dell'eseguibile `php-cgi.exe`. Se si inserisce il proprio runtime PHP nella directory `bin` nella radice dell'applicazione, il percorso sarà `D:\home\site\wwwroot\bin\php\php-cgi.exe`.
   
    ![Specificare il gestore nei mapping gestore][handler-mappings]
8. Fare clic sul pulsante **Salva** all'inizio del pannello **Impostazioni app Web**.
   
    ![Salvare le impostazioni di configurazione][save-button]

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Procedura: Abilitare l’automazione Composer in Azure
Per impostazione predefinita, il servizio app non esegue operazioni relative a composer.json, se questo è presente nel progetto PHP. Se si usa la [distribuzione Git](app-service-deploy-local-git.md), è possibile abilitare l'elaborazione di composer.json durante l'operazione di `git push` abilitando l'estensione Composer.

> [!NOTE]
> È possibile [votare qui per il supporto Composer avanzato nel servizio app](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
> 
> 

1. Nel pannello dell'app Web PHP nel [portale di Azure](https://portal.azure.com) fare clic su **Strumenti** > **Estensioni**.
   
    ![Pannello delle impostazioni del portale di Azure per abilitare l'automazione Composer in Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Fare clic su **Aggiungi**, quindi su **Compositore**.
   
    ![Aggiungere l’estensione Composer per abilitare l’automazione Composer in Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Fare clic su **OK** per accettare le note legali. Fare di nuovo clic su **OK** per aggiungere l'estensione.
   
    Nel pannello **Estensioni installate** è ora visualizzata l'estensione Compositore.  
    ![Accettare le note legali per abilitare l'automazione Composer in Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Eseguire ora `git add`, `git commit` e `git push` come nella sezione precedente. Si noterà che ora Composer installa le dipendenze definite in composer.json.
   
    ![Distribuzione Git con l’automazione Composer in Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni, vedere il [Centro per sviluppatori di PHP](/develop/php/).

> [!NOTE]
> Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](https://azure.microsoft.com/try/app-service/), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
> 
> 

[valutazione gratuita]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo ()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Lista delle direttive php.ini]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png

