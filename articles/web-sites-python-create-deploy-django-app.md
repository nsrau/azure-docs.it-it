<properties linkid="develop-python-web-site-with-django" urlDisplayName="Websites with Django" pageTitle="Python Websites with Django - Azure tutorial" metaKeywords="Azure django, django website" description="A tutorial that introduces you to running a Python website on Azure." metaCanonical="" services="web-sites" documentationCenter="Python" title="Creating Websites with Django" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Creazione di siti Web con Django

Questa esercitazione illustra le operazioni iniziali per l'esecuzione di Python nel servizio Siti Web di Azure, che offre hosting gratuito limitato e capacità di distribuzione rapida, oltre alla possibilità di usare Python. Se la crescita dell'applicazione lo richiede, è possibile passare all'hosting a pagamento e avvalersi dell'integrazione con tutti gli altri servizi di Azure.

In questa esercitazione viene illustrato come distribuire un'applicazione creata utilizzando il framework Web Django. Vengono illustrate le procedure dettagliate per la distribuzione dell'applicazione e di eventuali librerie necessarie, incluso Django. Tali elementi verranno inseriti in un archivio Git, in modo da velocizzare e semplificare il push di aggiornamenti nel sito Web. Il sito Web appena creato tramite Azure verrà infine configurato per l'esecuzione dell'applicazione Python.

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

In questa esercitazione vengono utilizzati Python 2.7 e Django 1.4. È possibile scaricarli autonomamente oppure installarli in modo semplice e rapido tramite il collegamento di Windows Installer, disponibile all'indirizzo [http://www.windowsazure.com/it-it/develop/python/](http://www.windowsazure.com/it-it/develop/python/).

**Nota**: Python (2.7.3 o 3.4.0, a seconda della selezione effettuata) e il gestore wfastcgi sono attualmente preinstallati in Siti Web di Azure, ma framework Web quali Django non sono inclusi. Se lo si preferisce, è possibile utilizzare comunque un altro interprete per Python. È necessario includerlo semplicemente nell'archivio Git e configurare il sito Web in modo che usi tale interprete in sostituzione dell'interprete per Python 2.7 già installato.

> [WACOM.NOTE] È ora possibile selezionare la versione di Python che si desidera usare nel portale di Siti Web di Azure aprendo la scheda Configura del sito Web e modificando l'impostazione di **Versione Python**.

Sarà inoltre necessario installare un'opzione di distribuzione per eseguire il push del sito in Azure. Sono disponibili svariati strumenti di distribuzione, ma in questa esercitazione verrà utilizzato Git. È consigliabile utilizzare [msysgit][msysgit].

**Nota**: la pubblicazione TFS non è attualmente supportata per progetti Python.

Al termine dell'installazione di Python, Django e Git, sarà possibile eseguire le operazioni iniziali.

## Creazione di un sito Web sul portale

Il primo passaggio per la creazione di un'app consiste nella creazione del sito Web tramite il portale di gestione di Azure. A tale scopo, sarà necessario accedere al portale e fare clic sul pulsante NEW nell'angolo inferiore sinistro. Verrà visualizzata una finestra. Fare clic su **Creazione rapida** immettere un URL, quindi selezionare **Crea sito Web**.

![][0]

Il sito verrà configurato rapidamente. Verrà quindi aggiunto il supporto per la pubblicazione tramite Git. A tale scopo, è possibile scegliere **Set up deployment from source control**.

![][1]

Nella finestra di dialogo **Set up deployment** scorrere verso il basso e selezionare l'opzione **Local Git**. Fare clic sulla freccia destra per continuare.

![][2]

Dopo la configurazione della pubblicazione Git, verrà visualizzata momentaneamente una pagina che indica che l'archivio è in fase di creazione. Al termine della creazione dell'archivio, verrà visualizzata la scheda relativa alle distribuzioni, che include istruzioni relative alle procedure per la connessione.

![][3]

## Sviluppo del sito Web

Dopo la creazione dell'archivio Git in Azure, sarà possibile iniziare a inserirvi le informazioni incluse nel sito Web disponibile nella macchina locale. Il primo passaggio consiste nel clonare il sito vuoto esistente utilizzando l'URL disponibile:

![][4]

Sarà quindi possibile iniziare a configurare l'integrazione con il sito Web. Sarà necessario eseguire le operazioni seguenti:

1.  Includere la libreria Django e altre librerie da usare per l'esecuzione del sito Web.
2.  Includere il codice dell'applicazione Django.

Includere innanzitutto la libreria Django. A tale scopo, creare una nuova directory con nome site-packages, quindi copiare la versione installata di Django in tale directory utilizzando i comandi seguenti:

    mkdir site-packages
    cd site-packages
    xcopy /s C:\Python27\lib\site-packages\* .

Tali comandi consentono di copiare tutte le librerie disponibili in site-packages, inclusa quella di Django. Se sono presenti librerie non utilizzate dal sito Web, è possibile rimuoverle.

![][5]

Creare quindi l'applicazione Django iniziale, eseguendo la stessa procedura utilizzata per la creazione di altre applicazioni Django dalla riga di comando oppure utilizzando [Python Tools for Visual Studio][Python Tools for Visual Studio] per creare il progetto. Entrambe le opzioni sono mostrate di seguito.

**Opzione 1:**
Per creare il nuovo progetto dalla riga di comando, eseguire il comando seguente. Il comando consente di creare l'applicazione Django nella cartella DjangoApplication:

     C:\Python27\python.exe -m django.bin.django-admin startproject DjangoApplication

![][6]

**Opzione 2:**
È inoltre possibile creare il nuovo sito usando Python Tools for Visual Studio. Avviare Visual Studio dopo avere installato Python Tools for Visual Studio, quindi selezionare **File**-\>**Nuovo progetto**. In **Altri linguaggi** passare ai progetti Python e selezionare **Django Application**. Immettere **DjangoApplication** come nome per il progetto, quindi verificare che l'opzione **Crea directory per soluzione** sia deselezionata, in modo da ottenere esattamente la stessa struttura di directory ottenuta creando l'applicazione Django dalla riga di comando. Questa opzione consente di configurare una soluzione e un file di progetto di Visual Studio che offrono debug di modelli e intellisense.

![][7]

È ora necessario aggiungere semplicemente tutti i file appena aggiunti ed eseguire il push del sito in Git. A tale scopo, eseguire i comandi seguenti:

    git add DjangoApplication site-packages
    git commit -m "Initial site"
    git remote add azure https://dinov@pythonwebsite.scm.azurewebsites.net/PythonWebSite.git
    git push azure master

Il primo comando consente di aggiungere i file non tracciati da tracciare. Il secondo comando esegue il commit dei file appena aggiunti all'archivio. Il terzo comando consente di aggiungere un archivio remoto con nome *azure* per l'archivio. Il comando finale accetta le modifiche e ne esegue il push nell'archivio remoto. L'ultimo comando consente inoltre di avviare la distribuzione. Il risultato finale dovrebbe essere analogo al seguente:

![][8]

Dopo l'esecuzione del push, il portale di Azure verrà aggiornato e verrà visualizzata la distribuzione attiva:

![][9]

## Configurazione del sito Web

È ora necessario configurare il sito Web, in modo che sia informato in merito al progetto Django e usi il gestore wfastcgi. A tale scopo, è possibile fare clic sulla scheda Configure nella parte superiore dello schermo, quindi scorrere verso il basso fino a visualizzare la parte inferiore della pagina, che include le sezioni **app settings** e **handler mappings**.

Tutte le impostazioni definite in questa schermata si trasformeranno in variabili di ambiente durante la richiesta effettiva. È quindi possibile utilizzare tali impostazioni per configurare la variabile di ambiente DJANGO\_SETTINGS\_MODULE, oltre a PYTHONPATH e WSGI\_HANDLER. Se l'applicazione include altri valori di configurazione, è possibile assegnarli in questa schermata e selezionarli dall'ambiente. In alcuni casi può essere opportuno impostare un valore corrispondente a un percorso di un file del sito Web. Ad esempio, è possibile che si desideri eseguire tale operazione per PYTHONPATH. Se viene eseguito come sito Web di Azure, il sito Web si troverà in \*D:\\home\\site\\wwwroot\*. Sarà quindi possibile usare tale valore in qualsiasi posizione che richieda un percorso completo per un file su disco.

Per configurare un'applicazione Django è necessario creare tre variabili di ambiente. La prima variabile è DJANGO\_SETTINGS\_MODULE, che specifica il nome del modulo dell'applicazione Django da utilizzare per la configurazione. La seconda variabile è PYTHONPATH, che specifica il pacchetto in cui si trova il modulo di impostazioni. La terza variabile è WSGI\_HANDLER, ovvero un nome modulo/pacchetto, seguito dall'attributo del modulo da utilizzare, ad esempio, mypackage.mymodule.handler. Aggiungere parentesi per indicare che l'attributo deve essere chiamato. È pertanto necessario configurare tali variabili come segue:

    DJANGO_SETTINGS_MODULE    DjangoApplication.settings
    PYTHONPATH                D:\home\site\wwwroot\DjangoApplication;D:\home\site\wwwroot\site-packages
    WSGI_HANDLER              django.core.handlers.wsgi.WSGIHandler()

![][10]

È ora necessario configurare il mapping del gestore. A tale scopo, registrare il gestore per tutte le estensioni, utilizzando il percorso dell'interprete per Python e il percorso dello script wfastcgi.py:

    EXTENSION                 *
    SCRIPT PROCESSOR PATH     D:\python27\python.exe
    ADDITIONAL ARGUMENTS      D:\python27\scripts\wfastcgi.py

![][11]

A questo punto è possibile fare clic sul pulsante **Save** in basso.

Tornare infine al Dashboard. Passare a **SITE URL** a sinistra. Fare clic sul collegamento e aprire il nuovo sito di Django, che avrà un aspetto analogo al seguente:

![][12]

## Passaggi successivi

È possibile continuare lo sviluppo dell'applicazione Django utilizzando gli strumenti già in uso. Se si utilizza [Python Tools for Visual Studio][Python Tools for Visual Studio] per lo sviluppo, è probabile che si desideri installare [VisualGit][VisualGit] per ottenere l'integrazione del controllo del codice sorgente dall'interno di Visual Studio.

È possibile che l'applicazione includa altre dipendenze oltre a quelle relative a Python e Django. Se Python è stato installato mediante il programma di installazione disponibile all'indirizzo [http://www.windowsazure.com/it-it/develop/python/](http://www.windowsazure.com/it-it/develop/python/), PIP sarà già stato installato e sarà possibile utilizzarlo per aggiungere rapidamente nuove dipendenze. Ad esempio, per installare il toolkit per linguaggi naturali e tutte le relative dipendenze, digitare:

    pip install nltk

Sarà necessario aggiornare la directory site-packages copiando i file da C:\\Python27\\Lib\\site-packages alla directory site-packages locale.

Dopo la copia dei file, eseguire il comando **git status** per visualizzare i file appena aggiunti, quindi **git add** seguito da **git commit** per eseguire il commit delle modifiche nell'archivio. È infine possibile eseguire un comando **git push**, che consente di distribuire il sito Web aggiornato in Azure.

È ora possibile passare alla directory DjangoApplication e utilizzare manage.py nel modo consueto per iniziare ad aggiungere nuove applicazioni al progetto di Django.

  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [0]: http://www.windowsazure.com/it-it/develop/python/
  [msysgit]: http://code.google.com/p/msysgit/
  [0]: ./media/web-sites-python-create-deploy-django-app/django-ws-003.png
  [1]: ./media/web-sites-python-create-deploy-django-app/django-ws-004.png
  [2]: ./media/web-sites-python-create-deploy-django-app/django-ws-005.png
  [3]: ./media/web-sites-python-create-deploy-django-app/django-ws-006.png
  [4]: ./media/web-sites-python-create-deploy-django-app/django-ws-007.png
  [5]: ./media/web-sites-python-create-deploy-django-app/django-ws-008.png
  [Python Tools for Visual Studio]: http://pytools.codeplex.com/
  [6]: ./media/web-sites-python-create-deploy-django-app/django-ws-010.png
  [7]: ./media/web-sites-python-create-deploy-django-app/django-ws-011.png
  [8]: ./media/web-sites-python-create-deploy-django-app/django-ws-013.png
  [9]: ./media/web-sites-python-create-deploy-django-app/django-ws-014.png
  [10]: ./media/web-sites-python-create-deploy-django-app/django-ws-015.png
  [11]: ./media/web-sites-python-create-deploy-django-app/django-ws-016.png
  [12]: ./media/web-sites-python-create-deploy-django-app/django-ws-017.png
  [VisualGit]: http://code.google.com/p/visualgit/
