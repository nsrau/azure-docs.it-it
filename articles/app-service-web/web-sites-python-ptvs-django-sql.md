<properties 
	pageTitle="Django e database SQL in Azure con Python Tools 2.2 per Visual Studio"
	description="Informazioni su come usare Python Tools per Visual Studio per creare un'app Web Django che archivia i dati in un'istanza di database SQL e per distribuirla in App Web del servizio app di Azure."
	services="app-service\web"
	tags="python"
	documentationCenter="python"
	authors="huguesv"
	manager="wpickett"
	editor=""/>

<tags 
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="08/30/2015"
	ms.author="huguesv"/>




# Django e database SQL in Azure con Python Tools 2.2 per Visual Studio 

In questa esercitazione si userà [Python Tools per Visual Studio] al fine di creare una semplice app Web per sondaggi con uno dei modelli di esempio PTVS. Questa esercitazione è anche disponibile in formato [video](https://www.youtube.com/watch?v=ZwcoGcIeHF4).

Si apprenderà come usare un database SQL ospitato in Azure, come configurare l'app Web per usare un database SQL e come pubblicare l'app Web in [App Web del servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Vedere il [Centro per sviluppatori Python] per consultare altri articoli che trattano lo sviluppo di app Web del servizio app di Azure con PTVS usando i framework Web di Bottle, Flask e Django con i servizi di MongoDB, archiviazione tabelle di Azure, MySQL e Database SQL. Sebbene questo articolo sia incentrato sul servizio app, i passaggi sono simili a quelli previsti per lo sviluppo dei [servizi cloud di Azure].

## Prerequisiti

 - Visual Studio 2013 o 2015
 - [Python Tools 2.2 per Visual Studio]
 - [VSIX degli esempi di Python Tools 2.2 per Visual Studio]
 - [Strumenti di Azure SDK per VS 2013] o [Strumenti di Azure SDK per VS 2015]
 - [Python 2.7 a 32 bit]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Creare il progetto

In questa sezione verrà creato un progetto di Visual Studio usando un modello di esempio. Verrà creato un ambiente virtuale e verranno installati i pacchetti necessari. Si creerà un database locale usando sqlite, Quindi, l'app Web verrà eseguita in locale.

1.  In Visual Studio selezionare **File**, **Nuovo progetto**.

1.  I modelli di progetto del pacchetto PTVS Samples VSIX sono disponibili in **Python**, **Esempi**. Selezionare **Polls Django Web Project** e fare clic su OK per creare il progetto.

  	![Finestra di dialogo Nuovo progetto](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)

1.  Verrà richiesto di installare pacchetti esterni. Selezionare **Installa in un ambiente virtuale**.

  	![Finestra di dialogo dei pacchetti esterni](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)

1.  Selezionare **Python 2.7** come interprete di base.

  	![Finestra di dialogo Aggiungi ambiente virtuale](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)

1.  Fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **Python**, **Django Sync DB**.

  	![Comando Django Sync DB](./media/web-sites-python-ptvs-django-sql/PollsDjangoSyncDB.png)

1.  Verrà aperta la console di gestione di Django. Seguire le istruzioni visualizzate per creare un utente.

    Verrà creato un database sqlite nella cartella di progetto.

  	![Finestra della console di gestione di Django](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Verificare che l'applicazione funzioni premendo <kbd>F5</kbd>.

1.  Fare clic su **Log in** sulla barra di spostamento in alto.

  	![Web browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Immettere le credenziali per l'utente creato al momento della sincronizzazione del database.

  	![Web browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Fare clic su **Create Sample Polls**.

  	![Web browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)

1.  Fare clic su un sondaggio e votare.

  	![Web browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

## Creare un database SQL

Per il database verrà creato un database SQL di Azure.

Per creare un database, seguire questa procedura.

1.  Accedere al [Portale di Azure].

1.  Nella parte inferiore del riquadro di spostamento fare clic su **NUOVO**, **Dati + archiviazione** > **Database SQL**.

  	<!-- ![New Button](./media/web-sites-python-ptvs-django-sql/PollsCommonAzurePlusNew.png) -->

1.  Configurare il nuovo database SQL creando un nuovo gruppo di risorse e selezionare il percorso appropriato.

  	<!-- ![Quick Create SQL Database](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlCreate.png) -->

1.  Dopo aver creato il database SQL, fare clic su **Apri in Visual Studio** nel pannello del database.
2.  Fare clic su **Configura firewall**.
3.  Nel pannello **Impostazioni firewall ** aggiungere una regola firewall con **IP INIZIALE** e **IP FINALE** impostati sull'indirizzo IP pubblico del computer di sviluppo. Fare clic su **Salva**.

	Questa impostazione permetterà le connessioni al server di database dal computer di sviluppo.

4.  Nel pannello del database fare clic su **Proprietà**, quindi su **Mostra stringhe di connessione database**.

2.  Usare il pulsante Copia per inserire il valore di **ADO.NET** negli Appunti.

## Configurare il progetto

In questa sezione l'app Web verrà configurata per usare il database SQL appena creato. e verranno installati i pacchetti Python aggiuntivi necessari per usare i database SQL con Django. Quindi, l'app Web verrà eseguita in locale.

1.  In Visual Studio aprire **settings.py** dalla cartella *NomeProgetto*. Incollare temporaneamente la stringa di connessione nell'editor. La stringa di connessione è nel formato seguente:

        Server=<ServerName>,<ServerPort>;Database=<DatabaseName>;User ID=<UserName>;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

Modificare la definizione di `DATABASES` per usare i valori precedenti.

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<DatabaseName>',
                'USER': '<UserName>',
                'PASSWORD': '{your_password_here}',
                'HOST': '<ServerName>',
                'PORT': '<ServerPort>',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': 'True',
                }
            }
        }

1.  In Esplora soluzioni, in **Python Environments** fare clic con il pulsante destro del mouse sull'ambiente virtuale e scegliere **Install Python Package**.

1.  Installare il pacchetto `pyodbc` usando **easy\_install**.

  	![Finestra di dialogo per l'installazione del pacchetto](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)

1.  Installare il pacchetto `django-pyodbc-azure` usando **pip**.

  	![Finestra di dialogo per l'installazione del pacchetto](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)

1.  Fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **Python**, **Django Sync DB**.

    Verranno create le tabelle per il database SQL creato nella sezione precedente. Seguire le istruzioni per creare un utente, che non deve necessariamente corrispondere all'utente nel database sqlite creato nella prima sezione.

  	![Finestra della console di gestione di Django](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Eseguire l'applicazione con `F5`. I sondaggi creati con **Create Sample Polls** e i dati inviati mediante voto verranno serializzati nel database SQL.


## Pubblicare l'app Web nel servizio app di Azure

L'SDK .NET di Azure offre un modo semplice di distribuire l'app Web in App Web del servizio app di Azure.

1.  In **Esplora soluzioni**, fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **Pubblica**.

  	<!-- ![Publish Web Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png) -->

1.  Fare clic su **App Web di Microsoft Azure**.

1.  Fare clic su **Nuovo** per creare una nuova app Web.

1.  Compilare i campi seguenti, quindi fare clic su **Crea**.
	-	**Nome dell'app Web**
	-	**Piano di servizio app**
	-	**Gruppo di risorse**
	-	**Area**
	-	Lasciare **Server database** impostato su **Nessun database**

  	<!-- ![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonCreateWebSite.png) -->

1.  Accettare tutte le altre impostazioni predefinite e fare clic su **Pubblica**.

1.  L'app Web pubblicata verrà aperto automaticamente nel Web browser. L'app Web dovrebbe funzionare come previsto, usando il database **SQL** ospitato in Azure.

    Congratulazioni.

  	![Web browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

## Passaggi successivi

Usare i collegamenti seguenti per altre informazioni su Python Tools per Visual Studio, Django e il database SQL.

- [Documentazione di Python Tools per Visual Studio]
  - [Progetti Web]
  - [Progetti servizio cloud]
  - [Debug remoto in Microsoft Azure]
- [Documentazione di Django]
- [Database SQL]

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale precedente per il nuovo portale, vedere: [riferimento per lo spostamento tra il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)


<!--Link references-->
[Centro per sviluppatori Python]: /develop/python/
[servizi cloud di Azure]: ../cloud-services-python-ptvs.md

<!--External Link references-->
[Portale di Azure]: https://portal.azure.com
[Python Tools per Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[VSIX degli esempi di Python Tools 2.2 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Strumenti di Azure SDK per VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Strumenti di Azure SDK per VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 a 32 bit]: http://go.microsoft.com/fwlink/?LinkId=517190
[Documentazione di Python Tools per Visual Studio]: http://aka.ms/ptvsdocs
[Debug remoto in Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Progetti Web]: http://go.microsoft.com/fwlink/?LinkId=624027
[Progetti servizio cloud]: http://go.microsoft.com/fwlink/?LinkId=624028
[Documentazione di Django]: https://www.djangoproject.com/
[Database SQL]: /documentation/services/sql-database/

<!---HONumber=September15_HO1-->