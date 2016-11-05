Per stabilire se l'applicazione usa Python, Azure verifica se **entrambe le condizioni seguenti sono soddisfatte**:

* Presenza del file requirements.txt nella cartella radice
* Presenza di un qualsiasi file .py nella cartella radice OPPURE di un file runtime.txt in cui viene specificato python

In tale circostanza, userà uno script di distribuzione specifico di Python che eseguirà la sincronizzazione standard dei file, oltre ad operazioni aggiuntive di Python, tra cui:

* Gestione automatica dell'ambiente virtuale
* Installazione dei pacchetti elencati in requirements.txt tramite pip
* Creazione del file web.config appropriato sulla base della versione di Python selezionata
* Raccolta di file statici per le applicazioni Django

È possibile controllare determinati aspetti della procedura di distribuzione predefinita senza dover personalizzare lo script.

Per ignorare tutti i passaggi della distribuzione specifici di Python, è possibile creare questo file vuoto:

    \.skipPythonDeployment

Per ignorare la raccolta dei file statici per l'applicazione Django:

    \.skipDjango 

Per un maggiore controllo sulla distribuzione, è possibile eseguire l'override dello script di distribuzione predefinito creando i file seguenti:

    \.deployment
    \deploy.cmd

Per creare i file, è possibile usare l'[interfaccia della riga di comando di Azure][interfaccia della riga di comando di Azure]. Usare questo comando dalla cartella del progetto:

    azure site deploymentscript --python

Se questi file non esistono, Azure creerà uno script di distribuzione temporaneo e lo eseguirà. Tale file è identico a quello creato con il comando precedente.

[interfaccia della riga di comando di Azure]: http://azure.microsoft.com/downloads/

<!---HONumber=AcomDC_0224_2016-->