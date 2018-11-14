---
title: Connettere un dispositivo Raspberry Pi all'applicazione Azure IoT Central (Python) | Microsoft Docs
description: Come connettere un dispositivo Raspberry Pi all'applicazione Azure IoT Central con Python in qualità di sviluppatore di dispositivi.
author: dominicbetts
ms.author: dobett
ms.date: 01/23/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 90837092390cd2550805658471ff7aa884773371
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239594"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Connettere un dispositivo Raspberry Pi all'applicazione Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Questo articolo descrive come connettere un dispositivo Raspberry Pi all'applicazione Microsoft Azure IoT Central usando il linguaggio di programmazione Python in qualità di sviluppatore di dispositivi.

## <a name="before-you-begin"></a>Prima di iniziare

Per seguire la procedura descritta in questo articolo sono necessari i componenti seguenti:

* Un'applicazione Azure IoT Central creata dal modello di applicazione **Sample Devkits**. Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).
* Un dispositivo Raspberry Pi che esegue il sistema operativo Raspbian. Per accedere all'ambiente GUI sono necessari monitor, tastiera e mouse connessi al dispositivo Raspberry Pi. Il dispositivo Raspberry Pi deve potersi [connettere a Internet](https://www.raspberrypi.org/learning/software-guide/wifi/).
* Facoltativamente, una scheda aggiuntiva [Sense Hat](https://www.raspberrypi.org/products/sense-hat/) per Raspberry Pi. Questa scheda raccoglie dati di telemetria da diversi sensori per inviarli all'applicazione Azure IoT Central. Se non si ha una scheda **Sense Hat**, è possibile usare un emulatore (disponibile come parte dell'immagine di Raspberry Pi).

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
    - Proprietà del dispositivo del codice
    - Proprietà cloud della posizione

Per i dettagli completi sulla configurazione del modello del dispositivo, vedere [Dettagli del modello del dispositivo Raspberry PI](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details)
    

## <a name="add-a-real-device"></a>Aggiungere un dispositivo reale

Nell'applicazione Azure IoT Central aggiungere un dispositivo reale dal modello di dispositivo **Raspberry Pi** e prendere nota dei dettagli relativi alla connessione del dispositivo (**ID ambito, ID dispositivo, chiave primaria**). Per altre informazioni, vedere [Aggiungere un dispositivo reale all'applicazione Azure IoT Central](tutorial-add-device.md).


### <a name="configure-the-raspberry-pi"></a>Configurare il dispositivo Raspberry Pi

I passaggi seguenti descrivono come scaricare e configurare l'applicazione di esempio Python da GitHub. L'applicazione di esempio:

* Invia i dati di telemetria e i valori delle proprietà ad Azure IoT Central.
* Risponde alle modifiche delle impostazioni apportate in Azure IoT Central.

Per configurare il dispositivo, [seguire le istruzioni dettagliate su GitHub.](https://aka.ms/iotcentral-docs-Raspi-releases)


> [!NOTE]
> Per altre informazioni sull'esempio Python di Raspberry Pi, vedere il file [README](https://aka.ms/iotcentral-docs-Raspi-releases) su GitHub.


1. Dopo la configurazione il dispositivo inizia a inviare momentaneamente dati ad Azure IoT Central.
1. Nell'applicazione Azure IoT Central si può vedere in che modo il codice eseguito sul dispositivo Raspberry Pi interagisce con l'applicazione:

    * Nella pagina **Measurements** (Misure) per il dispositivo reale è possibile vedere i dati di telemetria inviati dal dispositivo Raspberry Pi. Se si usa **Sense HAT Emulator**, è possibile modificare i valori dei dati di telemetria nella GUI sul dispositivo Raspberry Pi.
    * Nella pagina **Properties** (Proprietà) è possibile vedere il valore dichiarato per la proprietà **Die Number** (Numero modello).
    * Nella pagina **Settings** (Impostazioni) è possibile modificare diverse impostazioni nel dispositivo Raspberry Pi, ad esempio la tensione e la velocità della ventola. Quando Raspberry Pi riconosce la modifica, l'impostazione mostra l'indicatore **synced** (sincronizzato) in Azure IoT Central.


## <a name="raspberry-pi-device-template-details"></a>Dettagli del modello del dispositivo Raspberry PI

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

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per collegare il dispositivo Raspberry Pi all'applicazione Azure IoT Central, ecco i passi successivi suggeriti:

* [Connettere un'applicazione client Node.js generica ad Azure IoT Central](howto-connect-nodejs.md)
