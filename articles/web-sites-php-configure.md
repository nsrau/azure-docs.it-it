<properties title="How to Configure PHP in Azure Web Sites" pageTitle="How to Configure PHP in Azure Web Sites" metaKeywords="Azure, Azure Web Sites, configuration, PHP" description="Learn how to configure the default PHP installation or add a custom PHP installation in Azure Web Sites." services="Web Sites" documentationCenter="PHP" authors="" />

Come configurare PHP in Siti Web di Azure
=========================================

In questa guida verrà descritto come configurare il runtime PHP incorporato in Siti Web di Azure, fornire un runtime PHP personalizzato e abilitare le estensioni in Siti Web di Azure. Per utilizzare Siti Web di Azure effettuare l'iscrizione per ricevere la [versione di valutazione gratuita](https://www.windowsazure.com/en-us/pricing/free-trial/). Per ottenere il massimo da questa guida è consigliabile creare prima un sito PHP in Siti Web di Azure (vedere le [esercitazioni del Centro sviluppatori PHP](https://www.windowsazure.com/en-us/develop/php/tutorials/)). Per informazioni generali sulla configurazione dei siti in Siti Web di Azure vedere [Come configurare i siti Web](https://www.windowsazure.com/en-us/manage/services/web-sites/how-to-configure-websites/).

Sommario
--------

-   [Informazioni su Siti Web di Azure](#WhatIs)
-   [Procedura: Modificare la configurazione PHP predefinita](#ChangeBuiltInPHP)
-   [Procedura: Abilitare le estensioni nel runtime PHP incorporato](#EnableExtDefaultPHP)
-   [Procedura: Utilizzare un runtime PHP personalizzato](#UseCustomPHP)
-   [Passaggi successivi](#NextSteps)

Informazioni su Siti Web di Azure
---------------------------------

Siti Web di Azure consente di creare siti Web altamente scalabili in Azure. È possibile distribuire i siti in modo rapido e semplice in un ambiente di cloud altamente scalabile, che consente di iniziare con soluzioni di piccole dimensioni e ridimensionarle con l'aumentare del traffico. Siti Web di Azure utilizza il linguaggio e le applicazioni open source preferiti dall'utente e supporta la distribuzione con Git, FTP e TFS. È possibile integrare facilmente altri servizi come MySQL, SQL Database, Caching, CDN e Archiviazione.

Procedura: Modificare la configurazione PHP incorporata
-------------------------------------------------------

Per impostazione predefinita, PHP 5.4 è installato e immediatamente disponibile per l'uso quando si crea un sito Web di Azure. Il modo migliore per visualizzare la revisione della versione disponibile, la sua configurazione predefinita e le estensioni abilitate consiste nel distribuire uno script che chiama la funzione [phpinfo ()](http://php.net/manual/en/function.phpinfo.php).

È inoltre disponibile PHP 5.5, che però non è abilitato per impostazione predefinita. Per abilitarlo, eseguire la procedura seguente:

1.  Fare clic su **Configure** sul dashboard del proprio sito Web nel portale di Azure.

    ![Scheda Configure sul dashboard di Siti Web](./media/web-sites-php-configure/configure.png)

2.  Fare clic su PHP 5.5.

    ![Selezionare la versione di PHP](./media/web-sites-php-configure/select-php-version.png)

3.  Fare clic su **Save** nella parte inferiore della pagina.

    ![Salvare le impostazioni di configurazione](./media/web-sites-php-configure/save-button.png)

Per ognuno dei runtime PHP incorporati, è possibile modificare qualsiasi opzione di configurazione che non sia una direttiva solo a livello di sistema attenendosi alla procedura seguente (per informazioni sulle direttive solo a livello di sistema, vedere la [Lista delle direttive php.ini](http://www.php.net/manual/en/ini.list.php)).

1.  Aggiungere un file [.user.ini](http://www.php.net/manual/en/configuration.file.per-user.php) alla directory radice in uso.
2.  Aggiungere le impostazioni di configurazione al file `.user.ini` utilizzando la stessa sintassi che si userebbe in un file `php.ini`. Ad esempio, se si desidera attivare l'impostazione `display_errors` e configurare l'impostazione `upload_max_filesize` su 10M, il file `.user.ini` conterrebbe il testo seguente:

         ; Example Settings
         display_errors=On
         upload_max_filesize=10M

3.  Distribuire l'applicazione.
4.  Riavviare il sito Web. Il riavvio è necessario in quanto la frequenza di lettura dei file `.user.ini` da parte di PHP è governata dall'impostazione `user_ini.cache_ttl` configurata a livello di sistema, che per impostazione predefinita corrisponde a 300 secondi (5 minuti). Il riavvio del sito forza PHP a leggere le nuove impostazioni nel file `.user.ini`.

In alternativa a un file `.user.ini` è possibile utilizzare la funzione [ini\_set()](http://www.php.net/manual/en/function.ini-set.php) negli script per impostare le opzioni di configurazione che non sono direttive a livello di sistema.

Procedura: Abilitare le estensioni nel runtime PHP predefinito
--------------------------------------------------------------

Come indicato nella sezione precedente, il modo migliore per visualizzare la versione PHP disponibile, la sua configurazione predefinita e le estensioni abilitate consiste nel distribuire uno script che chiama la funzione [phpinfo ()](http://php.net/manual/en/function.phpinfo.php). Per abilitare le estensioni aggiuntive, eseguire la procedura seguente.

1.  Aggiungere una directory `bin` alla directory radice.
2.  Inserire i file con estensione `dll` nella directory `bin` (ad esempio, `php_mongo.dll`). Assicurarsi che le estensioni siano compatibili con la versione predefinita di PHP (la quale è, al momento della stesura di questo documento, PHP 5.4) e che siano compatibili con VC9 e non thread-safe (nts).
3.  Distribuire l'applicazione.
4.  Fare clic su **Configure** sul dashboard del proprio sito Web sul portale di Azure.

    ![Scheda Configure sul dashboard di Siti Web](./media/web-sites-php-configure/configure.png)

5.  Nella sezione **app settings**, creare una chiave **PHP\_EXTENSIONS** e un valore **bin\\your-ext-file**. Per abilitare più estensioni, includere un elenco di file `DLL` con valori delimitati da virgole.

    ![Abilitare le estensioni nelle impostazioni app](./media/web-sites-php-configure/app-settings.png)

6.  Fare clic su **Save** nella parte inferiore della pagina.

    ![Salvare le impostazioni di configurazione](./media/web-sites-php-configure/save-button.png)

Procedura: Utilizzare un runtime PHP personalizzato
---------------------------------------------------

Invece del runtime PHP predefinito, Siti Web di Azure può utilizzare un runtime PHP fornito dall'utente per l'esecuzione degli script PHP. Quest'ultimo può essere configurato da un file `php.ini` analogamente fornito dall'utente. Per utilizzare un runtime PHP personalizzato in Siti Web di Azure, eseguire la procedura seguente.

1.  Ottenere una versione compatibile con VC9 e non-thread-safe di PHP per Windows. Le versioni recenti di PHO per Windows sono disponibili all'indirizzo seguente: <http://windows.php.net/download/>. Le versioni precedenti sono archiviate all'indirizzo seguente: <http://windows.php.net/downloads/releases/archives/>.
2.  Modificare il file `php.ini` per il proprio runtime. Si noti che qualsiasi impostazione di configurazione che non sia una direttiva solo a livello di sistema verrà ignorata da Siti Web di Azure (per informazioni sulle direttive solo a livello di sistema, vedere la [Lista delle direttive php.ini](http://www.php.net/manual/en/ini.list.php)).
3.  Facoltativamente, aggiungere le estensioni al proprio runtime PHP e abilitarle nel file `php.ini`.
4.  Aggiungere la directory `bin` alla propria directory radice e inserirvi la directory contenente il proprio runtime PHP (ad esempio, `bin\php`).
5.  Distribuire l'applicazione.
6.  Fare clic su **Configure** sul dashboard del proprio sito Web sul portale di Azure.

    ![Scheda Configure sul dashboard di Siti Web](./media/web-sites-php-configure/configure.png)

7.  Nella sezione **handler mappings** aggiungere `*.php` a EXTENSION e quindi aggiungere il percorso al file eseguibile `php-cgi.exe`. Se si inserisce il proprio runtime PHP nella directory `bin` nella radice dell'applicazione il relativo percorso sarà `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

    ![Specificare il gestore nei mapping gestore](./media/web-sites-php-configure/handler-mappings.png)

8.  Fare clic su **Save** nella parte inferiore della pagina.

    ![Salvare le impostazioni di configurazione](./media/web-sites-php-configure/save-button.png)

Passaggi successivi
-------------------

A questo punto, dopo aver appreso come configurare PHP in Siti Web di Azure, selezionare i collegamenti seguenti per informazioni su ulteriori attività.

-   [Configurazione, monitoraggio e ridimensionamento dei siti Web in Azure](http://www.windowsazure.com/en-us/manage/services/web-sites/)
-   [Download di Azure SDK per PHP](http://www.windowsazure.com/en-us/develop/php/common-tasks/download-php-sdk/)

