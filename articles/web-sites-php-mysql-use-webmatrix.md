<properties linkid="develop-php-website-with-mysql-and-webmatrix" urlDisplayName="Web w/ WebMatrix" pageTitle="PHP website with MySQL and WebMatrix - Azure tutorial" metaKeywords="" description="A tutorial that demonstrates how to use the free WebMatrix IDE to create and deploy a PHP website that stores data in MySQL." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create and deploy a PHP-MySQL Azure Website using WebMatrix" authors="" solutions="" manager="" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Creare e distribuire un sito Web di Azure PHP-MySQL tramite WebMatrix

In questa esercitazione viene illustrato come usare WebMatrix per sviluppare e distribuire un'applicazione PHP-MySQ in un sito Web di Azure. WebMatrix è uno strumento di sviluppo Web gratuito di Microsoft che include tutto quanto necessario per lo sviluppo di siti Web. WebMatrix supporta PHP e comprende IntelliSense per lo sviluppo di PHP.

In questa esercitazione si presuppone che l'utente abbia installato [MySQL][] nel computer in modo che sia possibile testare un'applicazione in locale. È tuttavia possibile completare l'esercitazione senza aver installato MySQL. L'applicazione può infatti essere distribuita direttamente in Siti Web di Azure.

Dopo aver completato questa guida, si disporrà di un sito Web PHP-MySQL in esecuzione in Azure.

Si apprenderà come:

-   Creare un sito Web di Azure e un database MySQL mediante il portale di gestione di Azure. Poiché PHP è abilitato in Siti Web di Azure per impostazione predefinita, non è necessario completare operazioni speciali per eseguire il codice PHP.
-   Come sviluppare un'applicazione PHP con WebMatrix.
-   Come pubblicare e ripubblicare l'applicazione in Azure con WebMatrix.

Seguendo questa esercitazione, verrà creata una semplice applicazione Web Tasklist in PHP, ospitata in un sito Web di Azure. Di seguito è riportata una schermata dell'applicazione in esecuzione:

![Sito Web PHP di Azure][]

[WACOM.INCLUDE [create-account-and-websites-note][]]

## Prerequisiti

1.  [Scaricare][] i file dell'applicazione Tasklist. Si tratta di una semplice applicazione PHP che consente di aggiungere e/o eliminare gli elementi da un elenco attività oppure di contrassegnarli come completi. Gli elementi dell'elenco attività sono archiviate in un database MySQL. L'applicazione è costituita dai file seguenti:

    -   **additem.php**: consente di aggiungere un elemento all'elenco.
    -   **createtable.php**: consente di creare la tabella MySQL per l'applicazione. Questo file verrà chiamato una volta sola.
    -   **deleteitem.php**: consente di eliminare un oggetto.
    -   **getitems.php**: consente di ottenere tutti gli elementi del database.
    -   **index.php**: consente di visualizzare le attività e fornisce un modulo per l'aggiunta di un elemento all'elenco.
    -   **markitemcomplete.php**: consente di modificare lo stato di un elemento come completo.
    -   **taskmodel.php**: contiene funzioni per aggiungere, ottenere, aggiornare ed eliminare gli elementi dal database.

2.  Creare un database di SQL Server denominato `tasklist`. È possibile eseguire questa operazione dall'area di lavoro Database in WebMatrix (dopo averlo installato come descritto nell'esercitazione riportata di seguito) o dal prompt dei comandi di MySQL con il comando seguente:

        mysql> create database tasklist;

    Questo passaggio è necessario solo se si desidera testare l'applicazione localmente.

## <span id="CreateWebsite"></span></a>Creare un sito Web di Azure e un database MySQL

1.  Accedere al [portale di gestione][].
2.  Fare clic sull'icona **+ New** nella parte inferiore sinistra del portale.

    ![Creazione di un nuovo sito Web di Azure][]

3.  Fare clic su **SITO WEB** e quindi su **CREAZIONE PERSONALIZZATA**.

    ![Creazione personalizzata di un nuovo sito Web][]

    > [WACOM.NOTE]
    > Non è possibile creare un database MySQL per un sito Web dopo avere creato il sito Web. È necessario creare un sito Web e un database MySQL come descritto nella procedura seguente.

4.  Immettere un valore per **URL**, scegliere **Crea un nuovo database MySQL** nell'elenco a discesa **DATABASE**, quindi selezionare il data center per il sito Web nell'elenco a discesa **AREA**. Fare clic sulla freccia nella parte inferiore della finestra di dialogo.

    ![Inserimento di dettagli sul sito Web][]

5.  Immettere un valore per **NAME**, scegliere il data center per il database nell'elenco a discesa **REGION**, quindi selezionare la casella che indica che si accettano le note legali. Fare clic sul segno di spunta nella parte inferiore della finestra di dialogo.

    ![Creazione di un nuovo database MySQL][]

    In seguito alla creazione del sito Web verrà visualizzato il messaggio **Creazione del sito Web '[NOMESITO]' completata**.

    Sarà quindi necessario recuperare le informazioni sulla connessione a MySQL.

6.  Fare clic sul nome del sito Web visualizzato nell'elenco dei siti per aprire la pagina Avvio rapido.

    ![Apertura del dashboard del sito Web][]

7.  Fare clic sulla scheda **CONFIGURA**.

    ![Scheda per la configurazione][]

8.  Scorrere verso il basso fino alla sezione **stringhe di connessione**. I valori per `Database`, `Data Source`, `User Id` e `Password` sono rispettivamente il nome del database, il nome del server, il nome utente e la password utente. Prendere nota delle informazioni sulla connessione al database perché saranno necessarie in seguito.

    ![Stringa di connessione][]

## Installazione di WebMatrix e sviluppo dell'applicazione

È possibile installare WebMatrix dal [portale di gestione][].

1.  Dopo aver eseguito l'accesso, aprire la pagina avvio rapido del sito Web e fare clic sull'icona WebMatrix nella parte inferiore della pagina:

    ![Installazione di WebMatrix][]

    Per installare WebMatrix, attenersi alle istruzioni visualizzate.

2.  Al termine dell'installazione di WebMatrix, verrà effettuato un tentativo di apertura del sito come progetto WebMatrix. È possibile scegliere di modificare direttamente il sito attivo oppure scaricare una copia in locale. Per questa esercitazione, selezionare "Edit local copy".

3.  Quando viene richiesto di scaricare il sito, scegliere **Yes, install from the Template Gallery**.

    ![Scaricare il sito Web][]

4.  Scegliere **PHP** dai modelli disponibili.

    ![Sito dal modello][]

5.  Selezionare il modello **Empty Site**. Specificare un nome per il sito e fare clic su **NEXT**.

    ![Specificare un nome per il sito][]

    Il sito verrà aperto in WebMatrix con alcuni file predefiniti.

    Nei passaggi successivi verrà sviluppata l'applicazione Tasklist mediante l'aggiunta dei file precedentemente scaricati e l'esecuzione di alcune modifiche. È tuttavia possibile aggiungere i propri file esistenti o crearne di nuovi.

6.  Tenendo il sito aperto in WebMatrix, fare clic su **Add Existing** per aggiungere i file applicazione:

    ![WebMatrix - Aggiunta di file esistenti][]

    Nella finestra di dialogo risultante, passare ai file scaricati in precedenza, selezionarli tutte e fare clic su Open. Quando richiesto, scegliere di sostituire il file `index.php`.

7.  Sarà quindi necessario aggiungere le informazioni sulla connessione al database MySQL locale al file `taskmodel.php`. Fare doppio clic sul file `taskmodel.php` per aprirlo e aggiornare le informazioni di connessione al database nella funzione `connect`. (**Nota**: nel caso in cui non voglia testare l'applicazione localmente e si intenda pubblicarla direttamente nei siti Web di Azure, passare alla sezione [Pubblicazione dell'applicazione][]).

        // DB connection info
        $host = "localhost";
        $user = "your user name";
        $pwd = "your password";
        $db = "tasklist";

    Salvare il file `taskmodel.php`.

8.  Affinché sia possibile eseguire l'applicazione sarà necessario creare la tabella `items`. Fare clic con il pulsante destro del mouse sul file `createtable.php` e scegliere **Avvia nel browser**. Il file `createtable.php` verrà aperto nel browser e verrà eseguito il codice che consente di creare la tabella `items` nel database `tasklist`.

    ![WebMatrix - Avvio di createtable.php nel browser][]

9.  A questo punto è possibile testare l'applicazione in locale. Fare clic con il pulsante destro del mouse sul file `index.php` e scegliere **Avvia nel browser**. Testare l'applicazione aggiungendo degli elementi, contrassegnandoli come completi ed eliminandoli.

## <span id="Publish"></span></a>Pubblicazione dell'applicazione

Prima di pubblicare l'applicazione in Siti Web di Azure, è necessario aggiornare le informazioni di connessione al database nel file `taskmodel.php` con le informazioni di connessione ottenute in precedenza (nella sezione [Creare un sito Web di Azure e un database MySQL][]).

1.  Fare doppio clic sul file `taskmodel.php` per aprirlo e aggiornare le informazioni di connessione al database nella funzione `connect`.

        // DB connection info
        $host = "value of Data Source";
        $user = "value of User Id";
        $pwd = "value of Password";
        $db = "value of Database";

    Salvare il file `taskmodel.php`.

2.  Fare clic su **Publish** in WebMatrix e quindi fare clic su **Continue** nella finestra di dialogo **Publish Preview**.

    ![WebMatrix - Opzione Publish][]

3.  Passare a [http://[][]nome del sito Web].azurewebsites.net/createtable.php per creare la tabella `items`.

4.  Infine, passare a [http://[][]nome del sito Web].azurewebsites.net/index.php per avviare l'applicazione.

## Modifica e ripubblicazione dell'applicazione

Per modificare facilmente l'applicazione apportare le modifiche alla copia locale del sito scaricata in precedenza e ripubblicarla oppure modificarla direttamente in modalità remota. In tal modo, sarà possibile apportare una semplice modifica all'intestazione del file `index.php`, salvandolo direttamente sul sito attivo.

1.  Fare clic sulla scheda Remote del sito in WebMatrix e selezionare **Open Remote View**. Verrà aperto il sito remoto per la modifica diretta.
     ![WebMatrix - Apertura in visualizzazione remota][]

2.  Fare doppio clic sul file `index.php` per aprirlo.
    ![WebMatrix - Open index file][]

3.  Modificare **My ToDo List** in **My Task List** nei tag **title** e **h1**, quindi salvare il file.

4.  Al termine del salvataggio, fare clic sul pulsante Run per visualizzare le modifiche sul sito attivo.
    ![WebMatrix - Launch site in Remote][]

# Passaggi successivi

Si è appreso come creare e distribuire un sito Web da WebMatrix in Azure. Per altre informazioni su WebMatrix, fare riferimento alle risorse seguenti:

-   [WebMatrix per Azure][]

-   [Sito Web di WebMatrix][]

  [MySQL]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
  [Sito Web PHP di Azure]: ./media/web-sites-php-mysql-use-webmatrix/tasklist_app_windows.png
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Scaricare]: http://go.microsoft.com/fwlink/?LinkId=252506
  [portale di gestione]: https://manage.windowsazure.com
  [Creazione di un nuovo sito Web di Azure]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite1.jpg
  [Creazione personalizzata di un nuovo sito Web]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite2.png
  [Inserimento di dettagli sul sito Web]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite3.png
  [Creazione di un nuovo database MySQL]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite4.png
  [Apertura del dashboard del sito Web]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite5.png
  [Scheda per la configurazione]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite6.png
  [Stringa di connessione]: ./media/web-sites-php-mysql-use-webmatrix/ConnectionString.png
  [Installazione di WebMatrix]: ./media/web-sites-php-mysql-use-webmatrix/InstallWebMatrix.png
  [Scaricare il sito Web]: ./media/web-sites-php-mysql-use-webmatrix/download-site-1.png
  [Sito dal modello]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template.png
  [Specificare un nome per il sito]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template-2.png
  [WebMatrix - Aggiunta di file esistenti]: ./media/web-sites-php-mysql-use-webmatrix/edit_addexisting.png
  [Pubblicazione dell'applicazione]: #Publish
  [WebMatrix - Avvio di createtable.php nel browser]: ./media/web-sites-php-mysql-use-webmatrix/edit_run.png
  [Creare un sito Web di Azure e un database MySQL]: #CreateWebsite
  [WebMatrix - Opzione Publish]: ./media/web-sites-php-mysql-use-webmatrix/edit_publish.png
  [http://[]: http://[your
  [WebMatrix - Apertura in visualizzazione remota]: ./media/web-sites-php-mysql-use-webmatrix/OpenRemoteView.png
  [WebMatrix - Open index file]: ./media/web-sites-php-mysql-use-webmatrix/Remote_editIndex.png
  [WebMatrix - Launch site in Remote]: ./media/web-sites-php-mysql-use-webmatrix/Remote_run.png
  [WebMatrix per Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409
  [Sito Web di WebMatrix]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
