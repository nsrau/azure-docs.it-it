---
title: Connettere un dispositivo Raspberry Pi all'applicazione Azure IoT Central (Python) | Microsoft Docs
description: Gli sviluppatori di dispositivo come connettere un dispositivo Raspberry Pi all'applicazione di Azure IoT Central usando Python.
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 6ac16651e2d49dd903ff994b18a8f571bd92fbf6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272360"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Connettere un dispositivo Raspberry Pi all'applicazione Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Questo articolo descrive come connettere un dispositivo Raspberry Pi all'applicazione Microsoft Azure IoT Central usando il linguaggio di programmazione Python in qualità di sviluppatore di dispositivi.

## <a name="before-you-begin"></a>Prima di iniziare

Per seguire la procedura descritta in questo articolo sono necessari i componenti seguenti:

* Un'applicazione Azure IoT Central creata dal modello di applicazione **Sample Devkits**. Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).
* Un dispositivo Raspberry Pi che esegue il sistema operativo Raspbian. Il dispositivo Raspberry Pi deve essere in grado di connettersi a internet. Per altre informazioni, vedere [configurazione di Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="sample-devkits-application"></a>Applicazione **Sample Devkits**

Un'applicazione creata dal modello di applicazione **Sample Devkits** include un modello di dispositivo **Raspberry Pi** con le caratteristiche seguenti:

- I dati di telemetria includono le misure seguenti, che verranno raccolte dal dispositivo:
  - Umidità
  - Temperatura
  - Pressione
  - Magnetometro (X, Y, Z)
  - Accelerometro (X, Y, Z)
  - Giroscopio (X, Y, Z)
- Impostazioni
  - Tensione
  - Current
  - Velocità della ventola
  - Attiva/Disattiva runtime di integrazione.
- Properties
  - Proprietà Numero stampo del dispositivo
  - Proprietà cloud della posizione

Per i dettagli completi della configurazione del modello di dispositivo, vedere la [dettagli del dispositivo Raspberry Pi dispositivo modello](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Aggiungere un dispositivo reale

Nell'applicazione Azure IoT Central, aggiungere un dispositivo reale dal **Raspberry Pi** modello del dispositivo. Prendere nota del dispositivo i dettagli della connessione (**ambito ID**, **ID dispositivo**, e **chiave primaria**). Per altre informazioni, vedere [Aggiungere un dispositivo reale all'applicazione Azure IoT Central](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Configurare il dispositivo Raspberry Pi

I passaggi seguenti descrivono come scaricare e configurare l'applicazione di esempio Python da GitHub. L'applicazione di esempio:

* Invia i dati di telemetria e i valori delle proprietà ad Azure IoT Central.
* Risponde alle modifiche delle impostazioni apportate in Azure IoT Central.

Per configurare il dispositivo [seguire le istruzioni dettagliate su GitHub](https://github.com/Azure/iot-central-firmware/blob/master/RaspberryPi/README.md).

1. Quando il dispositivo è configurato, il dispositivo inizia a inviare le misurazioni di dati di telemetria per Azure IoT Central.
1. Nell'applicazione Azure IoT Central si può vedere in che modo il codice eseguito sul dispositivo Raspberry Pi interagisce con l'applicazione:

    * Nella pagina **Measurements** (Misure) per il dispositivo reale è possibile vedere i dati di telemetria inviati dal dispositivo Raspberry Pi.
    * Nel **impostazioni** pagina, è possibile modificare le impostazioni in Raspberry Pi, quali velocità tensione e ventola. Quando Raspberry Pi invia un acknowledgement per la modifica, l'impostazione viene illustrato come **sincronizzati**.

## <a name="raspberry-pi-device-template-details"></a>Raspberry Pi dispositivi i dettagli del modello

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

| Type            | Nome visualizzato | Nome campo | Tipo di dati |
| --------------- | ------------ | ---------- | --------- |
| Proprietà dispositivo | Numero stampo   | dieNumber  | number    |
| Text            | Località     | location   | N/D       |

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come connettere Raspberry Pi all'applicazione Azure IoT Central, ecco i passaggi successivi consigliati:

* [Connettere un'applicazione client Node. js generica per Azure IoT Central](howto-connect-nodejs.md)
