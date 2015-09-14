<properties
	pageTitle="Installare Python e l'SDK - Azure"
	description="Informazioni su come installare Python e l'SDK da usare con Azure."
	services=""
	documentationCenter="python"
	authors="huguesv"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="08/31/2015"
	ms.author="huvalo"/>

# Installazione di Python e dell'SDK

Python è piuttosto semplice da installare in Windows e viene fornito preinstallato in Mac e Linux. In questa guida vengono illustrate le procedure per installare il programma e per predisporre il computer per l'utilizzo con Azure.

## Descrizione di Python Azure SDK

Azure SDK per Python include componenti che consentono di sviluppare, distribuire e gestire applicazioni Python per Azure. In particolare, Azure SDK per Python include i componenti seguenti:

* **Librerie client Python per Azure**. Queste librerie di classi forniscono un'interfaccia per accedere alle funzionalità di Azure, ad esempio bus di servizio e di archiviazione e la gestione risorse di Azure, ad esempio gli account di archiviazione, le macchine virtuali e così via.
* **Emulatori di Azure (solo Windows)**. Gli emulatori di calcolo e archiviazione sono emulatori locali di servizi cloud e di gestione dati che consentono di testare un'applicazione in locale. Gli emulatori di Azure funzionano solo su Windows.

## Tipo e versione di Python da usare

Sono disponibili diverse versioni di interpreti Python, ad esempio:

* CPython: l'interprete Python standard e più comunemente usato
* IronPython: interprete Python eseguito in .NET/CLR
* JPython: interprete Python eseguito in .NET/CLR

Solo **CPython** è testato e supportato per i servizi di Azure e Python Azure SDK, ad esempio siti Web e Servizi cloud. È consigliabile usare la versione 2.7 o 3.4.

## Dove è possibile reperire Python

È possibile ottenere CPython in diversi modi:

* Direttamente da [www.python.org][]
* Da un distributore attendibile, ad esempio [www.continuum.io][], [www.enthought.com][] o [www.activestate.com][]
* Compilandolo da codice sorgente.

A meno che non vi siano esigenze specifiche, è consigliabile scegliere le prime due opzioni, come descritto di seguito.

## Installazione in Windows, Linux e MacOS (solo per le librerie client)

Se si dispone già di Python installato, è possibile utilizzare pip per installare un bundle di tutte le librerie client nell'ambiente Python 3.3 + o Python 2.7.esistenti. Questo scaricherà i pacchetti dal [Python Package Index][] (PyPI).

Si noti che potrebbe essere necessario utilizzare il `sudo` comando ie su Linux e MacOS. `sudo pip install azure`.

	pip install azure

A partire dalla versione 1.0.0, le librerie sono state separate in più pacchetti. È ora possibile installare solo i pacchetti che è necessario o il bundle.

Per installare le librerie client di Runtime di archiviazione di Azure:

	pip install azure-storage

Per installare le librerie client di Runtime del servizio bus di Azure:

	pip install azure-servicebus

Per installare le librerie client del Gestore risorse di Azure:

	pip install azure-mgmt

Per installare le librerie client della Gestione risorse di Azure:

	pip install azure-servicemanagement-legacy


## Installazione su Windows (Python, emulatori di Azure e librerie client)

Per Windows è possibile usare l'Installazione guidata piattaforma Web per semplificare l'installazione. Includono CPython da [www.python.org][].

* [Microsoft Azure SDK per Python 2.7][]
* [Microsoft Azure SDK per Python 3.4][]

**Nota**: in Windows Server, per scaricare il programma di installazione WebPI può essere necessario configurare le impostazioni di sicurezza avanzate di Internet Explorer (Start/Strumenti di amministrazione/Gestione server/Server locale, quindi fare clic su **Configura sicurezza avanzata di Internet Explorer** e disattivare l'opzione).

### Python 2.7

Il programma di installazione WebPI fornisce tutti gli strumenti necessari per sviluppare app Azure Python.

![how-to-install-python-webpi-27-1](./media/python-how-to-install/how-to-install-python-webpi-27-1.png)

Al termine dell'installazione, immettere `python` al prompt per verificare se la procedura è stata completata correttamente. A seconda di come è stata eseguita l'installazione, può essere necessario impostare la variabile "path" in modo da individuare la versione corretta di Python:

![how-to-install-python-win-run-27](./media/python-how-to-install/how-to-install-python-win-run-27.png)

Dopo l'installazione, le librerie Python e client dovrebbero essere disponibili nel percorso predefinito:

		C:\Python27\Lib\site-packages\azure


### Python 3.4

Il programma di installazione WebPI fornisce tutti gli strumenti necessari per sviluppare app Azure Python.

![how-to-install-python-webpi-34-1](./media/python-how-to-install/how-to-install-python-webpi-34-1.png)

Al termine dell'installazione, immettere python la prompt per verificare se la procedura è stata completata correttamente. A seconda di come è stata eseguita l'installazione, può essere necessario impostare la variabile "path" in modo da individuare la versione corretta di Python:

![how-to-install-python-win-run-34](./media/python-how-to-install/how-to-install-python-win-run-34.png)

Dopo l'installazione, le librerie Python e client dovrebbero essere disponibili nel percorso predefinito:

		C:\Python34\Lib\site-packages\azure

### Disinstallazione in Windows

I prodotti WebPI Azure SDK per **Azure SDK per Python** non sono applicazioni in senso stretto, ma costituiscono in realtà una raccolta di prodotti distinti, ad esempio Python 2.7/3.4 a 32 bit, librerie client di Azure per Python e così via, raggruppati in bundle. Di conseguenza, non è disponibile un tradizionale programma di disinstallazione autonomo, pertanto è necessario rimuovere singolarmente i programmi installati dal Pannello di controllo di Windows.

Se si desidera reinstallare **Azure SDK per Python**, è sufficiente aprire un prompt dei comandi di PowerShell come amministratore ed eseguire il comando seguente:

	rm -force "HKLM:\SOFTWARE\Microsoft\Python Tools for Azure"

e quindi eseguire di nuovo WebPI.

## Altri pacchetti

[Python Package Index][] (PyPI) è una selezione completa di librerie di Python. Se si è scelto di installare una distribuzione, si disporrà già della maggior parte dei componenti interessanti per una varietà di scenari, dallo sviluppo Web all'informatica tecnica.


## Python Tools per Visual Studio

[Python Tools per Visual Studio][] (PTVS) è un plug-in OSS/gratuito di Microsoft che trasforma VS in un ambiente IDE Python completo:

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

Anche se è facoltativo, l'uso di PTVS è consigliabile in quanto offre il supporto per la soluzione o il progetto Python e Web, oltre a funzionalità di debug, definizione dei profili, finestra interattiva, modifica di modelli e IntelliSense.

PTVS semplifica, inoltre, la distribuzione in Microsoft Azure e supporta la distribuzione in [Servizi cloud][] e [Siti Web][].

PTVS funziona con la versione di Visual Studio 2013 o 2015 attualmente installata. Per la documentazione, il download e le discussioni, vedere [Python Tools per Visual Studio].

## Scenari di Python per Azure in Linux e MacOS

Per Linux o MacOS, quelli qui riportati sono gli scenari principali di Azure supportati:

1. Utilizzo di Servizi di Azure mediante le librerie client per Python

2. Esecuzione dell'app in una macchina virtuale Linux

3. Sviluppo e pubblicazione nei siti Web di Azure tramite Git

Il primo scenario consente di creare app Web avanzate che sfruttano le funzionalità PaaS di Azure, come l'[archiviazione BLOB][], l'[archiviazione in code][], l'[archiviazione in tabelle][] e così via, tramite wrapper di Python per le API REST di Azure. Il funzionamento è identico in Windows, Mac e Linux. È inoltre possibile utilizzare queste librerie client dal computer di sviluppo locale oppure da una macchina virtuale Linux in esecuzione su Azure.

Per lo scenario della macchina virtuale, avviare una VM Linux a scelta (Ubuntu, CentOS, Suse) ed eseguire o gestire i componenti desiderati. È ad esempio possibile eseguire [IPython][] REPL/Notebook nel computer Windows/Mac/Linux e puntare il browser a una macchina virtuale Linux o Max multi-processore che esegue il motore IPython in Azure. Per ulteriori informazioni, vedere l'esercitazione [IPython Notebook in Azure][].

Per informazioni sulla procedura di configurazione di una macchina virtuale di Linux, vedere l'esercitazione [Creare una macchina virtuale che esegue Linux][].

Utilizzando la distribuzione GIT, è possibile sviluppare un'applicazione Web di Python e pubblicarla in un sito Web di Azure da qualsiasi sistema operativo. Quando si effettua il push del repository in Azure, viene creato automaticamente un ambiente virtuale e tramite pip vengono installati i pacchetti necessari.

Per ulteriori informazioni su sviluppo e pubblicazione di siti Web di Azure, vedere le esercitazioni [Creazione di siti Web con Django][], [Creazione di siti Web con Bottle][] e [Creazione di siti Web con Flask][]. Per informazioni generali sull'utilizzo di qualsiasi framework compatibile con WSGI, vedere [Configurazione di Python con Siti Web di Azure][].


## Risorse e software aggiuntivi:

* [Distribuzione Python per l'analisi di una sequenza di valori][]
* [Distribuzione Python di Enthought][]
* [Distribuzione Python di ActiveState][]
* [Serie di librerie scientifiche SciPy per Python][]
* [Serie di librerie numeriche NumPy per Python][]
* [Progetto Django: framework/CMS Web avanzato][]
* [IPython: REPL/Notebook avanzato per Python][]
* [IPython Notebook in Azure][]
* [Python Tools per Visual Studio su GitHub][]


[Distribuzione Python per l'analisi di una sequenza di valori]: http://continuum.io
[Distribuzione Python di Enthought]: http://www.enthought.com
[Distribuzione Python di ActiveState]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[Serie di librerie scientifiche SciPy per Python]: http://www.scipy.org
[Serie di librerie numeriche NumPy per Python]: http://www.numpy.org
[Progetto Django: framework/CMS Web avanzato]: http://www.djangoproject.com
[IPython: REPL/Notebook avanzato per Python]: http://ipython.org
[IPython]: http://ipython.org
[IPython Notebook in Azure]: virtual-machines-python-ipython-notebook.md
[Servizi cloud]: cloud-services-python-ptvs.md
[Siti Web]: web-sites-python-ptvs-django-mysql.md
[Python Tools per Visual Studio]: http://aka.ms/ptvs
[Python Tools per Visual Studio su GitHub]: https://github.com/microsoft/ptvs
[Python Package Index]: http://pypi.python.org/pypi
[Microsoft Azure SDK per Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK per Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[Creare una macchina virtuale che esegue Linux]: virtual-machines-linux-tutorial.md
[Creazione di siti Web con Django]: web-sites-python-create-deploy-django-app.md
[Creazione di siti Web con Bottle]: web-sites-python-create-deploy-bottle-app.md
[Creazione di siti Web con Flask]: web-sites-python-create-deploy-flask-app.md
[Configurazione di Python con Siti Web di Azure]: web-sites-python-configure.md
[archiviazione in tabelle]: storage-python-how-to-use-table-storage.md
[archiviazione in code]: storage-python-how-to-use-queue-storage.md
[archiviazione BLOB]: storage-python-how-to-use-blob-storage.md

<!---HONumber=September15_HO1-->