---
title: App Web Python con Django in una macchina virtuale Linux di Azure | Microsoft Docs
description: Informazioni su come ospitare un'app Web basata su Django in Azure usando una macchina virtuale Linux.
services: virtual-machines-linux
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
tags: azure-resource-manager
ms.assetid: 00ad4c2c-4316-4f9a-913f-f7f49b158db7
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: python
ms.topic: article
ms.date: 05/31/2017
ms.author: huvalo
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 6e2ab8c7da7496d0e2b567a4bdc9341adcf01552
ms.contentlocale: it-it
ms.lasthandoff: 06/22/2017


---
# <a name="django-hello-world-web-app-on-a-linux-vm"></a>App Web Hello World Django in una macchina virtuale Linux
> [!div class="op_single_selector"]
> * [Windows](../windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [Mac/Linux](../windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

<br>

In questa esercitazione viene illustrato come ospitare un sito Web basato su Django in Linux nelle macchine virtuali di Azure. Nell'esercitazione si presuppone che l'utente non abbia mai usato Azure. Al termine dell'esercitazione, si disporrà di un'applicazione basata su Django in esecuzione nel cloud.

È possibile passare agli argomenti seguenti:

* Configurare una macchina virtuale di Azure per l'hosting di Django. Sebbene nell'esercitazione la procedura venga illustrata in **Linux**, è possibile eseguirla anche con una macchina virtuale Windows Server ospitata in Azure. 
* Creare una nuova applicazione Django in Linux.

Nell'esercitazione viene illustrato come compilare una semplice applicazione Web Hello World, ospitata in una macchina virtuale di Azure.

In questo screenshot viene visualizzata l'applicazione completata:

![Finestra del browser con la pagina Hello World visualizzata in Azure](./media/python-django-web-app/mac-linux-django-helloworld-browser.png)

[!INCLUDE [create-account-and-vms-note](../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-set-up-an-azure-virtual-machine-to-host-django"></a>Creare e configurare una macchina virtuale di Azure per l'hosting di Django

1. Per creare una macchina virtuale di Azure con la distribuzione di Ubuntu Server 14.04 LTS, vedere [Creare una macchina virtuale Linux con il portale di Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). È anche possibile scegliere l'autenticazione della password invece di usare una chiave pubblica SSH.
2. Per modificare il gruppo di sicurezza di rete per consentire il traffico HTTP in ingresso alla porta 80, vedere [Creare gruppi di sicurezza di rete mediante il portale di Azure](../../virtual-network/virtual-networks-create-nsg-arm-pportal.md).
3. (Facoltativo) La nuova macchina virtuale non dispone di un nome di dominio completo (FQDN) per impostazione predefinita.  Per creare una macchina virtuale con un nome di dominio completo, vedere [Creare un nome di dominio completo nel portale di Azure per una macchina virtuale Windows](../windows/portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Questo passaggio non è necessario per completare questa esercitazione.

## <a id="setup"> </a>Configurare l'ambiente di sviluppo
> [!NOTE]
> Se è necessario installare Python o si desidera usare le librerie client, vedere la [Guida all'installazione di Python](../../python-how-to-install.md).

La macchina virtuale Ubuntu Linux include già Python 2.7, ma non Apache o Django. Per connettersi alla macchina virtuale e installare Apache e Django, seguire questa procedura:

1. Aprire una nuova finestra del terminale.
2. Per connettersi alla macchina virtuale di Azure, immettere il comando seguente. Se non è stato creato un nome di dominio completo, è possibile connettersi usando l'indirizzo IP pubblico visualizzato nel riepilogo della macchina virtuale nel portale di Azure.
   
       $ ssh yourusername@yourVmUrl
3. Per installare Django, immettere i comandi seguenti:
   
       $ sudo apt-get install python-setuptools python-pip
       $ sudo pip install django
4. Per installare Apache con mod-wsgi, immettere il comando seguente:
   
       $ sudo apt-get install apache2 libapache2-mod-wsgi

## <a name="create-a-new-django-app"></a>Creare una nuova app Django
1. Per usare SSH per accedere alla macchina virtuale, aprire la finestra del terminale usata nella sezione precedente.
2. Per creare un nuovo progetto Django, immettere i comandi seguenti:
   
       $ cd /var/www
       $ sudo django-admin.py startproject helloworld
   
   Lo script `django-admin.py` genera una struttura di base per i siti Web basati su Django:
   
   * `helloworld/manage.py` consente di avviare e arrestare l'hosting del sito Web basato su Django.
   * `helloworld/helloworld/settings.py` contiene le impostazioni di Django per l'applicazione.
   * `helloworld/helloworld/urls.py` contiene il codice di mapping tra ogni URL e la relativa visualizzazione.
3. Nella directory /var/www/helloworld/helloworld creare un nuovo file denominato views.py. Questo file conterrà la visualizzazione del rendering della pagina "hello world". Nell'editor del codice immettere i comandi seguenti:
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. Sostituire il contenuto del file urls.py con i comandi seguenti:
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="set-up-apache"></a>Configurare Apache
1. Creare un file di configurazione dell'host virtuale Apache nella cartella /etc/apache2/sites-available/helloworld.conf. Impostare il contenuto sui valori seguenti. Sostituire *yourVmName* con il nome effettivo del computer in uso (ad esempio, *pyubuntu*).
   
       <VirtualHost *:80>
       ServerName yourVmName
       </VirtualHost>
       WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
       WSGIPythonPath /var/www/helloworld
2. Per attivare il sito, usare il comando seguente:
   
       $ sudo a2ensite helloworld
3. Per riavviare Apache, usare il comando seguente:
   
       $ sudo service apache2 reload
4. Caricare la pagina Web nel browser:
   
   ![Finestra del browser con la pagina hello world visualizzata in Azure](./media/python-django-web-app/mac-linux-django-helloworld-browser.png)

## <a name="shut-down-your-azure-virtual-machine"></a>Arrestare la macchina virtuale di Azure
Al termine dell'esercitazione è consigliabile arrestare o rimuovere la macchina virtuale di Azure creata per l'esercitazione. Ciò consente di liberare risorse per altre esercitazioni ed evitare di incorrere in costi di utilizzo di Azure.


