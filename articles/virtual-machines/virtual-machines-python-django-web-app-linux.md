<properties 
	pageTitle="App Web Python con Django in Linux | Microsoft Azure" 
	description="Informazioni su come ospitare un’applicazione Web basato su Django in Azure usando una macchina virtuale Linux." 
	services="virtual-machines" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""
	tags=“azure-service-management"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="11/17/2015" 
	ms.author="huvalo"/>
	
# Applicazione Web Hello World Django in una macchina virtuale di Linux.

> [AZURE.SELECTOR]
- [Windows](virtual-machines-python-django-web-app-windows-server.md)
- [Mac/Linux](virtual-machines-python-django-web-app-linux.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello Gestione risorse.


Questa esercitazione illustra come ospitare un sito Web basato su Django in Microsoft Azure usando una macchina virtuale Linux. In questa esercitazione si presuppone che l'utente non abbia mai usato Azure. Al termine dell'esercitazione, si disporrà di un'applicazione basata su Django in esecuzione nel cloud.

Si apprenderà come:

* Configurare una macchina virtuale di Azure per l'hosting di Django. Sebbene nell'esercitazione la procedura venga illustrata in **Linux**, è possibile eseguirla anche con una macchina virtuale Windows Server ospitata in Azure. 
* Creare una nuova applicazione Django da Linux.

Seguendo questa esercitazione, verrà creata una semplice applicazione Web Hello World, ospitata in una macchina virtuale di Azure.

Di seguito è riportata una schermata dell'applicazione completata:

![Finestra del browser con la pagina Hello World visualizzata in Azure](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Creazione e configurazione di una macchina virtuale di Azure per l'hosting di Django

1. Attenersi alle istruzioni riportate [qui](virtual-machines-linux-tutorial-portal-rm.md) per creare una macchina virtuale di Azure della distribuzione *Ubuntu Server 14.04 LTS*. Se si preferisce, è possibile scegliere l'autenticazione di password al posto della chiave pubblica SSH.

1. Modificare il gruppo di sicurezza di rete per consentire il traffico http in ingresso alla porta 80 utilizzando le istruzioni [qui](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).

1. Per impostazione predefinita, la nuova macchina virtuale non dispone di un nome di dominio completo (FQDN). È possibile crearne uno seguendo le istruzioni [qui](virtual-machines-create-fqdn-on-portal.md). Questo passaggio è facoltativo per completare questa esercitazione.

## <a id="setup"> </a>Configurazione dell'ambiente di sviluppo

**Nota:** se è necessario installare Python o si desidera usare le librerie client, vedere la [guida all'installazione di Python](../python-how-to-install.md).

Anche se nella macchina virtuale Ubuntu Linux Python 2.7 è già preinstallato, non sono installati Apache né Django. Per connettersi alla macchina virtuale e installare Apache and django, attenersi alla procedura seguente.

1.  Aprire una nuova finestra **Terminal**.
    
1.  Immettere il comando seguente per connettersi alla macchina virtuale di Azure. Se non è stato creato un nome di dominio completo, è possibile connettersi utilizzando l'indirizzo IP pubblico visualizzato nel riepilogo della macchina virtuale nel portale di Azure classico.

		$ ssh yourusername@yourVmUrl

1.  Immettere i comandi seguenti per installare django:

		$ sudo apt-get install python-setuptools python-pip
		$ sudo pip install django

1.  Immettere il comando seguente per installare Apache con mod-wsgi:

		$ sudo apt-get install apache2 libapache2-mod-wsgi


## Creazione di una nuova applicazione Django

1.  Aprire la finestra **Terminal** utilizzata nella sezione precedente per connettersi tramite SSH alla macchina virtuale.
    
1.  Immettere i seguenti comandi per creare un nuovo progetto Django:

		$ cd /var/www
		$ sudo django-admin.py startproject helloworld

    Lo script **django-admin.py** genera una struttura di base per i siti Web basati su Django:- **helloworld/manage.py** consente di avviare e interrompere l'hosting del sito Web basato su Django - **helloworld/helloworld/settings.py** contiene le impostazioni di Django per l'applicazione. - **helloworld/helloworld/urls.py** contiene il codice di mapping tra ogni url e la relativa visualizzazione.

1.  Creare un nuovo file denominato **views.py** nella directory **/var/www/helloworld/helloworld**. Questo file conterrà la visualizzazione del rendering della pagina "hello world". Avviare l'editor e immettere quanto segue:
		
		from django.http import HttpResponse
		def home(request):
    		html = "<html><body>Hello World!</body></html>"
    		return HttpResponse(html)

1.  Sostituire ora il contenuto del file **urls.py** con il codice seguente:

		from django.conf.urls import patterns, url
		urlpatterns = patterns('',
			url(r'^$', 'helloworld.views.home', name='home'),
		)


## Configurazione di Apache

1.  Creare un file di configurazione dell'host virtuale Apache **/etc/apache2/sites-available/helloworld.conf**. Impostare il contenuto del file in base al seguente codice e sostituire *yourVmName*con il nome effettivo del computer in uso (ad esempio *pyubuntu*).

		<VirtualHost *:80>
		ServerName yourVmName
		</VirtualHost>
		WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
		WSGIPythonPath /var/www/helloworld

1.  Abilitare il sito con il seguente comando:

        $ sudo a2ensite helloworld

1.  Riavviare Apache con il comando seguente:

        $ sudo service apache2 reload

1.  Caricare infine la pagina Web nel browser:

	![Finestra del browser con la pagina Hello World visualizzata in Azure](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)


## Arresto della macchina virtuale di Azure

Al termine dell'esercitazione, arrestare e/o rimuovere la macchina virtuale di Azure creata per liberare le risorse per altre esercitazioni e per evitare di incorrere in addebiti per l'utilizzo di Azure.

<!---HONumber=AcomDC_1203_2015-->