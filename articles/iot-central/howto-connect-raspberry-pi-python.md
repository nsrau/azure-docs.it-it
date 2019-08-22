---
title: Connettere un dispositivo Raspberry Pi all'applicazione Azure IoT Central (Python) | Microsoft Docs
description: Come sviluppatore di dispositivi, come connettere un Raspberry Pi all'applicazione IoT Central di Azure usando Python.
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: bd506bf1210692feb017f3b526c3b6d4bca36004
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877433"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Connettere un dispositivo Raspberry Pi all'applicazione Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Questo articolo descrive come connettere un dispositivo Raspberry Pi all'applicazione Microsoft Azure IoT Central usando il linguaggio di programmazione Python in qualità di sviluppatore di dispositivi.

## <a name="before-you-begin"></a>Prima di iniziare

Per seguire la procedura descritta in questo articolo sono necessari i componenti seguenti:

* Un'applicazione Azure IoT Central creata dal modello di applicazione **Sample Devkits**. Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).
* Un dispositivo Raspberry Pi che esegue il sistema operativo Raspbian. Raspberry PI deve essere in grado di connettersi a Internet. Per ulteriori informazioni, vedere [la pagina relativa alla configurazione di Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

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
  - Corrente
  - Velocità della ventola
  - Attiva/Disattiva runtime di integrazione.
- Properties
  - Proprietà Numero stampo del dispositivo
  - Proprietà cloud della posizione

Per i dettagli completi della configurazione del modello di dispositivo, vedere i [Dettagli del modello di dispositivo Raspberry Pi](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Aggiungere un dispositivo reale

Nell'applicazione IoT Central di Azure aggiungere un dispositivo reale dal modello di dispositivo **Raspberry Pi** . Prendere nota dei dettagli della connessione del dispositivo (**ID ambito**, **ID dispositivo**e **chiave primaria**). Per altre informazioni, vedere [Aggiungere un dispositivo reale all'applicazione Azure IoT Central](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Configurare il dispositivo Raspberry Pi

I passaggi seguenti descrivono come scaricare e configurare l'applicazione di esempio Python da GitHub. L'applicazione di esempio:

* Invia i dati di telemetria e i valori delle proprietà ad Azure IoT Central.
* Risponde alle modifiche delle impostazioni apportate in Azure IoT Central.

Per configurare il dispositivo, [seguire le istruzioni dettagliate su GitHub](https://github.com/Azure/iot-central-firmware/blob/master/RaspberryPi/README.md).

1. Quando il dispositivo è configurato, il dispositivo inizia a inviare le misurazioni di telemetria ad Azure IoT Central.
1. Nell'applicazione Azure IoT Central si può vedere in che modo il codice eseguito sul dispositivo Raspberry Pi interagisce con l'applicazione:

    * Nella pagina **Measurements** (Misure) per il dispositivo reale è possibile vedere i dati di telemetria inviati dal dispositivo Raspberry Pi.
    * Nella pagina **Impostazioni** è possibile modificare le impostazioni di Raspberry Pi, ad esempio la velocità di tensione e della ventola. Quando Raspberry Pi riconosce la modifica, l'impostazione viene visualizzata come **sincronizzata**.

## <a name="raspberry-pi-device-template-details"></a>Dettagli del modello di dispositivo Raspberry Pi

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

| `Display name` | Nome campo | Unità | Cifre decimali | Minima | Massima | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Tensione      | setVoltage | Volt | 0              | 0       | 240     | 0       |
| Corrente      | setCurrent | Amp  | 0              | 0       | 100     | 0       |
| Velocità della ventola    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Impostazioni attivazione/disattivazione

| `Display name` | Nome campo | Testo attivato | Testo disattivato | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | SÌ      | NO      | Off     |

### <a name="properties"></a>Properties

| Type            | `Display name` | Nome campo | Tipo di dati |
| --------------- | ------------ | ---------- | --------- |
| Proprietà dispositivo | Numero stampo   | dieNumber  | number    |
| Text            | Location     | location   | N/D       |

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come connettere un dispositivo Raspberry Pi all'applicazione IoT Central di Azure, il passaggio successivo suggerito consiste nell'apprendere come [configurare un modello di dispositivo personalizzato](howto-set-up-template.md) per il proprio dispositivo.
