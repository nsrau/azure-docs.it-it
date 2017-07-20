---
title: 'Raspberry Pi al cloud (C): connettere Raspberry Pi ad Azure IoT Hub | Microsoft Docs'
description: Connettere Raspberry Pi ad Azure IoT Hub per permettere a Raspberry Pi di inviare dati al cloud di Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: azure iot raspberry pi, raspberry pi iot hub, raspberry pi invio dati al cloud, da raspberry pi al cloud
ms.assetid: 68c0e730-1dc8-4e26-ac6b-573b217b302d
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/15/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: b7c328ac622190d64ea1b07ee459c7f8f5d1e0f4
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---

# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Connettere Raspberry Pi ad Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Questa esercitazione illustra le nozioni di base sull'uso di Raspberry Pi con il sistema operativo Raspbian. Viene poi illustrato come connettere i dispositivi al cloud usando l'[hub IoT di Azure](iot-hub-what-is-iot-hub.md). Per esempi di Windows 10 IoT Core, vedere [Windows Dev Center](http://www.windowsondevices.com/).

Se non si ha ancora un kit, Provare il [simulatore online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). In alternativa, acquistare un nuovo kit [qui](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Operazioni da fare

* Installare Raspberry Pi.
* Creare un hub IoT.
* Registrare un dispositivo per Pi nel proprio hub IoT.
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
   1. [Scaricare Raspbian Jessie with Pixel](https://www.raspberrypi.org/downloads/raspbian/) (file .zip).
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

Usare i cavi ponticello e la basetta sperimentale per connettere un LED e un sensore BME280 a Pi, come indicato di seguito. Se non si dispone di sensori, ignorare questa sezione.

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

Accendere Pi usando il cavo micro USB e l'alimentatore. Usare il cavo Ethernet per connettere Pi alla rete cablata oppure seguire le [istruzioni riportate nella Guida di Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/wifi/) per connettere Pi alla rete wireless.

![Connesso alla rete cablata](media/iot-hub-raspberry-pi-kit-c-get-started/5_power-on-pi.jpg)


## <a name="run-a-sample-application-on-pi"></a>Eseguire un'applicazione di esempio in Pi

### <a name="install-the-prerequisite-packages"></a>Installare i pacchetti prerequisiti

1. Per connettersi a Raspberry Pi, usare uno dei client SSH seguenti dal computer host.
    - [PuTTY](http://www.putty.org/) per Windows.
    - Il client SSH incorporato in Ubuntu o macOS.

1. Installare i pacchetti di prerequisiti per Azure IoT SDK per dispositivi di Microsoft Azure per C e Cmake eseguendo i comandi seguenti:

   ```bash
   grep -q -F 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
   grep -q -F 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
   sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys FDA6A393E4C2257F
   sudo apt-get update
   sudo apt-get install -y azure-iot-sdk-c-dev cmake
   ```


### <a name="configure-the-sample-application"></a>Configurare l'applicazione di esempio

1. Clonare l'applicazione di esempio eseguendo il comando seguente:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app
   ```
1. Aprire il file di configurazione eseguendo i comandi seguenti:

   ```bash
   cd iot-hub-c-raspberry-pi-client-app
   nano config.h
   ```

   ![File di configurazione](media/iot-hub-raspberry-pi-kit-c-get-started/6_config-file.png)

   Questo file contiene due macro configurabili. La prima è `INTERVAL`, che definisce l'intervallo di tempo tra due messaggi inviati al cloud. La seconda è `SIMULATED_DATA`, ossia un valore booleano che indica se usare o no i dati del sensore simulato.

   Se **non si dispone del sensore**, impostare il valore `SIMULATED_DATA` su `1` per permettere all'applicazione di esempio di creare e usare i dati del sensore simulati.

1. Salvare e uscire premendo CTRL-O > Invio > CTRL-X.

### <a name="build-and-run-the-sample-application"></a>Compilare ed eseguire l'applicazione di esempio

1. Compilare l'applicazione di esempio eseguendo il comando seguente:

   ```bash
   cmake . && make
   ```
   ![Output della compilazione](media/iot-hub-raspberry-pi-kit-c-get-started/7_build-output.png)

1. Eseguire l'applicazione di esempio tramite il comando seguente:

   ```bash
   sudo ./app '<device connection string>'
   ```

   > [!NOTE] 
   Assicurarsi di copiare e incollare la stringa di connessione del dispositivo tra virgolette singole.


Dovrebbe essere visibile l'output seguente che mostra i dati del sensore e i messaggi inviati all'hub IoT.

![Output - dati del sensore inviati da Raspberry Pi all'hub IoT](media/iot-hub-raspberry-pi-kit-c-get-started/8_run-output.png)

## <a name="next-steps"></a>Passaggi successivi

È stata eseguita un'applicazione di esempio per raccogliere i dati del sensore da inviare all'hub IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

