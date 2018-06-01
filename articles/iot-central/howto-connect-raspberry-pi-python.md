---
title: Connettere un dispositivo Raspberry Pi all'applicazione Azure IoT Central (Python) | Microsoft Docs
description: Come connettere un dispositivo Raspberry Pi all'applicazione Azure IoT Central con Python in qualità di sviluppatore di dispositivi.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/23/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 23ab31d6d2357bfcb184e5b3022155bef5ace658
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199878"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Connettere un dispositivo Raspberry Pi all'applicazione Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Questo articolo descrive come connettere un dispositivo Raspberry Pi all'applicazione Microsoft Azure IoT Central usando il linguaggio di programmazione Python in qualità di sviluppatore di dispositivi.

## <a name="before-you-begin"></a>Prima di iniziare

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

* Un'applicazione Azure IoT Central creata dal modello di applicazione **Sample Devkits**. Per altre informazioni, vedere [Creare l'applicazione Azure IoT Central](howto-create-application.md).
* Un dispositivo Raspberry Pi che esegue il sistema operativo Raspbian. Per accedere all'ambiente GUI sono necessari monitor, tastiera e mouse connessi al dispositivo Raspberry Pi. Il dispositivo Raspberry Pi deve potersi [connettere a Internet](https://www.raspberrypi.org/learning/software-guide/wifi/).
* Facoltativamente, una scheda aggiuntiva [Sense Hat](https://www.raspberrypi.org/products/sense-hat/) per Raspberry Pi. Questa scheda raccoglie dati di telemetria da diversi sensori per inviarli all'applicazione Azure IoT Central. Se non si ha una scheda **Sense Hat**, è possibile usare un emulatore.

Un'applicazione creata dal modello di applicazione **Sample Devkits** include un modello di dispositivo **Raspberry Pi** con le caratteristiche seguenti:

### <a name="telemetry-measurements"></a>Misure di telemetria

| Nome campo     | Unità  | Minima | Massima | Cifre decimali |
| -------------- | ------ | ------- | ------- | -------------- |
| umidità       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Impostazioni

Impostazioni numeriche

| Nome visualizzato | Nome campo | Unità | Cifre decimali | Minima | Massima | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Tensione      | setVoltage | Volt | 0              | 0       | 240     | 0       |
| Current      | setCurrent | Amp  | 0              | 0       | 100     | 0       |
| Velocità della ventola    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Impostazioni attivazione/disattivazione

| Nome visualizzato | Nome campo | Testo attivato | Testo disattivato | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ATTIVA      | DISATTIVA      | Off     |

### <a name="properties"></a>Properties

| type            | Nome visualizzato | Nome campo | Tipo di dati |
| --------------- | ------------ | ---------- | --------- |
| Proprietà dispositivo | Numero stampo   | dieNumber  | number    |
| Text            | Località     | location   | N/D       |

### <a name="add-a-real-device"></a>Aggiungere un dispositivo reale

Nell'applicazione Azure IoT Central aggiungere un dispositivo reale dal modello di dispositivo **Raspberry Pi** e prendere nota della stringa di connessione del dispositivo. Per altre informazioni, vedere [Aggiungere un dispositivo reale all'applicazione Azure IoT Central](tutorial-add-device.md).

## <a name="configure-the-raspberry-pi"></a>Configurare il dispositivo Raspberry Pi

I passaggi seguenti descrivono come scaricare e configurare l'applicazione di esempio Python da GitHub. L'applicazione di esempio:

* Invia i dati di telemetria e i valori delle proprietà ad Azure IoT Central.
* Risponde alle modifiche delle impostazioni apportate in Azure IoT Central.

> [!NOTE]
> Per altre informazioni sull'esempio Python di Raspberry Pi, vedere il file [Leggimi](https://github.com/Microsoft/microsoft-iot-central-firmware/blob/master/RaspberryPi/README.md) su GitHub.

1. Usare il Web browser nel dispositivo Raspberry Pi desktop per passare alla pagina [Azure IoT Central firmware releases](https://github.com/Microsoft/microsoft-iot-central-firmware/releases) (Versioni firmware di Azure IoT Central).

1. Scaricare il file zip che contiene il firmware più recente nella home directory sul dispositivo Raspberry Pi. Il nome del file è simile a `RaspberryPi-IoTCentral-X.X.X.zip`.

1. Per decomprimere il file del firmware, usare **File Manager** in Raspberry Pi desktop. Fare clic con il pulsante destro del mouse sul file zip e scegliere **Estrai qui**. Questa operazione crea una cartella denominata `RaspberryPi-IoTCentral-X.X.X` nella cartella principale.

1. Se non si ha una scheda **Sense Hat** collegata al dispositivo Raspberry Pi, è necessario abilitare l'emulatore:
    1. In **File Manager**, nella cartella `RaspberryPi-IoTCentral-X.X.X`, fare clic con il pulsante destro del mouse sul file **config.iot** e scegliere **Text Editor**.
    1. Modificare la riga `"simulateSenseHat": false,` in `"simulateSenseHat": true,`.
    1. Salvare le modifiche e chiudere **Text Editor**.

1. Avviare una sessione di **Terminal** e usare il comando `cd` per passare alla cartella creata nel passaggio precedente.

1. Per avviare l'applicazione di esempio, digitare `./start.sh` nella finestra di **Terminal**. Se si usa **Sense HAT Emulator**, viene visualizzata la sua GUI. È possibile usare la GUI per modificare i valori dei dati di telemetria inviati all'applicazione Azure IoT Central.

1. La finestra di **Terminal** mostra un messaggio simile a `Device information being served at http://192.168.0.60:8080`. L'URL potrebbe essere diverso nell'ambiente in uso. Copiare l'URL e usare il Web browser per passare alla pagina di configurazione:

    ![Configurare il dispositivo](media/howto-connect-raspberry-pi-python/configure.png)

1. Immettere la stringa di connessione del dispositivo annotata quando il dispositivo reale è stato aggiunto all'applicazione Azure IoT Central. Quindi scegliere **Configure Device** (Configura dispositivo). Viene visualizzato il messaggio **Device configured, your device should start sending data to Azure IoT Central momentarily** (Il dispositivo è stato configurato e inizierà a inviare dati ad Azure IoT Central temporaneamente).

1. Nell'applicazione Azure IoT Central si può vedere in che modo il codice eseguito sul dispositivo Raspberry Pi interagisce con l'applicazione:

    * Nella pagina **Measurements** (Misure) per il dispositivo reale è possibile vedere i dati di telemetria inviati dal dispositivo Raspberry Pi. Se si usa **Sense HAT Emulator**, è possibile modificare i valori dei dati di telemetria nella GUI sul dispositivo Raspberry Pi.
    * Nella pagina **Properties** (Proprietà) è possibile vedere il valore dichiarato per la proprietà **Die Number** (Numero modello).
    * Nella pagina **Settings** (Impostazioni) è possibile modificare diverse impostazioni nel dispositivo Raspberry Pi, ad esempio la tensione e la velocità della ventola. Quando Raspberry Pi riconosce la modifica, l'impostazione mostra l'indicatore **synced** (sincronizzato) in Azure IoT Central.

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per collegare il dispositivo Raspberry Pi all'applicazione Azure IoT Central, ecco i passi successivi suggeriti:

* [Connettere un'applicazione client Node.js generica ad Azure IoT Central](howto-connect-nodejs.md)