<properties 
	pageTitle="Siti Web di Python con Bottle - Esercitazione di Azure" 
	description="Un'esercitazione che illustra l'esecuzione di un sito Web di Python in Azure." 
	services="app-service\web" 
	documentationCenter="python" 
	tags="python"
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




# Creazione di siti Web con Bottle

Questa esercitazione illustra le operazioni iniziali per l'esecuzione di Python nel servizio Siti Web di Azure,  che offre hosting gratuito limitato e capacità di distribuzione rapida, oltre alla possibilità di usare Python.  Se la crescita dell'applicazione lo richiede, è possibile passare all'hosting a pagamento e avvalersi dell'integrazione con tutti gli altri servizi di Azure.

Verrà creata un'applicazione usando il framework Web di Bottle (vedere le versioni alternative di questa esercitazione per [Django](web-sites-python-create-deploy-django-app.md) e [Flask](web-sites-python-create-deploy-flask-app.md)).  Verrà creato il sito Web dalla raccolta di Azure, sarà configurata la distribuzione Git e si procederà alla clonazione locale del repository.  Quindi verrà eseguita l'applicazione localmente, si apporteranno le modifiche, e queste verranno successivamente sottoposte al commit e al push in Azure.  Nell'esercitazione viene illustrato come eseguire queste operazioni da Windows o Mac/Linux.

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]


## Prerequisiti

- Windows, Mac o Linux
- Python 2.7 o 3.4
- setuptools, pip, virtualenv (solo Python 2.7)
- Git
- [Python Tools 2.1 per Visual Studio][] (facoltativo)

**Nota**: la pubblicazione TFS non è attualmente supportata per progetti Python.

### Windows

Se non è già installato Python 2.7 o 3.4 (32 bit), si consiglia di installare [Azure SDK per Python 2.7][] o [Azure SDK per Python 3.4][] mediante Installazione guidata piattaforma Web.  In tal modo viene installata la versione a 32 bit di Python, setuptools, pip, virtualenv e così via (Python a 32 bit è la versione installata sui computer host di Azure).  In alternativa, è possibile ottenere Python da [python.org][].

Per Git, è consigliabile [Git per Windows][] o [GitHub per Windows][].  Se si utilizza Visual Studio, è possibile utilizzare il supporto Git integrato.

È inoltre consigliabile installare [Python Tools 2.1 per Visual Studio][].  Si tratta di un'operazione facoltativa, ma se si dispone di [Visual Studio][], inclusa la versione gratuita di Visual Studio Community 2013 o Visual Studio Express 2013 per il Web, si otterrà anche l'IDE Python.

### Mac/Linux

È necessario avere già installato Python e Git, ma assicurarsi di disporre di Python 2.7 o 3.4.


## Creazione di un sito Web sul portale

Il primo passaggio per la creazione di un'app consiste nella creazione del sito Web tramite il portale di gestione di Azure.  A tale scopo, sarà necessario accedere al portale e fare clic sul pulsante **NUOVO** nell'angolo in basso a sinistra. Verrà visualizzata una finestra. Fare clic su **CALCOLO**, **SITO WEB**, quindi su **DA RACCOLTA**.

![](./media/web-sites-python-create-deploy-bottle-app/portal-create-site.png)

Verrà visualizzata una finestra in cui sono elencate le app disponibili nella raccolta. Fare clic sulla categoria **FRAMEWORK APP** a sinistra e selezionare **Bottle**.

![](./media/web-sites-python-create-deploy-bottle-app/portal-gallery-bottle.png)

Nella pagina successiva, immettere un nome e un'area per il sito, quindi fare clic sul pulsante di completamento.

Il sito verrà configurato rapidamente. È possibile fare clic sul pulsante **SFOGLIA** dalla barra degli strumenti in basso per visualizzare la nuova applicazione Bottle in esecuzione in Azure.

![](./media/web-sites-python-create-deploy-bottle-app/portal-website-bottle.png)
 
Verrà quindi aggiunto il supporto per la pubblicazione tramite Git.  A tale scopo, è possibile scegliere **Imposta distribuzione dal controllo del codice sorgente**.

![](./media/web-sites-python-create-deploy-bottle-app/portal-site-created.png)

Dalla finestra di dialogo **Imposta distribuzione**, scorrere verso il basso e selezionare l'opzione **Repository Git locale**. Fare clic sulla freccia destra per continuare.

![](./media/web-sites-python-create-deploy-bottle-app/portal-setup-deployment.png)

Dopo la configurazione della pubblicazione Git, verrà visualizzata momentaneamente una pagina che indica che il repository è in fase di creazione. Terminata la creazione, verranno visualizzate le istruzioni su come effettuare la connessione.  

![](./media/web-sites-python-create-deploy-bottle-app/portal-repo-created.png)

Le istruzioni verranno fornite nelle sezioni successive.


## Informazioni generali sull'applicazione

### Contenuti del repository Git

Di seguito viene fornita una panoramica dei file contenuti nel repository Git iniziale, che saranno clonati nella sezione successiva.

    \routes.py
    \static\content\
    \static\fonts\
    \static\scripts\
    \views\about.tpl
    \views\contact.tpl
    \views\index.tpl
    \views\layout.tpl

Fonti principali per l'applicazione.  È composta da 3 pagine (indice, informazioni su, contatti) con un layout master.  Il contenuto statico e gli script includono bootstrap, jquery, modernizr e respond.

    \app.py

Supporto del server di sviluppo locale. Consente di eseguire l'applicazione localmente.

    \BottleWebProject.pyproj
    \BottleWebProject.sln

File di progetto da utilizzare con [Python Tools per Visual Studio][].

    \ptvs_virtualenv_proxy.py

Proxy IIS per ambienti virtuali e supporto del debug remoto PTVS.

    \requirements.txt

Pacchetti esterni necessari da parte di questa applicazione. Lo script di distribuzione eseguirà l'installazione di pip dei pacchetti elencati in questo file.
 
    \web.2.7.config
    \web.3.4.config

File di configurazione IIS.  Lo script di distribuzione utilizzerà il file appropriato web.x.y.config e lo copierà come web.config.

### File facoltativi - Personalizzazione della distribuzione

[AZURE.INCLUDE [web-sites-python-customizing-deployment](../includes/web-sites-python-customizing-deployment.md)]

### File facoltativi - Runtime Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../includes/web-sites-python-customizing-runtime.md)]

### Altri file sul server

Alcuni file sono presenti sul server ma non vengono aggiunti al repository Git.  Si tratta di file creati dallo script di distribuzione.

    \web.config

File di configurazione IIS.  Creato da web.x.y.config per ogni distribuzione.

    \env\

Ambiente virtuale Python.  Creato durante la distribuzione se sul sito non esiste già un ambiente virtuale compatibile.  I pacchetti elencati in requirements.txt vengono installati con pip. Tuttavia, se i pacchetti sono installati, l'installazione di pip non verrà eseguita.

Nelle tre sezioni successive viene descritto come procedere con lo sviluppo dei siti Web in tre ambienti diversi:

- Windows, con Python Tools per Visual Studio
- Windows, con la riga di comando
- Mac/Linux, con la riga di comando


## Sviluppo di siti Web - Windows - Python Tools per Visual Studio

### Clonare il repository

Innanzitutto, clonare il repository utilizzando l'URL fornito sul portale di Azure.

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-git-clone.png)

Aprire il file della soluzione (.sln) incluso nella radice del repository.

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-solution-bottle.png)

### Creare l'ambiente virtuale

A questo punto verrà creato un ambiente virtuale per lo sviluppo locale.  Fare clic con il pulsante destro del mouse su **Ambienti Python** selezionare **Aggiungi ambiente virtuale...**.

- Assicurarsi che il nome dell'ambiente sia `env`.

- Selezionare l'interprete di base.  Assicurarsi di utilizzare la stessa versione di Python selezionata per il sito (in runtime.txt o nella pagina di configurazione del sito).

- Assicurarsi che l'opzione per scaricare e installare i pacchetti sia selezionata.

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-add-virtual-env-27.png)

Fare clic su **Crea**.  In tal modo verrà creato l'ambiente virtuale e verranno installate le dipendenze elencate in requirements.txt.

### Eseguire mediante il server di sviluppo

Premere F5 per avviare il debug. Il Web browser si aprirà automaticamente sulla pagina in esecuzione locale.

![](./media/web-sites-python-create-deploy-bottle-app/windows-browser-bottle.png)

È possibile impostare punti di interruzione nelle sorgenti, utilizzare finestre Espressioni di controllo e via di seguito.  Per ulteriori informazioni sulle diverse funzionalità, vedere la [documentazione PTVS][].

### Apportare modifiche

È possibile sperimentare apportando modifiche alle origini applicazioni e/o ai modelli.

Dopo aver testato le modifiche, eseguirne il commit al repository Git:

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-commit-bottle.png)

### Installare altri pacchetti

L'applicazione può avere altre dipendenze oltre Python e Bottle.

È possibile installare altri pacchetti utilizzando pip.  Per installare un pacchetto, fare clic con il pulsante destro del mouse e selezionare **Installa pacchetto Python**.

Ad esempio, per installare Azure SDK per Python, che fornisce l'accesso all'archivio Azure, al bus di servizio e ad altri servizi Azure, immettere `azure`:

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-install-package-dialog.png)

Fare clic con il pulsante destro del mouse sull'ambiente virtuale e selezionare **Genera requirements.txt** per aggiornare requirements.txt.

Quindi, eseguire il commit delle modifiche a requirements.txt al repository Git.

### Distribuire in Azure

Per attivare una distribuzione, fare clic su **Sincronizza** o su **Push**.  La sincronizzazione esegue sia il push che il pull.

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-git-push.png)

La prima distribuzione richiederà un po' di tempo, in quanto verrà creato un ambiente virtuale, si installeranno i pacchetti e così via.

In Visual Studio non viene visualizzato l'avanzamento della distribuzione.  Se si desidera rivedere l'output, vedere la sezione in [Risoluzione dei problemi- Distribuzione](#troubleshooting-deployment).

Passare all'URL di Azure per visualizzare le modifiche.


## Sviluppo di siti Web - Windows - Riga di comando

### Clonare il repository

Innanzitutto, clonare il repository utilizzando l'URL fornito sul portale di Azure e aggiungere il repository di Azure come remoto.

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Creare l'ambiente virtuale

Verrà creato un nuovo ambiente virtuale per lo sviluppo (non aggiungerlo al repository).  Non è possibile cambiare la posizione degli ambienti virtuali in Python, pertanto, ciascuno sviluppatore che lavora all'applicazione ne creerà una locale.

Assicurarsi di utilizzare la stessa versione di Python selezionata per il sito (in runtime.txt o nella pagina di configurazione del sito)

Per Python 2.7:

    c:\python27\python.exe -m virtualenv env

Per Python 3.4:

    c:\python34\python.exe -m venv env

Installare tutti i pacchetti esterni richiesti dall'applicazione. È possibile utilizzare il file requirements.txt nella radice del repository per installare i pacchetti nell'ambiente virtuale:

    env\scripts\pip install -r requirements.txt

### Eseguire mediante il server di sviluppo

È possibile avviare l'applicazione in un server di sviluppo con il seguente comando:

    env\scripts\python app.py

Sulla console verranno visualizzati l'URL e la porta su cui è in ascolto il server:

![](./media/web-sites-python-create-deploy-bottle-app/windows-run-local-bottle.png)

Quindi, aprire il Web browser su tale URL.

![](./media/web-sites-python-create-deploy-bottle-app/windows-browser-bottle.png)

### Apportare modifiche

È possibile sperimentare apportando modifiche alle origini applicazioni e/o ai modelli.

Dopo aver testato le modifiche, eseguirne il commit al repository Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Installare altri pacchetti

L'applicazione può avere altre dipendenze oltre Python e Bottle.

È possibile installare altri pacchetti utilizzando pip.  Ad esempio, per installare Azure SDK per Python,che fornisce l'accesso all'archivio Azure, al bus di servizio e ad altri servizi Azure, digitare:

    env\scripts\pip install azure

Assicurarsi di aggiornare requirements.txt:

    env\scripts\pip freeze > requirements.txt

Eseguire il commit delle modifiche:

    git add requirements.txt
    git commit -m "Added azure package"

### Distribuire in Azure

Per attivare una distribuzione, eseguire il push delle modifiche in Azure:

    git push azure master

Verrà visualizzato l'output dello script di distribuzione, inclusa la creazione dell'ambiente virtuale, l'installazione di pacchetti, la creazione di web.config.

Passare all'URL di Azure per visualizzare le modifiche.


## Sviluppo del sito Web - Mac/Linux - Riga di comando

### Clonare il repository

Innanzitutto, clonare il repository utilizzando l'URL fornito sul portale di Azure e aggiungere il repository di Azure come remoto.

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Creare l'ambiente virtuale

Verrà creato un nuovo ambiente virtuale per lo sviluppo (non aggiungerlo al repository).  Non è possibile cambiare la posizione degli ambienti virtuali in Python, pertanto, ciascuno sviluppatore che lavora all'applicazione ne creerà una locale.

Assicurarsi di utilizzare la stessa versione di Python selezionata per il sito (in runtime.txt o nella pagina di configurazione del sito).

Per Python 2.7:

    python -m virtualenv env

Per Python 3.4:

    python -m venv env

Installare tutti i pacchetti esterni richiesti dall'applicazione. È possibile utilizzare il file requirements.txt nella radice del repository per installare i pacchetti nell'ambiente virtuale:

    env/bin/pip install -r requirements.txt

### Eseguire mediante il server di sviluppo

È possibile avviare l'applicazione in un server di sviluppo con il seguente comando:

    env/bin/python app.py

Sulla console verranno visualizzati l'URL e la porta su cui è in ascolto il server:

![](./media/web-sites-python-create-deploy-bottle-app/mac-run-local-bottle.png)

Quindi, aprire il Web browser su tale URL.

![](./media/web-sites-python-create-deploy-bottle-app/mac-browser-bottle.png)

### Apportare modifiche

È possibile sperimentare apportando modifiche alle origini applicazioni e/o ai modelli.

Dopo aver testato le modifiche, eseguirne il commit al repository Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Installare altri pacchetti

L'applicazione può avere altre dipendenze oltre Python e Bottle.

È possibile installare altri pacchetti utilizzando pip.  Ad esempio, per installare Azure SDK per Python,che fornisce l'accesso all'archivio Azure, al bus di servizio e ad altri servizi Azure, digitare:

    env/bin/pip install azure

Assicurarsi di aggiornare requirements.txt:

    env/bin/pip freeze > requirements.txt

Eseguire il commit delle modifiche:

    git add requirements.txt
    git commit -m "Added azure package"

### Distribuire in Azure

Per attivare una distribuzione, eseguire il push delle modifiche in Azure:

    git push azure master

Verrà visualizzato l'output dello script di distribuzione, inclusa la creazione dell'ambiente virtuale, l'installazione di pacchetti, la creazione di web.config.

Passare all'URL di Azure per visualizzare le modifiche.


## Troubleshooting - Deployment

[AZURE.INCLUDE [web-sites-python-troubleshooting-deployment](../includes/web-sites-python-troubleshooting-deployment.md)]


## Risoluzione dei problemi - Installazione dei pacchetti

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../includes/web-sites-python-troubleshooting-package-installation.md)]


## Risoluzione dei problemi - Ambiente virtuale

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../includes/web-sites-python-troubleshooting-virtual-environment.md)]


## Passaggi successivi

Visitare i seguenti collegamenti per altre informazioni su Bottle e Python Tools per Visual Studio: 
 
- [Documentazione di Bottle][]
- [Documentazione di Python Tools per Visual Studio][] 

Per informazioni sull'uso di Archiviazione tabelle di Azure e MongoDB:

- [Bottle e MongoDB in Azure con Python Tools 2.1 per Visual Studio][]
- [Bottle e Archiviazione tabelle di Azure con Python Tools 2.1 per Visual Studio][]


<!--Link references-->
[Bottle e MongoDB in Azure con Python Tools 2.1 per Visual Studio]: web-sites-python-ptvs-bottle-table-storage.md
[Bottle e Archiviazione tabelle di Azure con Python Tools 2.1 per Visual Studio]: web-sites-python-ptvs-bottle-mongodb.md

<!--External Link references-->
[Azure SDK per Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[Azure SDK per Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git per Windows]: http://msysgit.github.io/
[GitHub per Windows]: https://windows.github.com/
[Python Tools per Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 per Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Visual Studio]: http://www.visualstudio.com/
[documentazione PTVS]: http://pytools.codeplex.com/documentation
[Documentazione di Python Tools per Visual Studio]: http://pytools.codeplex.com/documentation 
[Documentazione di Bottle]: http://bottlepy.org/docs/dev/index.html

<!--HONumber=52-->