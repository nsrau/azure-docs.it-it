<properties 
	pageTitle="Creazione di un'app Web PHP-MySQL in Servizio app di Azure e distribuzione tramite Git" 
	description="Un'esercitazione che illustra come usare l'IDE di WebMatrix gratuito per creare e distribuire un sito Web PHP che archivia i dati in MySQL."
	tags="azure-portal" 
	services="app-service\web" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="04/22/2015" 
	ms.author="tomfitz"/>





# Creazione di un'app Web PHP-MySQL in Servizio app di Azure e distribuzione tramite Git

In questa esercitazione viene illustrato come utilizzare WebMatrix per sviluppare e distribuire un'applicazione PHP-MySQ in [servizio App Azure](http://go.microsoft.com/fwlink/?LinkId=529714) applicazioni Web. WebMatrix è uno strumento di sviluppo Web gratuito di Microsoft che include tutto quanto necessario per lo sviluppo di siti Web. WebMatrix supporta PHP e comprende IntelliSense per lo sviluppo di PHP.

In questa esercitazione si presuppone che l'utente abbia installato [MySQL][install-mysql] nel computer in modo che sia possibile testare un'applicazione in locale. È tuttavia possibile completare l'esercitazione senza aver installato MySQL. L'applicazione può infatti essere distribuita direttamente sui siti Web di Azure.

Dopo aver completato questa guida, si disporrà di un sito Web PHP-MySQL in esecuzione in Azure.
 
Si acquisiranno le nozioni seguenti:

* Come creare un sito Web nell'applicazione servizio Web App e un database MySQL utilizzando il [portale Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Poiché PHP è abilitato in App Web di Azure per impostazione predefinita, non è necessario completare operazioni speciali per eseguire il codice PHP.
* Come sviluppare un'applicazione PHP con WebMatrix.
* Come pubblicare e ripubblicare l'applicazione in Azure con WebMatrix.
 
Seguendo questa esercitazione, verrà creata una semplice applicazione Web Tasklist in PHP, che verrà ospitata nelle app Web. Di seguito è riportata una schermata dell'applicazione in esecuzione:

![Sito Web PHP di Azure][running-app]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

##Prerequisiti

1. [Scaricare][tasklist-mysql-download] i file dell'applicazione Tasklist. Si tratta di una semplice applicazione PHP che consente di aggiungere e/o eliminare gli elementi da un elenco attività oppure di contrassegnarli come completi. Gli elementi dell'elenco attività sono archiviate in un database MySQL. L'applicazione è costituita dai file seguenti:

	* **AddItem**: aggiunge un elemento all'elenco.
	* **createtable.php**: consente di creare la tabella MySQL per l'applicazione. Questo file verrà chiamato una volta sola.
	* **DeleteItem. PHP**: elimina un elemento.
	* **getitems.php**: recupera tutti gli elementi nel database.
	* **index.php**: consente di visualizzare le attività e fornisce un modulo per l'aggiunta di un elemento all'elenco.
	* **markitemcomplete.php**: modifica lo stato di un elemento come completo.
	* **taskmodel.php**: contiene funzioni per aggiungere, ottenere, aggiornare ed eliminare gli elementi dal database.

1. Creare un database di SQL Server denominato `tasklist`. È possibile eseguire questa operazione dall'area di lavoro Database in WebMatrix (dopo averlo installato come descritto nell'esercitazione riportata di seguito) o dal prompt dei comandi di MySQL con il comando seguente:

		mysql> create database tasklist;

	Questo passaggio è necessario solo se si desidera testare l'applicazione localmente.

## Creare un'app Web e un database MySQL

Per creare un'app Web e un database MySQL, attenersi alla procedura seguente:

1. Eseguire l'accesso al [portale Azure](https://portal.azure.com).

2. Fare clic sull'icona **New** nella parte inferiore sinistra del portale.

	![Creazione di un nuovo sito Web di Azure](./media/web-sites-php-mysql-use-webmatrix/new_website2.png)

3. Fare clic su **Web + Mobile**, quindi su **App Web + MySQL**.

	![Creazione personalizzata di un nuovo sito Web](./media/web-sites-php-mysql-use-webmatrix/create_web_mysql.png)

4. Immettere un nome valido per il gruppo di risorse.

    ![Gruppo di risorse denominato ADF.](./media/web-sites-php-mysql-use-webmatrix/set_group.png)

5. Immettere i valori per la nuova app Web.

    ![Crea app Web](./media/web-sites-php-mysql-use-webmatrix/create_wa.png)

6. Immettere i valori per il nuovo database e accettare termini e condizioni.

	![Creazione di un nuovo database MySQL](./media/web-sites-php-mysql-use-webmatrix/create_db.png)

	Una volta creata l'app Web, verrà visualizzato il nuovo gruppo di risorse.

## Recupero di informazioni sulla connessione remota a MySQL

Per connettersi al database MySQL in esecuzione in App Web, saranno necessarie le informazioni sulla connessione. Per recuperare le informazioni sulla connessione a MySQL, attenersi alla procedura seguente:

1. Dal gruppo di risorse, fare clic sul database:

	![Selezionare il database](./media/web-sites-php-mysql-use-webmatrix/select_database.png)

2. Dal riepilogo del database, selezionare **Proprietà**.

    ![Selezionare le proprietà](./media/web-sites-php-mysql-use-webmatrix/select_properties.png)

2. Prendere nota dei valori di `Database`, `Host`, `User Id` e `Password`.

    ![Proprietà nota](./media/web-sites-php-mysql-use-webmatrix/note-properties.png)

## Creare l'applicazione in WebMatrix

Nei passaggi successivi verrà sviluppata l'applicazione Tasklist mediante l'aggiunta dei file precedentemente scaricati e l'esecuzione di alcune modifiche. È tuttavia possibile aggiungere i propri file esistenti o crearne di nuovi.

1. Avviare [Microsoft WebMatrix](http://www.microsoft.com/web/webmatrix/). Se già stato installato, farlo ora.
2. Se è la prima volta che si utilizza WebMatrix 3, verrà richiesto di effettuare l'accesso ad Azure. In caso contrario, è possibile fare clic sul pulsante **Sign In** e scegliere **Add Account**. Scegliere **Sign in** utilizzando il proprio account Microsoft.

	![Aggiunta di un account](./media/web-sites-php-mysql-use-webmatrix/webmatrix-add-account.png)

3. Se è stata effettuata l'iscrizione per un account Azure, è possibile accedere con il proprio account Microsoft:

	![Accesso ad Azure](./media/web-sites-php-mysql-use-webmatrix/webmatrix-sign-in.png)

1. Nella schermata Start fare clic sul pulsante **New** e scegliere **Template Gallery** per creare un nuovo sito dalla raccolta modelli:

	![Nuovo sito dalla raccolta modelli](./media/web-sites-php-mysql-use-webmatrix/webmatrix-site-from-template.png)

4. Scegliere **PHP** dai modelli disponibili.

	![Sito dal modello][site-from-template]

5. Selezionare il modello **Empty Site**. Specificare un nome per il sito e fare clic su **NEXT**.

	![Specificare un nome per il sito][site-from-template-2]

	Il sito verrà aperto in WebMatrix con alcuni file predefiniti.

	Nei passaggi successivi verrà sviluppata l'applicazione Tasklist mediante l'aggiunta dei file precedentemente scaricati e l'esecuzione di alcune modifiche. È tuttavia possibile aggiungere i propri file esistenti o crearne di nuovi.

6. Tenendo il sito aperto in WebMatrix, fare clic su **Add Existing** per aggiungere i file applicazione:

	![WebMatrix - Aggiunta di file esistenti][edit_addexisting]

	Nella finestra di dialogo risultante, passare ai file scaricati in precedenza, selezionarli tutte e fare clic su Open. Quando richiesto, scegliere di sostituire il file `index.php`.

7. Sarà quindi necessario aggiungere le informazioni sulla connessione al database MySQL locale al file `taskmodel.php`. Aprire il `taskmodel.php` fare doppio clic sul file e aggiornare le informazioni di connessione di database il `connect` (funzione). (* * Nota * *: passare a [pubblica l'applicazione](#Publish) se non si desidera testare l'applicazione localmente e intenda pubblicarla direttamente in Azure applicazione servizio Web Apps.)

		// DB connection info
		$host = "localhost";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Salvare il file.`taskmodel.php`

8. Affinché sia possibile eseguire l'applicazione sarà necessario creare la tabella `items`. Fare clic con il pulsante destro del mouse sul file `createtable.php` e scegliere **Avvia nel browser**. Il file `createtable.php` verrà aperto nel browser e verrà eseguito il codice che consente di creare la tabella `items` nel database `tasklist`.

	![WebMatrix - Avvio di createtable.php nel browser][edit_run]

9. A questo punto è possibile testare l'applicazione in locale. Fare clic con il pulsante destro del mouse sul file `index.php` e scegliere **Avvia nel browser**. Testare l'applicazione aggiungendo degli elementi, contrassegnandoli come completi ed eliminandoli.


## Pubblicare l'applicazione

Prima di pubblicare l'applicazione in Siti Web di Azure, è necessario aggiornare le informazioni di connessione al database nel file `taskmodel.php` con le informazioni di connessione ottenute in precedenza (nella sezione [Creare un sito Web di Azure e un database MySQL](#CreateWebsite)).

1. Fare doppio clic sul file `taskmodel.php` per aprirlo e aggiornare le informazioni di connessione al database nella funzione `connect`.

		// DB connection info
		$host = "value of Data Source";
		$user = "value of User Id";
		$pwd = "value of Password";
		$db = "value of Database";
	
	Salvare il file.`taskmodel.php`

2. Fare clic su **pubblicare** in WebMatrix.

	![WebMatrix - Opzione Publish][edit_publish]

3. Fare clic su **sito esistente da Windows Azure e scegliere**.

	![](./media/web-sites-php-mysql-use-webmatrix/webmatrix-publish-existing-site.png)

3. Selezionare l'applicazione web di App servizio creato in precedenza.

	![](./media/web-sites-php-mysql-use-webmatrix/webmatrix-publish-existing-site-choose.png)

3. Tenere premuto **Continua** fino a quando non WebMatrix pubblica il sito in Azure applicazione servizio Web Apps.

3. Passare a http://[yourhttp://[nome del sito Web].azurewebsites.net/createtable.php per creare la tabella `items`.

4. Infine, passare a http://[yourhttp://[nome del sito Web].azurewebsites.net/index.php per avviare l'applicazione.
	
##Modifica e ripubblicazione dell'applicazione

Per modificare facilmente l'applicazione apportare le modifiche alla copia locale del sito scaricata in precedenza e ripubblicarla oppure modificarla direttamente in modalità remota. In tal modo, sarà possibile apportare una semplice modifica all'intestazione del file `index.php`, salvandolo direttamente sul sito attivo.

1. Fare clic sulla scheda Remote del sito in WebMatrix e selezionare **Open Remote View**. Verrà aperto il sito remoto per le modifiche dirette. ![WebMatrix - Apertura in visualizzazione remota][OpenRemoteView]
 
2. Aprire il `index.php` file facendovi doppio clic. ![WebMatrix - Apertura del file index][Remote_editIndex]

3. Modificare **My ToDo List** in **My Task List** nei tag **title** e **h1**, quindi salvare il file.


4. Al termine del salvataggio, fare clic sul pulsante Run per visualizzare le modifiche sul sito attivo. ![WebMatrix - Avvio del sito in remoto][Remote_run]


## Passaggi successivi

* [Sito Web di WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)

## Modifiche apportate
* Per una Guida per la modifica di siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sui servizi di Azure esistente](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale precedente per il nuovo portale, vedere: [riferimento per lo spostamento tra il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)




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














<!--HONumber=54--> 