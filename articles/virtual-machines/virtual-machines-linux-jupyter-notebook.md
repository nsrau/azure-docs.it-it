---
title: Creare un Jupyter/IPython Notebook | Microsoft Docs
description: Informazioni su come distribuire Jupyter/IPython Notebook in una macchina virtuale Linux o Windows creata con il modello di distribuzione del gestore delle risorse in Azure.
services: virtual-machines-linux
documentationcenter: python
author: crwilcox
manager: wpickett
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 519f36dd-865e-4c1d-abe7-b87037796aa7
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: python
ms.topic: article
ms.date: 11/10/2015
ms.author: crwilcox
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 652c4c51d67b8914885406e631e7233694a8a1d8
ms.openlocfilehash: ad53cb9bb58064aeed49cb05fdcb936e408346d2
ms.lasthandoff: 03/01/2017

---
# <a name="creating-an-azure-vm-installing-jupyter-and-running-a-jupyter-notebook-on-azure"></a>Creazione di una macchina virtuale di Azure, installazione di Jupyter ed esecuzione di Jupyter Notebook in Azure
Il [progetto Jupyter](http://jupyter.org), noto in precedenza come [progetto IPython](http://ipython.org), fornisce un insieme di strumenti per il calcolo scientifico usando potenti shell interattive che consentono di combinare l'esecuzione di codice con la creazione di un documento di calcolo in tempo reale. I file dei blocchi appunti possono contenere testo arbitrario, formule matematiche, codice di input, risultati, grafica, video e altri tipi di contenuti multimediali visualizzabili sui moderni Web browser. Jupyter Notebook rappresenta la scelta ideale sia per gli utenti poco esperti di Python che desiderano apprenderne il funzionamento in un ambiente interattivo e divertente sia per quelli che si occupano di informatica avanzata in campo tecnico e parallelo.

![Schermata](./media/virtual-machines-linux-jupyter-notebook/ipy-notebook-spectral.png) Uso dei pacchetti SciPy e Matplotlib per l'analisi della struttura di una registrazione audio.

## <a name="jupyter-two-ways-azure-notebooks-or-custom-deployment"></a>Due modi di Jupyter: Azure Notebook o distribuzione personalizzata
Azure fornisce un servizio che è possibile utilizzare per [iniziare rapidamente a utilizzare Jupyter ](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).  Usando Azure Notebook Service, è possibile ottenere facilmente accesso all'interfaccia accessibile tramite Web di Jupyter per risorse di calcolo scalabili, con la potenza di Python e delle numerose librerie correlate.  Poiché l'installazione viene gestita dal servizio, gli utenti possono accedere a queste risorse senza la necessità di amministrazione e configurazione da parte dell'utente.

Se il servizio notebook non funziona per lo scenario, continuare a leggere questo articolo che illustra come distribuire il Jupyter Notebook in Microsoft Azure utilizzando macchine virtuali (VM) Linux.

[!INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="create-and-configure-a-vm-on-azure"></a>Creazione e configurazione di una macchina virtuale in Azure
Il primo passaggio consiste nel creare una macchina virtuale eseguita in Azure.
La VM è un sistema operativo completo nel cloud e verrà utilizzata per l'esecuzione di Jupyter Notebook. Azure consente di eseguire macchine virtuali sia Linux che Windows, e in questo articolo verrà descritta la configurazione di Jupyter in entrambi i tipi.

### <a name="create-a-linux-vm-and-open-a-port-for-jupyter"></a>Creare una VM Linux e aprire una porta per Jupyter
Seguire le istruzioni fornite [qui][portal-vm-linux] per creare una macchina virtuale della distribuzione *Ubuntu*. Questa esercitazione utilizza Ubuntu Server 14.04 LTS. Si presuppone che il nome utente sia *azureuser*.

Dopo aver distribuito la macchina virtuale è necessario aprire una regola di protezione nel gruppo di sicurezza di rete.  Dal portale di Azure, passare a **Gruppi di sicurezza di rete** e aprire la scheda per il gruppo di sicurezza corrispondente alla VM. È necessario aggiungere una regola di sicurezza in ingresso con le impostazioni seguenti: **TCP** per il protocollo, **\*** per la porta di origine (pubblica) e **9999** per la porta di destinazione (privata).

![Schermata](./media/virtual-machines-linux-jupyter-notebook/azure-add-endpoint.png)

Mentre ci si trova nel gruppo di sicurezza di rete, fare clic su **Interfacce di rete** e prendere nota dell’**Indirizzo IP pubblico**, poiché sarà necessario per connettersi alla VM nel passaggio successivo.

## <a name="install-required-software-on-the-vm"></a>Installazione del software necessario nella macchina virtuale
Per eseguire Jupyter Notebook nella VM, è necessario installare prima Jupyter e le relative dipendenze. Connettersi alla VM Linux mediante ssh e la coppia nomeutente/password selezionata durante la creazione della VM. In questa esercitazione verrà utilizzato PuTTY e ci si connetterà da Windows.

### <a name="installing-jupyter-on-ubuntu"></a>Installazione di Jupyter su Ubuntu
Installare Anaconda, una famosa distribuzione python per le scienze dei dati, utilizzando uno dei collegamenti forniti da [Continuum Analytics](https://www.continuum.io/downloads).  Al momento della stesura di questo documento, i collegamenti riportati di seguito sono le versioni più aggiornate.

#### <a name="anaconda-installs-for-linux"></a>Installazione di Anaconda per Linux
<table>
  <th>Python 3.4</th>
  <th>Python 2.7</th>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh'>64 bit</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86_64.sh'>64 bit</href>
    </td>
  </tr>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86.sh'>32 bit</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86.sh'>32 bit</href>
    </td>  
  </tr>
</table>


#### <a name="installing-anaconda3-230-64-bit-on-ubuntu"></a>Installazione di Anaconda3 2.3.0 a 64 bit su Ubuntu
Ad esempio, in questo modo è possibile installare Anaconda su Ubuntu

    # install anaconda
    cd ~
    mkdir -p anaconda
    cd anaconda/
    curl -O https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh
    sudo bash Anaconda3-2.3.0-Linux-x86_64.sh -b -f -p /anaconda3

    # clean up home directory
    cd ..
    rm -rf anaconda/

    # Update Jupyter to the latest install and generate its config file
    sudo /anaconda3/bin/conda install jupyter -y
    /anaconda3/bin/jupyter-notebook --generate-config


![Schermata](./media/virtual-machines-linux-jupyter-notebook/anaconda-install.png)

### <a name="configuring-jupyter-and-using-ssl"></a>Configurazione di Jupyter e utilizzo di SSL
Dopo l'installazione, è necessario utilizzare alcuni minuti per configurare i file di configurazione per Jupyter. Se si verificano problemi relativi alla configurazione di Jupyter, può essere utile esaminare la [Documentazione di Jupyter per l'esecuzione di un Notebook Server](http://jupyter-notebook.readthedocs.org/en/latest/public_server.html).

Passare alla directory del profilo tramite `cd` per creare il certificato SSL e modificare il file di configurazione del profilo.

Utilizzare il comando seguente su Linux.

    cd ~/.jupyter

Utilizzare il comando seguente per creare il certificato SSL (Linux e Windows).

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Si noti che, poiché verrà cercato un certificato SSL autofirmato, quando ci si connette al blocco appunti nel browser verrà visualizzato un avviso di sicurezza.  Per l'utilizzo in produzione a lungo termine, è consigliabile utilizzare un certificato con una firma appropriata associato all'organizzazione.  Poiché la gestione dei certificati esula dall'ambito di questa dimostrazione, per il momento verrà utilizzato un certificato autofirmato.

Oltre a utilizzare un certificato, è anche necessario specificare una password per proteggere il blocco appunti dall'utilizzo non autorizzato.  Per motivi di sicurezza, nel file di configurazione di Jupyter vengono utilizzate password crittografate, pertanto è necessario innanzitutto crittografare la password.  In IPython è disponibile un'utilità per questo scopo. Al prompt dei comandi eseguire:

    /anaconda3/bin/python -c "import IPython;print(IPython.lib.passwd())"

Verrà richiesto di specificare e confermare una password, quindi stampare la password. Annotarla per il passaggio seguente.

    Enter password:
    Verify password:
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

In seguito verrà modificato il file di configurazione del profilo, che corrisponde al file `jupyter_notebook_config.py` della directory corrente.  Si noti che questo file potrebbe non essere presente: in tal caso, crearlo.  Questo file contiene alcuni campi, tutti impostati come commento per impostazione predefinita.  È possibile aprire questo file con qualsiasi editor di testo, assicurandosi che includa almeno il contenuto seguente: **Assicurarsi di sostituire la password c.NotebookApp.password nella configurazione con sha1 dal passaggio precedente**.

    c = get_config()

    # You must give the path to the certificate file.
    c.NotebookApp.certfile = u'/home/azureuser/.jupyter/mycert.pem'

    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### <a name="run-the-jupyter-notebook"></a>Avviare il notebook Jupyter
A questo punto è possibile avviare Jupyter Notebook. A tale scopo, passare alla directory in cui si vogliono archiviare i blocchi appunti e avviare il server Jupyter Notebook con il comando seguente:

    /anaconda3/bin/jupyter-notebook

A questo punto dovrebbe essere possibile accedere a Jupyter Notebook all'indirizzo `https://[PUBLIC-IP-ADDRESS]:9999`.

La prima volta che si accede al blocco appunti, nella pagina di accesso viene richiesto di immettere la password: Dopo l'accesso, verrà visualizzata la pagina "Jupyter Notebook Dashboard", ossia il centro di controllo per tutte le operazioni sui blocchi appunti.  In questa pagina è possibile creare nuovi blocchi appunti e aprire quelli esistenti.

![Schermata](./media/virtual-machines-linux-jupyter-notebook/jupyter-tree-view.png)

### <a name="using-the-jupyter-notebook"></a>Utilizzo di Jupyter Notebook
Se si fa clic sul pulsante **Nuovo** , verrà visualizzata la seguente pagina di apertura.

![Schermata](./media/virtual-machines-linux-jupyter-notebook/jupyter-untitled-notebook.png)

L'area contrassegnata con `In []:` è l'area di input, in cui è possibile immettere qualsiasi codice Python valido che verrà eseguito premendo `Shift-Enter` oppure facendo clic sull'icona "Play", ossia il triangolo che punta a destra sulla barra degli strumenti.

## <a name="a-powerful-paradigm-live-computational-documents-with-rich-media"></a>Un potente paradigma: documenti di calcolo in tempo reale con contenuti multimediali
Il blocco appunti dovrebbe risultare familiare a chiunque abbia usato Python e un elaboratore di testo, perché è una combinazione di entrambi: è possibile eseguire blocchi di codice Python, ma anche mantenere note e altro testo modificando lo stile di una cella da "Code" a "Markdown" tramite il menu a discesa della barra degli strumenti:

Jupyter è molto di più di un elaboratore di testo, in quanto consente di combinare calcolo e contenuti multimediali, ossia testo, grafica, video e qualsiasi altro contenuto visualizzabile in un moderno Web browser. È possibile combinare, testo, codice, video e altro ancora!

![Schermata](./media/virtual-machines-linux-jupyter-notebook/jupyter-editing-experience.png)

Inoltre, con la potenza di molte eccellenti librerie di Python per il calcolo scientifico e tecnico, è possibile eseguire calcoli con la stessa facilità di un'analisi di rete complessa, tutto nello stesso ambiente:

Questo paradigma di combinare la potenza del moderno Web con il calcolo in tempo reale offre numerose possibilità ed è particolarmente indicato per il cloud. Il blocco appunti può essere utilizzato:

* Come blocco appunti computazionale per registrare il lavoro esplorativo su un problema.
* Per condividere i risultati con i colleghi, sotto forma di calcolo in tempo reale o in formati stampabili (HTML, PDF).
* Per distribuire e presentare materiale di formazione in tempo reale che implica calcoli, in modo che gli studenti possano sperimentare immediatamente con codice reale, modificarlo e rieseguirlo interattivamente.
* Per fornire "documenti eseguibili" che presentano i risultati di una ricerca in modo da poter essere immediatamente riprodotti, convalidati e ampliati da altri.
* Come piattaforma per l'informatica collaborativa: più utenti possono accedere allo stesso server notebook per condividere una sessione di calcolo in tempo reale.

Nel [repository][repository] di codice sorgente di IPython è disponibile un'intera directory con esempi di notebook che è possibile scaricare e provare nella propria VM Jupyter di Azure.  È sufficiente scaricare i file `.ipynb` dal sito e caricarli nel dashboard della macchina virtuale di Azure dei blocchi appunti oppure scaricarli direttamente nella macchina virtuale.

## <a name="conclusion"></a>Conclusioni
Jupyter Notebook offre un'eccellente interfaccia per l'accesso interattivo alla potenza dell'ecosistema Python in Azure.  Rende possibile un'ampia gamma di casi di utilizzo, tra cui la semplice esplorazione di Python per apprenderne le funzionalità, l'analisi e la visualizzazione di dati, la simulazione e l'elaborazione parallela. I documenti Notebook risultanti contengono un record completo dei calcoli eseguiti e possono essere condivisi con altri utenti di Jupyter.  È possibile utilizzare Jupyter Notebook come applicazione locale, ma si tratta di una soluzione particolarmente indicata per le distribuzioni cloud in Azure.

Le funzionalità di base di Jupyter sono anche disponibili all'interno di Visual Studio tramite [Python Tools per Visual Studio][Python Tools for Visual Studio] (PTVS). un plug-in open source reso disponibile da Microsoft che trasforma Visual Studio in un ambiente di sviluppo Python avanzato, con l'integrazione di un editor avanzato con IntelliSense, debug, profili ed elaborazione parallela.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni, vedere il [Centro per sviluppatori di Python](/develop/python/).

[portal-vm-linux]: https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-tutorial-portal-rm/
[repository]: https://github.com/ipython/ipython
[Python Tools for Visual Studio]: http://aka.ms/ptvs

