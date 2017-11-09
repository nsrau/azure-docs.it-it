## <a name="prepare-your-raspberry-pi"></a>Preparare Raspberry Pi

### <a name="install-raspbian"></a>Installare Raspbian

Se è la prima volta che si usa Raspberry Pi, è necessario installare il sistema operativo Raspbian tramite NOOBS nella scheda SD inclusa nel kit. La [Raspberry Pi Software Guide][lnk-install-raspbian] (Guida al software Raspberry Pi) descrive come installare un sistema operativo in Raspberry Pi. Questa esercitazione presuppone che si sia installato il sistema operativo Raspbian in Raspberry Pi.

> [!NOTE]
> NOOBS è già installato nella scheda SD inclusa in [Starter Kit di Microsoft Azure IoT per Raspberry Pi 3][lnk-starter-kits]. È possibile avviare Raspberry Pi da questa scheda e scegliere di installare il sistema operativo Raspbian.

### <a name="set-up-the-hardware"></a>Configurare l'hardware

Questa esercitazione usa il sensore BME280 incluso in [Starter Kit di Microsoft Azure IoT per Raspberry Pi 3][lnk-starter-kits] per generare dati di telemetria. Usa un LED per indicare quando Raspberry Pi elabora una chiamata a un metodo dal dashboard della soluzione.

I componenti sulla breadboard sono:

- LED rosso
- Resistore da 220 Ohm (rosso, rosso, marrone)
- Sensore BME280

Il diagramma seguente illustra come collegare l'hardware:

![Configurazione hardware per Raspberry Pi][img-connection-diagram]

La tabella seguente riepiloga i collegamenti da Raspberry Pi ai componenti della breadboard:

| Raspberry Pi            | Breadboard             |Colore         |
| ----------------------- | ---------------------- | ------------- |
| GND (Pin 14)            | LED -ve pin (18A)      | Viola          |
| GPCLK0 (Pin 7)          | Resistore (25A)         | Arancione          |
| SPI_CE0 (Pin 24)        | CS (39A)               | Blu          |
| SPI_SCLK (Pin 23)       | SCK (36A)              | Giallo        |
| SPI_MISO (Pin 21)       | SDO (37A)              | Bianco         |
| SPI_MOSI (Pin 19)       | SDI (38A)              | Verde         |
| GND (Pin 6)             | GND (35A)              | Nero         |
| 3.3 V (Pin 1)           | 3Vo (34A)              | Rosso           |

Per completare la configurazione dell'hardware, è necessario:

- Connettere Raspberry Pi all'alimentatore incluso nel kit.
- Connettere Raspberry Pi alla rete usando il cavo Ethernet incluso nel kit. In alternativa, è possibile configurare la [connettività wireless][lnk-pi-wireless] per Raspberry Pi.

La configurazione hardware di Raspberry Pi è stata quindi completata.

### <a name="sign-in-and-access-the-terminal"></a>Accedere al terminale

Esistono due opzioni per accedere all'ambiente di un terminale in Raspberry Pi:

- Se a Raspberry Pi sono connessi una tastiera e un monitor, è possibile usare l'interfaccia utente grafica di Raspbian per accedere a una finestra del terminale.

- Accedere alla riga di comando in Raspberry Pi usando SSH dal computer desktop.

#### <a name="use-a-terminal-window-in-the-gui"></a>Usare una finestra del terminale nell'interfaccia utente grafica

Le credenziali predefinite per Raspbian sono il nome utente **pi** e la password **raspberry**. Nella barra della applicazioni dell'interfaccia utente grafica è possibile avviare l'utilità **Terminal** (Terminale) usando l'icona del monitor.

#### <a name="sign-in-with-ssh"></a>Accedere con SSH

È possibile usare la riga di comando SSH per accedere a Raspberry Pi. L'articolo [SSH (Secure Shell)][lnk-pi-ssh] (SSH - Secure Shell) descrive come configurare SSH in Raspberry Pi e come connettersi da [Windows][lnk-ssh-windows] o [Linux e Mac OS][lnk-ssh-linux].

Accedere con il nome utente **pi** e la password **raspberry**.

#### <a name="optional-share-a-folder-on-your-raspberry-pi"></a>Facoltativo: condividere una cartella in Raspberry Pi

È facoltativamente possibile condividere una cartella in Raspberry Pi con l'ambiente desktop. La condivisione di una cartella consente di usare l'editor di testo desktop preferito (ad esempio, [Visual Studio Code](https://code.visualstudio.com/) o [Sublime Text](http://www.sublimetext.com/)) per modificare i file in Raspberry Pi invece di usare `nano` o `vi`.

Per condividere una cartella con Windows, configurare un server Samba in Raspberry Pi. Usare in alternativa il server [SFTP](https://www.raspberrypi.org/documentation/remote-access/) predefinito con un client SFTP sul desktop.

### <a name="enable-spi"></a>Abilitare SPI

Prima di poter eseguire l'applicazione di esempio, è necessario abilitare il bus SPI (Serial Peripheral Interface) in Raspberry Pi. Raspberry Pi comunica con il dispositivo sensore BME280 tramite il bus SPI. Usare il comando seguente per modificare il file di configurazione:

```sh
sudo nano /boot/config.txt
```

Trovare la riga:

`#dtparam=spi=on`

- Per rimuovere il commento dalla riga, eliminare `#` all'inizio.
- Salvare le modifiche (**CTRL+O**, **INVIO**) e chiudere l'editor (**CTRL+X**).
- Per abilitare SPI, riavviare Raspberry Pi. Il riavvio disconnette il terminale, quindi è necessario eseguire di nuovo l'accesso quando Raspberry Pi viene riavviato:

  ```sh
  sudo reboot
  ```


[img-connection-diagram]: media/iot-suite-v1-raspberry-pi-kit-prepare-pi/rpi2_remote_monitoring.png

[lnk-install-raspbian]: https://www.raspberrypi.org/learning/software-guide/quickstart/
[lnk-pi-wireless]: https://www.raspberrypi.org/documentation/configuration/wireless/README.md
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md
[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/