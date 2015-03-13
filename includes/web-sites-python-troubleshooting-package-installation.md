Alcuni pacchetti potrebbero non essere installati tramite pip se eseguiti su Azure.  Il motivo può essere semplicemente dovuto al fatto che il pacchetto non è disponibile nell'indice del pacchetto Python.  Potrebbe essere necessario un compilatore (se non è disponibile alcun compilatore nel computer che esegue il sito Web di Azure).

In questa sezione vengono esaminati alcuni metodi utili per risolvere questo problema.

### Richiedere i file wheel

Se per l'installazione del pacchetto è necessario un compilatore, provare a contattare il proprietario del pacchetto per richiedere che vengano resi disponibili i file wheel per il pacchetto.

Grazie alla recente disponibilità del [compilatore Microsoft Visual C++ per Python 2.7][], è ora più facile compilare pacchetti che includono codice nativo per Python 2.7.

### Creare i file wheel (richiede Windows)

Nota: quando si usa questa opzione, assicurarsi di compilare il pacchetto usando un ambiente Python corrispondente alla combinazione di piattaforma/architettura/versione usata nel sito Web di Azure (Windows/32 bit/2.7 o 3.4).

Se il pacchetto non viene installato perché richiede un compilatore, è possibile installare il compilatore nel computer locale e creare un file wheel per il pacchetto, da includere quindi nel repository.

Utenti Mac/Linux: se non si ha accesso a un computer Windows, vedere [Creare una macchina virtuale che esegue Windows][] per informazioni su come creare una macchina virtuale in Azure.  È possibile usare la macchina virtuale per creare i file wheel, aggiungerli al repository e quindi eliminare la macchina virtuale se lo si desidera. 

Per Python 2.7, è possibile installare il [compilatore Microsoft Visual C++ per Python 2.7][].

Per Python 3.4, è possibile installare il [compilatore Microsoft Visual C++ 2010 Express][].

Per creare i file wheel, sarà necessario il pacchetto wheel:

    env\scripts\pip install wheel

Usare  `pip wheel` per compilare una dipendenza:

    env\scripts\pip wheel azure==0.8.4

Verrà creato un file con estensione whl nella cartella \wheelhouse.  Aggiungere la cartella \wheelhouse e i file wheel nel repository.

Modificare requirements.txt per aggiungere l'opzione `--find-links` nella parte superiore. In questo modo, si indica a pip di cercare una corrispondenza esatta nella cartella locale prima di passare all'indice del pacchetto Python.

    --find-links wheelhouse
    azure==0.8.4
			
Per includere tutte le dipendenze nella cartella \wheelhouse senza usare del tutto l'indice del pacchetto Python, è possibile forzare pip in modo che ignori l'indice del pacchetto aggiungendo `--no-index` nella parte superiore di requirements.txt.

    --no-index

### Personalizzare l'installazione

È possibile personalizzare lo script di distribuzione in modo da installare un pacchetto nell'ambiente virtuale usando un programma di installazione alternativo, come easy\_install.  Per un esempio impostato come commento, vedere deploy.cmd.  Assicurarsi che questi pacchetti non siano elencati in requirements.txt, in modo da impedirne l'installazione da parte di pip.

Aggiungere questa riga allo script di distribuzione:

    env\scripts\easy_install somepackage

Potrebbe anche essere possibile usare easy\_install per eseguire l'installazione da un programma di installazione EXE (alcuni sono compatibili con il formato ZIP e quindi sono supportati da easy\_install).  Aggiungere il programma di installazione nel repository e richiamare easy\_install passando il percorso del file eseguibile.

Aggiungere questa riga allo script di distribuzione:

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### Includere l'ambiente virtuale nel repository (richiede Windows)

Nota: quando si usa questa opzione, assicurarsi di usare un ambiente virtuale corrispondente alla combinazione di piattaforma/architettura/versione usata nel sito Web di Azure (Windows/32 bit/2.7 o 3.4).

Se si include l'ambiente virtuale nel repository, è possibile impedire allo script di distribuzione di eseguire la gestione dell'ambiente virtuale in Azure creando un file vuoto:

    .skipPythonDeployment

È preferibile eliminare l'ambiente virtuale nel sito, per evitare la presenza di file rimasti in seguito alla gestione automatica dell'ambiente virtuale.


[Creare una macchina virtuale che esegue Windows]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/
[Compilatore Microsoft Visual C++ per Python 2.7]: http:://aka.ms/vcpython27
[compilatore Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949
<!--HONumber=42-->
