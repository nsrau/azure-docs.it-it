---
title: App Web Python con Django | Microsoft Docs
description: Questa esercitazione illustra come ospitare un sito Web basato su Django in Azure usando una macchina virtuale Windows Server 2012 R2 Datacenter utilizzando un modello di distribuzione classica.
services: virtual-machines-windows
documentationcenter: python
author: huguesv
manager: wpickett
editor: ''
tags: azure-service-management

ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: python
ms.topic: article
ms.date: 08/04/2015
ms.author: huvalo

---
# Applicazione Web Hello World Django in una macchina virtuale di Windows Server.
> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-classic-python-django-web-app.md)
> * [Mac/Linux](virtual-machines-linux-python-django-web-app.md)
> 
> 

<br>

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Questa esercitazione illustra come ospitare un sito Web basato su Django in Microsoft Azure usando una macchina virtuale Windows Server. In questa esercitazione si presuppone che l'utente non abbia mai usato Azure. Dopo aver completato questa esercitazione, si disporrà di un'applicazione basata su Django in esecuzione nel cloud.

Si apprenderà come:

* Configurare una macchina virtuale di Azure per l'hosting di Django. Sebbene nell'esercitazione la procedura venga illustrata in Windows Server, è possibile eseguirla anche con una macchina virtuale Linux ospitata in Azure.
* Creare una nuova applicazione Django da Windows.

Seguendo questa esercitazione, verrà creata una semplice applicazione Web Hello World, ospitata in una macchina virtuale di Azure.

Di seguito viene riportata una schermata dell'applicazione completata.

![Finestra del browser con la pagina Hello World visualizzata in Azure][1]

[!INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Creazione e configurazione di una macchina virtuale di Azure per l'hosting di Django
1. Seguire le istruzioni riportate [qui](virtual-machines-windows-classic-tutorial.md) per creare una macchina virtuale di Azure della distribuzione Windows Server 2012 R2 Datacenter.
2. Impostare Azure in modo da dirigere il traffico della porta 80 proveniente dal Web alla porta 80 della macchina virtuale:
   
   * Passare alla macchina virtuale appena creata nel portale di Azure classico e fare clic sulla scheda **ENDPOINTS**.
   * Fare clic sul pulsante **ADD** nella parte inferiore della schermata. ![add endpoint](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-addendpoint.png)
   * Selezionare il protocollo **TCP** e aprire **PUBLIC PORT 80** come **PRIVATE PORT 80**. ![][port80]
3. Dalla scheda **DASHBOARD** fare clic su **CONNECT** per usare **Desktop remoto** di Windows per accedere in remoto alla macchina virtuale di Azure appena creata.

**Nota importante:** tutte le istruzioni riportate di seguito presuppongono che l'accesso alla macchina virtuale sia stato eseguito in modo corretto e che i comandi vengano eseguiti dalla macchina virtuale anziché da quella locale.

## <a id="setup"> </a>Installazione di Python, Django, WFastCGI
**Nota:** per scaricare con Internet Explorer può essere necessario configurare le impostazioni di sicurezza avanzate di Internet Explorer (Start/Strumenti di amministrazione/Server Manager/Server locale, quindi fare clic su **Configurazione sicurezza avanzata IE** e disattivare l'opzione).

1. Installare la versione più recente di Python 2.7 o 3.4 dal sito [python.org][python.org].
2. Installare i pacchetti wfastcgi e django usando pip.
   
    Per Python 2.7, utilizzare il comando seguente.
   
        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django
   
    Per Python 3.4, utilizzare il comando seguente.
   
        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## Installazione di IIS con FastCGI
1. Installare IIS con il supporto di FastCGI. L'esecuzione può richiedere alcuni minuti.
   
        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## Creazione di una nuova applicazione Django
1. Da *C:\\inetpub\\wwwroot*, immettere il comando seguente per creare un nuovo progetto Django:
   
   Per Python 2.7, utilizzare il comando seguente.
   
       C:\Python27\Scripts\django-admin.exe startproject helloworld
   
   Per Python 3.4, utilizzare il comando seguente.
   
       C:\Python34\Scripts\django-admin.exe startproject helloworld
   
   ![Risultato del comando New-AzureService](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-cmd-new-azure-service.png)
2. Il comando **django-admin** genera una struttura di base per i siti Web basati su Django:
   
   * **helloworld\\manage.py** consente di attivare e disattivare l'hosting del sito Web basato su Django.
   * **helloworld\\helloworld\\settings.py** contiene le impostazioni Django per l'applicazione.
   * **helloworld\\helloworld\\urls.py** contiene il codice di mapping tra ogni URL e la relativa visualizzazione.
3. Creare un nuovo file denominato **views.py** nella directory *C:\\inetpub\\wwwroot\\helloworld\\helloworld*. Questo file conterrà la visualizzazione del rendering della pagina "hello world". Avviare l'editor e immettere quanto segue:
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. Sostituire il contenuto del file urls.py con il codice seguente.
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## Configurazione di IIS
1. Sbloccare la sezione dei gestori nel file applicationhost.config globale. In questo modo si abiliterà l'uso del gestore python nel file web.config.
   
        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers
2. Abilitare WFastCGI. In questo modo si aggiungerà un'applicazione al file applicationhost.config globale che si riferisce all'interprete Python eseguibile e allo script wfastcgi.py.
   
    Python 2.7:
   
        c:\python27\scripts\wfastcgi-enable
   
    Python 3.4:
   
        c:\python34\scripts\wfastcgi-enable
3. Creare un file web.config in *C:\\inetpub\\wwwroot\\helloworld*. Il valore dell'attributo `scriptProcessor` deve corrispondere all'output del passaggio precedente. Vedere la pagina relativa a [wfastcgi][wfastcgi] su pypi per altre informazioni sulle impostazioni wfastcgi.
   
    Python 2.7:
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
   
    Python 3.4:
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
4. Aggiornare la posizione del sito Web predefinito IIS in modo che punti alla cartella del progetto django.
   
        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"
5. Caricare infine la pagina Web nel browser.

![Finestra del browser con la pagina Hello World visualizzata in Azure][1]

## Arresto della macchina virtuale di Azure
Al termine dell'esercitazione, arrestare e/o rimuovere la macchina virtuale di Azure creata di recente per liberare le risorse per altre esercitazioni e per evitare di incorrere in addebiti per l'utilizzo di Azure.

[1]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi

<!---HONumber=AcomDC_0629_2016-->