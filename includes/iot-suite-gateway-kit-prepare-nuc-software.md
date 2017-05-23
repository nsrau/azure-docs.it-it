## <a name="build-iot-edge"></a>Compilare IoT Edge

Questa esercitazione mostra come usare moduli personalizzati di IoT Edge per comunicare con la soluzione preconfigurata di monitoraggio remoto. È quindi necessario compilare i moduli di IoT Edge dal codice sorgente personalizzato. Le sezioni seguenti illustrano come installare IoT Edge e compilare il modulo personalizzato di IoT Edge.

### <a name="install-iot-edge"></a>Installare IoT Edge

La procedura seguente illustra come installare il software IoT Edge precompilato in Intel NUC:

1. Configurare i repositori di pacchetti intelligenti necessari eseguendo questi comandi in Intel NUC:

    ```bash
    smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
    smart channel --add WR_Repo type=rpm-md baseurl=https://distro.windriver.com/release/idp-3-xt/public_feeds/WR-IDP-3-XT-Intel-Baytrail-public-repo/RCPL13/corei7_64/
    ```

    Immettere `y` quando viene richiesto se **includere questo canale**.

1. Aggiornare il gestore di pacchetti intelligenti eseguendo questo comando:

    ```bash
    smart update
    ```

1. Installare il pacchetto di Azure IoT Edge eseguendo questo comando:

    ```bash
    smart config --set rpm-check-signatures=false
    smart install packagegroup-cloud-azure -y
    ```

1. Verificare l'installazione eseguendo l'esempio Hello World. L'esempio scrive il messaggio Hello World nel file log.txt ogni cinque secondi. Eseguire l'esempio Hello World usando i comandi seguenti:

    ```bash
    cd /usr/share/azureiotgatewaysdk/samples/hello_world/
    ./hello_world hello_world.json
    ```

    Ignorare gli eventuali messaggi di **argomento non valido** quando si arresta l'esempio.

    Usare il comando seguente per visualizzare il contenuto del file di log:

    ```bash
    cat log.txt | more
    ```

### <a name="troubleshooting"></a>Risoluzione dei problemi

Se viene visualizzato l'errore: "no package provides util-linux-dev" (nessun pacchetto fornisce util-linux-dev), provare a riavviare Intel NUC.
