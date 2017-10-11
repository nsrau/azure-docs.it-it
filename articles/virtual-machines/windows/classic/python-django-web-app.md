---
title: App Web Django in una macchina virtuale Windows Server in Azure | Microsoft Docs
description: Informazioni su come ospitare un sito Web basato su Django in Azure usando una macchina virtuale Windows Server 2012 R2 Datacenter con il modello di distribuzione classica.
services: virtual-machines-windows
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
tags: azure-service-management
ms.assetid: e36484d1-afbf-47f5-b755-5e65397dc1c3
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: python
ms.topic: article
ms.date: 05/31/2017
ms.author: huvalo
ms.openlocfilehash: 283a296fb39863c2801be1093cc4f56904786abd
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="django-hello-world-web-app-on-a-windows-server-vm"></a>App Web Hello World Django in una macchina virtuale Windows Server

> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Azure Resource Manager e il modello di distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra il modello di distribuzione classica. Per le distribuzioni più recenti si consiglia di usare il modello di Resource Manager.

In questa esercitazione viene illustrato come ospitare un sito Web basato su Django in Windows Server nelle macchine virtuali di Azure. Nell'esercitazione si presuppone che l'utente non abbia mai usato Azure. Al termine dell'esercitazione, si disporrà di un'applicazione basata su Django in esecuzione nel cloud.

È possibile passare agli argomenti seguenti:

* Configurare una macchina virtuale di Azure per l'hosting di Django. Sebbene nell'esercitazione la procedura venga illustrata in **Windows Server**, è possibile eseguirla anche con una macchina virtuale Linux ospitata in Azure.
* Creare una nuova applicazione Django in Windows.

Nell'esercitazione viene illustrato come compilare una semplice applicazione Web Hello World, ospitata in una macchina virtuale di Azure.

In questo screenshot viene visualizzata l'applicazione completata:

![Finestra del browser con la pagina hello world visualizzata in Azure][1]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-set-up-an-azure-virtual-machine-to-host-django"></a>Creare e configurare una macchina virtuale di Azure per l'hosting di Django

1. Per creare una macchina virtuale di Azure con la distribuzione di Windows Server 2012 R2 Datacenter, vedere [Creare una macchina virtuale con Windows nel portale di Azure](tutorial.md).
2. Impostare Azure in modo da dirigere il traffico della porta 80 proveniente dal Web alla porta 80 della macchina virtuale:
   
   1. Nel portale di Azure passare al dashboard e selezionare la macchina virtuale appena creata.
   2. Far clic su **Endpoint** e selezionare **Aggiungi**.

     ![Aggiungere un endpoint](./media/python-django-web-app/django-helloworld-add-endpoint-new-portal.png)

   3. Nella pagina **Aggiungi endpoint** immettere **HTTP** nel campo **Nome**. Impostare le porte TCP pubblica e privata su **80**.

     ![Immettere il nome e impostare le porte pubblica e privata](./media/python-django-web-app/django-helloworld-add-endpoint-set-ports-new-portal.png)

   4. Fare clic su **OK**.
     
3. Nel dashboard selezionare la VM. Per usare Remote Desktop Protocol (RDP) per accedere in modalità remota alla macchina virtuale di Azure appena creata, fare clic su **Connetti**.  

> [!IMPORTANT] 
> Per le istruzioni seguenti, si suppone che l'accesso alla macchina virtuale sia stato eseguito correttamente e che i comandi vengano inviati dalla macchina virtuale e non dal computer locale.

## <a id="setup"> </a>Installare Python, Django e WFastCGI
> [!NOTE]
> Per il download tramite Internet Explorer potrebbe essere necessario configurarne le impostazioni di **Sicurezza avanzata**. A tale scopo, fare clic su **Start** > **Strumenti di amministrazione** > **Server Manager** > **Server locale**. Fare clic su **Configurazione sicurezza avanzata IE** e quindi **disattivare** l'opzione.

1. Installare le versioni più recenti di Python 2.7 o Python 3.4 da [python.org][python.org].
2. Installare i pacchetti wfastcgi e django usando pip.
   
    Per Python 2.7, usare il comando seguente:
   
        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django
   
    Per Python 3.4, usare il comando seguente:
   
        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="install-iis-with-fastcgi"></a>Installare IIS con FastCGI
* Installare Internet Information Services (IIS) con il supporto FastCGI. L'esecuzione può richiedere alcuni minuti.
   
        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="create-a-new-django-application"></a>Creare una nuova applicazione Django
1. Per creare un nuovo progetto Django, immettere in C:\inetpub\wwwroot il comando seguente:
   
   Per Python 2.7, usare il comando seguente:
   
       C:\Python27\Scripts\django-admin.exe startproject helloworld
   
   Per Python 3.4, usare il comando seguente:
   
       C:\Python34\Scripts\django-admin.exe startproject helloworld
   
   ![Risultato del comando New-AzureService](./media/python-django-web-app/django-helloworld-cmd-new-azure-service.png)
2. Il comando `django-admin` genera una struttura di base per i siti Web basati su Django:
   
   * `helloworld\manage.py` consente di avviare e arrestare l'hosting del sito Web basato su Django.
   * `helloworld\helloworld\settings.py` contiene le impostazioni di Django per l'applicazione.
   * `helloworld\helloworld\urls.py` contiene il codice di mapping tra ogni URL e la relativa visualizzazione.
3. Creare un nuovo file denominato views.py nella directory C:\inetpub\wwwroot\helloworld\helloworld. Questo file conterrà la visualizzazione del rendering della pagina "hello world". Nell'editor del codice immettere i comandi seguenti:
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. Sostituire il contenuto del file urls.py con i comandi seguenti:
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="set-up-iis"></a>Configurare IIS
1. Nel file applicationhost.config globale sbloccare la sezione dei gestori.  In questo modo si consentirà al file web.config di usare il gestore Python. Aggiungere questo comando:
   
        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers
2. Attivare WFastCGI. In questo modo si aggiungerà un'applicazione al file applicationhost.config globale che fa riferimento all'eseguibile dell'interprete Python e allo script wfastcgi.py.
   
    In Python 2.7:
   
        C:\python27\scripts\wfastcgi-enable
   
    In Python 3.4:
   
        C:\python34\scripts\wfastcgi-enable
3. Creare un file web.config in C:\inetpub\wwwroot\helloworld. Il valore dell'attributo `scriptProcessor` deve corrispondere all'output del passaggio precedente. Per altre informazioni sull'impostazione di wfastcgi, vedere [pypi wfastcgi][wfastcgi].
   
   In Python 2.7:
   
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
   
   In Python 3.4:
   
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
4. Aggiornare la posizione del sito Web predefinito IIS in modo che punti alla cartella del progetto Django:
   
        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"
5. Caricare la pagina Web nel browser.

![Finestra del browser con la pagina hello world visualizzata in Azure][1]

## <a name="shut-down-your-azure-virtual-machine"></a>Arrestare la macchina virtuale di Azure
Al termine dell'esercitazione è consigliabile arrestare o rimuovere la macchina virtuale di Azure creata per l'esercitazione. Ciò consente di liberare risorse per altre esercitazioni ed evitare di incorrere in costi di utilizzo di Azure.

[1]: ./media/python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi
