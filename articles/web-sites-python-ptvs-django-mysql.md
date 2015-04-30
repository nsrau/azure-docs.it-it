<properties 
	pageTitle="Django e MySQL in Azure con Python Tools 2.1 per Visual Studio" 
	description="Informazioni su come usare Python Tools per Visual Studio per creare un'applicazione Django che archivia i dati in un'istanza del database MySQL e che può essere distribuita in un sito Web." 
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
	ms.date="02/09/2015" 
	ms.author="huguesv"/>




# Django e MySQL in Azure con Python Tools 2.1 per Visual Studio 

In questa esercitazione si userà [Python Tools per Visual Studio][] per creare una semplice applicazione per sondaggi con uno dei modelli di esempio PTVS. Questa esercitazione è anche disponibile sotto forma di [video](https://www.youtube.com/watch?v=oKCApIrS0Lo).

Si apprenderà come usare un servizio MySQL ospitato in Azure, come configurare l'applicazione per l'uso di MySQL e come pubblicare l'applicazione in un sito Web di Azure.

Vedere il [Centro per sviluppatori Python][] per altri articoli che trattano lo sviluppo di siti Web di Azure con PTVS usando i framework Web di Bottle, Flask e Django con i servizi di MongoDB, archiviazione tabelle di Azure, MySQL e Database SQL.  Sebbene questo articolo sia incentrato su Siti Web di Azure, i passaggi sono simili a quelli dello sviluppo di [Servizi cloud di Azure][].

## Prerequisiti

 - Visual Studio 2012 o 2013
 - [Python Tools 2.1 per Visual Studio][]
 - [VSIX di esempio di Python Tools 2.1 per Visual Studio][]
 - [Strumenti di Azure SDK per VS 2013][] o [Strumenti di Azure SDK per VS 2012][]
 - [Python 2.7 a 32 bit][]

> [AZURE.NOTE]
> Per completare l'esercitazione, è necessario un account Azure. È possibile <a href="http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/">attivare i benefici della sottoscrizione MSDN</a> o <a href="http://azure.microsoft.com/pricing/free-trial/">iscriversi per una versione di valutazione gratuita</a>.
> 
> Per iniziare a usare Siti Web di Azure prima di registrare un account, andare alla pagina all'indirizzo <a href="https://trywebsites.azurewebsites.net/?language=python">https://trywebsites.azurewebsites.net</a>, in cui è possibile creare immediatamente e gratuitamente un sito di base ASP.NET temporaneo in Siti Web di Azure. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Creare il progetto

In questa sezione verrà creato un progetto di Visual Studio usando un modello di esempio. Verrà creato un ambiente virtuale e verranno installati i pacchetti necessari.  Si creerà un database locale usando sqlite,  quindi verrà eseguita l'applicazione in locale.

1.  In Visual Studio selezionare **File**, **Nuovo progetto**.

1.  I modelli di progetto del pacchetto VSIX degli esempi di PTVS sono disponibili in **Python**, **Esempi**.  Selezionare **Polls Django Web Project** e fare clic su OK per creare il progetto.

  	![Finestra di dialogo Nuovo progetto](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1.  Verrà richiesto di installare pacchetti esterni.  Selezionare **Installa in un ambiente virtuale**.

  	![Finestra di dialogo dei pacchetti esterni](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1.  Selezionare **Python 2.7** come interprete di base.

  	![Finestra di dialogo Aggiungi ambiente virtuale](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1.  Fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **Python**, **Django Sync DB**.

  	![Comando Django Sync DB](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSyncDB.png)

1.  Verrà aperta la console di gestione di Django.  Seguire le istruzioni visualizzate per creare un utente.

    Verrà creato un database sqlite nella cartella di progetto.

  	![Finestra della console di gestione di Django](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Verificare che l'applicazione funzioni premendo <kbd>F5</kbd>.

1.  Fare clic su **Log in** sulla barra di spostamento in alto.

  	![Web browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Immettere le credenziali per l'utente creato al momento della sincronizzazione del database.

  	![Web browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Fare clic su **Create Sample Polls**.

  	![Web browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1.  Fare clic su un sondaggio e votare.

  	![Web browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

## Creare un database MySQL

Per il database verrà creato un database ospitato MySQL di ClearDB in Azure.

In alternativa, è possibile creare una propria macchina virtuale in esecuzione in Azure, quindi installare e amministrare MySQL manualmente.

Per creare un database con un piano gratuito, attenersi alla procedura seguente.

1.  Accedere al [portale di gestione di Azure][].

1.  Nella parte inferiore del pannello di navigazione fare clic su **NUOVO**.

  	![Pulsante Nuovo](./media/web-sites-python-ptvs-django-mysql/PollsCommonAzurePlusNew.png)

1.  Fare clic su **NEGOZIO**, quindi su **ClearDB MySQL Database**.

  	![Finestra di dialogo Scegli componente aggiuntivo](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon1.png)

1.  In Nome digitare un nome da usare per il servizio di database.

1.  Scegliere un'area o un gruppo di affinità in cui posizionare il servizio di database. Se si userà il database dalla propria applicazione Azure, selezionare la stessa area in cui verrà distribuita l'applicazione.

  	![Finestra di dialogo Personalizza componente aggiuntivo](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon2.png)

1.  Fare clic su **ACQUISTA**.

## Configurare il progetto

In questa sezione verrà configurata l'applicazione per usare il database MySQL appena creato.  Verranno fornite informazioni su come ottenere le impostazioni di connessione dal portale di Azure.  Verranno inoltre installati i pacchetti Python aggiuntivi necessari per usare i database MySQL con Django,  quindi verrà eseguita l'applicazione in locale.

1.  Nel [portale di gestione di Azure][] fare clic su **COMPONENTI AGGIUNTIVI**, quindi fare clic sul servizio ClearDB MySQL Database creato in precedenza.

1.  Fare clic su **INFORMAZIONI DI CONNESSIONE**.  È possibile usare il pulsante Copia per inserire il valore di **CONNECTIONSTRING** negli Appunti.

  	![Finestra di dialogo Informazioni di connessione](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLConnectionInfo.png)

1.  In Visual Studio aprire **settings.py** dalla cartella  *ProjectName*.  Incollare temporaneamente la stringa di connessione nell'editor.  La stringa di connessione è nel formato seguente:

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

    Modificare il database predefinito **ENGINE** in modo che usi MySQL e impostare i valori relativi a **NAME**, **USER**, **PASSWORD** e **HOST** da **CONNECTIONSTRING**.

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<Database>',
                'USER': '<User Id>',
                'PASSWORD': '<Password>',
                'HOST': '<Data Source>',
                'PORT': '',
            }
        }


1.  In Esplora soluzioni, in **Python Environments** fare clic con il pulsante destro del mouse sull'ambiente virtuale e scegliere **Install Python Package**.

1. Installare il pacchetto  `mysql-python` usando **easy_install**.

  	![Finestra di dialogo per l'installazione del pacchetto](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1.  Fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **Python**, **Django Sync DB**.  

    Verranno in tal modo create le tabelle per il database MySQL creato nella sezione precedente.  Seguire le istruzioni per creare un utente, che non deve necessariamente corrispondere all'utente nel database sqlite creato nella prima sezione.

  	![Finestra della console di gestione di Django](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Eseguire l'applicazione con <kbd>F5</kbd>.  I sondaggi creati con **Create Sample Polls** e i dati inviati mediante voto verranno serializzati nel database MySQL.

## Pubblicare in un sito Web di Azure

PTVS offre un semplice metodo per distribuire l'applicazione Web in un sito Web di Azure.

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **Pubblica**.

  	![Finestra di dialogo Pubblica sito Web](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1.  Fare clic su **Siti Web di Microsoft Azure**.

1.  Fare clic su **Nuovo** per creare un nuovo sito.

1.  Selezionare un nome in **Nome sito** e un'area in **Area**, quindi fare clic su **Crea**.

  	![Finestra di dialogo Crea sito in Microsoft Azure](./media/web-sites-python-ptvs-django-mysql/PollsCommonCreateWebSite.png)

1.  Accettare tutte le altre impostazioni predefinite e fare clic su **Pubblica**.

1.  Il sito pubblicato verrà aperto automaticamente nel Web browser.  L'applicazione dovrebbe funzionare come previsto, usando il database **MySQL** ospitato in Azure.

    Congratulazioni.

  	![Web browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

## Passaggi successivi

Usare i collegamenti seguenti per altre informazioni su Python Tools per Visual Studio, Django e MySQL.

- [Documentazione di Python Tools per Visual Studio][]
  - [Progetti Web][]
  - [Progetti servizio cloud][]
  - [Debug remoto in Microsoft Azure][]
- [Documentazione di Django][]
- [MySQL][]


<!--Link references-->
[Centro per sviluppatori Python]: /develop/python/
[Servizi cloud di Azure]: cloud-services-python-ptvs.md

<!--External Link references-->
[Portale di gestione di Azure]: https://manage.windowsazure.com
[Python Tools per Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[VSIX di esempio di Python Tools 2.1 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Strumenti di Azure SDK per VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Strumenti di Azure SDK per VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 a 32 bit]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Documentazione di Python Tools per Visual Studio]: http://pytools.codeplex.com/documentation
[Debug remoto in Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Progetti Web]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Progetti servizio cloud]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Documentazione di Django]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/

<!--HONumber=52-->