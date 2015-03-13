<properties 
	pageTitle="IPython Notebook - Esercitazione in Azure" 
	description="Esercitazione che mostra come distribuire IPython Notebook in Azure usando macchine virtuali (VM) Linux o Windows." 
	services="virtual-machines" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="huvalo"/>





# IPython Notebook in Azure

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Il <a href="http://ipython.org">progetto IPython</a> rende disponibile una raccolta di strumenti per il calcolo scientifico che includono potenti shell interattive, librerie parallele a elevate prestazioni e facili da usare e un ambiente basato su Web denominato IPython Notebook. Si tratta di un ambiente di lavoro per l'elaborazione interattiva che combina l'esecuzione di codice con la creazione di un documento di calcolo in tempo reale. I file dei blocchi appunti possono contenere testo arbitrario, formule matematiche, codice di input, risultati, grafica, video e altri tipi di contenuti multimediali visualizzabili sui moderni Web browser.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="label">video di esercitazione</a> <a style="background-image: url('/media/devcenter/python/ipy-youtube2.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="dev-onpage-video"><span class="icon">Riproduci video</span></a> <span class="time">05:22</span></div>
</div>

IPython Notebook rappresenta la scelta ideale sia per gli utenti poco esperti di Python che desiderano apprenderne il funzionamento in un ambiente interattivo e divertente sia per quelli che si occupano di informatica avanzata in campo tecnico e parallelo. Nella schermata seguente, per un'illustrazione delle funzionalità, viene mostrato l'utilizzo di IPython Notebook in combinazione con i pacchetti SciPy e matplotlib per l'analisi della struttura di una registrazione audio:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png)

Questo documento mostra come distribuire IPython Notebook in Microsoft Azure con macchine virtuali Linux o Windows.  Utilizzando IPython Notebook in Azure, è possibile fornire un'interfaccia accessibile tramite Web a risorse di calcolo scalabili, con la potenza di Python e delle numerose librerie correlate.  Poiché l'installazione viene eseguita interamente nel cloud, gli utenti possono accedere a queste risorse senza la necessità di una configurazione locale, ma con un semplice Web browser moderno.

[AZURE.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Creazione e configurazione di una macchina virtuale in Azure

Il primo passaggio consiste nel creare una macchina virtuale eseguita in Azure.
La VM è un sistema operativo completo nel cloud e verrà utilizzata per l'esecuzione di IPython Notebook. Azure consente di eseguire macchine virtuali sia Linux che Windows, e in questo articolo verrà descritta la configurazione di IPython in entrambi i tipi.

### Macchina virtuale Linux

Attenersi alle istruzioni riportate [qui][portal-vm-linux] per creare una macchina virtuale della distribuzione *OpenSUSE* o  *Ubuntu*. In questa esercitazione si usano OpenSUSE 13.1 e Ubuntu Server 14.04 LTS. Si presuppone che il nome utente predefinito sia  *azureuser*.

### Macchina virtuale Windows

Attenersi alle istruzioni riportate [qui][portal-vm-windows] per creare una macchina virtuale della distribuzione  *Windows Server 2012 R2 Datacenter*. In questa esercitazione si presuppone che il nome utente sia  *azureuser*.

## Creazione di un endpoint per IPython Notebook

Questo passaggio si applica alla macchina virtuale sia Linux che Windows. In seguito, IPython verrà configurato per eseguire il proprio server notebook sulla porta 9999. Per rendere questa porta pubblicamente disponibile, è necessario creare un endpoint nel portale di gestione di Azure. Questo endpoint apre una porta nel firewall di Azure e mappa la porta pubblica (HTTPS, 443) alla porta privata sulla VM (9999).

Per creare un endpoint, accedere al dashboard della macchina virtuale, fare clic su "Endpoint", quindi su "Aggiungi endpoint" e creare un nuovo endpoint, in questo esempio denominato  `ipython_nb`. Selezionare TCP come protocollo, 443 come porta pubblica e 9999 come porta privata:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-005.png)

Dopo questo passaggio, la scheda "Endpoint" del dashboard avrà l'aspetto seguente:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-006.png)

## Installazione del software necessario nella macchina virtuale

Per eseguire IPython Notebook nella VM, è necessario installare prima IPython e le relative dipendenze.

### Linux (OpenSUSE)

Per installare IPython e le relative dipendenze, eseguire SSH nella VM Linux e completare la seguente procedura.

Installare [NumPy][numpy], [Matplotlib][matplotlib], [Tornado][tornado] e altre dipendenze di IPython eseguendo i comandi:

    sudo zypper install python-matplotlib
    sudo zypper install python-tornado
    sudo zypper install python-jinja2
    sudo zypper install ipython

### Linux (Ubuntu)

Per installare IPython e le relative dipendenze, eseguire SSH nella VM Linux e completare la seguente procedura.

Installare [NumPy][numpy], [Matplotlib][matplotlib], [Tornado][tornado] e altre dipendenze di IPython eseguendo i comandi:

    sudo apt-get install python-matplotlib
    sudo apt-get install python-tornado
    sudo apt-get install ipython
    sudo apt-get install ipython-notebook

### Windows

Per installare IPython e le relative dipendenze nella macchina virtuale Windows, usare Desktop remoto per connettersi alla macchina virtuale. Quindi completare la seguente procedura, usando Windows PowerShell per eseguire tutte le azioni dalla riga di comando.

**Nota**: per scaricare contenuti tramite Internet Explorer, è necessario modificare alcune impostazioni di sicurezza.  In **Gestione server** fare clic su **Server locale**, quindi su **Configurazione sicurezza avanzata IE** e disattivare questa opzione per gli amministratori.  È possibile abilitarla di nuovo dopo aver installato IPython.

1.  Installare Python 2.7.8 (32 bit) da [python.org](http://www.python.org/download). 
    Sarà inoltre necessario aggiungere  `C:\Python27` e `C:\Python27\Scripts` alla variabile di ambiente `PATH`
    .

1.  Installare pip e setuptools scaricando il file **get-pip.py**
    da [https://pip.pypa.io/en/latest/installing.html](https://pip.pypa.io/en/latest/installing.html), quindi eseguire il
    comando:

        python get-pip.py

1.  Installare [Tornado][tornado], [PyZMQ][pyzmq] e altre dipendenze di IPython eseguendo i comandi:

        easy_install tornado
        easy_install pyzmq
        easy_install jinja2
        easy_install six
        easy_install python-dateutil
        easy_install pyparsing

1.  Scaricare e installare [NumPy][numpy] usando il
   file di installazione binario  `.exe` disponibile sul sito Web.  Al momento della stesura di questo articolo, la versione più recente è **numpy-1.9.0-win32-superpack-python2.7.exe**.

1.  Scaricare e installare [Matplotlib][matplotlib] usando il
   file di installazione binario  `.exe` disponibile sul sito Web.  Al momento della stesura di questo articolo, la versione più recente è **matplotlib-1.4.0.win32-py2.7.exe**.

1.  Scaricare e installare OpenSSL. Le versioni di OpenSSL per Windows sono disponibili all'indirizzo [http://slproweb.com/products/Win32OpenSSL.html](http://slproweb.com/products/Win32OpenSSL.html).

	* Se si installa una versione **Light**, è necessario installare anche **Visual C++ 2008 Redistributable** (disponibile nella stessa pagina).

	* È necessario aggiungere  `C:\OpenSSL-Win32\bin` alla variabile di ambiente  `PATH`.

	> [AZURE.NOTE] Quando si installa OpenSSL, usare la versione 1.0.1g o successiva, in quanto include una correzione per la vulnerabilità di sicurezza Heartbleed.

1.  Installare IPython usando il comando:

        easy_install ipython

1.  Aprire una porta in Windows Firewall.  In Windows Server 2012 il firewall blocca le connessioni in ingresso per impostazione predefinita.  Per aprire la porta 9999, eseguire la seguente procedura:

    - Avviare **Windows Firewall con protezione avanzata** dalla schermata Start.

    - Fare clic su **Regole connessioni in entrata** nel pannello sinistro.

	- Fare clic su **Nuova regola** nel riquadro Azioni.

	- Nella Creazione guidata nuova regola connessioni in entrata selezionare **Porta**.

	- Nella schermata successiva selezionare **TCP** e immettere **9999** in **Porte locali specifiche**.

	- Accettare le impostazioni predefinite, assegnare un nome alla regola e fare clic su Fine.

### Configurare IPython Notebook

A questo punto è necessario configurare IPython Notebook. Il primo passaggio consiste nel creare un
profilo di configurazione di IPython personalizzato per incapsulare le informazioni di configurazione:

    ipython profile create nbserver

Passare alla directory del profilo tramite `cd` per creare il certificato SSL e modificare il file di configurazione del profilo.

In Linux (OpenSUSE):

    cd ~/.config/ipython/profile_nbserver/

In Linux (Ubuntu):

    cd ~/.ipython/profile_nbserver/

In Windows:

    cd \users\azureuser\.ipython\profile_nbserver

In entrambe le piattaforme eseguire il comando seguente per creare il certificato SSL:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Si noti che, poiché verrà cercato un certificato SSL autofirmato, quando ci si connette al blocco appunti nel browser verrà visualizzato un avviso di sicurezza.  Per l'utilizzo in produzione a lungo termine, è consigliabile utilizzare un certificato con una firma appropriata associato all'organizzazione.  Poiché la gestione dei certificati esula dall'ambito di questa dimostrazione, per il momento verrà utilizzato un certificato autofirmato.

Oltre a utilizzare un certificato, è anche necessario specificare una password per proteggere il blocco appunti dall'utilizzo non autorizzato.  Per motivi di sicurezza, nel file di configurazione di IPython vengono utilizzate password crittografate, pertanto è necessario innanzitutto crittografare la password.  In IPython è disponibile un'utilità per questo scopo. Al prompt dei comandi eseguire:

    python -c "import IPython;print IPython.lib.passwd()"

Verrà richiesto di specificare e confermare una password, quindi la password verrà stampata come segue:

    Enter password: 
    Verify password: 
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)
    
Successivamente, verrà modificato il file di configurazione del profilo, ovvero il
file `ipython_notebook_config.py` nella directory del profilo in esecuzione. Si noti che questo file potrebbe non essere presente: in tal caso, crearlo.  Questo file contiene alcuni campi, tutti impostati come commento per impostazione predefinita.  È possibile aprire questo file con qualsiasi editor di testo, assicurandosi che includa almeno il contenuto seguente:

    c = get_config()
    
    # This starts plotting support always with matplotlib
    c.IPKernelApp.pylab = 'inline'
    
    # You must give the path to the certificate file.
    
    # If using a Linux VM (OpenSUSE):
    c.NotebookApp.certfile = u'/home/azureuser/.config/ipython/profile_nbserver/mycert.pem'

    # If using a Linux VM (Ubuntu):
    c.NotebookApp.certfile = u'/home/azureuser/.ipython/profile_nbserver/mycert.pem'
    
    # And if using a Windows VM:
    c.NotebookApp.certfile = r'C:\Users\azureuser\.ipython\profile_nbserver\mycert.pem'
    
    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '
    
    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port
    
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### Eseguire IPython Notebook

A questo punto è possibile avviare IPython Notebook. A tale scopo, passare alla directory in cui archiviare i blocchi appunti e avviare il server IPython Notebook:

    ipython notebook --profile=nbserver

A questo punto dovrebbe essere possibile accedere a IPython Notebook all'indirizzo
`https://[Your Chosen Name Here].cloudapp.net`.

La prima volta che si accede al blocco appunti, nella pagina di accesso viene richiesto di immettere la password:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-001.png)

Dopo l'accesso, verrà visualizzata la pagina "IPython Notebook Dashboard", ossia il centro di controllo per tutte le operazioni sui blocchi appunti.  In questa pagina è possibile creare nuovi blocchi appunti, aprire quelli esistenti e così via:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-002.png)

Se si fa clic sul pulsante "New Notebook", verrà visualizzata la pagina di apertura seguente:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-003.png)

L'area contrassegnata con un prompt dei comandi  `In []:`  è l'area di input e qui è possibile
immettere qualsiasi codice Python valido che verrà eseguito premendo  `Shift-Enter` oppure facendo clic sull'icona "Play", ovvero il triangolo che punta a destra sulla barra degli strumenti.

Poiché il blocco appunti è stato configurato per avviare automaticamente il supporto di NumPy e matplotlib, è anche possibile produrre cifre, ad esempio:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-004.png)

## Un potente paradigma: documenti di calcolo in tempo reale con contenuti multimediali

Il blocco appunti dovrebbe risultare familiare a chiunque abbia utilizzato Python e un elaboratore di testo, perché è in qualche modo una combinazione di entrambi: è possibile eseguire blocchi di codice Python, ma anche mantenere note e altro testo modificando lo stile di una cella da "Code" a "Markdown" tramite il menu a discesa nella barra degli strumenti:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-005.png)


Ma IPython Notebook è molto di più di un elaboratore di testo, in quanto consente di combinare calcolo e contenuti multimediali, ossia testo, grafica, video e qualsiasi altro contenuto visualizzabile in un moderno Web browser. È ad esempio possibile combinare video illustrativi con calcoli per scopi didattici:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-006.png)

 In alternativa è possibile incorporare siti Web esterni che rimangono attivi e possono essere usati all'interno di un file del blocco appunti:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-007.png)

Inoltre, con la potenza di molte eccellenti librerie di Python per il calcolo scientifico e tecnico, è possibile eseguire calcoli con la stessa facilità di un'analisi di rete complessa, tutto nello stesso ambiente:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-008.png)

Questo paradigma di combinare la potenza del moderno Web con il calcolo in tempo reale offre numerose possibilità ed è particolarmente indicato per il cloud. Il blocco appunti può essere utilizzato:

* Come blocco appunti computazionale per registrare il lavoro esplorativo su un problema.

* Per condividere i risultati con i colleghi, sotto forma di calcolo  'live'o in
  formati stampabili (HTML, PDF).

* Per distribuire e presentare materiali di formazione in tempo reale che implicano calcoli,
  in modo che gli studenti possano sperimentare immediatamente con codice reale, modificarlo ed
  eseguirlo nuovamente in modo interattivo.

* Per fornire "documenti eseguibili" che presentano i risultati della ricerca in modo
  che possano essere immediatamente riprodotti, convalidati e ampliati da altri.

* Come piattaforma per l'elaborazione collaborativa: più utenti possono accedere allo
  stesso server notebook per condividere una sessione di calcolo attiva.

* E altro ancora...

Nell'archivio di codice sorgente di IPython è disponibile un'intera directory con [esempi di blocchi appunti](https://github.com/ipython/ipython/tree/master/examples/notebooks) che è possibile scaricare e provare nella propria macchina virtuale di Azure IPython.
È sufficiente scaricare i file  `.ipynb` dal sito e caricarli nel dashboard della macchina virtuale di Azure dei blocchi appunti oppure scaricarli direttamente nella macchina virtuale.

## Conclusioni

IPython Notebook offre un'eccellente interfaccia per l'accesso interattivo alla potenza dell'ecosistema Python in Azure.  Rende possibile un'ampia gamma di casi di utilizzo, tra cui la semplice esplorazione di Python per apprenderne le funzionalità, l'analisi e la visualizzazione di dati, la simulazione e l'elaborazione parallela. I documenti risultanti contengono un record completo dei calcoli eseguiti e possono essere condivisi con altri utenti di IPython.  È possibile utilizzare IPython Notebook come applicazione locale, ma si tratta di una soluzione particolarmente indicata per le distribuzioni cloud in Azure.

Le funzionalità di base di IPython sono anche disponibili all'interno di Visual Studio tramite 
[Python Tools for Visual Studio](http://pytools.codeplex.com) (PTVS). PTVS è un plug-in open source gratuito reso disponibile da Microsoft che trasforma Visual Studio in un ambiente di sviluppo Python avanzato, con l'integrazione di un editor avanzato con IntelliSense, debug, profili ed elaborazione parallela.



[tornado]:      http://www.tornadoweb.org/          "Tornado"
[PyZMQ]:        https://github.com/zeromq/pyzmq     "PyZMQ"
[NumPy]:        http://www.numpy.org/               "NumPy"
[Matplotlib]:   http://matplotlib.sourceforge.net/  "Matplotlib"

[portal-vm-windows]: /it-it/manage/windows/tutorials/virtual-machine-from-gallery/
[portal-vm-linux]: /it-it/manage/linux/tutorials/virtual-machine-from-gallery/


<!--HONumber=42-->
