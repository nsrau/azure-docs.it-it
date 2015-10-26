<properties
	pageTitle="Configurazione di PHP nelle app Web di Servizio app di Azure"
	description="Informazioni su come configurare l'installazione predefinita di PHP o aggiungere un'installazione personalizzata di PHP in Servizio app di Azure.";"
	services="app-service\web"
	documentationCenter="php"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="tomfitz"/>

#Configurazione di PHP nelle app Web di Servizio app di Azure

## Introduzione

In questa guida verrà descritto come configurare il runtime PHP incorporato in App Web per [Servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714), fornire un PHP personalizzato runtime e abilitare le estensioni. Per usare Servizio app di Azure, effettuare l'iscrizione per ricevere la versione di [valutazione gratuita]. Per ottenere il massimo da questa guida, è necessario innanzitutto creare un'app Web PHP in Servizio app.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Procedura: modificare la versione PHP incorporata
Per impostazione predefinita, PHP 5.4 è installato e immediatamente disponibile per l'uso quando si crea un'app Web di Azure. Il modo migliore per visualizzare la revisione della versione disponibile, la sua configurazione predefinita e le estensioni abilitate consiste nel distribuire uno script che chiama la funzione [phpinfo ()].

Sono inoltre disponibili PHP 5.5 e PHP 5.6, che però non sono abilitate per impostazione predefinita. Per aggiornare la versione di PHP, seguire uno dei metodi seguenti:

### Portale di Azure

1. Passare all'app Web nel [Portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715) e fare clic sul pulsante **Impostazioni**.

	![Impostazioni app][settings-button]

2. Dal pannello **Impostazioni** selezionare **Impostazioni dell'applicazione** e scegliere la nuova versione di PHP.

    ![Impostazioni dell'applicazione][application-settings]

3. Fare clic sul pulsante **Salva** all'inizio del pannello **Impostazioni app Web**.

	![Salvare le impostazioni di configurazione][save-button]

### Azure PowerShell (solo Windows).

1. Aprire Windows PowerShell ISE.
2. Digitare `Set-AzureWebsite -PhpVersion [5.4 | 5.5 | 5.6] -Name <site-name>`e premere INVIO.
3. La versione di PHP è ora impostata.

	![Impostazione della versione PHP con Azure PowerShell][SETPHPVERPS]
4. È possibile confermare le impostazioni digitando `Get-AzureWebiste -Name <site-name>` e premere INVIO.

	![Verifica della versione PHP con Azure PowerShell][GETPHPVERPS]

### Interfaccia della riga di comando di Azure (Linux, Mac, Windows)

Per usare l'interfaccia della riga di comando di Azure, è necessario che **Node.js** sia installato nel computer.

1. Aprire il terminale.
2. Digitare `azure site set --php-version [5.4 | 5.5] [site-name]`e premere INVIO.
3. La versione di PHP è ora impostata.

	![Impostazione della versione PHP con interfaccia della riga di comando di Azure][SETPHPVERCLI]
4. È possibile confermare le impostazioni digitando `azure site show [site-name]` e premere INVIO.

	![Verifica della versione PHP con interfaccia della riga di comando di Azure][GETPHPVERCLI]

## Modificare la configurazione PHP incorporata

Per qualsiasi runtime PHP incorporato, è possibile modificare le opzioni di configurazione eseguendo la procedura seguente. (per informazioni sulle direttive solo a livello di sistema, vedere la [Lista delle direttive php.ini]).

### PHP\_INI\_USER, PHP\_INI\_PERDIR, la modifica delle impostazioni di configurazione PHP\_INI\_ALL

1. Aggiungere un file [.user.ini] alla directory radice in uso.
2. Aggiungere le impostazioni di configurazione al file `.user.ini` usando la stessa sintassi che si userebbe in un file `php.ini`. Ad esempio, se si desidera attivare l'impostazione `display_errors` e configurare l'impostazione `upload_max_filesize` su 10M, il file `.user.ini` conterrà il testo seguente:

		; Example Settings
		display_errors=On
		upload_max_filesize=10M

3. Distribuire l'app Web.
4. Riavviare l'app Web . Il riavvio è necessario in quanto la frequenza di lettura dei file `.user.ini` a parte di PHP è governata dall'impostazione `user_ini.cache_ttl` configurata a livello di sistema, che per impostazione predefinita corrisponde a 300 secondi (5 minuti). Il riavvio del sito forza PHP a leggere le nuove impostazioni nel file `.user.ini`.

In alternativa a un file `.user.ini` è possibile usare la funzione [ini\_set()] negli script per impostare le opzioni di configurazione che non sono direttive a livello di sistema.

### Modifica delle impostazioni di configurazione PHP\_INI\_SYSTEM

1. Aggiungere un'impostazione dell'applicazione all'applicazione Web con la chiave `PHP_INI_SCAN_DIR` e valore `d:\home\site\ini`
2. Creare un `settings.ini` file utilizzando la Console Kudu (http://&lt;site-name&gt;.scm.azurewebsite.net) nella `d:\home\site\ini` directory.
3. Aggiungere le impostazioni di configurazione al file `settings.ini` utilizzando la stessa sintassi che si userebbe in un file php.ini. Ad esempio, se si desidera fare riferimento il `curl.cainfo` impostazione su un `*.crt` file e impostare su 512K 'wincache.maxfilesize' impostazione del `settings.ini` file conterrebbe il testo:

		; Example Settings
		curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
		wincache.maxfilesize=512
4. Riavviare l'applicazione Web per caricare le modifiche.

## Abilitare le estensioni nel runtime PHP predefinito
Come indicato nella sezione precedente, il modo migliore per visualizzare la versione PHP disponibile, la sua configurazione predefinita e le estensioni abilitate consiste nel distribuire uno script che chiama la funzione [phpinfo ()]. Per abilitare le estensioni aggiuntive, eseguire la procedura seguente.

### Configurare tramite le impostazioni del file ini

1. Aggiungere una `ext` directory alla `d:\home\site` directory.
2. Inserire `.dll` i file con estensione nella `ext` directory (ad esempio `php_mongo.dll` e `php_xdebug.dll`). Assicurarsi che le estensioni siano compatibili con la versione predefinita di PHP (la quale è, al momento della stesura di questo documento, PHP 5.4) e che siano compatibili con VC9 e non thread-safe (nts).
3. Aggiungere un'impostazione dell'applicazione all'applicazione Web con la chiave `PHP_INI_SCAN_DIR` e valore `d:\home\site\ini`
4. Creare un `ini` del file in `d:\home\site\ini` chiamato `extensions.ini`.
5. Aggiungere le impostazioni di configurazione al file `extensions.ini` utilizzando la stessa sintassi che si userebbe in un file php.ini. Ad esempio, se si desidera abilitare le estensioni di MongoDB e XDebug il `extensions.ini` file conterrebbe il testo:

		; Enable Extensions
		extension=d:\home\site\ext\php_mongo.dll
		zend_extension=d:\home\site\ext\php_xdebug.dll
6. Riavviare l'applicazione Web per caricare le modifiche.

### Configurare tramite impostazione dell'applicazione

1. Aggiungere una `bin` directory alla directory radice.
2. Inserire i file con estensione `.dll` nella directory `bin` (ad esempio `php_mongo.dll`). Assicurarsi che le estensioni siano compatibili con la versione predefinita di PHP (la quale è, al momento della stesura di questo documento, PHP 5.4) e che siano compatibili con VC9 e non thread-safe (nts).
3. Distribuire l'app Web.
4. Passare all'app Web nel Portale di Azure e fare clic sul pulsante **Impostazioni**.

	![Impostazioni app][settings-button]

5. Dal pannello **Impostazioni** selezionare **Impostazioni dell'applicazione** e scorrere fino alla sezione **Impostazioni app**.
6. Nella sezione **Impostazioni app** creare una chiave **PHP\_EXTENSIONS**. Il valore di questa chiave deve corrispondere a un percorso relativo alla radice del sito Web: **bin\\your-ext-file**.

	![Abilitare le estensioni nelle impostazioni app][php-extensions]

7. Fare clic sul pulsante **Salva** all'inizio del pannello **Impostazioni app Web**.

	![Salvare le impostazioni di configurazione][save-button]

Con l'utilizzo di una chiave **PHP\_ZENDEXTENSIONS** sono supportate anche le estensioni Zend. Per abilitare più estensioni, includere un elenco separato da virgole di `.dll` file per il valore dell'impostazione dell'app.


## Usare un runtime PHP personalizzato
Invece del runtime PHP predefinito, App Web di Servizio Web può utilizzare un runtime PHP fornito dall'utente per l'esecuzione degli script PHP. Quest'ultimo può essere configurato da un file `php.ini` analogamente fornito dall'utente. Per usare un runtime PHP personalizzato con App Web, attenersi alla procedura seguente.

1. Ottenere una versione compatibile con VC9 o VC11 e non-thread-safe di PHP per Windows. Versioni recenti di PHP per Windows sono disponibili qui: [http://windows.php.net/download/]. Versioni precedenti sono disponibili nell'archivio qui: [http://windows.php.net/downloads/releases/archives/].
2. Modificare il file `php.ini` per il proprio runtime. Si noti che qualsiasi impostazione di configurazione che non sia una direttiva solo a livello di sistema verrà ignorata da App Web (per informazioni sulle direttive solo a livello di sistema, vedere la [Lista delle direttive php.ini]).
3. Facoltativamente, aggiungere le estensioni al proprio runtime PHP e abilitarle nel file `php.ini`.
4. Aggiungere una directory `bin` alla propria directory radice e inserirvi la directory contenente il proprio runtime PHP (ad esempio, `bin\php`).
5. Distribuire l'app Web.
4. Passare all'app Web nel Portale di Azure e fare clic sul pulsante **Impostazioni**.

	![Impostazioni app][settings-button]

7. Dal pannello **Impostazioni** selezionare **Impostazioni dell'applicazione** e scorrere fino alla sezione **Mapping dei gestori**. Aggiungere `*.php` al campo Estensione e aggiungere il percorso dell'eseguibile `php-cgi.exe`. Se si inserisce il proprio runtime PHP nella directory `bin` nella radice dell'applicazione, il percorso sarà `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

	![Specificare il gestore nei mapping gestore][handler-mappings]

8. Fare clic sul pulsante **Salva** all'inizio del pannello **Impostazioni app Web**.

	![Salvare le impostazioni di configurazione][save-button]

## Passaggi successivi

Per ulteriori informazioni, vedere il [Centro per sviluppatori di PHP](/develop/php/).

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale precedente per il nuovo portale, vedere: [riferimento per lo spostamento tra il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

[valutazione gratuita]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo ()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Lista delle direttive php.ini]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini\_set()]: http://www.php.net/manual/en/function.ini-set.php
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
 

<!---HONumber=Oct15_HO3-->