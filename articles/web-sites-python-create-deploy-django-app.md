<properties 
	pageTitle="Siti Web Python con Django - Esercitazione di Azure" 
	description="Un'esercitazione che illustra l'esecuzione di un sito Web di Python in Azure." 
	services="web-sites" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="12/17/2014" 
	ms.author="huvalo"/>




# Creazione di siti Web con Django

Questa esercitazione illustra le operazioni iniziali per l'esecuzione di Python nel servizio Siti Web di Azure,  che offre hosting gratuito limitato e capacità di distribuzione rapida, oltre alla possibilità di usare Python.  Se la crescita dell'applicazione lo richiede, è possibile passare all'hosting a pagamento e avvalersi dell'integrazione con tutti gli altri servizi di Azure.

Verrà creata un'applicazione usando il framework Web di Django (vedere le versioni alternative di questa esercitazione per [Flask](../web-sites-python-create-deploy-flask-app) e [Bottle](../web-sites-python-create-deploy-bottle-app)).  Verrà creato il sito Web dalla raccolta di Azure, verrà configurata la distribuzione Git e verrà clonato l'archivio localmente.  Infine, verrà eseguita l'applicazione localmente, verranno apportate modifiche e ne verrà eseguito il commit e il push in Azure.  L'esercitazione illustra come eseguire queste operazioni da Windows o Mac/Linux.

> [AZURE.NOTE]
> Per completare l'esercitazione, è necessario un account Azure. È possibile <a href="http://azure.microsoft.com/it-it/pricing/member-offers/msdn-benefits-details/">attivare i benefici della sottoscrizione MSDN</a> o <a href="http://azure.microsoft.com/it-it/pricing/free-trial/">iscriversi per una versione di valutazione gratuita</a>.
> 
> Per iniziare a usare Siti Web di Azure prima di iscriversi per ottenere un account, visitare la pagina all'indirizzo <a href="https://trywebsites.azurewebsites.net/?language=python">https://trywebsites.azurewebsites.net</a>, in cui è possibile creare immediatamente e gratuitamente un sito di base ASP.NET temporaneo in Siti Web di Azure. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.


+ [Prerequisiti](#prerequisites)
+ [Creazione di un sito Web sul portale](#website-creation-on-portal)
+ [Informazioni generali sull'applicazione](#application-overview)
+ Sviluppo del sito Web
  + [Windows - Python Tools per Visual Studio](#website-development-windows-ptvs)
  + [Windows - Riga di comando](#website-development-windows-command-line)
  + [Mac/Linux - Riga di comando](#website-development-mac-linux-command-line)
+ [Risoluzione dei problemi - Distribuzione](#troubleshooting-deployment)
+ [Risoluzione dei problemi - Installazione dei pacchetti](#troubleshooting-package-installation)
+ [Risoluzione dei problemi - Ambiente virtuale](#troubleshooting-virtual-environment)
+ [Risoluzione dei problemi - File statici](#troubleshooting-static-files)
+ [Risoluzione dei problemi - Impostazioni](#troubleshooting-settings)
+ [Uso di un database](#using-a-database)
+ [Interfaccia di amministrazione di Django](#django-admin-interface)
+ [Passaggi successivi](#next-steps)


<h2><a name="prerequisites"></a>Prerequisiti</h2>

- Windows, Mac o Linux
- Python 2.7 o 3.4
- setuptools, pip, virtualenv (solo Python 2.7)
- Git
- Python Tools per Visual Studio (facoltativo)

**Nota**: la pubblicazione TFS non è attualmente supportata per progetti Python.

### Windows

Se non è già stato installato Python 2.7 o 3.4 (32 bit), si consiglia di installare [Azure SDK per Python 2.7](http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409) o [Azure SDK per Python 3.4](http://go.microsoft.com/fwlink/?LinkID=516990&clcid=0x409) tramite Installazione guidata piattaforma Web.  Questa operazione installa la versione a 32 bit di Python, setuptools, pip, virtualenv e così via (Python a 32 bit viene installato nei computer host di Azure).  In alternativa, è possibile ottenere Python da [python.org](http://www.python.org/).

Per Git, è consigliabile [Git per Windows](http://msysgit.github.io/). Questa esercitazione usa la shell di Git da Git per Windows.  Se si usa Visual Studio, è possibile usare il supporto Git integrato.

Si consiglia inoltre di installare [Python Tools per Visual Studio](http://pytools.codeplex.com).  Quest'ultimo è facoltativo, tuttavia, se si dispone di [Visual Studio](http://www.visualstudio.com/), inclusa l'applicazione gratuita Visual Studio Express 2013 per Web, si avrà a disposizione un eccellente ambiente IDE per Python.

### Mac/Linux

Python e Git dovrebbero essere già installati, tuttavia, assicurarsi di disporre di Python 2.7 o 3.4.


<h2><a name="website-creation-on-portal"></a>Creazione di un sito Web sul portale</h2>

Il primo passaggio per la creazione di un'app consiste nella creazione del sito Web tramite il portale di gestione di Azure.  A tale scopo, è necessario accedere al portale e fare clic sul pulsante **NUOVO** nell'angolo inferiore sinistro. Verrà visualizzata una finestra. Fare clic su **CALCOLO**, **SITO WEB**, quindi su **DA RACCOLTA**.

![](./media/web-sites-python-create-deploy-django-app/portal-create-site.png)

Verrà visualizzata una finestra di dialogo che elenca le applicazioni disponibili nella raccolta. Fare clic sulla categoria **FRAMEWORK APP** a sinistra e selezionare **Django**.

![](./media/web-sites-python-create-deploy-django-app/portal-gallery-django.png)

Nella pagina successiva, immettere un nome e un'area per il sito e fare clic sul pulsante Completa.

Il sito verrà configurato rapidamente. È possibile fare clic sul pulsante **SFOGLIA** dalla barra degli strumenti in basso per visualizzare la nuova applicazione Django in esecuzione su Azure.

![](./media/web-sites-python-create-deploy-django-app/portal-website-django.png)
 
Verrà quindi aggiunto il supporto per la pubblicazione tramite Git.  A tale scopo, è possibile scegliere **Imposta distribuzione dal controllo del codice sorgente**.

![](./media/web-sites-python-create-deploy-django-app/portal-site-created.png)

Nella finestra di dialogo **Imposta distribuzione**, scorrere verso il basso e selezionare l'opzione **Archivio Git locale**. Fare clic sulla freccia destra per continuare.

![](./media/web-sites-python-create-deploy-django-app/portal-setup-deployment.png)

Dopo la configurazione della pubblicazione Git, verrà visualizzata momentaneamente una pagina che indica che l'archivio è in fase di creazione. Quando è pronto, verranno visualizzate le istruzioni su come connettersi.  

![](./media/web-sites-python-create-deploy-django-app/portal-repo-created.png)

Le seguenti sezioni fanno riferimento a tali istruzioni.


<h2><a name="application-overview"></a>Informazioni generali sull'applicazione</h2>

### Contenuti dell'archivio Git

Di seguito vengono fornite informazioni generali sui file disponibili nell'archivio Git iniziale, che verrà clonato nella sezione successiva.

    \app\__init__.py
    \app\forms.py
    \app\models.py
    \app\tests.py
    \app\views.py
    \app\static\content\
    \app\static\fonts\
    \app\static\scripts\
    \app\templates\about.html
    \app\templates\contact.html
    \app\templates\index.html
    \app\templates\layout.html
    \app\templates\login.html
    \app\templates\loginpartial.html
    \DjangoWebProject\__init__.py
    \DjangoWebProject\settings.py
    \DjangoWebProject\urls.py
    \DjangoWebProject\wsgi.py

Origini principali per l'applicazione.  3 pagine (Index, About, Contact) con un layout master.  Il contenuto statico e gli script includono bootstrap, jquery, modernizr e respond.

    \manage.py

Gestione locale e supporto del server di sviluppo. Consente di eseguire l'applicazione localmente, sincronizzare il database e così via.

    \db.sqlite3

Database predefinito. Include le tabelle necessarie per l'applicazione da eseguire, ma non contiene utenti (sincronizzare il database per creare un utente).

    \DjangoWebProject.pyproj
    \DjangoWebProject.sln

File di progetto da usare con [Python Tools per Visual Studio](http://pytools.codeplex.com).

    \ptvs_virtualenv_proxy.py

Proxy IIS per ambienti virtuali e supporto per il debug remoto di PTVS.

    \requirements.txt

Pacchetti esterni richiesti dall'applicazione. Lo script di distribuzione eseguirà l'installazione tramite pip dei pacchetti elencati in questo file.
 
    \web.2.7.config
    \web.3.4.config

File di configurazione di IIS.  Lo script di distribuzione usa il file web.x.y.config appropriato e lo copia come web.config.

### File facoltativi - Personalizzazione della distribuzione

[AZURE.INCLUDE [web-sites-python-customizing-deployment](../includes/web-sites-python-customizing-deployment.md)]

### File facoltativi - Runtime di Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../includes/web-sites-python-customizing-runtime.md)]

### File aggiuntivi sul server

Alcuni file sono presenti sul server, ma non vengono aggiunti all'archivio Git.  Vengono creati dallo script di distribuzione.

    \web.config

File di configurazione di IIS.  Creato da web.x.y.config in ogni distribuzione.

    \env\

Ambiente virtuale Python.  Creato durante la distribuzione se nel sito non esiste già un ambiente virtuale compatibile.  I pacchetti elencati in requirements.txt vengono installati tramite pip, tuttavia pip non eseguirà l'installazione se i pacchetti sono già installati.

Le 3 sezioni successive descrivono come procedere con lo sviluppo del sito Web in 3 ambienti diversi:

- Windows, con Python Tools per Visual Studio
- Windows, con la riga di comando
- Mac/Linux, con la riga di comando


<h2><a name="website-development-windows-ptvs"></a>Sviluppo del sito Web - Windows - Python Tools per Visual Studio</h2>

### Clonare l'archivio

Innanzitutto, clonare l'archivio usando l'URL fornito nel portale Azure.

![](./media/web-sites-python-create-deploy-django-app/ptvs-git-clone.png)

Aprire il file di soluzione (.sln) incluso nella radice dell'archivio.

![](./media/web-sites-python-create-deploy-django-app/ptvs-solution-django.png)

### Creare un ambiente virtuale

Ora verrà creato un ambiente virtuale per lo sviluppo locale.  Fare clic con il pulsante destro del mouse su **Ambienti Python** e selezionare **Aggiungi ambiente virtuale**.

- Assicurarsi che il nome dell'ambiente corrisponda a  `env`.

- Selezionare l'interprete di base.  Assicurarsi di usare la stessa versione di Python selezionata per il sito (in runtime.txt o nella pagina di configurazione del sito).

- Assicurarsi che sia selezionata l'opzione per scaricare e installare i pacchetti.

![](./media/web-sites-python-create-deploy-django-app/ptvs-add-virtual-env-27.png)

Fare clic su **Crea**.  Verrà creato l'ambiente virtuale e verranno installate le dipendenze elencate in requirements.txt.

### Creare un utente avanzato

Nel database incluso con l'applicazione non è definito un utente avanzato.  Per usare la funzionalità di accesso nell'applicazione o l'interfaccia di amministrazione di Django (se si decide di abilitarla), è necessario creare un utente avanzato.

Attivare il seguente comando dalla riga di comando nella cartella del progetto:

    env\scripts\python manage.py createsuperuser

Seguire le istruzioni per impostare il nome utente, la password e così via.

### Eseguire tramite il server di sviluppo

Premere F5 per avviare il debug. Il Web browser verrà aperto automaticamente nella pagina in esecuzione in locale.

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

È possibile impostare punti di interruzione nelle origini, usare le finestre Espressioni di controllo e così via. Per altre informazioni sulle varie funzionalità, vedere la [documentazione di PTVS](http://pytools.codeplex.com/documentation).

### Apportare modifiche

È ora possibile sperimentare apportando modifiche alle origini e/o ai modelli dell'applicazione.

Dopo aver verificato le modifiche, eseguirne il commit nell'archivio Git:

![](./media/web-sites-python-create-deploy-django-app/ptvs-commit-django.png)

### Installare più pacchetti

È possibile che l'applicazione includa altre dipendenze oltre a quelle relative a Python e Django.

È possibile installare altri pacchetti tramite pip.  Per installare un pacchetto, fare clic con il pulsante destro del mouse sull'ambiente virtuale e selezionare **Installa pacchetto Python**.

Ad esempio, per installare Azure SDK per Python, che consente di accedere all'archiviazione di Azure, al bus di servizio e ad altri servizi di Azure, immettere  `azure`:

![](./media/web-sites-python-create-deploy-django-app/ptvs-install-package-dialog.png)

Fare clic con il pulsante destro del mouse sull'ambiente virtuale e selezionare **Genera requirements.txt** per aggiornare requirements.txt.

Quindi, eseguire il commit delle modifiche apportate a requirements.txt nell'archivio Git.

### Distribuire in Azure

Per attivare una distribuzione, fare clic su **Sincronizza** o **Push**.  Sincronizza esegue sia un'operazione di push che di pull.

![](./media/web-sites-python-create-deploy-django-app/ptvs-git-push.png)

La prima distribuzione richiede tempo, in quanto crea un ambiente virtuale, installa i pacchetti e così via.

Visual Studio non visualizza lo stato di avanzamento della distribuzione.  Se si desidera esaminare l'output, vedere la sezione [Risoluzione dei problemi - Distribuzione](#troubleshooting-deployment).

Passare all'URL di Azure per visualizzare le modifiche.


<h2><a name="website-development-windows-command-line"></a>Sviluppo del sito Web - Windows - Riga di comando</h2>

### Clonare l'archivio

Innanzitutto, clonare l'archivio usando l'URL fornito nel portale Azure e aggiungere l'archivio Azure come remoto.

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Creare un ambiente virtuale

Verrà creato un nuovo ambiente virtuale per scopi di sviluppo (non aggiungerlo all'archivio).  Gli ambienti virtuali in Python non sono ricollocabili, pertanto tutti gli sviluppatori che lavorano all'applicazione creeranno il proprio ambiente in locale.

Assicurarsi di usare la stessa versione di Python selezionata per il sito (in runtime.txt o nella pagina di configurazione del sito)

Per Python 2.7:

    c:\python27\python.exe -m virtualenv env

Per Python 3.4:

    c:\python34\python.exe -m venv env

Installare gli eventuali pacchetti esterni richiesti dall'applicazione. Per installare i pacchetti nell'ambiente virtuale, è possibile usare il file requirements.txt nella radice dell'archivio:

    env\scripts\pip install -r requirements.txt

### Creare un utente avanzato

Nel database incluso con l'applicazione non è definito un utente avanzato.  Per usare la funzionalità di accesso nell'applicazione o l'interfaccia di amministrazione di Django (se si decide di abilitarla), è necessario creare un utente avanzato.

Attivare il seguente comando dalla riga di comando nella cartella del progetto:

    env\scripts\python manage.py createsuperuser

Seguire le istruzioni per impostare il nome utente, la password e così via.

### Eseguire tramite il server di sviluppo

È possibile avviare l'applicazione in un server di sviluppo con il seguente comando:

    env\scripts\python manage.py runserver

La console visualizza l'URL e la porta di ascolto del server:

![](./media/web-sites-python-create-deploy-django-app/windows-run-local-django.png)

Quindi, aprire il Web browser con tale URL.

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

### Apportare modifiche

È ora possibile sperimentare apportando modifiche alle origini e/o ai modelli dell'applicazione.

Dopo aver verificato le modifiche, eseguirne il commit nell'archivio Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Installare più pacchetti

È possibile che l'applicazione includa altre dipendenze oltre a quelle relative a Python e Django.

È possibile installare altri pacchetti tramite pip.  Ad esempio, per installare Azure SDK per Python, che consente di accedere all'archiviazione di Azure, al bus di servizio e ad altri servizi di Azure, digitare:

    env\scripts\pip install azure

Assicurarsi di aggiornare requirements.txt:

    env\scripts\pip freeze > requirements.txt

Eseguire il commit delle modifiche:

    git add requirements.txt
    git commit -m "Added azure package"

### Distribuire in Azure

Per avviare una distribuzione, effettuare il push delle modifiche in Azure:

    git push azure master

Verrà visualizzato l'output dello script di distribuzione, tra cui la creazione dell'ambiente virtuale, l'installazione dei pacchetti, la creazione di web. config.

Passare all'URL di Azure per visualizzare le modifiche.


<h2><a name="website-development-mac-linux-command-line"></a>Sviluppo del sito Web - Mac/Linux - Riga di comando</h2>

### Clonare l'archivio

Innanzitutto, clonare l'archivio usando l'URL fornito nel portale Azure e aggiungere l'archivio Azure come remoto.

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Creare un ambiente virtuale

Verrà creato un nuovo ambiente virtuale per scopi di sviluppo (non aggiungerlo all'archivio).  Gli ambienti virtuali in Python non sono ricollocabili, pertanto tutti gli sviluppatori che lavorano all'applicazione creeranno il proprio ambiente in locale.

Assicurarsi di usare la stessa versione di Python selezionata per il sito (in runtime.txt o nella pagina di configurazione del sito).

Per Python 2.7:

    python -m virtualenv env

Per Python 3.4:

    python -m venv env

Installare gli eventuali pacchetti esterni richiesti dall'applicazione. Per installare i pacchetti nell'ambiente virtuale, è possibile usare il file requirements.txt nella radice dell'archivio:

    env/bin/pip install -r requirements.txt

### Creare un utente avanzato

Nel database incluso con l'applicazione non è definito un utente avanzato.  Per usare la funzionalità di accesso nell'applicazione o l'interfaccia di amministrazione di Django (se si decide di abilitarla), è necessario creare un utente avanzato.

Attivare il seguente comando dalla riga di comando nella cartella del progetto:

    env/bin/python manage.py createsuperuser

Seguire le istruzioni per impostare il nome utente, la password e così via.

### Eseguire tramite il server di sviluppo

È possibile avviare l'applicazione in un server di sviluppo con il seguente comando:

    env/bin/python manage.py runserver

La console visualizza l'URL e la porta di ascolto del server:

![](./media/web-sites-python-create-deploy-django-app/mac-run-local-django.png)

Quindi, aprire il Web browser con tale URL.

![](./media/web-sites-python-create-deploy-django-app/mac-browser-django.png)

### Apportare modifiche

È ora possibile sperimentare apportando modifiche alle origini e/o ai modelli dell'applicazione.

Dopo aver verificato le modifiche, eseguirne il commit nell'archivio Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Installare più pacchetti

È possibile che l'applicazione includa altre dipendenze oltre a quelle relative a Python e Django.

È possibile installare altri pacchetti tramite pip.  Ad esempio, per installare Azure SDK per Python, che consente di accedere all'archiviazione di Azure, al bus di servizio e ad altri servizi di Azure, digitare:

    env/bin/pip install azure

Assicurarsi di aggiornare requirements.txt:

    env/bin/pip freeze > requirements.txt

Eseguire il commit delle modifiche:

    git add requirements.txt
    git commit -m "Added azure package"

### Distribuire in Azure

Per avviare una distribuzione, effettuare il push delle modifiche in Azure:

    git push azure master

Verrà visualizzato l'output dello script di distribuzione, tra cui la creazione dell'ambiente virtuale, l'installazione dei pacchetti, la creazione di web. config.

Passare all'URL di Azure per visualizzare le modifiche.


<h2><a name="troubleshooting-deployment"></a>Risoluzione dei problemi - Distribuzione</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-deployment](../includes/web-sites-python-troubleshooting-deployment.md)]


<h2><a name="troubleshooting-package-installation"></a>Risoluzione dei problemi - Installazione dei pacchetti</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../includes/web-sites-python-troubleshooting-package-installation.md)]


<h2><a name="troubleshooting-virtual-environment"></a>Risoluzione dei problemi - Ambiente virtuale</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../includes/web-sites-python-troubleshooting-virtual-environment.md)]


<h2><a name="troubleshooting-static-files"></a>Risoluzione dei problemi - File statici</h2>

Django include il concetto di raccolta di file statici,  che preleva tutti i file statici dal percorso originale e li copia in un'unica cartella.  Per questa applicazione, vengono copiati in  `/static`.

Questa operazione viene eseguita perché i file statici possono provenire da diverse  'apps' Django.  Ad esempio, i file statici delle interfacce di amministrazione di Django si trovano in una sottocartella della libreria Django nell'ambiente virtuale.  I file statici definiti dall'applicazione si trovano in  `/app/static`.  Poiché si usano più  'apps' Django, saranno presenti file statici in varie posizioni.

Quando viene eseguita in modalità di debug, l'applicazione gestisce i file statici dal percorso originale.

Quando viene eseguita in modalità di rilascio, l'applicazione **non** gestisce i file statici.  Il server Web è responsabile di gestire i file.  Per questa applicazione, IIS gestisce i file statici da  `/static`.

La raccolta dei file statici viene eseguita automaticamente come parte dello script di distribuzione, eliminando i file precedentemente raccolti.  Questo significa che la raccolta viene eseguita ad ogni distribuzione, rallentando leggermente il processo, tuttavia garantisce che i file obsoleti non siano disponibili, evitando un potenziale problema di protezione. 

Se non si desidera eseguire la raccolta di file statici per l'applicazione Django:

    \.skipDjango 

Quindi, è necessario eseguire la raccolta manualmente sul computer locale:

    env\scripts\python manage.py collectstatic

Quindi, rimuovere la cartella  `\static` da  `.gitignore` e aggiungerla all'archivio Git.


<h2><a name="troubleshooting-settings"></a>Risoluzione dei problemi - Impostazioni</h2>

È possibile modificare diverse impostazioni per l'applicazione in  `DjangoWebProject/settings.py`.

Per comodità dello sviluppatore, la modalità di debug è abilitata.  Uno dei vantaggi che questo comporta è la possibilità di visualizzare immagini e altri contenuti statici durante l'esecuzione in locale, senza dover raccogliere i file statici.

Per disabilitare la modalità di debug:

    DEBUG = False

Quando la modalità di debug è disabilitata, è necessario aggiornare il valore di  `ALLOWED_HOSTS` in modo che includa il nome host di Azure.  Ad esempio:

    ALLOWED_HOSTS = (
        'pythonapp.azurewebsites.net',
    )

o per abilitarli:

    ALLOWED_HOSTS = (
        '*',
    )
 
In pratica, si potrebbero voler eseguire operazioni più complesse per gestire il passaggio dalla modalità di debug alla modalità di rilascio e ottenere il nome host.

È possibile impostare le variabili di ambiente tramite la pagina **CONFIGURA** del portale Azure, nella sezione **Impostazioni app**.  Questo può essere utile per impostare valori che non si desidera visualizzare nelle origini (stringhe di connessione, password e così via) o che si desidera impostare in modo diverso in Azure e sul computer locale.  In  `settings.py`, è possibile eseguire query sulle variabili di ambiente usando  `os.getenv`.


<h2><a name="using-a-database"></a>Uso di un database</h2>

Il database incluso con l'applicazione è un database sqlite.  Si tratta di un database predefinito utile e pratico da usare per lo sviluppo, poiché richiede pochissime operazioni di configurazione.  Il database è archiviato nel file db.sqlite3 nella cartella del progetto.

Azure offre servizi di database facili da usare da un'applicazione Django.  Le esercitazioni sull'uso di [Database SQL ](../web-sites-python-ptvs-django-sql) e [MySQL](../web-sites-python-ptvs-django-mysql) da un'applicazione Django illustrano i passaggi necessari per creare il servizio di database, modificare le impostazioni del database in  `DjangoWebProject/settings.py` e le librerie necessarie per l'installazione.

Naturalmente, se si preferisce gestire i propri server di database, è possibile farlo usando macchine virtuali Windows o Linux in esecuzione su Azure.


<h2><a name="django-admin-interface"></a>Interfaccia di amministrazione di Django</h2>

Una volta iniziati a creare i modelli, si consiglia di popolare il database con alcuni dati.  Un modo semplice per aggiungere e modificare i contenuti in modo interattivo consiste nell'usare l'interfaccia di amministrazione di Django.

Il codice per l'interfaccia di amministrazione è impostato come commento nelle origini dell'applicazione, tuttavia è contrassegnato in modo chiaro, per poterlo abilitare facilmente (cercare  'admin').

Dopo averlo abilitato, sincronizzare il database, eseguire l'applicazione e passare a  `/admin`.


<h2><a name="next-steps"></a>Passaggi successivi</h2>

Visitare i seguenti collegamenti per altre informazioni su Django e Python Tools per Visual Studio: 
 
- [Documentazione di Django][]
- [Documentazione di Python Tools per Visual Studio][] 

Per informazioni sull'uso di Database SQL e MySQL:

- [Django e Database SQL in Azure con Python Tools 2.1 per Visual Studio][]
- [Django e MySQL in Azure con Python Tools 2.1 per Visual Studio][]


<!--Link references-->
[Django e MySQL in Azure con Python Tools 2.1 per Visual Studio]: ../web-sites-python-ptvs-django-mysql
[Django e Database SQL in Azure con Python Tools 2.1 per Visual Studio]: ../web-sites-python-ptvs-django-sql

<!--External Link references-->
[Documentazione di Python Tools per Visual Studio]: http://pytools.codeplex.com/documentation 
[Documentazione di Django]: https://www.djangoproject.com/



<!--HONumber=42-->
