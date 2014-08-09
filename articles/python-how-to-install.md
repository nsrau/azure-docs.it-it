<properties linkid="develop-python-install-python" urlDisplayName="Install Python" pageTitle="Install Python and the SDK - Azure" metaKeywords="Azure Python SDK" description="Learn how to install Python and the SDK to use with Azure." metaCanonical="" services="" documentationCenter="Python" title="Installing Python and the SDK" authors="" solutions="" manager="" editor="" />

Installazione di Python e dell'SDK
==================================

Python è piuttosto semplice da installare in Windows e viene fornito preinstallato in Mac e Linux. In questa guida vengono illustrate le procedure per installare il programma e per predisporre il computer per l'utilizzo con Azure. Verranno fornite le informazioni seguenti:

-   Descrizione di Python Azure SDK
-   Tipo e versione di Python da utilizzare
-   Installazione in Windows
-   Installazione in Mac e Linux

Descrizione di Python Azure SDK
-------------------------------

Azure SDK per Python include componenti che consentono di sviluppare, distribuire e gestire applicazioni Python per Azure. In particolare, Azure SDK per Python include i componenti seguenti:

-   **Librerie client Python per Azure**. Queste librerie di classi offrono un'interfaccia per accedere alle funzionalità di Azure, ad esempio i servizi di gestione dati e i servizi cloud.
-   **Strumenti da riga di comando di Azure per Mac e Linux** Questo insieme di strumenti da riga di comando consente la distribuzione e la gestione di servizi di Azure, come Siti Web e Macchine virtuali di Azure. Gli strumenti funzionano su qualsiasi piattaforma, incluse le piattaforme Mac, Linux e Windows.
-   **PowerShell per Azure (solo Windows)**. Questo insieme di cmdlet di PowerShell consente la distribuzione e la gestione di servizi di Azure, come servizi cloud e macchine virtuali.
-   **Emulatori di Azure (solo Windows)**. Gli emulatori di calcolo e archiviazione sono emulatori locali di servizi cloud e di gestione dati che consentono di testare un'applicazione in locale. Gli emulatori di Azure funzionano solo su Windows.

I principali scenari per questa versione sono i seguenti:

-   **Windows**: servizio cloud, ad esempio un sito Django che utilizza ruoli Web
-   **Mac/Linux**: IaaS, che consente di eseguire qualsiasi applicazione in una VM e di utilizzare i servizi di Azure tramite Python

Tipo e versione di Python da utilizzare
---------------------------------------

Sono disponibili diverse versioni di interpreti Python, ad esempio:

-   CPython: l'interprete Python standard e più comunemente utilizzato
-   IronPython: interprete Python eseguito in .NET/CLR
-   JPython: interprete Python eseguito in .NET/CLR

Per gli scopi di questa versione, è stato testato e supportato solo **CPython**. È inoltre consigliabile utilizzare almeno la versione 2.7. **Nel prossimo futuro verrà aggiunto anche il supporto per** IronPython.

Dove è possibile reperire Python
--------------------------------

È possibile ottenere CPython in diversi modi:

-   Direttamente dal sito Web www.python.org
-   Da un distributore affidabile come www.enthought.com o www.ActiveState.com
-   Compilandolo da codice sorgente.

A meno che non vi siano esigenze specifiche, è consigliabile scegliere le prime due opzioni, come descritto di seguito.

Installazione in Windows
------------------------

Per Windows, è possibile utilizzare il [programma di installazione WebPI](http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409) reso disponibile dal principale centro per sviluppatori Python per semplificare l'installazione. Questo programma recupererà CPython da www.python.org.

**Nota:** in Windows Server per scaricare il programma di installazione WebPI può essere necessario configurare le impostazioni di sicurezza avanzate di Internet Explorer (Start/Strumenti di amministrazione/Server Manager, quindi fare clic su **Configura sicurezza avanzata di Internet Explorer** e disattivare l'opzione).

![how-to-install-python-webpi-1](./media/python-how-to-install/how-to-install-python-webpi-1.png)

Il programma di installazione WebPI fornisce tutto il necessario per le app Azure Python, oltre al supporto specifico per le app Django:

![how-to-install-Python-webpi-2](./media/python-how-to-install/how-to-install-python-webpi-2.png)

A termine, dovrebbe essere visualizzata la schermata seguente, in cui vengono confermate le opzioni scelte per l'installazione:

![how-to-install-python-webpi-3](./media/python-how-to-install/how-to-install-python-webpi-3.png)

Al termine dell'installazione, immettere python la prompt per verificare se la procedura è stata completata correttamente. A seconda di come è stata eseguita l'installazione, può essere necessario impostare la variabile "path" in modo da individuare la versione corretta di Python:

![how-to-install-python-win-run](./media/python-how-to-install/how-to-install-python-win-run.png)

Anche se questa versione è incentrata prevalentemente sulle app Web con Django, è consigliabile esplorare la pagina [Python Package Index (PyPI)](http://pypi.python.org/pypi) per una selezione completa di altri prodotti software. Se si è scelto di installare una distribuzione, si disporrà già della maggior parte dei componenti interessanti per una varietà di scenari, dallo sviluppo Web all'informatica tecnica.

Per individuare quali pacchetti Python sono installati in **site-packages**, immettere il comando seguente per trovare il percorso:

![how-to-install-python-win-site](./media/python-how-to-install/how-to-install-python-win-site.png)

Verrà visualizzato una lista dei pacchetti installati nel sistema.

Dopo l'installazione, Python, Django e le librerie client dovrebbero essere disponibili nel percorso predefinito:

     C:\Python27\Lib\site-packages\windowsazure
        C:\Python27\Lib\site-packages\django

### Python Tools per Visual Studio

Python Tools per Visual Studio è un plug-in OSS/gratuito di Microsoft che trasforma VS in un ambiente IDE Python completo:

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

Anche se è facoltativo, è consigliabile utilizzare Python Tools per Visual Studio in quanto offre il supporto per la soluzione o il progetto Python e Django, oltre a funzionalità di debug, profilatura, modifica di modelli e Intellisense, distribuzione nel cloud e così via. Questo componente aggiuntivo è compatibile con l'attuale installazione di VS2010. Se non si dispone di VS2010, con WebPI verranno installati gratuitamente la shell integrata e PTVS, che rendono disponibile in sostanza un ambiente IDE "VS Python Express" **completamente gratuito**. Per ulteriori informazioni, vedere [Python Tools per Visual Studio sul sito CodePlex](http://pytools.codeplex.com).

Nota: anche se il plug-in PTVS è di piccole dimensioni, la shell integrata comporta tempi di download più lunghi. La versione corrente della shell integrata, inoltre, non supporta la funzionalità per l'aggiunta di progetti di distribuzione di Azure.

Disinstallazione in Windows
---------------------------

Il prodotto WebPI **Azure SDK per Python di giugno 2012** non è un'applicazione in senso stretto, ma si tratta in realtà di una raccolta di prodotti distinti, ad esempio Python 2.7 a 32 bit, API client di Azure per Python, Django e così via, raggruppati in bundle. Di conseguenza, non è disponibile un tradizionale programma di disinstallazione autonomo, pertanto è necessario rimuovere singolarmente i programmi installati dal Pannello di controllo di Windows.

Se si desidera reinstallare **Azure SDK per Python**, è sufficiente aprire un prompt dei comandi di PowerShell come amministratore ed eseguire il comando seguente:

    rm -force "HKLM:\SOFTWARE\Microsoft\Windows Azure SDK for Python - June 2012"

e quindi eseguire di nuovo WebPI.

Installazione in Linux e MacOS
------------------------------

È molto probabile che Python sia già installato nel computer di sviluppo. Per verificarlo, immettere il comando seguente:

![how-to-install-python-linux-run](./media/python-how-to-install/how-to-install-python-linux-run.png)

Come si può notare, in questa macchina virtuale Suse di Azure è installato CPython 2.7.2, che è appropriato per l'esecuzione delle esercitazioni di Azure e degli esempi di Django. Se è necessario eseguire un aggiornamento, attenersi alle istruzioni sui pacchetti consigliati per il sistema operativo in uso. Si noti, tuttavia, che in generale è preferibile lasciare isolata la versione Python del sistema, da cui potrebbero dipendere altri componenti, e installare la versione più recente tramite [Virtualenv](http://pypi.python.org/pypi/virtualenv).

Per installare le librerie client Python per Azure, utilizzare **pip** per recuperarle da **PyPI**:

    curl https://raw.github.com/pypa/pip/master/contrib/get-pip.py | sudo python

Il comando precedente richiederà automaticamente la password radice. Immetterla e premere INVIO. Quindi, immettere il comando seguente:

    sudo /usr/local/bin/pip-2.7 install azure

A questo punto dovrebbero essere visualizzate le librerie installate in **site-packages**. In MacOS:

![site-packages in MacOS](./media/python-how-to-install/how-to-install-python-mac-site.png)

Quando si sviluppa da Mac/Linux, per questa versione sono supportati due scenari principali:

1.  Utilizzo di Servizi di Azure mediante le librerie client per Python

2.  Esecuzione dell'app in una macchina virtuale Linux

Il primo scenario consente di creare app Web avanzate che sfruttano le funzionalità PaaS di Azure, come l'archiviazione BLOB, le code e così via, tramite wrapper Pythonic per le API REST di Azure. Il funzionamento è identico in Windows, Mac e Linux. Per alcuni esempi, vedere le esercitazioni e le guide procedurali. È inoltre possibile utilizzare queste librerie client in una macchina virtuale Linux.

Per lo scenario della macchina virtuale, avviare una VM Linux a scelta (Ubuntu, CentOS, Suse) ed eseguire o gestire i componenti desiderati. È ad esempio possibile eseguire [IPython](http://ipython.org) REPL/Notebook nel computer Windows/Mac/Linux e puntare il browser a una macchina virtuale Linux o Max multi-processore che esegue il motore IPython in Azure. Per ulteriori informazioni sull'installazione di IPython, vedere l'esercitazione corrispondente.

Per informazioni su come configurare una VM Linux, vedere la sezione relativa alla [gestione di Linux.](/en-us/manage/linux/)

Risorse e software aggiuntivi:
------------------------------

-   [Distribuzione Python di Enthought](http://www.enthought.com)
-   [Distribuzione Python di ActiveState](http://www.activestate.com)
-   [Serie di librerie scientifiche SciPy per Python](http://www.scipy.org)
-   [Serie di librerie numeriche NumPy per Python](http://www.numpy.org)
-   [Progetto Django: framework/CMS Web avanzato](http://www.djangoproject.com)
-   [IPython: REPL/Notebook avanzato per Python](http://ipython.org)
-   [IPython Notebook in Azure](http://windowsazure.com/it-it/documentation/articles/virtual-machines-python-ipython-notebook)
-   [Python Tools per Visual Studio su CodePlex](http://pytools.codeplex.com)
-   [Virtualenv](http://pypi.python.org/pypi/virtualenv)

