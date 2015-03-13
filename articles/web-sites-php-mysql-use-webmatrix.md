<properties 
	pageTitle="Sito Web PHP con MySQL e WebMatrix - Esercitazione di Azure" 
	description="Un'esercitazione che illustra come usare l'IDE di WebMatrix gratuito per creare e distribuire un sito Web PHP che archivia i dati in MySQL." 
	services="web-sites" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/14/2014" 
	ms.author="tomfitz"/>





#Creare e distribuire un sito Web di Azure PHP-MySQL tramite WebMatrix

In questa esercitazione viene illustrato come usare WebMatrix per sviluppare e distribuire un'applicazione PHP-MySQ in un sito Web di Azure. WebMatrix è uno strumento di sviluppo Web gratuito di Microsoft che include tutto quanto necessario per lo sviluppo di siti Web. WebMatrix supporta PHP e comprende IntelliSense per lo sviluppo di PHP.

A tale scopo, si presuppone che nel computer sia installato [MySQL][install-mysql], in modo che sia possibile testare un'applicazione in locale. È tuttavia possibile completare l'esercitazione senza aver installato MySQL. L'applicazione può infatti essere distribuita direttamente in Siti Web di Azure.

Dopo aver completato questa guida, si disporrà di un sito Web PHP-MySQL in esecuzione in Azure.
 
Si apprenderà come:

* Creare un sito Web di Azure e un database MySQL mediante il portale di gestione di Azure. Poiché PHP è abilitato in Siti Web di Azure per impostazione predefinita, non è necessario completare operazioni speciali per eseguire il codice PHP.
* Sviluppare un'applicazione PHP con WebMatrix.
* Come pubblicare e ripubblicare l'applicazione in Azure con WebMatrix.
 
Seguendo questa esercitazione, verrà creata una semplice applicazione Web Tasklist in PHP, ospitata in un sito Web di Azure. Di seguito è riportata una schermata dell'applicazione in esecuzione:

![Azure PHP Web Site][running-app]

> [AZURE.NOTE]
> Per completare l'esercitazione, è necessario un account Azure. È possibile <a href="http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/">attivare i benefici della sottoscrizione MSDN</a> o <a href="http://azure.microsoft.com/pricing/free-trial/">iscriversi per una versione di valutazione gratuita</a>.
> 
> Per iniziare a usare Siti Web di Azure prima di iscriversi per ottenere un account, visitare la pagina all'indirizzo <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>, in cui è possibile creare immediatamente e gratuitamente un sito di base ASP.NET temporaneo in Siti Web di Azure. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

##Prerequisiti

1. [Scaricare][tasklist-mysql-download] i file dell'applicazione Tasklist. Si tratta di una semplice applicazione PHP che consente di aggiungere, contrassegnare come completi e/o eliminare gli elementi in un elenco attività. Gli elementi dell'elenco attività sono archiviate in un database MySQL. L'applicazione è costituita dai file seguenti:

	* **additem.php**: consente di aggiungere un elemento all'elenco.
	* **createtable.php**: consente di creare la tabella MySQL per l'applicazione. Questo file verrà chiamato una volta sola.
	* **deleteitem.php**: consente di eliminare un oggetto.
	* **getitems.php**: consente di ottenere tutti gli elementi del database.
	* **index.php**: consente di visualizzare le attività e fornisce un modulo per l'aggiunta di un elemento all'elenco.
	* **markitemcomplete.php**: consente di modificare lo stato di un elemento come completo.
	* **taskmodel.php**: contiene funzioni per aggiungere, ottenere, aggiornare ed eliminare gli elementi dal database.

1. Creare un database MySQL locale denominato  `tasklist`. È possibile eseguire questa operazione dall'area di lavoro Database in WebMatrix (dopo averlo installato come descritto nell'esercitazione riportata di seguito) o dal prompt dei comandi di MySQL con il comando seguente:

		mysql> create database tasklist;

	Questo passaggio è necessario solo se si desidera testare l'applicazione localmente.

<h2><a id="CreateWebsite"></a>Creare un sito Web di Azure e un database MySQL</h2>

1. Accedere al [portale di gestione][preview-portal].
1. Fare clic sull'icona **+ Nuovo** nella parte inferiore sinistra del portale.

	![Create New Azure Web Site][NewWebSite1]

1. Fare clic su **SITO WEB** e quindi su **CREAZIONE PERSONALIZZATA**.

	![Custom Create a new Web Site][NewWebSite2]

	> [AZURE.NOTE]
	> Dopo avere creato un sito Web non è possibile creare un database MySQL per tale sito. È necessario creare un sito Web e un database MySQL come descritto nella procedura seguente.

1. Immettere un valore per **URL**, scegliere **Crea un nuovo database MySQL** dall'elenco a discesa **DATABASE**, quindi selezionare il data center per il sito Web nell'elenco a discesa **AREA**. Fare clic sulla freccia nella parte inferiore della finestra di dialogo.

	![Fill in web site details][NewWebSite3]

5. In **NOME** immettere un valore per il database, selezionare il data center per il database nell'elenco a discesa **AREA**, quindi selezionare la casella che indica che si accettano le note legali. Fare clic sul segno di spunta nella parte inferiore della finestra di dialogo.

	![Create new MySQL database][NewWebSite4]

	Dopo la creazione del sito Web verrà visualizzato il messaggio **La creazione sito Web '[NOMESITO]' è riuscita**.

	Sarà quindi necessario recuperare le informazioni sulla connessione a MySQL.


6. Fare clic sul nome del sito Web visualizzato nell'elenco dei siti per aprire la pagina Avvio rapido.

	![Open web site dashboard][NewWebSite5]

7. Fare clic sulla scheda **CONFIGURA**.

	![Configure tab][NewWebSite6]

8. Scorrere verso il basso fino alla sezione **stringhe di connessione**. I valori per `Database`, `Data Source`, `User Id` e  `Password` sono rispettivamente il nome del database, il nome del server, il nome utente e la password utente. Prendere nota delle informazioni sulla connessione al database perché saranno necessarie in seguito.

	![Connection string][ConnectionString]

##Installazione di WebMatrix e sviluppo dell'applicazione

È possibile installare WebMatrix dal [portale di gestione][preview-portal]. 

1. Dopo aver eseguito l'accesso, aprire la pagina Avvio rapido del sito Web e fare clic sull'icona WebMatrix nella parte inferiore della pagina:

	![Install WebMatrix][InstallWebMatrix]

	Per installare WebMatrix, attenersi alle istruzioni visualizzate.

2. Al termine dell'installazione di WebMatrix, verrà effettuato un tentativo di apertura del sito come progetto WebMatrix. È possibile scegliere di modificare direttamente il sito attivo oppure scaricare una copia in locale. Per questa esercitazione, selezionare  'Edit local copy'. 

3. Quando viene richiesto di scaricare il sito, scegliere **Sì, installa dalla raccolta di modelli**.

	![Download web site][download-site]

4. Scegliere **PHP** dai modelli disponibili.

	![Site from template][site-from-template]

5. Selezionare il modello **Sito vuoto**. Specificare un nome per il sito e fare clic su **AVANTI**.

	![Provide name for site][site-from-template-2]

	Il sito verrà aperto in WebMatrix con alcuni file predefiniti.

	Nei passaggi successivi verrà sviluppata l'applicazione Tasklist mediante l'aggiunta dei file precedentemente scaricati e l'esecuzione di alcune modifiche. È tuttavia possibile aggiungere i propri file esistenti o crearne di nuovi.

6. Fare clic su **Aggiungi esistente** per aggiungere i file dell'applicazione:

	![WebMatrix - Add existing files][edit_addexisting]

	Nella finestra di dialogo risultante, passare ai file scaricati in precedenza, selezionarli tutte e fare clic su Open. Quando richiesto, scegliere di sostituire il file  `index.php`. 

7. Sarà quindi necessario aggiungere al file  `taskmodel.php` le informazioni sulla connessione al database MySQL locale. Fare doppio clic sul file  `taskmodel.php` per aprirlo e aggiornare le informazioni di connessione al database nella funzione  `connect`. **Nota**: passare alla sezione [Pubblicare l'applicazione](#Publish) nel caso in cui non si voglia testare l'applicazione localmente e si intenda pubblicarla direttamente in Siti Web di Azure.

		// DB connection info
		$host = "localhost";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Salvare il file  `taskmodel.php`.

8. Per eseguire l'applicazione è necessario creare la tabella  `items`. Fare clic con il pulsante destro del mouse sul file  `createtable.php` e scegliere **Avvia nel browser**. Il file  `createtable.php` verrà aperto nel browser e verrà eseguito il codice che consente di creare la tabella  `items` nel database  `tasklist`.

	![WebMatrix - Launch createtable.php in browser][edit_run]

9. A questo punto è possibile testare l'applicazione in locale. Fare clic con il pulsante destro del mouse sul file  `index.php` e scegliere **Avvia nel browser**. Testare l'applicazione aggiungendo degli elementi, contrassegnandoli come completi ed eliminandoli.  


<h2><a id="Publish"></a>Pubblicare l'applicazione</h2>

Prima di pubblicare l'applicazione in Siti Web di Azure, è necessario aggiornare le informazioni di connessione al database nel file  `taskmodel.php` con le informazioni di connessione ottenute in precedenza (nella sezione [Creare un sito Web di Azure e un database MySQL](#CreateWebsite) ).

1. Fare doppio clic sul file  `taskmodel.php` per aprirlo e aggiornare le informazioni di connessione al database nella funzione  `connect`.

		// DB connection info
		$host = "value of Data Source";
		$user = "value of User Id";
		$pwd = "value of Password";
		$db = "value of Database";
	
	Save the `taskmodel.php` file.

2. Fare clic su **Pubblica** in WebMatrix e quindi fare clic su **Continua** nella finestra di dialogo **Anteprima pubblicazione**.

	![WebMatrix - Publish][edit_publish]

3. Passare a http://[nome del sito Web].azurewebsites.net/createtable.php per creare la tabella  `items`.

4. Infine, passare a http://[nome del sito Web].azurewebsites.net/index.php  per usare l'applicazione.
	
##Modifica e ripubblicazione dell'applicazione

Per modificare facilmente l'applicazione apportare le modifiche alla copia locale del sito scaricata in precedenza e ripubblicarla oppure modificarla direttamente in modalità remota. In questo modo sarà possibile apportare una semplice modifica all'intestazione del file  `index.php` salvandolo direttamente sul sito attivo.

1. Fare clic sulla scheda Remoto del sito in WebMatrix e selezionare **Apri visualizzazione remota**. Verrà aperto il sito remoto per le modifiche dirette.
	 ![WebMatrix - Open Remote View][OpenRemoteView]
 
2. Fare doppio clic sul file  `index.php` per aprirlo.
	![WebMatrix - Open index file][Remote_editIndex]

3. Modificare **My ToDo List** in **My Task List** nei tag **title** e **h1**, quindi salvare il file.


4. Al termine del salvataggio, fare clic sul pulsante Run per visualizzare le modifiche sul sito attivo.
	![WebMatrix - Launch site in Remote][Remote_run]


# Passaggi successivi

Si è appreso come creare e distribuire un sito Web da WebMatrix in Azure. Per altre informazioni su WebMatrix, fare riferimento alle risorse seguenti:

* [WebMatrix per Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [Sito Web di WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[running-app]: ./media/web-sites-php-mysql-use-webmatrix/tasklist_app_windows.png
[tasklist-mysql-download]: http://go.microsoft.com/fwlink/?LinkId=252506
[NewWebSite1]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite1.jpg
[NewWebSite2]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite2.png
[NewWebSite3]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite3.png
[NewWebSite4]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite4.png
[NewWebSite5]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite5.png
[NewWebSite6]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite6.png
[ConnectionString]: ./media/web-sites-php-mysql-use-webmatrix/ConnectionString.png
[InstallWebMatrix]: ./media/web-sites-php-mysql-use-webmatrix/InstallWebMatrix.png
[download-site]: ./media/web-sites-php-mysql-use-webmatrix/download-site-1.png
[site-from-template]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template.png
[site-from-template-2]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template-2.png
[edit_addexisting]: ./media/web-sites-php-mysql-use-webmatrix/edit_addexisting.png
[edit_run]: ./media/web-sites-php-mysql-use-webmatrix/edit_run.png
[edit_publish]: ./media/web-sites-php-mysql-use-webmatrix/edit_publish.png
[OpenRemoteView]: ./media/web-sites-php-mysql-use-webmatrix/OpenRemoteView.png
[Remote_editIndex]: ./media/web-sites-php-mysql-use-webmatrix/Remote_editIndex.png
[Remote_run]: ./media/web-sites-php-mysql-use-webmatrix/Remote_run.png













[preview-portal]: https://manage.windowsazure.com















<!--HONumber=42-->
