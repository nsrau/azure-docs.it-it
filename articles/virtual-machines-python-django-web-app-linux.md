<properties 
	pageTitle="App Web Python con Django in Mac - Esercitazione di Azure" 
	description="Esercitazione che illustra come ospitare un sito Web basato su Django in Azure usando una macchina virtuale Linux." 
	services="virtual-machines" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/05/2015" 
	ms.author="huvalo"/>





# Applicazione Web Hello World Django (mac-linux)

<div class="dev-center-tutorial-selector sublanding"><a href="/develop/python/tutorials/web-app-with-django/" title="Windows">Windows</a><a href="/develop/python/tutorials/django-hello-world-(maclinux)/" title="Mac/Linux" class="current">Mac/Linux</a></div>

Questa esercitazione illustra come ospitare un sito Web basato su Django in Microsoft Azure usando una macchina virtuale Linux. In questa esercitazione si presuppone che l'utente non abbia mai usato Azure. Al termine dell'esercitazione, si disporrà di un'applicazione basata su Django in esecuzione nel cloud.

Si apprenderà come:

* Configurare una macchina virtuale di Azure per l'hosting di Django. Sebbene nell'esercitazione la procedura venga illustrata in **Linux**, è possibile eseguirla anche con una macchina virtuale Windows Server ospitata in Azure. 
* Creare una nuova applicazione Django da Linux.

Seguendo questa esercitazione, verrà creata una semplice applicazione Web Hello World. L'applicazione verrà ospitata in una macchina virtuale di Azure.

Di seguito è riportata una schermata dell'applicazione completata:

![A browser window displaying the hello world page on Azure](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)

[AZURE.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Creazione e configurazione di una macchina virtuale di Azure per l'hosting di Django

1. Attenersi alle istruzioni riportate [qui][portal-vm] per creare una macchina virtuale di Azure della distribuzione *Ubuntu Server 14.04 LTS*.

  **Nota:** è necessario creare *only* la macchina virtuale. Arrestare la sezione intitolata *How to log on to the virtual machine after you create it*.

1. Impostare Azure in modo da dirigere il traffico della porta **80** proveniente dal Web alla porta **80** della macchina virtuale:
	* Passare alla macchina virtuale appena creata nel portale di Azure e fare clic sulla scheda *ENDPOINTS*.
	* Fare clic sul pulsante *ADD* nella parte inferiore della schermata.
	![add endpoint](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-add-endpoint.png)
	* Aprire la *PUBLIC PORT 80* del protocollo *TCP* come *PRIVATE PORT 80*.
	![port80](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-port80.png)

## <a id="setup"> </a>Configurazione dell'ambiente di sviluppo

**Nota:** se è necessario installare Python o si desidera usare le librerie client, vedere la [guida all'installazione di Python](python-how-to-install.md).

Anche se nella macchina virtuale Ubuntu Linux Python 2.7 è già preinstallato, non sono installati Apache né Django.  Per connettersi alla macchina virtuale e installare Apache e Django, seguire questa procedura.

1.  Aprire una nuova finestra **Terminal**.
    
1.  Immettere il seguente comando per connettersi alla macchina virtuale di Azure.

		$ ssh yourusername@yourVmUrl

1.  Immettere i seguenti comandi per installare Django:

		$ sudo apt-get install python-setuptools
		$ sudo easy_install django

1.  Immettere il seguente comando per installare Apache con mod-wsgi:

		$ sudo apt-get install apache2 libapache2-mod-wsgi


## Creazione di una nuova applicazione Django

1.  Aprire la finestra **Terminal** usata nella sezione precedente per connettersi tramite SSH alla macchina virtuale.
    
1.  Immettere i seguenti comandi per creare un nuovo progetto Django:

		$ cd /var/www
		$ sudo django-admin.py startproject helloworld

    Lo script **django-admin.py** genera una struttura di base per i siti Web basati su Django:
    -   **helloworld/manage.py** consente di avviare e interrompere l'hosting del sito Web basato su Django.
    -   **helloworld/helloworld/settings.py** contiene le impostazioni di Django per l'applicazione.
    -   **helloworld/helloworld/urls.py** contiene il codice di mapping tra ogni URL e la relativa visualizzazione.

1.  Creare un nuovo file denominato **views.py** nella directory **/var/www/helloworld/helloworld**. Questo file conterrà la visualizzazione del rendering della pagina "hello world". Avviare l'editor e immettere quanto segue:
		
		from django.http import HttpResponse
		def home(request):
    		html = "<html><body>Hello World!</body></html>"
    		return HttpResponse(html)

1.  Sostituire ora il contenuto del file **urls.py** con il seguente codice:

		from django.conf.urls import patterns, url
		urlpatterns = patterns('',
			url(r'^$', 'helloworld.views.home', name='home'),
		)


## Configurazione di Apache

1.  Creare un file di configurazione dell'host virtuale Apache **/etc/apache2/sites-available/helloworld.conf**. Impostare il contenuto del file in base al seguente codice e assicurarsi di sostituire *yourVmUrl* con l'URL effettivo della macchina in uso (ad esempio *pyubuntu.cloudapp.net*).

		<VirtualHost *:80>
		ServerName yourVmUrl
		</VirtualHost>
		WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
		WSGIPythonPath /var/www/helloworld

1.  Abilitare il sito con il seguente comando:

        $ sudo a2ensite helloworld

1.  Riavviare Apache con il seguente comando:

        $ sudo service apache2 reload

1.  Caricare infine la pagina Web nel browser:

	![A browser window displaying the hello world page on Azure](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)


## Arresto della macchina virtuale di Azure

Al termine dell'esercitazione, arrestare e/o rimuovere la macchina virtuale di Azure creata per liberare le risorse per altre esercitazioni e per evitare di incorrere in addebiti per l'utilizzo di Azure.


[portal-vm]: /manage/linux/tutorials/virtual-machine-from-gallery/

<!--HONumber=47-->
