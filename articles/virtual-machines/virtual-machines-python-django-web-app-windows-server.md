<properties 
	pageTitle="App Web Python con Django - Esercitazione di Azure" 
	description="Questa esercitazione illustra come ospitare un sito Web basato su Django in Azure usando una macchina virtuale Windows Server 2012 R2 Datacenter." 
	services="virtual-machines" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>


<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="huvalo"/>




# Applicazione Web Hello World Django

<div class="dev-center-tutorial-selector sublanding"><a href="/develop/python/tutorials/web-app-with-django/" title="Windows" class="current">Windows</a><a href="/develop/python/tutorials/django-hello-world-(maclinux)/" title="MacLinux">Mac/Linux</a></div>

Questa esercitazione illustra come ospitare un sito Web basato su Django in Microsoft Azure usando una macchina virtuale Windows Server. In questa esercitazione si presuppone che l'utente non abbia mai usato Azure. Al termine dell'esercitazione, si disporrà di un'applicazione basata su Django in esecuzione nel cloud.

Si apprenderà come:

* Configurare una macchina virtuale di Azure per l'hosting di Django. Sebbene nell'esercitazione la procedura venga illustrata in **Windows Server**, è possibile eseguirla anche con una macchina virtuale Linux ospitata in Azure. 
* Creare una nuova applicazione Django da Windows.

Seguendo questa esercitazione, verrà creata una semplice applicazione Web Hello World, ospitata in una macchina virtuale di Azure.

Di seguito è riportata una schermata dell'applicazione completata:

![Finestra del browser con la pagina Hello World visualizzata in Azure][1]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Creazione e configurazione di una macchina virtuale di Azure per l'hosting di Django

1. Attenersi alle istruzioni riportate [qui][portal-vm] per creare una macchina virtuale di Azure della distribuzione *Windows Server 2012 R2 Datacenter*.

1. Impostare Azure in modo da dirigere il traffico della porta **80** proveniente dal Web alla porta **80** della macchina virtuale:
 - Passare alla macchina virtuale appena creata nel portale di Azure e fare clic sulla scheda *ENDPOINTS*.
 - Fare clic sul pulsante *ADD* nella parte inferiore della schermata. ![add endpoint](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png)

 - Selezionare il protocollo *TCP* e aprire *PUBLIC PORT 80* come *PRIVATE PORT 80*. ![][port80]
1. Dalla scheda *DASHBOARD* fare clic su *CONNECT* per usare *Desktop remoto* di Windows per accedere in remoto alla macchina virtuale di Azure appena creata.  

**Nota importante:** tutte le istruzioni seguenti presuppongono che l'accesso alla macchina virtuale sia stato eseguito in modo corretto e che i comandi vengano eseguiti dalla macchina virtuale anziché da quella locale.

## <a id="setup"> </a>Configurazione di Python e Django

**Nota:** per scaricare con Internet Explorer può essere necessario configurare le impostazioni di sicurezza avanzate di Internet Explorer (Start/Strumenti di amministrazione/Server Manager/Server locale, quindi fare clic su **Configurazione sicurezza avanzata IE** e disattivare l'opzione).

1. Installare l'[Installazione guidata piattaforma Web][].
1. Installare Python WFastCGI con il programma di installazione della piattaforma Web. Il file wfastcgi.py verrà installato nella cartella degli script di Python.
	1. Avviare Installazione guidata piattaforma Web.
	1. Digitare WFastCGI nella barra di ricerca. 
	1. Selezionare la voce WFactCGI per la versione di Python che si vuole usare (2.7 o 3.4). Notare che verrà installato Python come dipendenza di WFastCGI. 
1. Installare Django usando pip.

    Python 2.7:

        c:\python27\scripts\pip install django

    Python 3.4:

        c:\python34\scripts\pip install django


## Configurazione di IIS con FastCGI

1. Installare IIS con il supporto di FastCGI. L'esecuzione può richiedere alcuni minuti.

		start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI


### Python 2.7

Eseguire questi comandi solo se si usa Python 2.7.

1. Configurare il gestore FastCGI Python.

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py']"


1. Registrare il gestore per il sito.

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python27\python.exe|C:\Python27\Scripts\wfastcgi.py',resourceType='Unspecified']"


1. Configurare il gestore per l'esecuzione dell'applicazione Django.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='helloworld.settings']" /commit:apphost


1. Configurare PYTHONPATH in modo che l'applicazione Django possa essere rilevata dall'interprete Python.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\helloworld']" /commit:apphost


1. Indicare al gateway FastCGI - WSGI quale gestore WSGI usare.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost


1. Dovrebbe essere visualizzata la seguente schermata:

	![IIS config1](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis-27.png)

### Python 3.4

Eseguire questi comandi solo se si usa Python 3.4.

1. Configurare il gestore FastCGI Python.

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py']"


1. Registrare il gestore per il sito.

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python34\python.exe|C:\Python34\Scripts\wfastcgi.py',resourceType='Unspecified']"


1. Configurare il gestore per l'esecuzione dell'applicazione Django.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='helloworld.settings']" /commit:apphost


1. Configurare PYTHONPATH in modo che l'applicazione Django possa essere rilevata dall'interprete Python.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\helloworld']" /commit:apphost


1. Indicare al gateway FastCGI - WSGI quale gestore WSGI usare.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

1. Dovrebbe essere visualizzata la seguente schermata:

	![IIS config1](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis-34.png)


## Creazione di una nuova applicazione Django


1.  Da *C:\inetpub\wwwroot*, immettere il comando seguente per creare un nuovo progetto Django:

    Python 2.7:

		C:\Python27\Scripts\django-admin.exe startproject helloworld

    Python 3.4:

		C:\Python34\Scripts\django-admin.exe startproject helloworld
    
	![Risultato del comando New-AzureService](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png)

1.  Il comando **django-admin** genera una struttura di base per i siti Web basati su Django:
    
  -   **helloworld\manage.py** consente di attivare e disattivare l'hosting del sito Web basato su Django.
  -   **helloworld\helloworld\settings.py** contiene le impostazioni Django per l'applicazione.
  -   **helloworld\helloworld\urls.py** contiene il codice di mapping tra ogni URL e la relativa visualizzazione.



1.  Creare un nuovo file denominato **views.py** nella directory *C:\inetpub\wwwroot\helloworld\helloworld*. Questo file conterrà la visualizzazione del rendering della pagina "hello world". Avviare l'editor e immettere quanto segue:
		
		from django.http import HttpResponse
		def home(request):
    		html = "<html><body>Hello World!</body></html>"
    		return HttpResponse(html)

1.  Sostituire il contenuto del file **urls.py** con il codice seguente:

		from django.conf.urls import patterns, url
		urlpatterns = patterns('',
			url(r'^$', 'helloworld.views.home', name='home'),
		)

1. Caricare infine la pagina Web nel browser.

![Finestra del browser con la pagina Hello World visualizzata in Azure][1]

## Arresto della macchina virtuale di Azure

Al termine dell'esercitazione, arrestare e/o rimuovere la macchina virtuale di Azure creata per liberare le risorse per altre esercitazioni e per evitare di incorrere in addebiti per l'utilizzo di Azure.

[1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png

[portal-vm]: /manage/windows/tutorials/virtual-machine-from-gallery/

[Installazione guidata piattaforma Web]: http://www.microsoft.com/web/downloads/platform.aspx

 

<!---HONumber=July15_HO1-->