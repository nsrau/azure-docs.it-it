---
title: Configurare il runtime PHP
description: Informazioni su come configurare l'installazione predefinita di PHP o aggiungere un'installazione personalizzata di PHP in Servizio app di Azure.
author: msangapu-msft
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.devlang: php
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 2d35c31e23da7addcf0b4c341c6925f258d5c232
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688252"
---
# <a name="configure-php-in-azure-app-service"></a>Configurare PHP in Servizio app di Azure

## <a name="introduction"></a>Introduzione

Questa guida descrive come configurare il runtime PHP incorporato per le app Web, i back-end per dispositivi mobili e le app per le API in [Servizio app di Azure](https://go.microsoft.com/fwlink/?LinkId=529714), offrire un runtime PHP personalizzato e abilitare le estensioni. Per usare Servizio app di Azure, effettuare l'iscrizione per ricevere la versione di [valutazione gratuita]. Per ottenere il massimo da questa guida, è necessario innanzitutto creare un'app PHP in Servizio app.

## <a name="how-to-change-the-built-in-php-version"></a>Procedura: modificare la versione PHP incorporata

Per impostazione predefinita, PHP 5.6 è installato e immediatamente disponibile per l'uso quando si crea un'app di Servizio app. Il modo migliore per visualizzare la revisione della versione disponibile, la sua configurazione predefinita e le estensioni abilitate consiste nel distribuire uno script che chiama la funzione [phpinfo ()] .

Sono anche disponibili le versioni PHP 7.0 e PHP 7.2, che però non sono abilitate per impostazione predefinita. Per aggiornare la versione di PHP, seguire uno dei metodi seguenti:

### <a name="azure-portal"></a>Portale di Azure

1. Passare all'app nella [portale di Azure](https://portal.azure.com) e scorrere fino alla pagina di **configurazione** .

2. In **configurazione**selezionare **Impostazioni generali** e scegliere la nuova versione di php.

3. Fare clic sul pulsante **Salva** nella parte superiore del pannello **Impostazioni generali** .

### <a name="azure-powershell-windows"></a>Azure PowerShell (solo Windows).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Aprire Azure PowerShell e accedere al proprio account:

        PS C:\> Connect-AzAccount
2. Impostare la versione PHP per l'app.

        PS C:\> Set-AzureWebsite -PhpVersion {5.6 | 7.0 | 7.2} -Name {app-name}
3. La versione di PHP è ora impostata. È possibile verificare queste impostazioni:

        PS C:\> Get-AzureWebsite -Name {app-name} | findstr PhpVersion

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure 

Per usare l'interfaccia della riga di comando di Azure, è necessario [installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nel computer.

1. Aprire il terminale e accedere al proprio account.

        az login

1. Controllo per visualizzare l'elenco dei runtime supportati.

        az webapp list-runtimes | grep php

2. Impostare la versione PHP per l'app.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

3. La versione di PHP è ora impostata. È possibile verificare queste impostazioni:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Modificare la configurazione PHP incorporata

Per qualsiasi runtime PHP incorporato, è possibile modificare le opzioni di configurazione eseguendo la procedura seguente. (per informazioni sulle direttive solo a livello di sistema, vedere la [Lista delle direttive php.ini]).

### <a name="changing-php_ini_user-php_ini_perdir-php_ini_all-configuration-settings"></a>Modifica delle impostazioni di configurazione PHP\_INI\_USER, PHP\_INI\_PERDIR, PHP\_INI\_ALL

1. Aggiungere un file [.user.ini] alla directory radice in uso.
1. Aggiungere le impostazioni di configurazione al file  `.user.ini` usando la stessa sintassi che si userebbe in un file `php.ini`. Ad esempio, se si desidera attivare l'impostazione `display_errors` e impostare `upload_max_filesize` su 10 M, il file `.user.ini` conterrà il testo seguente:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Distribuire l'app.
3. Riavviare l'app. Il riavvio è necessario in quanto la frequenza di lettura dei file `.user.ini` a parte di PHP è governata dall'impostazione `user_ini.cache_ttl` configurata a livello di sistema, che per impostazione predefinita corrisponde a 300 secondi (5 minuti). Il riavvio dell'app forza PHP a leggere le nuove impostazioni nel file `.user.ini`.

In alternativa a un file `.user.ini` è possibile usare la funzione [ini_set()] negli script per impostare le opzioni di configurazione che non sono direttive a livello di sistema.

### <a name="changing-php_ini_system-configuration-settings"></a>Modifica delle impostazioni di configurazione PHP\_INI\_SYSTEM

1. Aggiungere all'app un'impostazione dell'applicazione con la chiave `PHP_INI_SCAN_DIR` e il valore `d:\home\site\ini`
1. Creare un file `settings.ini` usando la console Kudu (http://&lt;site-name&gt;.scm.azurewebsite.net) nella directory `d:\home\site\ini`.
1. Aggiungere le impostazioni di configurazione al file  `settings.ini` usando la stessa sintassi che si userebbe in un file `php.ini`. Ad esempio, se si desidera fare riferimento il `curl.cainfo` impostazione su un `*.crt` file e impostare su 512K 'wincache.maxfilesize' impostazione del `settings.ini` file conterrebbe il testo:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Per caricare di nuovo le modifiche, riavviare l'app.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Abilitare le estensioni nel runtime PHP predefinito

Come indicato nella sezione precedente, il modo migliore per visualizzare la versione PHP disponibile, la sua configurazione predefinita e le estensioni abilitate consiste nel distribuire uno script che chiama la funzione [phpinfo ()]. Per abilitare le estensioni aggiuntive, eseguire la procedura seguente:

### <a name="configure-via-ini-settings"></a>Configurare tramite le impostazioni del file ini

1. Aggiungere una `ext` directory alla `d:\home\site` directory.
1. Inserire i file con estensione `.dll` nella directory `ext` (ad esempio `php_xdebug.dll`). Verificare che le estensioni siano compatibili con la versione predefinita di PHP e con VC9 e non thread-safe (nts).
1. Aggiungere all'app un'impostazione dell'applicazione con la chiave `PHP_INI_SCAN_DIR` e il valore `d:\home\site\ini`
1. Creare un `ini` del file in `d:\home\site\ini` chiamato `extensions.ini`.
1. Aggiungere le impostazioni di configurazione al file  `extensions.ini` usando la stessa sintassi che si userebbe in un file `php.ini`. Ad esempio, se si desidera abilitare le estensioni di MongoDB e XDebug il `extensions.ini` file conterrebbe il testo:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Per caricare le modifiche, riavviare l'app.

### <a name="configure-via-app-setting"></a>Configurare tramite impostazione dell'applicazione

1. Aggiungere una `bin` directory alla directory radice.
2. Inserire i file con estensione `.dll` nella directory `bin` (ad esempio `php_xdebug.dll`). Verificare che le estensioni siano compatibili con la versione predefinita di PHP e con VC9 e non thread-safe (nts).
3. Distribuire l'app.
4. Passare all'app nel portale di Azure e fare clic sulla sezione **configurazione** che si trova sotto **Impostazioni** .
5. Nel pannello **configurazione** selezionare **Impostazioni applicazione**.
6. Nella sezione **Impostazioni applicazione** fare clic su **+ nuova impostazione applicazione** e creare una chiave **PHP_EXTENSIONS** . Il valore di questa chiave deve corrispondere a un percorso relativo alla radice del sito Web: **bin\your-ext-file**.
7. Fare clic sul pulsante **Aggiorna** nella parte inferiore e quindi fare clic su **Salva** sopra la scheda **Impostazioni applicazione** .

Con l'uso di una chiave **PHP_ZENDEXTENSIONS** sono supportate anche le estensioni Zend. Per abilitare più estensioni, includere un elenco separato da virgole di `.dll` file per il valore dell'impostazione dell'app.

## <a name="how-to-use-a-custom-php-runtime"></a>Usare un runtime PHP personalizzato

Invece del runtime PHP predefinito, Servizio app può usare un runtime PHP fornito dall'utente per l'esecuzione degli script PHP. Quest'ultimo può essere configurato da un file `php.ini` analogamente fornito dall'utente. Per usare un runtime PHP personalizzato con Servizio app, attenersi alla procedura seguente.

1. Ottenere una versione compatibile con VC9 o VC11 e non-thread-safe di PHP per Windows. Le versioni recenti di PHP per Windows sono disponibili qui: [https://windows.php.net/download/]. Le versioni precedenti sono disponibili in questo archivio: [https://windows.php.net/downloads/releases/archives/].
2. Modificare il file `php.ini` per il proprio runtime. Qualsiasi impostazione di configurazione che non sia una direttiva solo a livello di sistema verrà ignorata da Servizio app. (per informazioni sulle direttive solo a livello di sistema, vedere la [Lista delle direttive php.ini]).
3. Facoltativamente, aggiungere le estensioni al proprio runtime PHP e abilitarle nel file `php.ini` .
4. Aggiungere una directory `bin` alla propria directory radice e inserirvi la directory contenente il proprio runtime PHP (ad esempio, `bin\php`).
5. Distribuire l'app.
6. Passare all'app nel portale di Azure e fare clic sul pannello **configurazione** .
8. Nel pannello **configurazione** selezionare mapping dei **percorsi**. 
9. Fare clic su **+ nuovo gestore** e aggiungere `*.php` al campo estensione e aggiungere il percorso del `php-cgi.exe` eseguibile nel **processore di script**. Se si inserisce il proprio runtime PHP nella directory `bin` nella radice dell'applicazione, il percorso è `D:\home\site\wwwroot\bin\php\php-cgi.exe`.
10. Nella parte inferiore, fare clic su **Aggiorna** per completare l'aggiunta del mapping del gestore.
11. Fare clic su **Salva** per salvare le modifiche.

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Procedura: Abilitare l’automazione Composer in Azure

Per impostazione predefinita, il servizio app non esegue operazioni relative a composer.json, se questo è presente nel progetto PHP. Se si usa la [distribuzione Git](deploy-local-git.md), è possibile abilitare l'elaborazione di composer.json durante l'operazione di `git push` abilitando l'estensione Composer.

> [!NOTE]
> È possibile [votare qui per il supporto Composer avanzato nel servizio app](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
>

1. Nel pannello dell'app PHP nel [portale di Azure](https://portal.azure.com) fare clic su **Strumenti** > **Estensioni**.

    ![Pannello delle impostazioni del portale di Azure per abilitare l'automazione Compositore in Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Fare clic su **Aggiungi**, quindi su **Compositore**.

    ![Aggiungere l’estensione Composer per abilitare l’automazione Composer in Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Fare clic su **OK** per accettare le note legali. Fare di nuovo clic su **OK** per aggiungere l'estensione.

    Il pannello **Estensioni installate** mostra l'estensione Compositore.
    ![Accettare le note legali per abilitare l'automazione Composer in Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. A questo punto, in una finestra del terminale del computer locale, eseguire `git add`, `git commit` e `git push` nell'app. Si noti che Compositore installa le dipendenze definite in composer.json.

    ![Distribuzione Git con l’automazione Composer in Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere il [Centro per sviluppatori di PHP](https://azure.microsoft.com/develop/php/).

[valutazione gratuita]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo ()]: https://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Lista delle direttive php.ini]: https://www.php.net/manual/en/ini.list.php
[.user.ini]: https://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: https://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[https://windows.php.net/download/]: https://windows.php.net/download/
[https://windows.php.net/downloads/releases/archives/]: https://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png
