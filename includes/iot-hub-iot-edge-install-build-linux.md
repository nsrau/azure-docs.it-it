## <a name="install-the-prerequisites"></a>Installare i prerequisiti

Per i passaggi di questa esercitazione si presuppone che sia in esecuzione Ubuntu Linux.

Per installare i pacchetti prerequisiti, aprire una shell ed eseguire i comandi seguenti:

```bash
sudo apt-get update
sudo apt-get install curl build-essential libcurl4-openssl-dev git cmake pkg-config libssl-dev uuid-dev valgrind libglib2.0-dev libtool autoconf
```

Nella shell eseguire il comando seguente per clonare il repository GitHub Azure IoT Edge nel computer locale:

```bash
git clone https://github.com/Azure/iot-edge.git
```

## <a name="how-to-build-the-sample"></a>Come compilare l'esempio

È ora possibile compilare il runtime e gli esempi di IoT Edge nel computer locale:

1. Aprire una shell.

1. Accedere alla cartella radice nella copia locale del repository **iot-edge**.

1. Eseguire lo script di compilazione come segue:

    ```sh
    tools/build.sh --disable-native-remote-modules
    ```

Questo script usa l'utilità **cmake** per creare una cartella denominata **build** nella cartella radice della copia locale del repository **iot-edge** e generare un makefile. Lo script quindi compila la soluzione senza eseguire i test dell'unità e i test end-to-end. Per compilare ed eseguire unit test, aggiungere il parametro `--run-unittests`. Per compilare ed eseguire test end-to-end, aggiungere il parametro `--run-e2e-tests`.

> [!NOTE]
> Ogni volta che si esegue lo script **build.sh**, la cartella **build** viene eliminata e ricreata nella cartella radice della copia locale del repository **iot-edge**.