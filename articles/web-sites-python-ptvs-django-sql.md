<properties linkid="web-sites-python-ptvs-django-sql" title="Django and SQL Database on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Django e database SQL in Azure con Python Tools 2.1 per Visual Studio" description="Informazioni su come usare Python Tools per Visual Studio per creare un'applicazione Django che archivia i dati in un'istanza di database SQL e che può essere distribuita in un sito Web." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Django e database SQL in Azure con Python Tools 2.1 per Visual Studio 

In questa esercitazione verrà creata una semplice applicazione per sondaggi usando uno dei modelli di esempio PTVS. Questa esercitazione è anche disponibile sotto forma di [video](https://www.youtube.com/watch?v=ZwcoGcIeHF4).

Si apprenderà come usare un database SQL ospitato in Azure, come configurare l'applicazione per l'uso del database SQL e come pubblicare l'applicazione in un sito Web di Azure.

Vedere il [Centro per sviluppatori Python][] per altri articoli che trattano lo sviluppo di siti Web di Azure con PTVS usando i framework Web di Bottle, Flask e Django con i servizi di MongoDB, archiviazione tabelle di Azure, MySQL e Database SQL.  Sebbene questo articolo sia incentrato sui siti Web di Azure, i passaggi sono simili a quelli dello sviluppo di [Servizi Cloud di Azure][].

+ [Prerequisiti](#prerequisites)
+ [Creare il progetto](#create-the-project)
+ [Creare un database SQL](#create-a-sql-database)
+ [Configurare il progetto](#configure-the-project)
+ [Pubblicare in un sito Web di Azure](#publish-to-an-azure-website)
+ [Passaggi successivi](#next-steps)

##<a name="prerequisites"></a>Prerequisiti

 - Visual Studio 2012 o 2013
 - [Python Tools 2.1 per Visual Studio][]
 - [VSIX di esempio di Python Tools 2.1 per Visual Studio][]
 - [Strumenti di Azure SDK per Visual Studio 2013][] o [Strumenti di Azure SDK per Visual Studio 2012][]
 - [Python 2.7 a 32 bit][]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Creare il progetto

In questa sezione verrà creato un progetto di Visual Studio usando un modello di esempio. Verrà creato un ambiente virtuale e verranno installati i pacchetti necessari.  Si creerà un database locale usando sqlite,  quindi verrà eseguita l'applicazione in locale.

1.  In Visual Studio selezionare **File**, **Nuovo progetto**.

1.  I modelli di progetto del pacchetto VSIX degli esempi di PTVS sono disponibili in **Python**, **Esempi**.  Selezionare **Polls Django Web Project** e fare clic su OK per creare il progetto.

  	![New Project Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)

1.  Verrà richiesto di installare pacchetti esterni.  Selezionare **Installa in un ambiente virtuale**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)

1.  Selezionare **Python 2.7** come interprete di base.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)

1.  Fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **Python**, **Django Sync DB**.

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-sql/PollsDjangoSyncDB.png)

1.  Verrà aperta la console di gestione di Django.  Seguire le istruzioni visualizzate per creare un utente.

    Verrà creato un database sqlite nella cartella di progetto.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Verificare che l'applicazione funzioni premendo <kbd>F5</kbd>.

1.  Fare clic su **Log in** sulla barra di spostamento in alto.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Immettere le credenziali per l'utente creato al momento della sincronizzazione del database.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Fare clic su **Create Sample Polls**.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)

1.  Fare clic su un sondaggio e un voto.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

##<a name="create-a-sql-database"></a>Creare un database SQL

Per il database verrà creato un database SQL di Azure.

Per creare un database, seguire questa procedura.

1.  Accedere al [portale di gestione di Azure][].

1.  Nella parte inferiore del pannello di navigazione fare clic su **NUOVO**.

  	![New Button](./media/web-sites-python-ptvs-django-sql/PollsCommonAzurePlusNew.png)

1.  Fare clic su **SERVIZI DATI**, quindi su **DATABASE SQL** e infine su **CREAZIONE RAPIDA**.

  	![Quick Create SQL Database](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlCreate.png)

1.  Scegliere di creare un nuovo server di database SQL.

1.  Scegliere un'area o un gruppo di affinità in cui posizionare il database. Se si userà il database dalla propria applicazione Azure, selezionare la stessa area in cui verrà distribuita l'applicazione.

##<a name="configure-the-project"></a>Configurare il progetto

In questa sezione l'applicazione verrà configurata per l'uso del database SQL appena creato.  Verranno fornite informazioni su come ottenere le impostazioni di connessione dal portale di Azure  e verranno installati i pacchetti Python aggiuntivi necessari per usare i database SQL con Django.  Quindi, verrà eseguita l'applicazione in locale.

1.  In [Portale di gestione di Azure][] fare clic su **DATABASE SQL**, quindi fare clic sul database creato in precedenza.

1.  Fare clic su **GESTISCI**.

  	![Manage Button](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlManage.png)

1.  Verrà richiesto se aggiornare le regole del firewall. Fare clic su **SÌ**.  Questa impostazione permetterà le connessioni al server di database dal computer di sviluppo.

  	![Allow Connections](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlUpdateFirewall.png)

1.  Fare clic su **DATABASE SQL**, quindi su **SERVER**.  Fare clic sul server per il database, quindi su **CONFIGURA**.

1.  In questa pagina verrà visualizzato l'indirizzo IP di ogni computer autorizzato a connettersi al server di database.  Dovrebbe essere visualizzato l'indirizzo IP del proprio computer.

    Di seguito, in **Servizi consentiti** verificare che i servizi di Azure possano accedere al server.  Quando l'applicazione è in esecuzione in un sito Web di Azure (argomento che verrà trattato nella sezione successiva di questa esercitazione), potrà connettersi al database.  Fare clic su **SALVA** per salvare le modifiche.

  	![Allowed Services](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlAllowedServices.png)

1.  In Visual Studio aprire **settings.py** dalla cartella *NomeProgetto*. Modificare la definizione di `DATABASES`.

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<DatabaseName>',
                'USER': '<User>@<ServerName>',
                'PASSWORD': '<Password>',
                'HOST': '<ServerName>.database.windows.net',
                'PORT': '<ServerPort>',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': 'True',
                }
            }
        }

    `<DatabaseName>`, `<User>` e `<Password>` sono i valori specificati quando sono stati creati il database e il server.

    I valori per `<ServerName>` e `<ServerPort>` vengono generati da Azure quando viene creato il server e sono disponibili nella sezione **Connetti al database**.

1.  In Esplora soluzioni, in **Python Environments** fare clic con il pulsante destro del mouse sull'ambiente virtuale e scegliere **Install Python Package**.

1.  Installare il pacchetto `pyodbc` usando **easy_install**.

  	![Install Python Package Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)

1.  Installare il pacchetto `django-pyodbc-azure` usando **pip**.

  	![Install Python Package Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)

1.  Fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **Python**, **Django Sync DB**.  

    Verranno create le tabelle per il database SQL creato nella sezione precedente.  Seguire le istruzioni per creare un utente, che non deve necessariamente corrispondere all'utente nel database sqlite creato nella prima sezione.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Eseguire l'applicazione con <kbd>F5</kbd>.  I sondaggi creati con **Create Sample Polls** e i dati inviati mediante voto verranno serializzati nel database SQL.


##<a name="publish-to-an-azure-website"></a>Pubblicare in un sito Web di Azure

PTVS offre un semplice metodo per distribuire l'applicazione Web in un sito Web di Azure.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **Pubblica**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png)

1.  Fare clic su **Siti Web di Microsoft Azure**.

1.  Fare clic su **Nuovo** per creare un nuovo sito.

1.  Selezionare un nome in **Nome sito** e un'area in **Area**, quindi fare clic su **Crea**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonCreateWebSite.png)

1.  Accettare tutte le altre impostazioni predefinite e fare clic su **Pubblica**.

1.  Il sito pubblicato verrà aperto automaticamente nel Web browser.  L'applicazione dovrebbe funzionare nel modo previsto, usando il database **SQL** ospitato in Azure.

    Congratulazioni.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

##<a name="next-steps"></a>Passaggi successivi

Usare i collegamenti seguenti per altre informazioni su Python Tools per Visual Studio, Django e il database SQL.

- [Documentazione di Python Tools per Visual Studio][]
  - [Progetti Web][]
  - [Progetti servizio cloud][]
  - [Debug remoto in Microsoft Azure][]
- [Documentazione di Django][]
- [Database SQL][]


<!--Link references-->
[Centro per sviluppatori di Python]: /it-it/develop/python/
[Servizi cloud di Azure]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Portale di gestione di Azure]: https://manage.windowsazure.com
[Python Tools 2.1 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[VSIX di esempio di Python Tools 2.1 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Strumenti di Azure SDK per Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Strumenti di Azure SDK per Visual Studio 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 a 32 bit]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Documentazione di Python Tools per Visual Studio]: http://pytools.codeplex.com/documentation
[Debug remoto in Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Progetti Web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Progetti servizio cloud]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Documentazione di Django]: https://www.djangoproject.com/
[Database SQL]: /it-it/documentation/services/sql-database/

<!--HONumber=35.1-->
