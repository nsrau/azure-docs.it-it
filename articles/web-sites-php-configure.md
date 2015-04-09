<properties
	pageTitle="Configurazione di PHP nelle app Web di Servizio app di Azure "
	description="Informazioni su come configurare l'installazione predefinita di PHP o aggiungere un'installazione personalizzata di PHP in Servizio app di Azure."
	services="app-service\web"
	documentationCenter="php"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="tomfitz"/>

#Configurazione di PHP nelle app Web di Servizio app di Azure 

## Introduzione

In questa guida verrà descritto come configurare il runtime PHP incorporato in App Web per [Servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714), fornire un PHP personalizzato runtime e abilitare le estensioni. Per usare Servizio app di Azure, effettuare l'iscrizione per ricevere la [versione di valutazione gratuita]. Per ottenere il massimo da questa guida, è necessario innanzitutto creare un'app Web PHP in Servizio app.

## Procedura: Modificare la configurazione PHP incorporata
Per impostazione predefinita, PHP 5.4 è installato e immediatamente disponibile per l'uso quando si crea un'app Web Web di Azure. Il modo migliore per visualizzare la revisione della versione disponibile, la sua configurazione predefinita e le estensioni abilitate consiste nel distribuire uno script che chiama la funzione [phpinfo()].

Sono inoltre disponibili PHP 5.5 e PHP 5.6, che però non sono abilitate per impostazione predefinita. Per aggiornare la versione di PHP, attenersi alla procedura seguente:

1. Passare all'app Web nel [Portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715) e fare clic sul pulsante **Impostazioni**.

	![Impostazioni app Web][pulsante-impostazioni]

2. Dal pannello **Impostazioni** selezionare **Impostazioni dell'applicazione** e scegliere la nuova versione di PHP.

    ![Impostazioni dell'applicazione][impostazioni-applicazione]

3. Fare clic sul pulsante **Salva** all'inizio del pannello **Impostazioni app Web**.

	![Salvare le impostazioni di configurazione][pulsante-salva]

Per ognuno dei runtime PHP incorporati, è possibile modificare qualsiasi opzione di configurazione che non sia una direttiva solo a livello di sistema attenendosi alla procedura seguente  (per informazioni sulle direttive solo a livello di sistema, vedere la [Lista delle direttive php.ini]).

1. Aggiungere un file [.user.ini] alla directory radice in uso.
2. Aggiungere le impostazioni di configurazione al file  `.user.ini` utilizzando la stessa sintassi che si adopererebbe in un file  `php.ini`. Ad esempio, se attivare l'impostazione  `display_errors` e impostare l'impostazione  `upload_max_filesize` su 10M, il file  `.user.ini` deve contenere il testo seguente:

		; Example Settings
		display_errors=On
		upload_max_filesize=10M

3. Distribuire l'app Web.
4. Riavviare l'app Web . Il riavvio è necessario perché la frequenza con cui PHP legge i file  `.user.ini` è regolata dall'impostazione  `user_ini.cache_ttl`, configurata a livello di sistema, che per impostazione predefinita corrisponde a 300 secondi (5 minuti). Il riavvio del sito forza PHP a leggere le nuove impostazioni nel file  `.user.ini`.

Come alternativa all'uso di un file  `.user.ini`, è possibile utilizzare la funzione [ini_set()] negli script per impostare le opzioni di configurazione che non sono direttive a livello di sistema.

## Procedura: Abilitare le estensioni nel runtime PHP predefinito
Come indicato nella sezione precedente, il modo migliore per visualizzare la versione PHP disponibile, la sua configurazione predefinita e le estensioni abilitate consiste nel distribuire uno script che chiama la funzione [phpinfo()]. Per abilitare le estensioni aggiuntive, eseguire la procedura seguente.

1. Aggiungere una directory  `bin` alla directory radice.
2. Inserire i file con estensione  `.dll` nella directory  `bin` (ad esempio,  `php_mongo.dll`). Assicurarsi che le estensioni siano compatibili con la versione predefinita di PHP (la quale è, al momento della stesura di questo documento, PHP 5.4) e che siano compatibili con VC9 e non thread-safe (nts).
3. Distribuire l'app Web.
4. Passare all'app Web nel Portale di Azure e fare clic sul pulsante **Impostazioni**.

	![Web App Settings][pulsante-impostazioni]

5. Dal pannello **Impostazioni** selezionare **Impostazioni dell'applicazione** e scorrere fino alla sezione **Impostazioni app**.
6. Nella sezione **Impostazioni app** creare una chiave **PHP_EXTENSIONS**. Il valore di questa chiave deve corrispondere a un percorso relativo alla radice del sito Web: **bin\your-ext-file**.

	![Enable extension in app settings][php-extensions]

7. Fare clic sul pulsante **Salva** all'inizio del pannello **Impostazioni app Web**.

	![Save configuration settings][pulsante-salva]

Con l'utilizzo di una chiave **PHP_ZENDEXTENSIONS** sono supportate anche le estensioni Zend. Per abilitare più estensioni, includere un elenco separato da virgole di file  `.dll` per il valore dell'impostazione dell'app.

## Procedura: Usare un runtime PHP personalizzato
Invece del runtime PHP predefinito, App Web di Servizio Web può utilizzare un runtime PHP fornito dall'utente per l'esecuzione degli script PHP. Tale runtime può essere configurato da un file  `php.ini` fornito dall'utente. Per usare un runtime PHP personalizzato con App Web, attenersi alla procedura seguente.

1. Ottenere una versione compatibile con VC9 e non-thread-safe di PHP per Windows. Le versioni recenti di PHP per Windows sono disponibili all'indirizzo seguente: [http://windows.php.net/download/]. Le versioni precedenti sono archiviate all'indirizzo seguente: [http://windows.php.net/downloads/releases/archives/].
2. Modificare il file  `php.ini` per il runtime. Si noti che qualsiasi impostazione di configurazione che non sia una direttiva solo a livello di sistema verrà ignorata da App Web  (per informazioni sulle direttive solo a livello di sistema, vedere la [Lista delle direttive php.ini]).
3. Facoltativamente, aggiungere le estensioni al proprio runtime PHP e abilitarle nel file  `php.ini`.
4. Aggiungere una directory  `bin` alla propria directory radice e inserirvi la directory contenente il proprio runtime PHP (ad esempio,  `bin\php`).
5. Distribuire l'app Web.
4. Passare all'app Web nel Portale di Azure e fare clic sul pulsante **Impostazioni**.

	![Web App Settings][pulsante-impostazioni]

7. Dal pannello **Impostazioni** selezionare **Impostazioni dell'applicazione** e scorrere fino alla sezione **Mapping dei gestori**. Aggiungere `*.php` al campo Estensione e aggiungere il percorso dell'eseguibile  `php-cgi.exe`. Se si inserisce il proprio runtime PHP nella directory  `bin` nella radice dell'applicazione,  il percorso sarà  `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

	![Specify handler in hander mappings][handler-mappings]

8. Fare clic sul pulsante **Salva** all'inizio del pannello **Impostazioni app Web**.

	![Save configuration settings][pulsante-salva]

>[AZURE.NOTE] Per iniziare a usare il servizio app di Azure prima di registrare un account di Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751) dove è possibile creare immediatamente un'app Web di base e temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere: [Il servizio app di Azure e il suo impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida relativa al passaggio dal vecchio al nuovo portale, vedere: [Riferimenti per esplorare il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

[versione di valutazione gratuita]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Lista delle direttive php.ini]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[impostazioni-applicazione]: ./media/web-sites-php-configure/application-settings.png
[pulsante-impostazioni]: ./media/web-sites-php-configure/settings-button.png
[pulsante-salva]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/

<!--HONumber=49-->