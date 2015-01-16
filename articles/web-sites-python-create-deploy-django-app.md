<properties urlDisplayName="Websites with Django" pageTitle="Siti Web Python con Django - Esercitazione su Azure" metaKeywords="Azure django, django website" description="Un'esercitazione che illustra l'esecuzione di un sito Web di Python in Azure." metaCanonical="" services="web-sites" documentationCenter="Python" title="Creating Websites with Django" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="08/01/2014" ms.author="huvalo" />




# Creazione di siti Web con Django

Questa esercitazione illustra le operazioni iniziali per l'esecuzione di Python nel servizio Siti Web di Azure,  che offre hosting gratuito limitato e capacità di distribuzione rapida, oltre alla possibilità di usare Python.  Se la crescita dell'applicazione lo richiede, è possibile passare all'hosting a pagamento e avvalersi dell'integrazione con tutti gli altri servizi di Azure.  

In questa esercitazione viene illustrato come distribuire un'applicazione creata usando il framework Web Django.  Vengono illustrate le procedure dettagliate per la distribuzione dell'applicazione e di eventuali librerie necessarie, incluso Django.  Tali elementi verranno inseriti in un archivio Git, in modo da velocizzare e semplificare il push di aggiornamenti nel sito Web.  Il sito Web appena creato tramite Azure verrà infine configurato per l'esecuzione dell'applicazione Python.  

> [WACOM.NOTE]
> Per completare l'esercitazione, è necessario un account Azure. È possibile: <a href="http://azure.microsoft.com/it-it/pricing/member-offers/msdn-benefits-details/">attivare i benefici della sottoscrizione MSDN</a> oppure <a href="http://azure.microsoft.com/it-it/pricing/free-trial/">iscriversi per una versione di valutazione gratuita</a>.
> 
> Per iniziare a usare Siti Web di Azure prima di iscriversi per ottenere un account, visitare la pagina all'indirizzo <a href="https://trywebsites.azurewebsites.net/?language=python">https://trywebsites.azurewebsites.net</a>in cui è possibile creare immediatamente e gratuitamente un sito di base ASP.NET temporaneo in Siti Web di Azure. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

In questa esercitazione vengono usati Python 2.7 e Django 1.4.  È possibile scaricarli autonomamente oppure installarli in modo semplice e rapido tramite il collegamento di Windows Installer, disponibile all'indirizzo [http://www.windowsazure.com/it-it/develop/python/](http://www.windowsazure.com/it-it/develop/python/).  

**Nota**: Python (2.7.3 o 3.4.0, a seconda della selezione effettuata) e il gestore wfastcgi sono attualmente preinstallati in Siti Web di Azure,  ma framework Web quali Django non sono inclusi.  Se lo si preferisce, è possibile usare comunque un altro interprete per Python.  È necessario includerlo semplicemente nell'archivio Git e configurare il sito Web in modo che usi tale interprete in sostituzione dell'interprete per Python 2.7 già installato.

> [WACOM.NOTE] Per selezionare la versione di Python da usare nel portale di Siti Web di Azure, aprire la scheda Configura del sito Web e modificare l'impostazione **Versione Python**.

Sarà inoltre necessario installare un'opzione di distribuzione per eseguire il push del sito in Azure. Sono disponibili svariati strumenti di distribuzione, ma in questa esercitazione verrà usato Git. È consigliabile usare [msysgit](http://code.google.com/p/msysgit/). 

**Nota**: la pubblicazione TFS non è attualmente supportata per progetti Python.

Al termine dell'installazione di Python, Django e Git, sarà possibile eseguire le operazioni iniziali.

## Creazione di un sito Web sul portale

Il primo passaggio per la creazione di un'app consiste nella creazione del sito Web tramite il portale di gestione di Azure.  A tale scopo, sarà necessario accedere al portale e fare clic sul pulsante NUOVO nell'angolo inferiore sinistro. Verrà visualizzata una finestra. Fare clic su **Creazione rapida**, immettere un URL, quindi selezionare **Crea sito Web**.

![](./media/web-sites-python-create-deploy-django-app/django-ws-003.png)

Il sito verrà configurato rapidamente.  Verrà quindi aggiunto il supporto per la pubblicazione tramite Git.  A tale scopo, è possibile scegliere **Imposta distribuzione dal controllo del codice sorgente**.

![](./media/web-sites-python-create-deploy-django-app/django-ws-004.png)

Nella finestra di dialogo **Imposta distribuzione** scorrere verso il basso e selezionare l'opzione **Archivio Git locale**. Fare clic sulla freccia destra per continuare.

![](./media/web-sites-python-create-deploy-django-app/django-ws-005.png)

Dopo la configurazione della pubblicazione Git, verrà visualizzata momentaneamente una pagina che indica che l'archivio è in fase di creazione. Al termine della creazione dell'archivio, verrà visualizzata la scheda relativa alle distribuzioni, che include istruzioni relative alle procedure per la connessione.  

![](./media/web-sites-python-create-deploy-django-app/django-ws-006.png)


## Sviluppo del sito Web

Dopo la creazione dell'archivio Git in Azure, sarà possibile iniziare a inserirvi le informazioni incluse nel sito Web disponibile nella macchina locale.  Il primo passaggio consiste nel clonare il sito vuoto esistente usando l'URL disponibile:

![](./media/web-sites-python-create-deploy-django-app/django-ws-007.png)

Sarà quindi possibile iniziare a configurare l'integrazione con il sito Web.  Sarà necessario eseguire le operazioni seguenti:

1.  Includere la libreria Django e altre librerie da usare per l'esecuzione del sito Web.
2.  Includere il codice dell'applicazione Django.

Includere innanzitutto la libreria Django.  A tale scopo, creare una nuova directory con nome site-packages, quindi copiare la versione installata di Django in tale directory usando i comandi seguenti:

	mkdir site-packages
	cd site-packages
	xcopy /s C:\Python27\lib\site-packages\* .

Tali comandi consentono di copiare tutte le librerie disponibili in site-packages, inclusa quella di Django.  Se sono presenti librerie non usate dal sito Web, è possibile rimuoverle.

![](./media/web-sites-python-create-deploy-django-app/django-ws-008.png)
 
Creare quindi l'applicazione Django iniziale,  eseguendo la stessa procedura usata per la creazione di qualsiasi altra applicazione Django dalla riga di comando oppure usando [Python Tools per Visual Studio](http://pytools.codeplex.com/) per creare il progetto.  Entrambe le opzioni sono mostrate di seguito.

**Opzione 1:** 
Per creare il nuovo progetto dalla riga di comando, eseguire il comando seguente. Il comando consente di creare l'applicazione Django nella cartella DjangoApplication:

	 C:\Python27\python.exe -m django.bin.django-admin startproject DjangoApplication

![](./media/web-sites-python-create-deploy-django-app/django-ws-010.png)

**Opzione 2:**  
È inoltre possibile creare il nuovo sito usando Python Tools per Visual Studio.  Avviare Visual Studio dopo avere installato Python Tools per Visual Studio, quindi selezionare **File**->**Nuovo progetto**.  In **Altri linguaggi**passare ai progetti Python e selezionare l'applicazione ****Django.  Immettere **DjangoApplication** come nome per il progetto, quindi verificare che l'opzione **Crea directory per soluzione** sia deselezionata, in modo da ottenere esattamente la stessa struttura di directory ottenuta creando l'applicazione Django dalla riga di comando. Questa opzione consente di configurare una soluzione e un file di progetto di Visual Studio che offrono debug di modelli e intellisense.

![](./media/web-sites-python-create-deploy-django-app/django-ws-011.png)

È ora necessario aggiungere semplicemente tutti i file appena aggiunti ed eseguire il push del sito in Git.  A tale scopo, eseguire i comandi seguenti:

	git add DjangoApplication site-packages
	git commit -m "Initial site"
	git remote add azure https://dinov@pythonwebsite.scm.azurewebsites.net/PythonWebSite.git
	git push azure master

Il primo comando consente di aggiungere i file non tracciati da tracciare.  Il secondo comando esegue il commit dei file appena aggiunti all'archivio.  Il terzo comando consente di aggiungere un repository remoto con nome *azure* per il repository.  Il comando finale accetta le modifiche e ne esegue il push nell'archivio remoto. L'ultimo comando consente inoltre di avviare la distribuzione.  Il risultato finale dovrebbe essere analogo al seguente:

![](./media/web-sites-python-create-deploy-django-app/django-ws-013.png)

Dopo l'esecuzione del push, il portale di Azure verrà aggiornato e verrà visualizzata la distribuzione attiva:

![](./media/web-sites-python-create-deploy-django-app/django-ws-014.png)

## Configurazione del sito Web

È ora necessario configurare il sito Web, in modo che sia informato in merito al progetto Django e usi il gestore wfastcgi.  A tale scopo, è possibile fare clic sulla scheda Configura nella parte superiore dello schermo, quindi scorrere verso il basso fino a visualizzare la seconda metà della pagina, che include le sezioni **Impostazioni app**e **Mapping handler**.  

Tutte le impostazioni definite in questa schermata si trasformeranno in variabili di ambiente durante la richiesta effettiva.  È quindi possibile usare tali impostazioni per configurare la variabile di ambiente DJANGO\_SETTINGS\_MODULE, oltre a PYTHONPATH e WSGI\_HANDLER.  Se l'applicazione include altri valori di configurazione, è possibile assegnarli in questa schermata e selezionarli dall'ambiente.  In alcuni casi può essere opportuno impostare un valore corrispondente a un percorso di un file del sito Web. Ad esempio, è possibile eseguire tale operazione per PYTHONPATH.  Se viene eseguito come sito Web di Azure, il sito Web si troverà in **D:\home\site\wwwroot\**. Sarà quindi possibile usare tale valore in qualsiasi posizione quando è necessario un percorso completo per un file su disco.

Per configurare un'applicazione Django è necessario creare tre variabili di ambiente.  La prima variabile è DJANGO\_SETTINGS\_MODULE, che specifica il nome del modulo dell'applicazione Django da usare per la configurazione.  La seconda variabile di ambiente è PYTHONPATH, che specifica il pacchetto in cui si trova il modulo di impostazioni. La terza variabile è WSGI\_HANDLER,  ovvero un nome modulo/pacchetto, seguito dall'attributo del modulo da usare, ad esempio, mypackage.mymodule.handler.  Aggiungere parentesi per indicare che l'attributo deve essere chiamato.  È pertanto necessario configurare tali variabili come segue:
                
	DJANGO_SETTINGS_MODULE    DjangoApplication.settings
	PYTHONPATH                D:\home\site\wwwroot\DjangoApplication;D:\home\site\wwwroot\site-packages
	WSGI_HANDLER              django.core.handlers.wsgi.WSGIHandler()

![](./media/web-sites-python-create-deploy-django-app/django-ws-015.png)

È ora necessario configurare il mapping del gestore.  A tale scopo, registrare il gestore per tutte le estensioni, usando il percorso dell'interprete per Python e il percorso dello script wfastcgi.py:

	EXTENSION                 *
	SCRIPT PROCESSOR PATH     D:\python27\python.exe
	ADDITIONAL ARGUMENTS      D:\python27\scripts\wfastcgi.py

![](./media/web-sites-python-create-deploy-django-app/django-ws-016.png)

A questo punto, è possibile fare clic sul pulsante **Salva** in basso.

Tornare infine al Dashboard. Passare a **URL SITO** sul lato sinistro. Fare clic sul collegamento e aprire il nuovo sito di Django, che avrà un aspetto analogo al seguente:

![](./media/web-sites-python-create-deploy-django-app/django-ws-017.png)

## Passaggi successivi

È possibile continuare lo sviluppo dell'applicazione Django usando gli strumenti già in uso. Se si usa [Python Tools per Visual Studio](http://pytools.codeplex.com/) per lo sviluppo, potrebbe essere utile installare [VisualGit](http://code.google.com/p/visualgit/) per ottenere l'integrazione del controllo del codice sorgente dall'interno di Visual Studio.  

È possibile che l'applicazione includa altre dipendenze oltre a quelle relative a Python e Django.  Se Python è stato installato mediante il programma di installazione disponibile all'indirizzo [http://www.windowsazure.com/it-it/develop/python/](http://www.windowsazure.com/it-it/develop/python/), PIP sarà già installato e sarà possibile usarlo per aggiungere rapidamente nuove dipendenze.  Ad esempio, per installare il toolkit per linguaggi naturali e tutte le relative dipendenze, digitare:

	pip install nltk

Sarà necessario aggiornare la directory site-packages copiando i file da C:\Python27\Lib\site-packages alla directory site-packages locale.

Dopo la copia dei file, eseguire il comando **git status** per visualizzare i file appena aggiunti, quindi **git add** seguito da **git commit** per eseguire il commit delle modifiche nel repository.  È infine possibile eseguire un comando **git push**, che consente di distribuire il sito Web aggiornato in Azure.

È ora possibile passare alla directory DjangoApplication e usare manage.py nel modo consueto per iniziare ad aggiungere nuove applicazioni al progetto di Django.  

<!--HONumber=35.1-->
