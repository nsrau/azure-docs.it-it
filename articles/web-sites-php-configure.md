<properties title="How to Configure PHP in Azure Websites" pageTitle="Come configurare PHP in Siti Web di Azure metaKeywords="Azure, Siti Web di Azure, configurazione, PHP" description="Learn how to configure the default PHP installation or add a custom PHP installation in Azure Websites." services="Web Sites" documentationCenter="PHP" authors="cephalin" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

#Come configurare PHP in Siti Web di Azure

In questa guida verrà descritto come configurare il runtime PHP incorporato in Siti Web di Azure, fornire un runtime PHP personalizzato e abilitare le estensioni in Siti Web di Azure. Per usare Siti Web di Azure, effettuare l'iscrizione per ricevere la [versione di valutazione gratuita]. Per ottenere il massimo da questa guida è consigliabile creare prima un sito PHP in Siti Web di Azure (vedere le [esercitazioni del Centro sviluppatori PHP]). Per informazioni generali sulla configurazione dei siti in Siti Web di Azure vedere [Come configurare i siti Web].

##Sommario

* [Informazioni su Siti Web di Azure](#WhatIs)
* [Procedura: Modificare la configurazione PHP predefinita](#ChangeBuiltInPHP)
* [Procedura: Abilitare le estensioni nel runtime PHP incorporato](#EnableExtDefaultPHP)
* [Procedura: Usare un runtime PHP personalizzato](#UseCustomPHP)
* [Passaggi successivi](#NextSteps)

<h2><a name="WhatIs"></a>Informazioni su Siti Web di Azure</h2>
Siti Web di Azure consente di creare siti Web altamente scalabili in Azure. È possibile distribuire i siti in modo rapido e semplice in un ambiente di cloud altamente scalabile, che consente di iniziare con soluzioni di piccole dimensioni e ridimensionarle con l'aumentare del traffico. Siti Web di Azure usa il linguaggio e le applicazioni open source preferiti dall'utente e supporta la distribuzione con Git, FTP e TFS. È possibile integrare facilmente altri servizi come MySQL, SQL Database, Caching, CDN e Archiviazione.

<h2><a name="ChangeBuiltInPHP"></a>Procedura: Modificare la configurazione PHP incorporata</h2>
Per impostazione predefinita, PHP 5.4 è installato e immediatamente disponibile per l'uso quando si crea un sito Web di Azure. Il modo migliore per visualizzare la revisione della versione disponibile, la sua configurazione predefinita e le estensioni abilitate consiste nel distribuire uno script che chiama la funzione [phpinfo()].

È inoltre disponibile PHP 5.5, che però non è abilitato per impostazione predefinita. Per abilitarlo, eseguire la procedura seguente:

1. Nel portale di Azure fare clic su **Configura**. sul dashboard del proprio sito Web.

	![Configure tab on Web Sites dashboard][configure]

1. Fare clic su PHP 5.5.

	![Select PHP version][select-php-version]

1. Fare clic su **Salva** nella parte inferiore della pagina.

	![Save configuration settings][save-button]

Per ognuno dei runtime PHP incorporati, è possibile modificare qualsiasi opzione di configurazione che non sia una direttiva solo a livello di sistema attenendosi alla procedura seguente (per informazioni sulle direttive solo a livello di sistema, vedere la [Lista delle direttive php.ini]).

1. Aggiungere un file [.user.ini] alla directory radice in uso.
1. Aggiungere le impostazioni di configurazione al file ".user.ini" usando la stessa sintassi che si userebbe in un file "php.ini". Ad esempio, per attivare l'impostazione "display_errors" e configurare l'impostazione "upload_max_filesize" su 10M, il file ".user.ini" dovrebbe contenere il testo seguente:

		; Example Settings
		display_errors=On
		upload_max_filesize=10M

1. Distribuire l'applicazione.
1. Riavviare il sito Web. Il riavvio è necessario in quanto la frequenza di lettura dei file ".user.ini" da parte di PHP è governata dall'impostazione "user_ini.cache_ttl" configurata a livello di sistema, che per impostazione predefinita corrisponde a 300 secondi (5 minuti). Il riavvio del sito forza PHP a leggere le nuove impostazioni nel file ".user.ini".

Come alternativa a un file ".user.ini" è possibile usare la funzione [ini_set()] negli script per impostare le opzioni di configurazione che non sono direttive a livello di sistema.

<h2><a name="EnableExtDefaultPHP"></a>Procedura: Abilitare le estensioni nel runtime PHP predefinito</h2>
Come indicato nella sezione precedente, il modo migliore per visualizzare la versione PHP disponibile, la sua configurazione predefinita e le estensioni abilitate consiste nel distribuire uno script che chiama la funzione [phpinfo()]. Per abilitare le estensioni aggiuntive, eseguire la procedura seguente.

1. Aggiungere una directory "bin" alla directory radice.
1. Inserire i file con estensione "dll" nella directory bin (ad esempio, "php_mongo.dll"). Assicurarsi che le estensioni siano compatibili con la versione predefinita di PHP (la quale è, al momento della stesura di questo documento, PHP 5.4) e che siano compatibili con VC9 e non thread-safe (nts).
1. Distribuire l'applicazione.
1. Passare al dashboard del proprio sito Web nel portale di Azure e fare clic su **Configura**.

	![Configure tab on Web Sites dashboard][configure]

1. Nella sezione **Impostazioni app**, creare una chiave **PHP_EXTENSIONS** e un valore **bin\your-ext-file**. Per abilitare più estensioni, includere un elenco di file ".dll" con valori delimitati da virgole.

	![Enable extension in app settings][app-settings]

1. Fare clic su **Salva** nella parte inferiore della pagina.

	![Save configuration settings][save-button]

<h2><a name="UseCustomPHP"></a>Procedura: Usare un runtime PHP personalizzato</h2>
Invece del runtime PHP predefinito, Siti Web di Azure può usare un runtime PHP fornito dall'utente per l'esecuzione degli script PHP. Quest'ultimo può essere configurato da un file "php.ini" anch'esso fornito dall'utente. Per usare un runtime PHP personalizzato in Siti Web di Azure, eseguire la procedura seguente.

1. Ottenere una versione compatibile con VC9 e non-thread-safe di PHP per Windows. Le versioni recenti di PHP per Windows sono disponibili all'indirizzo seguente: [http://windows.php.net/download/]. Le versioni precedenti sono archiviate all'indirizzo seguente: [http://windows.php.net/downloads/releases/archives/].
1. Modificare il file "php.ini" per il proprio runtime. Si noti che qualsiasi impostazione di configurazione che non sia una direttiva solo a livello di sistema verrà ignorata da Siti Web di Azure (per informazioni sulle direttive solo a livello di sistema, vedere la [Lista delle direttive php.ini]).
1. Facoltativamente, aggiungere le estensioni al proprio runtime PHP e abilitarle nel "file php.ini".
1. Aggiungere la directory "bin" alla propria directory radice e inserirvi la directory contenente il proprio runtime PHP (ad esempio, "bin\php").
1. Distribuire l'applicazione.
1. Passare al dashboard del proprio sito Web nel portale di Azure e fare clic su **Configura**.

	![Configure tab on Web Sites dashboard][configure]

1. Nella sezione **mapping handler** aggiungere "*.php" a ESTENSIONE e quindi aggiungere il percorso al file eseguibile "php-cgi.exe". Se si inserisce il proprio runtime PHP nella directory "bin" nella radice dell'applicazione il relativo percorso sarà "D:\home\site\wwwroot\bin\php\php-cgi.exe".

	![Specify handler in hander mappings][handler-mappings]

1. Fare clic su **Salva** nella parte inferiore della pagina.

	![Save configuration settings][save-button]

<h2><a name="NextSteps"></a>Passaggi successivi</h2>
A questo punto, dopo aver appreso come configurare PHP in Siti Web di Azure, selezionare i collegamenti seguenti per informazioni su altre attività.

- [Configurare, monitorare e scalare i siti Web in Azure]
- [Download di Azure SDK per PHP]


[versione di valutazione gratuita]: https://www.windowsazure.com/it-it/pricing/free-trial/
[Esercitazioni del Centro sviluppatori PHP]: https://www.windowsazure.com/it-it/develop/php/tutorials/
[Come configurare i siti Web]: https://www.windowsazure.com/it-it/manage/services/web-sites/how-to-configure-websites/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[List of php.ini directives]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[configure]: ./media/web-sites-php-configure/configure.png
[app-settings]: ./media/web-sites-php-configure/app-settings.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[Configurare, monitorare e scalare i siti Web in Azure]: http://www.windowsazure.com/it-it/manage/services/web-sites/
[Download di Azure SDK per PHP]: http://www.windowsazure.com/it-it/develop/php/common-tasks/download-php-sdk/
