## <a name="install-the-prerequisites"></a>Installare i prerequisiti

1. Installare [Visual Studio 2015 o 2017](https://www.visualstudio.com). Se si soddisfano i requisiti di licenza, è possibile usare la versione gratuita Community Edition. Assicurarsi di includere Visual C++ e Gestione pacchetti NuGet.

1. Installare [git](http://www.git-scm.com) e verificare di riuscire a eseguire git.exe dalla riga di comando.

1. Installare [CMake](https://cmake.org/download/) e verificare di riuscire a eseguire cmake.exe dalla riga di comando. È consigliabile usare CMake versione 3.7.2 o successive. Il programma di installazione **.msi** è l'opzione più semplice in Windows. Aggiungere CMake alla variabile di ambiente PATH almeno per l'utente corrente quando viene dal programma di installazione.

1. Installare [Python 2.7](https://www.python.org/downloads/release/python-27). Assicurarsi di aggiungere Python alla variabile di ambiente `PATH`. Andare a **Pannello di controllo** > **Sistema e sicurezza** > **Sistema** > **Impostazioni di sistema avanzate** > **Variabili di ambiente**. Aggiungere `C:\Python27` al percorso. 

1. Al prompt dei comandi, eseguire il comando seguente per clonare il repository GitHub Azure IoT Edge nel computer locale:

    ```cmd
    git clone https://github.com/Azure/iot-edge.git
    ```

## <a name="how-to-build-the-sample"></a>Come compilare l'esempio

È ora possibile compilare il runtime e gli esempi di IoT Edge nel computer locale:

1. Aprire il **prompt dei comandi per gli sviluppatori per VS 2015** o il **prompt dei comandi per gli sviluppatori per VS 2017**, a seconda della versione.

1. Accedere alla cartella radice nella copia locale del repository **iot-edge**.

1. Eseguire lo script di compilazione come segue:

    ```cmd
    tools\build.cmd --disable-native-remote-modules
    ```

Questo script crea un file di soluzione Visual Studio e compila la soluzione. È possibile trovare la soluzione di Visual Studio nella cartella **build** nella copia locale del repository **iot-edge**. Per compilare ed eseguire unit test, aggiungere il parametro `--run-unittests`. Per compilare ed eseguire test end-to-end, aggiungere il parametro `--run-e2e-tests`.

> [!NOTE]
> Ogni volta che si esegue lo script **build.cmd**, la cartella **build** viene eliminata e ricreata nella cartella radice della copia locale del repository **iot-edge**.