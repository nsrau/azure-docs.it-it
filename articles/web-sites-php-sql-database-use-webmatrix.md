<properties urlDisplayName="Web w/ SQL + WebMatrix" pageTitle="Sito Web PHP con database SQL e WebMatrix - Esercitazione su Azure" metaKeywords="" description="A tutorial that demonstrates how to use the free WebMatrix IDE to create and deploy a PHP website that stores data in SQL Database." metaCanonical="" services="" documentationCenter="" title="Create and Deploy a PHP Website and SQL Database using WebMatrix" authors="cephalin" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />





#Creare e distribuire un sito Web PHP e un database SQL con WebMatrix

In questa esercitazione viene illustrato come usare WebMatrix per sviluppare e distribuire un'applicazione PHP che usa un database SQL di Azure in un sito Web di Azure. WebMatrix è uno strumento di sviluppo Web gratuito di Microsoft che include tutto quanto necessario per lo sviluppo di siti Web. WebMatrix supporta PHP e comprende IntelliSense per lo sviluppo di PHP. 

In questa esercitazione si presuppone che l'utente abbia installato [SQL Server Express][install-SQLExpress] nel computer in modo che sia possibile testare un'applicazione in locale. È tuttavia possibile completare l'esercitazione senza aver installato SQL Server Express. L'applicazione può infatti essere distribuita direttamente in Siti Web di Azure.

Dopo avere completato questa guida, si disporrà di un database SQL-PHP in esecuzione in Azure.
 
Si apprenderà come:

* Creare un sito Web di Azure e un database SQL usando il portale di gestione. Poiché PHP è abilitato in Siti Web di Azure per impostazione predefinita, non è necessario completare operazioni speciali per eseguire il codice PHP.
* Come sviluppare un'applicazione PHP con WebMatrix.
* Come pubblicare e ripubblicare l'applicazione in Azure con WebMatrix.
 
Seguendo questa esercitazione, verrà creata una semplice applicazione Web Tasklist in PHP, ospitata in un sito Web di Azure. Di seguito è riportata una schermata dell'applicazione in esecuzione:

![Azure PHP Web Site][running-app]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##Prerequisiti

1. [Scaricare][tasklist-sqlazure-download] i file dell'applicazione Tasklist. Si tratta di una semplice applicazione PHP che consente di aggiungere e/o eliminare gli elementi da un elenco attività oppure di contrassegnarli come completi. Gli elementi dell'elenco attività sono memorizzati in un database SQL (SQL Server Express per test locali). L'applicazione è costituita dai file seguenti:

* **index.php**: consente di visualizzare le attività e fornisce un modulo per l'aggiunta di un elemento all'elenco.
* **additem.php**: consente di aggiungere un elemento all'elenco.
* **getitems.php**: consente di ottenere tutti gli elementi del database.
* **markitemcomplete.php**: consente di modificare lo stato di un elemento come completo.
* **deleteitem.php**: consente di eliminare un oggetto.
* **taskmodel.php**: contiene funzioni per aggiungere, ottenere, aggiornare ed eliminare gli elementi dal database.
* **createtable.php**: consente di creare la tabella di database SQL per l'applicazione. Questo file verrà chiamato una volta sola.

2. Creare un database di SQL Server denominato "tasklist". A tale scopo, immettere al prompt dei comandi "sqlcmd" i comandi seguenti:

		>sqlcmd -S <server name>\sqlexpress -U <user name> -P <password>
		1> create database tasklist
		2> GO

	Questo passaggio è necessario solo se si desidera testare l'applicazione localmente.

## Creare un sito Web e un database SQL

1. Accedere al [portale di gestione][preview-portal].
2. Fare clic sull'icona **+ Nuovo** nella parte inferiore sinistra del portale.

	![Create New Azure Web Site][NewWebSite1]

3. Fare clic su **SITO WEB** e quindi su **CREAZIONE PERSONALIZZATA**.

	![Custom Create a new Web Site][NewWebSite2]

	Immettere un valore per **URL**, scegliere **Crea un nuovo database SQL** nell'elenco a discesa **DATABASE**, quindi selezionare il data center per il sito Web nell'elenco a discesa **AREA**. Fare clic sulla freccia nella parte inferiore della finestra di dialogo.

	![Fill in web site details][NewWebSite3_SQL]

4. Immettere un valore per il nome del database in **NOME** e selezionare **Nuovo server di database SQL**. Immettere un nome utente e una password per l'accesso al server, confermare la password e scegliere l'area in cui verrà creato il nuovo server di database SQL.

	![Fill in SQL Database settings][NewWebSite4_SQL]

	Dopo la creazione del sito Web verrà visualizzato il messaggio **Creazione del sito Web "[NOMESITO]" completata**. Verranno quindi recuperate le informazioni sulla connessione al database.

5. Fare clic su **RISORSE COLLEGATE**, quindi sul nome del database.

	![Linked Resources][NewWebSite6_SQL]

6. Fare clic su **Visualizza stringhe di connessione**.

	![Connection string][NewWebSite7]
	
Nella sezione **PHP** della finestra di dialogo risultante prendere nota dei valori per "UID", "PWD", "Database" e "$serverName". Queste informazioni verranno usate in seguito.

##Installazione di WebMatrix

È possibile installare WebMatrix dal [portale di gestione][preview-portal]. 

1. Dopo aver eseguito l'accesso, aprire la pagina avvio rapido del sito Web e fare clic sull'icona WebMatrix nella parte inferiore della pagina:

	![Install WebMatrix][InstallWebMatrix]

	Per installare WebMatrix, attenersi alle istruzioni visualizzate.

2. Al termine dell'installazione di WebMatrix, verrà effettuato un tentativo di apertura del sito come progetto WebMatrix. È possibile scegliere di modificare direttamente il sito attivo oppure scaricare una copia in locale. Per questa esercitazione, selezionare "Edit local copy". 

3. Quando viene richiesto di scaricare il sito, scegliere **Sì, installa dalla raccolta di modelli**.

	![Download web site][download-site]

4. Scegliere **PHP** dai modelli disponibili.

	![Site from template][site-from-template]

5. Selezionare il modello **Sito vuoto**. Specificare un nome per il sito e fare clic su **SUCCESSIVO**.

	![Provide name for site][site-from-template-2]

Il sito verrà aperto in WebMatrix con alcuni file predefiniti.

##Sviluppo dell'applicazione

Nei passaggi successivi verrà sviluppata l'applicazione Tasklist mediante l'aggiunta dei file precedentemente scaricati e l'esecuzione di alcune modifiche. È tuttavia possibile aggiungere i propri file esistenti o crearne di nuovi.

1. Tenendo il sito aperto in WebMatrix, fare clic su **Aggiungi esistente** per aggiungere i file applicazione:

	![WebMatrix - Add existing files][edit_addexisting]

	Nella finestra di dialogo risultante, passare ai file scaricati in precedenza, selezionarli tutte e fare clic su Open. Quando richiesto, scegliere di sostituire il file "index.php". 

2. Sarà quindi necessario aggiungere le informazioni di connessione al database SQL Server locale al file "taskmodel.php". Fare doppio clic sul file "taskmodel.php" per aprirlo e aggiornare le informazioni di connessione al database nella funzione "connect". **Nota**: passare alla sezione [Pubblicazione dell'applicazione](#Publish) nel caso in cui non voglia testare l'applicazione localmente e si intenda pubblicarla direttamente in Siti Web di Azure.

		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Salvare il file "taskmodel.php".

3. Affinché sia possibile eseguire l'applicazione sarà necessario creare la tabella "items". Fare clic con il pulsante destro del mouse sul file "createtable.php" e scegliere **Avvia in browser**. Il file createtable.php verrà aperto nel browser e verrà eseguito il codice che consente di creare la tabella "items" nel database "tasklist".

	![WebMatrix - Launch createtable.php in browser][edit_run]

4. A questo punto è possibile testare l'applicazione in locale. Fare clic con il pulsante destro del mouse sul file "index.php" e scegliere **Avvia in browser**. Testare l'applicazione aggiungendo degli elementi, contrassegnandoli come completi ed eliminandoli.   


<h2><a id="Publish"></a>Pubblicare l'applicazione</h2>

Prima di pubblicare l'applicazione in Siti Web di Azure, è necessario aggiornare le informazioni di connessione al database nel file "taskmodel.php" con le informazioni di connessione ottenute in precedenza (nella sezione [Creare un sito Web di Azure e un database SQL](#CreateWebsite) ).

1. Fare doppio clic sul file "taskmodel.php" per aprirlo e aggiornare le informazioni di connessione al database nella funzione "connect".

		// DB connection info
		$host = "value of $serverName";
		$user = "value of UID";
		$pwd = "the SQL password you created when creating the website";
		$db = "value of Database";
	
	Save the `taskmodel.php` file.

2. Fare clic su **Pubblica** in WebMatrix e quindi fare clic **Continua** nella finestra di dialogo **Anteprima pubblicazione**.

	![WebMatrix - Publish][edit_publish]

3. Passare a http://[nome del sito Web].azurewebsites.net/createtable.php per creare la tabella "items".

4. Infine, passare a http://[nome del sito Web].azurewebsites.net/index.php per avviare l'applicazione.
	
##Modifica e ripubblicazione dell'applicazione

Per modificare facilmente l'applicazione apportare le modifiche alla copia locale del sito scaricata in precedenza e ripubblicarla oppure modificarla direttamente in modalità remota. In questa sezione verrà apportata una semplice modifica all'intestazione del file "index.php", che verrà salvata direttamente sul sito attivo.

1. Fare clic sulla scheda Remoto del sito in WebMatrix e selezionare **Apri visualizzazione remota**. Verrà aperto il sito remoto per le modifiche dirette.
	 ![WebMatrix - Open Remote View][OpenRemoteView]
 
2. Fare doppio clic sul file "index.php" per aprirlo.
	![WebMatrix - Open index file][Remote_editIndex]

3. Modificare **My ToDo List** in **My Task List** nei tag **title** e **h1**, quindi salvare il file.


4. Al termine del salvataggio, fare clic sul pulsante Run per visualizzare le modifiche sul sito attivo.
	![WebMatrix - Launch site in Remote][Remote_run]



## Passaggi successivi

Si è appreso come creare e distribuire un sito Web da WebMatrix in Azure. Per altre informazioni su WebMatrix, fare riferimento alle risorse seguenti:

* [WebMatrix per Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [Sito Web di WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





[install-SQLExpress]: http://www.microsoft.com/it-it/download/details.aspx?id=29062
[running-app]: ./media/web-sites-php-sql-database-use-webmatrix/tasklist_app_windows.png
[tasklist-sqlazure-download]: http://go.microsoft.com/fwlink/?LinkId=252504
[NewWebSite1]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite1.jpg
[NewWebSite2]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite2.png
[NewWebSite3_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite3_SQL.png
[NewWebSite4_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite4_SQL.png

[NewWebSite6_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite6_SQL.png
[NewWebSite7]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite7.png

[InstallWebMatrix]: ./media/web-sites-php-sql-database-use-webmatrix/InstallWebMatrix.png
[download-site]: ./media/web-sites-php-sql-database-use-webmatrix/download-site-1.png
[site-from-template]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template.png
[site-from-template-2]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template-2.png
[edit_addexisting]: ./media/web-sites-php-sql-database-use-webmatrix/edit_addexisting.png
[edit_run]: ./media/web-sites-php-sql-database-use-webmatrix/edit_run.png
[edit_publish]: ./media/web-sites-php-sql-database-use-webmatrix/edit_publish.png
[OpenRemoteView]: ./media/web-sites-php-sql-database-use-webmatrix/OpenRemoteView.png
[Remote_editIndex]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_editIndex.png
[Remote_run]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_run.png





















[preview-portal]: https://manage.windowsazure.com








