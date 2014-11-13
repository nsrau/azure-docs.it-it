<properties urlDisplayName="Web with Django (Windows)" pageTitle="App Web Python con Django - Esercitazione di Azure" metaKeywords="Azure Django web app, Azure Django virtual machine" description="Questa esercitazione illustra come ospitare un sito Web basato su Django in Azure usando una macchina virtuale Windows Server 2012 R2 Datacenter." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Applicazione Web Hello World Django" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-windows" ms.devlang="python" ms.topic="article" ms.date="09/25/2014" ms.author="huvalo" />

# Applicazione Web Hello World Django

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/develop/python/tutorials/web-app-with-django/" title="Windows" class="current">Windows</a><a href="/it-it/develop/python/tutorials/django-hello-world-(maclinux)/" title="MacLinux">Mac/Linux</a></div>

Questa esercitazione illustra come ospitare un sito Web basato su Django in Microsoft
Azure usando una macchina virtuale Windows Server. In questa esercitazione si presuppone che l'utente non abbia mai usato Azure. Al termine dell'esercitazione, si disporrà di un'applicazione basata su Django in esecuzione nel cloud.

Si apprenderà come:

-   Configurare una macchina virtuale di Azure per l'hosting di Django. Sebbene nell'esercitazione la procedura venga illustrata in **Windows Server**, è possibile eseguirla anche con una macchina virtuale Linux ospitata in Azure.
-   Creare una nuova applicazione Django da Windows.

Seguendo questa esercitazione, si creerà una semplice applicazione
Web Hello World. L'applicazione verrà ospitata in una macchina virtuale di Azure.

Di seguito è riportata una schermata dell'applicazione completata:

![Finestra del browser con la pagina Hello World visualizzata in Azure][Finestra del browser con la pagina Hello World visualizzata in Azure]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Creazione e configurazione di una macchina virtuale di Azure per l'hosting di Django

1.  Attenersi alle istruzioni riportate [qui][qui] per creare una macchina virtuale di Azure della distribuzione *Windows Server 2012 R2 Datacenter*.

2.  Impostare Azure in modo da dirigere il traffico della porta **80** proveniente dal Web alla porta **80** della macchina virtuale:

-   Passare alla macchina virtuale appena creata nel portale di Azure e fare clic sulla scheda *ENDPOINTS*.
-   Fare clic sul pulsante *ADD* nella parte inferiore della schermata.
    ![add endpoint][add endpoint]

-   Selezionare il protocollo *TCP* e aprire *PUBLIC PORT 80* come *PRIVATE PORT 80*.
    ![][0]

1.  Dalla scheda *DASHBOARD* fare clic su *CONNECT* per usare *Desktop remoto* di Windows per accedere in remoto alla macchina virtuale di Azure appena creata.

**Nota importante:** tutte le istruzioni riportate di seguito presuppongono che l'accesso alla macchina virtuale sia stato eseguito in modo corretto e che i comandi vengano eseguiti dalla macchina virtuale anziché da quella locale.

## <span id="setup"></span> </a>Configurazione di Python e Django

**Nota:** per scaricare con Internet Explorer può essere necessario configurare le impostazioni di sicurezza avanzate di (Start/Strumenti di amministrazione/Server Manager/Server locale, quindi fare clic su **Configura sicurezza avanzata di Internet Explorer** e disattivare l'opzione).

1.  Installare l'[Installazione guidata piattaforma Web][Installazione guidata piattaforma Web].
2.  Installare Python WFastCGI con il programma di installazione della piattaforma Web. Il file wfastcgi.py verrà installato nella cartella degli script di Python.

    1.  Avviare Installazione guidata piattaforma Web.
    2.  Digitare WFastCGI nella barra di ricerca.
    3.  Selezionare la voce WFactCGI per la versione di Python che si vuole usare (2.7 o 3.4). Notare che verrà installato Python come dipendenza di WFastCGI.

3.  Se è stato installato Python 2.7, [seguire queste istruzioni per installare pip manualmente][seguire queste istruzioni per installare pip manualmente] (Python 3.4 viene fornito con pip già installato).
4.  Installare Django usando pip.

    Python 2,7:

        c:\python27\scripts\pip install django

    Python 3.4:

        c:\python34\scripts\pip install django

## Configurazione di IIS con FastCGI

1.  Installare IIS con il supporto di FastCGI. L'esecuzione può richiedere alcuni minuti.

        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

### Python 2,7

Eseguire questi comandi solo se si usa Python 2,7.

1.  Configurare il gestore FastCGI Python.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py']"

2.  Registrare il gestore per il sito.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python27\python.exe|C:\Python27\Scripts\wfastcgi.py',resourceType='Unspecified']"

3.  Configurare il gestore per l'esecuzione dell'applicazione Django.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='helloworld.settings']" /commit:apphost

4.  Configurare PYTHONPATH in modo che l'applicazione Django possa essere rilevata dall'interprete Python.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\helloworld']" /commit:apphost

5.  Indicare al gateway FastCGI - WSGI quale gestore WSGI usare.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

6.  Dovrebbe essere visualizzata la schermata seguente:

    ![IIS config1][IIS config1]

### Python 3.4

Eseguire questi comandi solo se si usa Python 3.4.

1.  Configurare il gestore FastCGI Python.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py']"

2.  Registrare il gestore per il sito.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python34\python.exe|C:\Python34\Scripts\wfastcgi.py',resourceType='Unspecified']"

3.  Configurare il gestore per l'esecuzione dell'applicazione Django.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='helloworld.settings']" /commit:apphost

4.  Configurare PYTHONPATH in modo che l'applicazione Django possa essere rilevata dall'interprete Python.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\helloworld']" /commit:apphost

5.  Indicare al gateway FastCGI - WSGI quale gestore WSGI usare.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

6.  Dovrebbe essere visualizzata la schermata seguente:

    ![IIS config1][1]

## Creazione di una nuova applicazione Django

1.  Da *C:\\inetpub\\wwwroot*, immettere il comando seguente per creare un nuovo progetto Django:

    Python 2,7:

        C:\Python27\Scripts\django-admin.exe startproject helloworld

    Python 3.4:

        C:\Python34\Scripts\django-admin.exe startproject helloworld

    ![Risultato del comando New-AzureService][Risultato del comando New-AzureService]

2.  Il comando **django-admin** genera una struttura di base per i siti Web basati su Django:

-   **helloworld\\manage.py** consente di attivare e disattivare l'hosting del sito Web basato su Django.
-   **helloworld\\helloworld\\settings.py** contiene le impostazioni Django per l'applicazione.
-   **helloworld\\helloworld\\urls.py** contiene il codice di mapping tra ogni URL e la relativa visualizzazione.

1.  Creare un nuovo file denominato **views.py** nella directory *C:\\inetpub\\wwwroot\\helloworld\\helloworld*. Questo file conterrà la visualizzazione del rendering della pagina "hello world". Avviare l'editor e immettere quanto segue:

        from django.http import HttpResponse
        def home(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

2.  Sostituire il contenuto del file **urls.py** con il codice seguente:

        from django.conf.urls import patterns, url
        urlpatterns = patterns('',
            url(r'^$', 'helloworld.views.home', name='home'),
        )

3.  Caricare infine la pagina Web nel browser.

![Finestra del browser con la pagina Hello World visualizzata in Azure][Finestra del browser con la pagina Hello World visualizzata in Azure]

## Arresto della macchina virtuale di Azure

Al termine dell'esercitazione, arrestare e/o rimuovere la macchina virtuale di Azure creata per liberare le risorse per altre esercitazioni e per evitare di incorrere in addebiti per l'utilizzo di Azure.

  [Finestra del browser con la pagina Hello World visualizzata in Azure]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png
  [qui]: /it-it/manage/windows/tutorials/virtual-machine-from-gallery/
  [add endpoint]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png
  [0]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png
  [Installazione guidata piattaforma Web]: http://www.microsoft.com/web/downloads/platform.aspx
  [seguire queste istruzioni per installare pip manualmente]: https://pip.pypa.io/en/latest/installing.html
  [IIS config1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis-27.png
  [1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis-34.png
  [Risultato del comando New-AzureService]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png
