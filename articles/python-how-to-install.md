---
title: Installare Python e l&quot;SDK - Azure
description: Informazioni su come installare Python e l&quot;SDK da usare con Azure.
services: 
documentationcenter: python
author: lmazuel
manager: wpickett
editor: 
ms.assetid: f36294be-daeb-4caf-9129-fce18130f552
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 09/06/2016
ms.author: lmazuel
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: be8fed81757d3a7467707e4e0fdf1fd5734e0f07
ms.lasthandoff: 04/03/2017


---
# <a name="installing-python-and-the-sdk"></a>Installazione di Python e dell'SDK
Python è semplice da installare in Windows e viene fornito preinstallato in Mac, Linux e [Bash per Windows](https://msdn.microsoft.com/commandline/wsl/about). In questa guida vengono illustrate le procedure per installare il programma e per predisporre il computer per l'utilizzo con Azure.

## <a name="whats-in-the-python-azure-sdk"></a>Descrizione di Python Azure SDK
Azure SDK per Python include componenti che consentono di sviluppare, distribuire e gestire applicazioni Python per Azure. In particolare, Azure SDK per Python include i componenti seguenti:

* **Librerie di gestione**. Queste librerie di classe offre un'interfaccia di gestione delle risorse di Azure, come ad esempio gli account di archiviazione e le macchine virtuali.
* **Librerie di runtime**. Queste librerie di classe offrono un'interfaccia per accedere alle funzionalità di Azure, ad esempio l'archiviazione e il bus di servizio.

## <a name="which-python-and-which-version-to-use"></a>Tipo e versione di Python da utilizzare
Sono disponibili diverse versioni di interpreti Python, ad esempio:

* CPython: l'interprete Python standard e più comunemente usato
* PyPy - implementazione alternativa rapida e conforme a CPython
* IronPython: interprete Python eseguito in .NET/CLR
* Jython: interprete Python eseguito sulla macchina virtuale Java

**CPython** le versioni v2.7 o v3.3+ e PyPy 5.4.0 vengono testate e supportate per Python Azure SDK.

## <a name="where-to-get-python"></a>Dove è possibile reperire Python
È possibile ottenere CPython in diversi modi:

* Direttamente da [www.python.org][www.python.org]
* Da un distributore attendibile, ad esempio [www.continuum.io][www.continuum.io], [www.enthought.com][www.enthought.com] o [www.activestate.com][www.activestate.com]
* Compilandolo da codice sorgente.

Salvo esigenze specifiche, si consiglia di scegliere le prime due opzioni.

## <a name="sdk-installation-on-windows-linux-and-macos-client-libraries-only"></a>Installazione dell'SDK in Windows, Linux e MacOS (solo per le librerie client)
Se si dispone già di Python installato, è possibile utilizzare pip per installare un bundle di tutte le librerie client nell'ambiente Python 3.3 + o Python 2.7.esistenti. I pacchetti verranno scaricati da [Python Package Index][Python Package Index] (PyPI).

Tale operazione potrebbe richiedere i diritti di amministratore:

* Per Linux e MacOS utilizzare il comando `sudo`: `sudo pip install azure-mgmt-compute`.
* Windows: aprire il prompt dei comandi PowerShell come amministratore

È possibile installare singolarmente ogni libreria per ciascun servizio di Azure:

```console
   $ pip install azure-batch          # Install the latest Batch runtime library
   $ pip install azure-mgmt-scheduler # Install the latest Storage management library
```

I pacchetti di anteprima possono essere installati mediante il flag `--pre` :

```console
   $ pip install --pre azure-mgmt-compute # will install only the latest Compute Management library
```

È inoltre possibile installare un set di librerie di Azure in una singola riga utilizzando il meta pacchetto `azure` . Poiché non tutti i pacchetti presenti in questo meta pacchetto sono già stati pubblicati come stabili, il meta pacchetto `azure` è ancora in anteprima.
Tuttavia, al momento i pacchetti principali possono essere considerati come stabili dal punto di vista della qualità e della completezza

* Il meta pacchetto lo sarà ufficialmente in sincronia con le altre lingue il prima possibile.
  Fino ad allora non si prevedono altre modifiche sostanziali.

Trattandosi di una versione in anteprima, è necessario utilizzare il flag `--pre` :

```console
   $ pip install --pre azure
```

o direttamente

```console
   $ pip install azure==2.0.0rc6
```

## <a name="getting-more-packages"></a>Altri pacchetti
[Python Package Index][Python Package Index] (PyPI) è una selezione completa di librerie Python.  Se si è scelto di installare una distribuzione, si disporrà già della maggior parte dei componenti interessanti per vari scenari, dallo sviluppo Web all'informatica tecnica.

## <a name="python-tools-for-visual-studio"></a>Python Tools per Visual Studio
[Python Tools per Visual Studio][Python Tools per Visual Studio] (PTVS) è un plug-in OSS/gratuito di Microsoft che trasforma VS in un ambiente IDE Python completo:

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

Anche se è facoltativo, l'uso di PTVS è consigliabile in quanto offre il supporto per la soluzione o il progetto Python e Web, oltre a funzionalità di debug, definizione dei profili, finestra interattiva, modifica di modelli e IntelliSense.

PTVS semplifica, inoltre, la distribuzione in Microsoft Azure e supporta la distribuzione in [Servizi cloud](cloud-services/cloud-services-python-ptvs.md) e [Siti Web](app-service-web/web-sites-python-ptvs-django-mysql.md).

PTVS funziona con la versione di Visual Studio 2013 o 2015 attualmente installata.  Per la documentazione, il download e le discussioni, vedere [Python Tools per Visual Studio].  

## <a name="python-azure-scenarios-for-linux-and-macos"></a>Scenari di Python per Azure in Linux e MacOS
Per Linux o MacOS, ecco gli scenari principali di Azure supportati:

1. Uso di Servizi di Azure mediante le librerie client per Python
2. Esecuzione dell'app in una macchina virtuale Linux
3. Sviluppo e pubblicazione nei siti Web di Azure tramite Git

Il primo scenario consente di creare App Web avanzate che sfruttano le funzionalità PaaS di Azure, come l'[archiviazione BLOB](virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), l'[archiviazione code](storage/storage-python-how-to-use-queue-storage.md), l'[archiviazione tabelle](storage/storage-python-how-to-use-table-storage.md) e così via, tramite wrapper di Python per le API REST di Azure. Il funzionamento è identico in Windows, Mac e Linux.  È inoltre possibile usare queste librerie client dal computer di sviluppo locale oppure da una macchina virtuale Linux in esecuzione su Azure.

Per lo scenario della macchina virtuale, avviare una VM Linux a scelta (Ubuntu, CentOS, Suse) ed eseguire o gestire i componenti desiderati.  È possibile ad esempio eseguire [IPython][IPython] REPL/Notebook nel computer Windows/Mac/Linux e puntare il browser a una VM multi-processore Linux o Max che esegue il motore IPython in Azure. Per altre informazioni, vedere l'esercitazione [IPython Notebook in Azure](virtual-machines/linux/jupyter-notebook.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Per informazioni sulla procedura di configurazione di una VM di Linux, vedere l'esercitazione [Creare una macchina virtuale che esegue Linux](virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Usando la distribuzione GIT, è possibile sviluppare un'applicazione Web di Python e pubblicarla in un sito Web di Azure da qualsiasi sistema operativo.  Quando si effettua il push del repository in Azure, viene creato automaticamente un ambiente virtuale e tramite pip vengono installati i pacchetti necessari.

Per altre informazioni su sviluppo e pubblicazione di siti Web di Azure, vedere le esercitazioni [Creazione di siti Web con Django](app-service-web/web-sites-python-create-deploy-django-app.md), [Creazione di siti Web con Bottle](app-service-web/web-sites-python-create-deploy-bottle-app.md) e [Creazione di siti Web con Flask](app-service-web/web-sites-python-create-deploy-flask-app.md). Per informazioni generali sull'uso di qualsiasi framework compatibile con WSGI, vedere [Configurazione di Python con Siti Web di Azure](app-service-web/web-sites-python-configure.md).

## <a name="additional-software-and-resources"></a>Risorse e software aggiuntivi:
* [Azure SDK per Python ReadTheDocs](http://azure-sdk-for-python.readthedocs.io/en/latest/)
* [Azure SDK per Python GitHub](https://github.com/Azure/azure-sdk-for-python)
* [Esempi ufficiali di Azure per Python](https://azure.microsoft.com/documentation/samples/?platform=python)
* [Distribuzione Python per l'analisi di una sequenza di valori][Continuum Analytics Python Distribution]
* [Distribuzione Python di Enthought][Enthought Python Distribution]
* [Distribuzione Python di ActiveState][ActiveState Python Distribution]
* [Serie di librerie scientifiche SciPy per Python][SciPy - A suite of Scientific Python libraries]
* [Serie di librerie numeriche NumPy per Python][NumPy - A numerics library for Python]
* [Progetto Django: framework/CMS Web avanzato][Django Project - A mature web framework/CMS]
* [IPython: REPL/Notebook avanzato per Python][IPython - an advanced REPL/Notebook for Python]
* [IPython Notebook in Azure](virtual-machines/linux/jupyter-notebook.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Python Tools per Visual Studio su GitHub][Python Tools for Visual Studio on GitHub]
* [Centro per sviluppatori Python](/develop/python/)

[Continuum Analytics Python Distribution]: http://continuum.io
[Enthought Python Distribution]: http://www.enthought.com
[ActiveState Python Distribution]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy - A suite of Scientific Python libraries]: http://www.scipy.org
[NumPy - A numerics library for Python]: http://www.numpy.org
[Django Project - A mature web framework/CMS]: http://www.djangoproject.com
[IPython - an advanced REPL/Notebook for Python]: http://ipython.org
[IPython]: http://ipython.org
[IPython Notebook on Azure]: virtual-machines-linux-jupyter-notebook.md
[Cloud Services]: cloud-services-python-ptvs.md
[Websites]: web-sites-python-ptvs-django-mysql.md
[Python Tools per Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio on GitHub]: https://github.com/microsoft/ptvs
[Python Package Index]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[Create a Virtual Machine Running Linux]: virtual-machines-linux-quick-create-cli.md
[Creating Websites with Django]: web-sites-python-create-deploy-django-app.md
[Creating Websites with Bottle]: web-sites-python-create-deploy-bottle-app.md
[Creating Websites with Flask]: web-sites-python-create-deploy-flask-app.md
[Configuring Python with Azure Websites]: web-sites-python-configure.md
[table storage]: storage-python-how-to-use-table-storage.md
[queue storage]: storage-python-how-to-use-queue-storage.md
[blob storage]: storage-python-how-to-use-blob-storage.md

