---
title: Raspberry Pi al cloud (Node. js) - Connettere Raspberry Pi ad Azure IoT Hub | Microsoft Docs
description: "Informazioni su come configurare e connettere Raspberry Pi all'hub IoT di Azure perché Raspberry Pi invii i dati alla piattaforma cloud di Azure in questa esercitazione."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: azure iot raspberry pi, raspberry pi iot hub, raspberry pi invio dati al cloud, da raspberry pi al cloud
ms.assetid: b0e14bfa-8e64-440a-a6ec-e507ca0f76ba
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 9/14/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7bf423fd05d6651bf16693e6d6930fada8b5da70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Connettere Raspberry Pi ad Azure IoT Hub (Node. js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Questa esercitazione illustra le nozioni di base sull'uso di Raspberry Pi con il sistema operativo Raspbian. Viene poi illustrato come connettere i dispositivi al cloud usando l'[hub IoT di Azure](iot-hub-what-is-iot-hub.md). Per esempi di Windows 10 IoT Core, vedere [Windows Dev Center](http://www.windowsondevices.com/).

Se non si ha ancora un kit, Provare il [simulatore online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). In alternativa, acquistare un nuovo kit [qui](https://azure.microsoft.com/develop/iot/starter-kits).


## <a name="what-you-do"></a>Operazioni da fare

* Creare un hub IoT.
* Registrare un dispositivo per Pi nel proprio hub IoT.
* Installare Raspberry Pi.
* Eseguire un'applicazione di esempio in Pi per inviare i dati del sensore all'hub IoT.

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

* Come creare un hub IoT di Azure e ottenere la stringa di connessione del nuovo dispositivo.
* Come connettere Pi con un sensore BME280.
* Come raccogliere i dati del sensore eseguendo un'applicazione di esempio in Pi.
* Come inviare i dati del sensore all'hub IoT.

## <a name="what-you-need"></a>Elementi necessari

![Elementi necessari](media/iot-hub-raspberry-pi-kit-node-get-started/0_starter_kit.jpg)

* Una scheda di Raspberry Pi 2 o Raspberry Pi 3.
* Una sottoscrizione di Azure attiva. Se non si ha un account Azure, [creare un account Azure gratuito](https://azure.microsoft.com/free/) in pochi minuti.
* Un monitor, una tastiera USB e un mouse collegato a Pi.
* Un Mac o PC che esegue Windows o Linux.
* Una connessione Internet.
* Una scheda microSD da 16 GB o più.
* Una scheda microSD o un adattatore USB-SD con cui masterizzare l'immagine del sistema operativo nella scheda microSD.
* Un alimentatore da 5 V/2 A con cavo micro USB da 1,8 metri circa.

Gli elementi seguenti sono opzionali:

* Un sensore Adafruit BME280 assemblato per rilevare umidità, temperatura e pressione.
* Una basetta sperimentale.
* 6 cavi ponticello F/M.
* Un LED da 10 mm a luce diffusa.


> [!NOTE] 
Se non si dispone di elementi facoltativi, è possibile usare i dati dei sensori simulati.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="set-up-raspberry-pi"></a>Installare Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Installare il sistema operativo Raspbian per Pi

Preparare la scheda microSD per l'installazione dell'immagine di Raspbian.

1. Scaricare Raspbian.
   1. [Scaricare l'estensione Raspbian](http://downloads.raspberrypi.org/raspbian/images/raspbian-2017-07-05/) (il file con estensione zip).

   > [!WARNING]
   > Usare il collegamento precedente per scaricare l'immagine con estensione zip `raspbian-2017-07-5`. La versione più recente delle immagini Raspbian presenta alcuni problemi noti a livello del nodo Wiring-Pi e ciò potrebbe generare errori nei passaggi successivi.
   1. Estrarre l'immagine di Raspbian in una cartella nel computer.

1. Installare Raspbian nella scheda microSD.
   1. [Scaricare e installare l'utilità di masterizzazione Etcher per schede SD](https://etcher.io/).
   1. Eseguire Etcher e selezionare l'immagine di Raspbian estratta nel passaggio 1.
   1. Selezionare l'unità della scheda microSD. Etcher potrebbe avere già selezionato l'unità corretta.
   1. Fare clic su Flash per installare Raspbian nella scheda microSD.
   1. Rimuovere la scheda microSD dal computer al termine dell'installazione. È possibile rimuovere direttamente la scheda microSD perché viene espulsa o smontata automaticamente da Etcher al termine dell'operazione.
   1. Inserire la scheda microSD in Pi.

### <a name="enable-ssh-and-i2c"></a>Abilitare SSH e I2C

1. Collegare Pi al monitor, alla tastiera e al mouse. 
1. Avviare Pi e accedere a Raspbian usando `pi` come nome utente e `raspberry` come password.
1. Fare clic sull'icona di Raspberry > **Preferenze** > **Raspberry Pi Configuration** (Configurazione di Raspberry Pi).

   ![Il menu Preferenze di Raspbian](media/iot-hub-raspberry-pi-kit-node-get-started/1_raspbian-preferences-menu.png)

1. Nella scheda **Interfacce** impostare **I2C** e **SSH** su **Abilita**, quindi fare clic su **OK**. Se non si hanno sensori fisici e si vogliono usare i dati di sensori simulati, questo passaggio è facoltativo.

   ![Abilitare I2C e SSH su Raspberry Pi](media/iot-hub-raspberry-pi-kit-node-get-started/2_enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE] 
Per abilitare SSH e I2C è possibile trovare più documenti di riferimento su [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) e [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>Connettere il sensore a Pi

Usare i cavi ponticello e la basetta sperimentale per connettere un LED e un sensore BME280 a Pi, come indicato di seguito. In assenza di un sensore, [ignorare questa sezione](#connect-pi-to-the-network).

![La connessione di Raspberry Pi e del sensore](media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)

Il sensore BME280 può raccogliere i dati relativi a temperatura e umidità. Il LED lampeggia quando il dispositivo invia un messaggio al cloud. 

Per i pin dei sensori usare i collegamenti seguenti:

| Inizio (sensore e LED)     | Fine (scheda)            | Colore del cavo   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 5G)             | 3,3 V PWR (Pin 1)       | Cavo bianco   |
| GND (Pin 7G)             | GND (Pin 6)            | Cavo marrone   |
| SDI (Pin 10G)            | I2C1 SDA (Pin 3)       | Cavo rosso     |
| SCK (Pin 8G)             | I2C1 SCL (Pin 5)       | Cavo arancione  |
| LED VDD (Pin 18F)        | GPIO 24 (Pin 18)       | Cavo bianco   |
| LED GND (Pin 17F)        | GND (Pin 20)           | Cavo nero   |

Fare clic per visualizzare i [mapping dei pin di Raspberry Pi 2 e 3](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) come riferimento.

Dopo aver correttamente collegato BME280 a Raspberry Pi, dovrebbe apparire come mostrato nell'immagine di seguito.

![Pi e BME280 connessi](media/iot-hub-raspberry-pi-kit-node-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Connettere Pi alla rete

Accendere Pi usando il cavo micro USB e l'alimentatore. Usare il cavo Ethernet per connettere Pi alla rete cablata oppure seguire le [istruzioni riportate nella Guida di Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/wifi/) per connettere Pi alla rete wireless. Dopo aver connesso Pi alla rete, è necessario annotarne [l'indirizzo IP](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Connesso alla rete cablata](media/iot-hub-raspberry-pi-kit-node-get-started/5_power-on-pi.jpg)

> [!NOTE]
> Verificare che Pi sia connesso alla stessa rete del computer. Se il computer è connesso a una rete wireless mentre il dispositivo Pi è connesso a una rete cablata, ad esempio, l'indirizzo IP potrebbe non essere incluso nell'output di devdisco.

## <a name="run-a-sample-application-on-pi"></a>Eseguire un'applicazione di esempio in Pi

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Clonare l'applicazione di esempio e installare i pacchetti di prerequisiti

1. Connettersi a Raspberry Pi usando uno dei client SSH seguenti dal computer host.
   
   **Utenti Windows**
   1. Scaricare e installare [PuTTY](http://www.putty.org/) per Windows. 
   1. Copiare l'indirizzo IP di Pi nella sezione relativa a nome host o indirizzo IP e selezionare SSH come tipo di connessione.
   
   ![PuTTy](media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Utenti Mac e Ubuntu**
   
   Usare il client SSH predefinito in Ubuntu o macOS. Per connettere Pi tramite SSH potrebbe essere necessario eseguire `ssh pi@<ip address of pi>`.
   > [!NOTE] 
   Il nome utente predefinito è `pi` e la password è `raspberry`.

1. Installare Node.js e NPM in Pi.
   
   Controllare prima la versione di Node.js. 
   
   ```bash
   node -v
   ```

   Se la versione è anteriore alla 4.x o se non è presente alcun Node.js in Pi, installare la versione più recente.

   ```bash
   curl -sL http://deb.nodesource.com/setup_4.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

1. Clonare l'applicazione di esempio.

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-client-app
   ```

1. Installare tutti i pacchetti relativi all'esempio. L'installazione include Azure IoT SDK per dispositivi, la libreria del sensore BME280 e libreria di Pi per i collegamenti.

   ```bash
   cd iot-hub-node-raspberrypi-client-app
   sudo npm install
   ```
   > [!NOTE] 
   A seconda della connessione di rete, il completamento del processo di installazione potrebbe richiedere alcuni minuti.

### <a name="configure-the-sample-application"></a>Configurare l'applicazione di esempio

1. Aprire il file di configurazione eseguendo i comandi seguenti:

   ```bash
   nano config.json
   ```

   ![File di configurazione](media/iot-hub-raspberry-pi-kit-node-get-started/6_config-file.png)

   Questo file contiene due elementi configurabili. Il primo è `interval`, che definisce l'intervallo di tempo (in millisecondi) tra due messaggi inviati al cloud. Il secondo è `simulatedData`, ossia un valore booleano che indica se usare o no i dati del sensore simulato.

   Se **non si dispone del sensore**, impostare il valore `simulatedData` su `true` per permettere all'applicazione di esempio di creare e usare i dati del sensore simulati.

1. Salvare e uscire premendo CTRL-O > INVIO > CTRL-X.

### <a name="run-the-sample-application"></a>Eseguire l'applicazione di esempio

Eseguire l'applicazione di esempio tramite il comando seguente:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   Assicurarsi di copiare e incollare la stringa di connessione del dispositivo tra virgolette singole.


Dovrebbe essere visibile l'output seguente che mostra i dati del sensore e i messaggi inviati all'hub IoT.

![Output - dati del sensore inviati da Raspberry Pi all'hub IoT](media/iot-hub-raspberry-pi-kit-node-get-started/8_run-output.png)

## <a name="next-steps"></a>Passaggi successivi

È stata eseguita un'applicazione di esempio per raccogliere i dati del sensore da inviare all'hub IoT. Per visualizzare i messaggi inviati da Raspberry Pi all'hub IoT o per inviare messaggi a Raspberry Pi in un'interfaccia della riga di comando, vedere l'[esercitazione sulla gestione della messaggistica tra cloud e dispositivo con iothub-explorer](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-explorer-cloud-device-messaging).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
