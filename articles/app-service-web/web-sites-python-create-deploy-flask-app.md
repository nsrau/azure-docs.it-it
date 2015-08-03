<properties 
	pageTitle="Creazione di app Web con Flask in Azure" 
	description="Esercitazione che illustra l'esecuzione di un'app Web Python in Azure." 
	services="app-service\web" 
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
	ms.date="04/15/2015" 
	ms.author="huguesv"/>




# Creazione di app Web con Flask in Azure

Questa esercitazione illustra le operazioni iniziali per l'esecuzione di Python in [App Web del servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Le app Web di Azure offrono hosting gratuito limitato e capacità di distribuzione rapida, oltre alla possibilità di utilizzare Python! Se la crescita dell'applicazione lo richiede, è possibile passare all'hosting a pagamento e avvalersi dell'integrazione con tutti gli altri servizi di Azure.

Verrà creata un'applicazione usando il framework Web di Flask (vedere le versioni alternative di questa esercitazione per [Django](web-sites-python-create-deploy-django-app.md) e [Bottle](web-sites-python-create-deploy-bottle-app.md)). Verrà creato il sito Web dalla raccolta di Azure, sarà configurata la distribuzione Git e si procederà alla clonazione locale del repository. Quindi verrà eseguita l'applicazione localmente, si apporteranno le modifiche, e queste verranno successivamente sottoposte al commit e al push in Azure. Nell'esercitazione viene illustrato come eseguire queste operazioni da Windows o Mac/Linux.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Prerequisiti

- Windows, Mac o Linux
- Python 2.7 o 3.4
- setuptools, pip, virtualenv (solo Python 2.7)
- Git
- [Python Tools 2.1 per Visual Studio](optional)

**Nota**: la pubblicazione TFS non è attualmente supportata per progetti Python.

### Windows

Se non è già installato Python 2.7 o 3.4 (32 bit), si consiglia di installare [Azure SDK per Python 2.7] o [Azure SDK per Python 3.4] mediante Installazione guidata piattaforma Web. In tal modo viene installata la versione a 32 bit di Python, setuptools, pip, virtualenv e così via (Python a 32 bit è la versione installata sui computer host di Azure). In alternativa, è possibile ottenere Python da [python.org].

Per Git, è consigliabile [Git per Windows] o [GitHub per Windows]. Se si utilizza Visual Studio, è possibile utilizzare il supporto Git integrato.

È inoltre consigliabile installare [Python Tools 2.1 per Visual Studio]. Si tratta di un'operazione facoltativa, ma se si dispone di [Visual Studio], inclusa la versione gratuita di Visual Studio Community 2013 o Visual Studio Express 2013 per il Web, si otterrà anche l'IDE Python.

### Mac/Linux

È necessario avere già installato Python e Git, ma assicurarsi di disporre di Python 2.7 o 3.4.


## Creazione di un'app Web nel portale di Azure

Il primo passaggio per la creazione di un'app consiste nella creazione dell'app Web tramite il [portale di Azure](https://portal.azure.com).

1. Accedere al portale di Azure e scegliere il **nuovo** pulsante nell'angolo inferiore sinistro. 
2. Fare clic su **Web + Mobile** > **Azure Marketplace** > **App Web**.
3. Nella casella di ricerca digitare "python".
4. Nei risultati della ricerca selezionare **Flask** e quindi fare clic su **Crea**.
5. Configurare la nuova app Flask, ad esempio creando un nuovo piano di servizio app e un nuovo gruppo di risorse correlato. Fare quindi clic su **Crea**.
6. Configurare la pubblicazione Git per l'applicazione web appena creato seguendo le istruzioni in [la distribuzione continua utilizzando GIT nel servizio di Azure App](web-sites-publish-source-control.md).


## Informazioni generali sull'applicazione

### Contenuti del repository Git

Di seguito viene fornita una panoramica dei file contenuti nel repository Git iniziale, che saranno clonati nella sezione successiva.

    \FlaskWebProject__init__.py
    \FlaskWebProject\views.py
    \FlaskWebProject\static\content\
    \FlaskWebProject\static\fonts\
    \FlaskWebProject\static\scripts\
    \FlaskWebProject\templates\about.html
    \FlaskWebProject\templates\contact.html
    \FlaskWebProject\templates\index.html
    \FlaskWebProject\templates\layout.html

Fonti principali per l'applicazione. È composta da 3 pagine (indice, informazioni su, contatti) con un layout master. Il contenuto statico e gli script includono bootstrap, jquery, modernizr e respond.

    \runserver.py

Supporto del server di sviluppo locale. Consente di eseguire l'applicazione localmente.

    \FlaskWebProject.pyproj
    \FlaskWebProject.sln

File di progetto da utilizzare con [Python Tools per Visual Studio](http://pytools.codeplex.com).

    \ptvs_virtualenv_proxy.py

Proxy IIS per ambienti virtuali e supporto del debug remoto PTVS.

    \requirements.txt

Pacchetti esterni necessari da parte di questa applicazione. Lo script di distribuzione eseguirà l'installazione di pip dei pacchetti elencati in questo file.
 
    \web.2.7.config
    \web.3.4.config

File di configurazione IIS. Lo script di distribuzione utilizzerà il file appropriato web.x.y.config e lo copierà come web.config.

### File facoltativi - Personalizzazione della distribuzione

[AZURE.INCLUDE [web-sites-python-customizing-deployment](../../includes/web-sites-python-customizing-deployment.md)]

### File facoltativi - Runtime Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

### Altri file sul server

Alcuni file sono presenti sul server ma non vengono aggiunti al repository Git. Si tratta di file creati dallo script di distribuzione.

    \web.config

File di configurazione IIS. Creato da web.x.y.config per ogni distribuzione.

    \env\

Ambiente virtuale Python. Creato durante la distribuzione se nell'app non esiste già un ambiente virtuale compatibile. I pacchetti elencati in requirements.txt vengono installati con pip. Tuttavia, se i pacchetti sono installati, l'installazione di pip non verrà eseguita.

Nelle tre sezioni successive viene descritto come procedere con lo sviluppo dei siti Web in tre ambienti diversi:

- Windows, con Python Tools per Visual Studio
- Windows, con la riga di comando
- Mac/Linux, con la riga di comando


## Sviluppo di siti Web - Windows - Python Tools per Visual Studio

### Clonare il repository

Innanzitutto, clonare il repository utilizzando l'URL fornito sul portale di Azure. Per ulteriori informazioni, vedere [la distribuzione continua utilizzando GIT nel servizio di Azure App](web-sites-publish-source-control.md).

Aprire il file della soluzione (.sln) incluso nella radice del repository.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-solution-flask.png)

### Creare l'ambiente virtuale

A questo punto verrà creato un ambiente virtuale per lo sviluppo locale. Fare clic con il pulsante destro del mouse su **Ambienti Python** selezionare **Aggiungi ambiente virtuale....**.

- Assicurarsi che il nome dell'ambiente sia `env`.

- Selezionare l'interprete di base. Assicurarsi di utilizzare la stessa versione di Python è selezionata per l'applicazione web (in runtime.txt o **le impostazioni dell'applicazione** blade dell'applicazione web nel portale di Azure).

- Assicurarsi che l'opzione per scaricare e installare i pacchetti sia selezionata.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-add-virtual-env-27.png)

Fare clic su **Create**. In tal modo verrà creato l'ambiente virtuale e verranno installate le dipendenze elencate in requirements.txt.

### Eseguire mediante il server di sviluppo

Premere F5 per avviare il debug. Il Web browser si aprirà automaticamente sulla pagina in esecuzione locale.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

È possibile impostare punti di interruzione nelle origini, utilizzare le finestre Espressioni di controllo e così via. Vedere il [documentazione PTVS] per ulteriori informazioni su varie funzionalità.

### Apportare modifiche

È possibile sperimentare apportando modifiche alle origini applicazioni e/o ai modelli.

Dopo aver testato le modifiche, eseguirne il commit al repository Git:

![](./media/web-sites-python-create-deploy-flask-app/ptvs-commit-flask.png)

### Installare altri pacchetti

L'applicazione potrebbe avere dipendenze oltre Python e Flask.

È possibile installare altri pacchetti utilizzando pip. Per installare un pacchetto, fare clic con il pulsante destro del mouse e selezionare **Installa pacchetto Python**.

Ad esempio, per installare Azure SDK per Python, che fornisce l'accesso all'archivio Azure, al bus di servizio e ad altri servizi Azure, immettere `azure`:

![](./media/web-sites-python-create-deploy-flask-app/ptvs-install-package-dialog.png)

Fare clic con il pulsante destro del mouse sull'ambiente virtuale e selezionare **Genera requirements.txt** per aggiornare requirements.txt.

Quindi, eseguire il commit delle modifiche a requirements.txt al repository Git.

### Distribuzione in Azure

Per attivare una distribuzione, fare clic su **Sincronizza** o su **Push**. La sincronizzazione esegue sia il push che il pull.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-push.png)

La prima distribuzione richiederà un po' di tempo, in quanto verrà creato un ambiente virtuale, si installeranno i pacchetti e così via.

In Visual Studio non viene visualizzato l'avanzamento della distribuzione. Se si desidera rivedere l'output, vedere la sezione in [Risoluzione dei problemi- Distribuzione](#troubleshooting-deployment).

Passare all'URL di Azure per visualizzare le modifiche.


## Sviluppo di siti Web - Windows - Riga di comando

### Clonare il repository

Innanzitutto, clonare il repository utilizzando l'URL fornito sul portale di Azure e aggiungere il repository di Azure come remoto. Per ulteriori informazioni, vedere [la distribuzione continua utilizzando GIT nel servizio di Azure App](web-sites-publish-source-control.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Creare l'ambiente virtuale

Verrà creato un nuovo ambiente virtuale per lo sviluppo (non aggiungerlo al repository). Non è possibile cambiare la posizione degli ambienti virtuali in Python, pertanto, ciascuno sviluppatore che lavora all'applicazione ne creerà una locale.

Assicurarsi di utilizzare la stessa versione di Python è selezionata per l'applicazione web (in runtime.txt o **le impostazioni dell'applicazione** blade dell'applicazione web nel portale di Azure).

Per Python 2.7:

    c:\python27\python.exe -m virtualenv env

Per Python 3.4:

    c:\python34\python.exe -m venv env

Installare tutti i pacchetti esterni richiesti dall'applicazione. È possibile utilizzare il file requirements.txt nella radice del repository per installare i pacchetti nell'ambiente virtuale:

    env\scripts\pip install -r requirements.txt

### Eseguire mediante il server di sviluppo

È possibile avviare l'applicazione in un server di sviluppo con il seguente comando:

    env\scripts\python runserver.py

Sulla console verranno visualizzati l'URL e la porta su cui è in ascolto il server:

![](./media/web-sites-python-create-deploy-flask-app/windows-run-local-flask.png)

Quindi, aprire il Web browser su tale URL.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

### Apportare modifiche

È possibile sperimentare apportando modifiche alle origini applicazioni e/o ai modelli.

Dopo aver testato le modifiche, eseguirne il commit al repository Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Installare altri pacchetti

L'applicazione potrebbe avere dipendenze oltre Python e Flask.

È possibile installare altri pacchetti utilizzando pip. Ad esempio, per installare Azure SDK per Python,che fornisce l'accesso all'archivio Azure, al bus di servizio e ad altri servizi Azure, digitare:

    env\scripts\pip install azure

Assicurarsi di aggiornare requirements.txt:

    env\scripts\pip freeze > requirements.txt

Eseguire il commit delle modifiche:

    git add requirements.txt
    git commit -m "Added azure package"

### Distribuzione in Azure

Per attivare una distribuzione, eseguire il push delle modifiche in Azure:

    git push azure master

Verrà visualizzato l'output dello script di distribuzione, inclusa la creazione dell'ambiente virtuale, l'installazione di pacchetti, la creazione di web.config.

Passare all'URL di Azure per visualizzare le modifiche.


## Sviluppo del sito Web - Mac/Linux - Riga di comando

### Clonare il repository

Innanzitutto, clonare il repository utilizzando l'URL fornito sul portale di Azure e aggiungere il repository di Azure come remoto. Per ulteriori informazioni, vedere [la distribuzione continua utilizzando GIT nel servizio di Azure App](web-sites-publish-source-control.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Creare l'ambiente virtuale

Verrà creato un nuovo ambiente virtuale per lo sviluppo (non aggiungerlo al repository). Non è possibile cambiare la posizione degli ambienti virtuali in Python, pertanto, ciascuno sviluppatore che lavora all'applicazione ne creerà una locale.

Assicurarsi di utilizzare la stessa versione di Python è selezionata per l'applicazione web (in runtime.txt o **le impostazioni dell'applicazione** blade dell'applicazione web nel portale di Azure).

Per Python 2.7:

    python -m virtualenv env

Per Python 3.4:

    python -m venv env

Installare tutti i pacchetti esterni richiesti dall'applicazione. È possibile utilizzare il file requirements.txt nella radice del repository per installare i pacchetti nell'ambiente virtuale:

    env/bin/pip install -r requirements.txt

### Eseguire mediante il server di sviluppo

È possibile avviare l'applicazione in un server di sviluppo con il seguente comando:

    env/bin/python runserver.py

Sulla console verranno visualizzati l'URL e la porta su cui è in ascolto il server:

![](./media/web-sites-python-create-deploy-flask-app/mac-run-local-flask.png)

Quindi, aprire il Web browser su tale URL.

![](./media/web-sites-python-create-deploy-flask-app/mac-browser-flask.png)

### Apportare modifiche

È possibile sperimentare apportando modifiche alle origini applicazioni e/o ai modelli.

Dopo aver testato le modifiche, eseguirne il commit al repository Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Installare altri pacchetti

L'applicazione potrebbe avere dipendenze oltre Python e Flask.

È possibile installare altri pacchetti utilizzando pip. Ad esempio, per installare Azure SDK per Python,che fornisce l'accesso all'archivio Azure, al bus di servizio e ad altri servizi Azure, digitare:

    env/bin/pip install azure

Assicurarsi di aggiornare requirements.txt:

    env/bin/pip freeze > requirements.txt

Eseguire il commit delle modifiche:

    git add requirements.txt
    git commit -m "Added azure package"

### Distribuzione in Azure

Per attivare una distribuzione, eseguire il push delle modifiche in Azure:

    git push azure master

Verrà visualizzato l'output dello script di distribuzione, inclusa la creazione dell'ambiente virtuale, l'installazione di pacchetti, la creazione di web.config.

Passare all'URL di Azure per visualizzare le modifiche.


## Risoluzione dei problemi - Installazione dei pacchetti

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## Risoluzione dei problemi - Ambiente virtuale

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]


## Passaggi successivi

Visitare i seguenti collegamenti per altre informazioni su Flask e Python Tools per Visual Studio:
 
- [Documentazione di Flask]
- [Documentazione di Python Tools per Visual Studio]

Per informazioni sull'uso di Archiviazione tabelle di Azure e MongoDB:

- [Flask e MongoDB in Azure con Python Tools 2.1 per Visual Studio]
- [Flask e archiviazione tabelle di Azure con Python Tools 2.1 per Visual Studio]

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale precedente per il nuovo portale, vedere: [riferimento per lo spostamento tra il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)


<!--Link references-->
[Flask e MongoDB in Azure con Python Tools 2.1 per Visual Studio]: web-sites-python-ptvs-flask-mongodb.md
[Flask e archiviazione tabelle di Azure con Python Tools 2.1 per Visual Studio]: web-sites-python-ptvs-flask-table-storage.md

<!--External Link references-->
[Azure SDK per Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[Azure SDK per Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git per Windows]: http://msysgit.github.io/
[GitHub per Windows]: https://windows.github.com/
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Visual Studio]: http://www.visualstudio.com/
[documentazione PTVS]: http://pytools.codeplex.com/documentation
[Documentazione di Python Tools per Visual Studio]: http://pytools.codeplex.com/documentation
[Documentazione di Flask]: http://flask.pocoo.org/
 

<!---HONumber=July15_HO4-->