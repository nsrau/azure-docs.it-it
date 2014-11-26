<properties linkid="develop-python-web-app-with-django-mac" urlDisplayName="Web with Django" pageTitle="Python web app with Django on Mac - Azure tutorial" metaKeywords="" description="A tutorial that shows how to host a Django-based website on Azure using a Linux virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World Web Application (mac-linux)" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-linux" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Applicazione Web Hello World Django (mac-linux)

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/develop/python/tutorials/web-app-with-django/" title="Windows">Windows</a><a href="/it-it/develop/python/tutorials/django-hello-world-(maclinux)/" title="Mac/Linux" class="current">Mac/Linux</a></div>

Questa esercitazione illustra come ospitare un sito Web basato su Django in
Windows Azure usando una macchina virtuale Linux. In questa esercitazione si presuppone che l'utente non abbia mai utilizzato Azure. Al termine dell'esercitazione, si disporrà di un'applicazione basata su Django in esecuzione nel cloud.

Si apprenderà come:

-   Configurare una macchina virtuale di Azure per l'hosting di Django. Sebbene nell'esercitazione la procedura venga illustrata in **Linux**, è possibile eseguirla anche con una macchina virtuale Windows Server ospitata in Azure.
-   Creare una nuova applicazione Django da Linux.

Seguendo questa esercitazione, si creerà una semplice applicazione
Web Hello World. L'applicazione verrà ospitata in una macchina virtuale di Azure.

Di seguito è riportata una schermata dell'applicazione completata:

![Finestra del browser con la pagina Hello World visualizzata in Azure][Finestra del browser con la pagina Hello World visualizzata in Azure]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Creazione e configurazione di una macchina virtuale di Azure per l'hosting di Django

1.  Attenersi alle istruzioni riportate [qui][qui] per creare una macchina virtuale di Azure della distribuzione *Ubuntu Server 14.04*.

**Nota:** è necessario creare *solo* la macchina virtuale. Arrivare solo fino alla sezione intitolata *Come accedere alla macchina virtuale dopo averla creata*.

1.  Impostare Azure in modo da dirigere il traffico della porta **80** proveniente dal Web alla porta **80** della macchina virtuale:

    -   Passare alla macchina virtuale appena creata nel portale di Azure e fare clic sulla scheda *ENDPOINTS*.
    -   Fare clic sul pulsante *ADD ENDPOINT* nella parte inferiore della schermata.
        ![Pulsante ADD ENDPOINT][Pulsante ADD ENDPOINT]
    -   Selezionare il protocollo *TCP* e aprire *PUBLIC PORT 80* come *PRIVATE PORT 80*.
        ![port80][port80]

## <span id="setup"></span> </a>Configurazione dell'ambiente di sviluppo

**Nota:** se è necessario installare Python o si desidera utilizzare le librerie client, vedere la [guida all'installazione di Python][guida all'installazione di Python].

Anche se nella macchina virtuale Ubuntu Linux Python 2.7 è già preinstallato, non sono installati Apache né django. Per connettersi alla macchina virtuale e installare Apache and django, attenersi alla procedura seguente.

1.  Aprire una nuova finestra **Terminal**.

2.  Immettere il comando seguente per connettersi alla macchina virtuale di Azure.

        $ ssh yourusername@yourVmUrl

3.  Immettere i comandi seguenti per installare django:

        $ sudo apt-get install python-setuptools
        $ sudo easy_install django

4.  Immettere il comando seguente per installare Apache con mod-wsgi:

        $ sudo apt-get install apache2 libapache2-mod-wsgi

## Creazione di una nuova applicazione Django

1.  Aprire la finestra **Terminal** utilizzata nella sezione precedente per connettersi tramite SSH alla macchina virtuale.

2.  Immettere i comandi seguenti per creare un nuovo progetto Django:

        $ cd /var/www
        $ sudo django-admin.py startproject helloworld

    Lo script **django-admin.py** genera una struttura di base per i siti Web basati su Django:

    -   **helloworld/manage.py** consente di attivare e disattivare l'hosting del sito Web basato su Django.
    -   **helloworld/helloworld/settings.py** contiene le impostazioni Django per l'applicazione.
    -   **helloworld/helloworld/urls.py** contiene il codice di mapping tra ogni URL e la relativa visualizzazione.

3.  Creare un nuovo file denominato **views.py** nella directory **/var/www/helloworld/helloworld**, come elemento di pari livello di **urls.py**. Questo file conterrà la visualizzazione del rendering della pagina "hello world". Avviare l'editor e immettere quanto segue:

        from django.http import HttpResponse
        def hello(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

4.  Sostituire ora il contenuto del file **urls.py** con il codice seguente:

        from django.conf.urls.defaults import patterns, include, url
        from helloworld.views import hello
        urlpatterns = patterns('',
            (r'^$',hello),
        )

## Configurazione di Apache

1.  Creare un file di configurazione dell'host virtuale Apache **/etc/apache2/sites-available/helloworld.conf**. Impostare il contenuto del file in base al codice riportato di seguito e assicurarsi di sostituire *URLmacchinavirtuale* con l'URL effettivo della macchina in uso, ad esempio *pyubuntu.cloudapp.net*.

        <VirtualHost *:80>
        ServerName yourVmUrl
        </VirtualHost>
        WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
        WSGIPythonPath /var/www/helloworld

2.  Abilitare il sito con il comando seguente:

        $ sudo a2ensite helloworld

3.  Riavviare Apache con il comando seguente:

        $ sudo service apache2 reload

4.  Caricare infine la pagina Web nel browser:

    ![Finestra del browser con la pagina Hello World visualizzata in Azure][Finestra del browser con la pagina Hello World visualizzata in Azure]

## Arresto della macchina virtuale di Azure

Al termine dell'esercitazione, arrestare e/o rimuovere la macchina virtuale di Azure creata per liberare le risorse per altre esercitazioni e per evitare di incorrere in addebiti per l'utilizzo di Azure.

  [Finestra del browser con la pagina Hello World visualizzata in Azure]: ./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png
  [qui]: /it-it/manage/linux/tutorials/virtual-machine-from-gallery/
  [Pulsante ADD ENDPOINT]: ./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-add-endpoint.png
  [port80]: ./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-port80.png
  [guida all'installazione di Python]: ../python-how-to-install/
