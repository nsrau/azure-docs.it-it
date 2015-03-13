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
	ms.date="02/05/2015" 
	ms.author="huvalo"/>




# Installazione di Python e dell'SDK
Python è piuttosto semplice da installare in Windows e viene fornito preinstallato in Mac e Linux.  In questa guida vengono illustrate le procedure per installare il programma e per predisporre il computer per l'uso con Azure.  Verranno fornite le informazioni seguenti:

* Descrizione di Python Azure SDK
* Tipo e versione di Python da usare
* Installazione in Windows
* Installazione in Mac e Linux

## Descrizione di Python Azure SDK

Azure SDK per Python include componenti che consentono di sviluppare, distribuire e gestire applicazioni Python per Azure. In particolare, Azure SDK per Python include i componenti seguenti:

* **Librerie client Python per Azure**. Queste librerie di classi offrono un'interfaccia per accedere alle funzionalità di Azure, ad esempio i servizi di gestione dati e i servizi cloud.  
* **Emulatori di Azure (solo Windows)**. Gli emulatori di calcolo e archiviazione sono emulatori locali di servizi cloud e di gestione dati che consentono di testare un'applicazione in locale. Gli emulatori di Azure funzionano solo su Windows.


I principali scenari per questa versione sono i seguenti:

* **Windows**: servizio cloud, sito Web, ad esempio un sito Django che usa ruoli Web
* **Mac/Linux**: IaaS, che consente di eseguire qualsiasi applicazione in una VM e di usare i servizi di Azure tramite Python

## Tipo e versione di Python da usare

Sono disponibili diverse versioni di interpreti Python, ad esempio:

* CPython: l'interprete Python standard e più comunemente usato
* IronPython: interprete Python eseguito in .NET/CLR
* JPython: interprete Python eseguito in .NET/CLR

Per gli scopi di questa versione, è stato testato e supportato solo **CPython**.  È consigliabile usare la versione 2.7 o 3.4.

## Dove è possibile reperire Python

È possibile ottenere CPython in diversi modi:

* Direttamente dal sito Web www.python.org
* Da un distributore affidabile come www.enthought.com, www.ActiveState.com o www.continuum.io
* Compilandolo da codice sorgente.

A meno che non vi siano esigenze specifiche, è consigliabile scegliere le prime due opzioni, come descritto di seguito.

## Installazione in Windows

Per Windows è possibile usare l'Installazione guidata piattaforma Web per semplificare l'installazione, che include CPython da www.python.org.

* [Microsoft Azure SDK per Python 2.7][]
* [Microsoft Azure SDK per Python 3.4][]

**Nota:** In Windows Server per scaricare il programma di installazione WebPI può essere necessario configurare le impostazioni di sicurezza avanzate di Internet Explorer (Start/Strumenti di amministrazione/Server Manager/Server locale, quindi fare clic su **Configura sicurezza avanzata di Internet Explorer** e disattivare l'opzione).


### Python 2.7

Il programma di installazione WebPI fornisce tutti gli strumenti necessari per sviluppare app Azure Python.

![how-to-install-python-webpi-27-1](./media/python-how-to-install/how-to-install-python-webpi-27-1.png)

A termine, dovrebbe essere visualizzata la schermata seguente, in cui vengono confermate le opzioni scelte per l'installazione:

![how-to-install-python-webpi-27-2](./media/python-how-to-install/how-to-install-python-webpi-27-2.png)

Al termine dell'installazione, immettere python la prompt per verificare se la procedura è stata completata correttamente.  A seconda di come è stata eseguita l'installazione, può essere necessario impostare la variabile "path" in modo da individuare la versione corretta di Python:

![how-to-install-python-win-run-27](./media/python-how-to-install/how-to-install-python-win-run-27.png)

Dopo l'installazione, le librerie Python e client dovrebbero essere disponibili nel percorso predefinito:

		C:\Python27\Lib\site-packages\azure


### Python 3.4

Il programma di installazione WebPI fornisce tutti gli strumenti necessari per sviluppare app Azure Python.

![how-to-install-python-webpi-34-1](./media/python-how-to-install/how-to-install-python-webpi-34-1.png)
 
A termine, dovrebbe essere visualizzata la schermata seguente, in cui vengono confermate le opzioni scelte per l'installazione:

![how-to-install-python-webpi-34-2](./media/python-how-to-install/how-to-install-python-webpi-34-2.png)

Al termine dell'installazione, immettere python la prompt per verificare se la procedura è stata completata correttamente.  A seconda di come è stata eseguita l'installazione, può essere necessario impostare la variabile "path" in modo da individuare la versione corretta di Python:

![how-to-install-python-win-run-34](./media/python-how-to-install/how-to-install-python-win-run-34.png)

Dopo l'installazione, le librerie Python e client dovrebbero essere disponibili nel percorso predefinito:

		C:\Python34\Lib\site-packages\azure


### Altri pacchetti

Nonostante questa versione sia incentrata prevalentemente sulle app Web, è possibile esplorare la pagina [Python Package Index (PyPI)][] per una selezione completa di altri prodotti software.  Se si è scelto di installare una distribuzione, si disporrà già della maggior parte dei componenti interessanti per una varietà di scenari, dallo sviluppo Web all'informatica tecnica.


### Python Tools per Visual Studio

Python Tools per Visual Studio (PTVS) è un plug-in OSS/gratuito di Microsoft che trasforma VS in un ambiente IDE Python completo:

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

Anche se è facoltativo, l'uso di PTVS è consigliabile in quanto offre il supporto per la soluzione o il progetto Python e Django, oltre a funzionalità di debug, definizione dei profili, finestra interattiva, modifica di modelli e IntelliSense.

PTVS semplifica, inoltre, la distribuzione in Microsoft Azure e supporta la [distribuzione nei servizi cloud e nei siti Web][].

PTVS funziona con la versione di Visual Studio 2010, 2012 o 2013 attualmente installata.  Per altre informazioni e per i download, vedere [Python Tools per Visual Studio sul sito CodePlex][].  

## Disinstallazione in Windows

I prodotti WebPI Azure SDK per **Azure SDK per Python** non sono applicazioni in senso stretto, ma costituiscono in realtà una raccolta di prodotti distinti, ad esempio Python 2.7/3.4 a 32 bit, librerie client di Azure per Python e così via, raggruppati in bundle.  Di conseguenza, non è disponibile un tradizionale programma di disinstallazione autonomo, pertanto è necessario rimuovere singolarmente i programmi installati dal Pannello di controllo di Windows.  

Se si desidera reinstallare **Azure SDK per Python**, è sufficiente aprire un prompt dei comandi di PowerShell come amministratore ed eseguire il comando seguente:

	rm -force "HKLM:\SOFTWARE\Microsoft\Python Tools for Azure"

e quindi eseguire di nuovo WebPI.

## Installazione in Linux e MacOS

È molto probabile che Python sia già installato nel computer di sviluppo.  Per verificarlo, immettere il comando seguente:

![how-to-install-python-linux-run](./media/python-how-to-install/how-to-install-python-linux-run.png)

In questa macchina virtuale di Ubuntu Server 14.04 LTS in esecuzione su Azure è installato CPython 2.7.6. Se è necessario eseguire un aggiornamento, attenersi alle istruzioni sui pacchetti consigliati per il sistema operativo in uso.

Per installare le librerie client Python per Azure, usare **pip** per recuperarle da **PyPI**:

	curl https://bootstrap.pypa.io/get-pip.py | sudo python
	
Il comando precedente richiederà automaticamente la password radice. Immetterla e premere INVIO.  Quindi, immettere il comando seguente:
	
	sudo pip install azure

A questo punto dovrebbero essere visualizzate le librerie installate in **site-packages**.  In MacOS:

![how-to-install-python-mac-site](./media/python-how-to-install/how-to-install-python-mac-site.png)

Quando si sviluppa da Mac/Linux, sono supportati due scenari principali:

1. Utilizzo di Servizi di Azure mediante le librerie client per Python

2. Esecuzione dell'app in una macchina virtuale Linux

Il primo scenario consente di creare app Web avanzate che sfruttano le funzionalità PaaS di Azure, come l'archiviazione BLOB, le code e così via, tramite wrapper Pythonic per le API REST di Azure. Il funzionamento è identico in Windows, Mac e Linux.  Per alcuni esempi, vedere le esercitazioni e le guide procedurali.  È inoltre possibile usare queste librerie client in una macchina virtuale Linux.

Per lo scenario della macchina virtuale, avviare una VM Linux a scelta (Ubuntu, CentOS, Suse) ed eseguire o gestire i componenti desiderati.  È ad esempio possibile eseguire [IPython](http://ipython.org) REPL/Notebook nel computer Windows/Mac/Linux e puntare il browser a una macchina virtuale Linux o Max multi-processore che esegue il motore IPython in Azure. Per altre informazioni sull'installazione di IPython, vedere l'esercitazione corrispondente.

Per informazioni su come configurare una VM Linux, vedere la sezione relativa alla [gestione di Linux.](/it-it/manage/linux/)

## Risorse e software aggiuntivi:

* [Distribuzione Python di Enthought][]
* [Distribuzione Python di ActiveState][]
* [Serie di librerie scientifiche SciPy per Python][]
* [Serie di librerie numeriche NumPy per Python][]
* [Progetto Django: framework/CMS Web avanzato][]
* [IPython: REPL/Notebook avanzato per Python][]
* [IPython Notebook in Azure][]
* [Python Tools per Visual Studio su CodePlex][]
* [Virtualenv][]


[Distribuzione Python di Enthought]: http://www.enthought.com 
[Distribuzione Python di ActiveState]: http://www.activestate.com
[Serie di librerie scientifiche SciPy per Python]: http://www.scipy.org
[Serie di librerie numeriche NumPy per Python]: http://www.numpy.org
[Progetto Django: framework/CMS Web avanzato]: http://www.djangoproject.com 
[IPython: REPL/Notebook avanzato per Python]: http://ipython.org
[IPython Notebook in Azure]: /it-it/documentation/articles/virtual-machines-python-ipython-notebook
[distribuzione nei servizi cloud e nei siti Web]: /it-it/documentation/articles/cloud-services-web-sites-python-django-app-with-ptvs/
[Python Tools per Visual Studio su CodePlex]: http://pytools.codeplex.com 
[Python Tools per Visual Studio sul sito CodePlex]: http://pytools.codeplex.com 
[Python Package Index (PyPI)]: http://pypi.python.org/pypi
[Virtualenv]: http://pypi.python.org/pypi/virtualenv 
[Microsoft Azure SDK per Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409
[Microsoft Azure SDK per Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990&clcid=0x409
[Configurazione di una macchina virtuale Linux tramite il portale di Azure]: ../../../shared/chunks/create-and-configure-opensuse-vm-in-portal
[Come usare gli strumenti da riga di comando di Azure per Mac e Linux]: ../../shared/chunks/crossplat-cmd-tools


<!--HONumber=46--> 
