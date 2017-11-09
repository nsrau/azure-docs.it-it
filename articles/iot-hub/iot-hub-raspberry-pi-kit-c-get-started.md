---
title: 'Raspberry Pi al cloud (C): connettere Raspberry Pi ad Azure IoT Hub | Microsoft Docs'
description: "Informazioni su come configurare e connettere Raspberry Pi all'hub IoT di Azure perché Raspberry Pi invii i dati alla piattaforma cloud di Azure in questa esercitazione."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: azure iot raspberry pi, raspberry pi iot hub, raspberry pi invio dati al cloud, da raspberry pi al cloud
ms.assetid: 68c0e730-1dc8-4e26-ac6b-573b217b302d
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/12/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d643647d4103acd511ed270132c844da12f2ac9b
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2017
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Connettere Raspberry Pi ad Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Questa esercitazione illustra le nozioni di base sull'uso di Raspberry Pi con il sistema operativo Raspbian. Viene poi illustrato come connettere i dispositivi al cloud usando l'[hub IoT di Azure](iot-hub-what-is-iot-hub.md). Per esempi di Windows 10 IoT Core, vedere [Windows Dev Center](http://www.windowsondevices.com/).

Se non si ha ancora un kit, Provare il [simulatore online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). In alternativa, acquistare un nuovo kit [qui](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Operazioni da fare

* Creare un hub IoT.
* Registrare un dispositivo per Pi nel proprio hub IoT.
* Installare Raspberry Pi.
* Eseguire un'applicazione di esempio in Pi per inviare i dati del sensore all'hub IoT.

Connettere Raspberry Pi a un hub IoT creato dall'utente. Dopodiché, eseguire un'applicazione di esempio in Pi per raccogliere i dati di temperatura e umidità da un sensore BME280. Infine inviare i dati del sensore all'hub IoT.

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

* Come creare un hub IoT di Azure e ottenere la stringa di connessione del nuovo dispositivo.
* Come connettere Pi con un sensore BME280.
* Come raccogliere i dati del sensore eseguendo un'applicazione di esempio in Pi.
* Come inviare i dati del sensore all'hub IoT.

## <a name="what-you-need"></a>Elementi necessari

![Elementi necessari](media/iot-hub-raspberry-pi-kit-c-get-started/0_starter_kit.jpg)

* La scheda di Raspberry Pi 2 o di Raspberry Pi 3.
* Una sottoscrizione di Azure attiva. Se non si ha un account di Azure, [creare un account di Azure gratuito](https://azure.microsoft.com/free/) in pochi minuti.
* Un monitor, una tastiera USB e un mouse da collegare a Pi.
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
Questi elementi sono opzionali, poiché l'esempio di codice supporta i dati del sensore simulati.


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-raspberry-pi"></a>Installare Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Installare il sistema operativo Raspbian per Pi

Preparare la scheda microSD per l'installazione dell'immagine di Raspbian.

1. Scaricare Raspbian.
   1. [Scaricare Raspbian Jessie with Desktop](https://www.raspberrypi.org/downloads/raspbian/) (file ZIP).
   1. Estrarre l'immagine di Raspbian in una cartella nel computer.
1. Installare Raspbian nella scheda microSD.
   1. [Scaricare e installare l'utilità di masterizzazione Etcher per schede SD](https://etcher.io/).
   1. Eseguire Etcher e selezionare l'immagine di Raspbian estratta nel passaggio 1.
   1. Selezionare l'unità della scheda microSD. Si noti che Etcher potrebbe avere già selezionato l'unità corretta.
   1. Fare clic su Flash per installare Raspbian nella scheda microSD.
   1. Rimuovere la scheda microSD dal computer al termine dell'installazione. È possibile rimuovere direttamente la scheda microSD perché viene espulsa o smontata automaticamente da Etcher al termine dell'operazione.
   1. Inserire la scheda microSD in Pi.

### <a name="enable-ssh-and-spi"></a>Abilitare SSH e SPI

1. Collegare Pi al monitor, alla tastiera e al mouse, avviare Pi e accedere a Raspbian usando `pi` come nome utente e `raspberry` come password.
1. Fare clic sull'icona di Raspberry > **Preferenze** > **Raspberry Pi Configuration** (Configurazione di Raspberry Pi).

   ![Il menu Preferenze di Raspbian](media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. Nella scheda **Interfacce** impostare **SPI** e **SSH** su **Abilita**, quindi fare clic su **OK**. Se non si hanno sensori fisici e si vogliono usare i dati di sensori simulati, questo passaggio è facoltativo.

   ![Abilitare SPI e SSH su Raspberry Pi](media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
Per abilitare SSH e SPI è possibile trovare più documenti di riferimento su [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) e [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

### <a name="connect-the-sensor-to-pi"></a>Connettere il sensore a Pi

Usare i cavi ponticello e la basetta sperimentale per connettere un LED e un sensore BME280 a Pi, come indicato di seguito. In assenza di un sensore, [ignorare questa sezione](#connect-pi-to-the-network).

![La connessione di Raspberry Pi e del sensore](media/iot-hub-raspberry-pi-kit-c-get-started/3_raspberry-pi-sensor-connection.png)

Il sensore BME280 può raccogliere i dati relativi a temperatura e umidità. Il LED sarà intermittente se viene stabilita una comunicazione tra il dispositivo e il cloud. 

Per i pin dei sensori usare i collegamenti seguenti:

| Inizio (sensore e LED)     | Fine (scheda)            | Colore del cavo   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (Pin 5G)         | GPIO 4 (Pin 7)         | Cavo bianco   |
| LED GND (Pin 6G)         | GND (Pin 6)            | Cavo nero   |
| VDD (Pin 18F)            | 3,3 V PWR (Pin 17)      | Cavo bianco   |
| GND (Pin 20F)            | GND (Pin 20)           | Cavo nero   |
| SCK (Pin 21F)            | SPI0 SCLK (Pin 23)     | Cavo arancione  |
| SDO (Pin 22F)            | SPI0 MISO (Pin 21)     | Cavo giallo  |
| SDI (Pin 23F)            | SPI0 MOSI (Pin 19)     | Cavo verde   |
| CS (Pin 24F)             | SPI0 CS (Pin 24)       | Cavo blu    |

Fare clic per visualizzare i [mapping Pin di Raspberry Pi 2 e 3](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) come riferimento.

Dopo aver correttamente collegato BME280 a Raspberry Pi, dovrebbe apparire come mostrato nell'immagine di seguito.

![Pi e BME280 connessi](media/iot-hub-raspberry-pi-kit-c-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Connettere Pi alla rete

Accendere Pi usando il cavo micro USB e l'alimentatore. Usare il cavo Ethernet per connettere Pi alla rete cablata oppure seguire le [istruzioni riportate nella Guida di Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/wifi/) per connettere Pi alla rete wireless. Dopo aver connesso Pi alla rete, è necessario annotarne [l'indirizzo IP](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Connesso alla rete cablata](media/iot-hub-raspberry-pi-kit-c-get-started/5_power-on-pi.jpg)


## <a name="run-a-sample-application-on-pi"></a>Eseguire un'applicazione di esempio in Pi

### <a name="login-to-your-raspberry-pi"></a>Accedere a Raspberry Pi

1. Per connettersi a Raspberry Pi, usare uno dei client SSH seguenti dal computer host.
   
   **Utenti Windows**
   1. Scaricare e installare [PuTTY](http://www.putty.org/) per Windows. 
   1. Copiare l'indirizzo IP di Pi nella sezione relativa a nome host o indirizzo IP e selezionare SSH come tipo di connessione.
   
   ![PuTTy](media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Utenti Mac e Ubuntu**
   
   Usare il client SSH predefinito in Ubuntu o macOS. Per connettere Pi tramite SSH potrebbe essere necessario eseguire `ssh pi@<ip address of pi>`.
   > [!NOTE] 
   Il nome utente predefinito è `pi` e la password è `raspberry`.


### <a name="configure-the-sample-application"></a>Configurare l'applicazione di esempio

1. Clonare l'applicazione di esempio eseguendo il comando seguente:

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. Eseguire lo script di configurazione:

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > Se **non di dispone di un BME280 fisico**, è possibile usare "--simulated-data" come parametro della riga di comando per simulare i dati temperature&humidity. `sudo ./setup.sh --simulated-data`

### <a name="build-and-run-the-sample-application"></a>Compilare ed eseguire l'applicazione di esempio

1. Compilare l'applicazione di esempio eseguendo il comando seguente:

   ```bash
   cmake . && make
   ```
   ![Output della compilazione](media/iot-hub-raspberry-pi-kit-c-get-started/7_build-output.png)

1. Eseguire l'applicazione di esempio tramite il comando seguente:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   Assicurarsi di copiare e incollare la stringa di connessione del dispositivo tra virgolette singole.


Dovrebbe essere visibile l'output seguente che mostra i dati del sensore e i messaggi inviati all'hub IoT.

![Output - dati del sensore inviati da Raspberry Pi all'hub IoT](media/iot-hub-raspberry-pi-kit-c-get-started/8_run-output.png)

## <a name="next-steps"></a>Passaggi successivi

È stata eseguita un'applicazione di esempio per raccogliere i dati del sensore da inviare all'hub IoT. Per visualizzare i messaggi inviati da Raspberry Pi all'hub IoT o per inviare messaggi a Raspberry Pi in un'interfaccia della riga di comando, vedere l'[esercitazione sulla gestione della messaggistica tra cloud e dispositivo con iothub-explorer](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-explorer-cloud-device-messaging).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
