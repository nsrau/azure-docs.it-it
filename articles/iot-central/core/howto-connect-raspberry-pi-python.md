---
title: Connettere un dispositivo Raspberry Pi all'applicazione Azure IoT Central (Python) | Microsoft Docs
description: Come sviluppatore di dispositivi, come connettere un Raspberry Pi all'applicazione IoT Central di Azure usando Python.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 258410bcd4f916ac381188bb38d90a3b89c87c89
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954240"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Connettere un dispositivo Raspberry Pi all'applicazione Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Questo articolo descrive come connettere un dispositivo Raspberry Pi all'applicazione Microsoft Azure IoT Central usando il linguaggio di programmazione Python in qualità di sviluppatore di dispositivi.

## <a name="before-you-begin"></a>Prima di iniziare

Per seguire la procedura descritta in questo articolo sono necessari i componenti seguenti:

* Un'applicazione Azure IoT Central creata dal modello di applicazione **Sample Devkits**. Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).
* Un dispositivo Raspberry Pi che esegue il sistema operativo Raspbian. Raspberry PI deve essere in grado di connettersi a Internet. Per ulteriori informazioni, vedere [la pagina relativa alla configurazione di Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

> [!TIP]
> Per informazioni sull'impostazione e sulla connessione a un dispositivo Raspberry Pi, vedere [Introduzione a Raspberry Pi](https://projects.raspberrypi.org/en/pathways/getting-started-with-raspberry-pi)

## <a name="sample-devkits-application"></a>Applicazione **Sample Devkits**

Un'applicazione creata dal modello di applicazione **Sample Devkits** include un modello di dispositivo **Raspberry Pi** con le caratteristiche seguenti:

- I dati di telemetria includono le misure seguenti, che verranno raccolte dal dispositivo:
  - Umidità
  - Temperatura
  - Pressure (Pressione)
  - Magnetometro (X, Y, Z)
  - Accelerometro (X, Y, Z)
  - Giroscopio (X, Y, Z)
- Impostazioni
  - Tensione
  - Current
  - Velocità della ventola
  - Attiva/Disattiva runtime di integrazione.
- properties
  - Proprietà Numero stampo del dispositivo
  - Proprietà cloud della posizione

Per i dettagli completi della configurazione del modello di dispositivo, vedere i [Dettagli del modello di dispositivo Raspberry Pi](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Aggiungere un dispositivo reale

Nell'applicazione IoT Central di Azure aggiungere un dispositivo reale dal modello di dispositivo **Raspberry Pi** . Prendere nota dei dettagli della connessione del dispositivo (**ID ambito**, **ID dispositivo**e **chiave primaria**). Per altre informazioni, vedere [Aggiungere un dispositivo reale all'applicazione Azure IoT Central](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Configurare il dispositivo Raspberry Pi

I passaggi seguenti descrivono come scaricare e configurare l'applicazione di esempio Python da GitHub. L'applicazione di esempio:

* Invia i dati di telemetria e i valori delle proprietà ad Azure IoT Central.
* Risponde alle modifiche delle impostazioni apportate in Azure IoT Central.

1. Connettersi a un ambiente shell nel dispositivo Raspberry Pi, dal desktop Raspberry Pi o in remoto tramite SSH.

1. Eseguire il comando seguente per installare il client di IoT Central Python:

    ```bash
    pip install iotc
    ```

1. Scaricare il codice Python di esempio:

    ```bash
    curl -O https://raw.githubusercontent.com/Azure/iot-central-firmware/master/RaspberryPi/app.py
    ```

1. Modificare il file di `app.py` scaricato e sostituire i segnaposto `DEVICE_ID`, `SCOPE_ID`e `PRIMARY/SECONDARY device KEY` con i valori di connessione annotati in precedenza. Salvare le modifiche.

    > [!TIP]
    > Nella shell di Raspberry Pi è possibile usare gli editor di testo **nano** o **vi** .

1. Usare il comando seguente per eseguire l'esempio:

    ```bash
    python app.py
    ```

    Raspberry Pi inizia a inviare misure di telemetria ad Azure IoT Central.

1. Nell'applicazione Azure IoT Central si può vedere in che modo il codice eseguito sul dispositivo Raspberry Pi interagisce con l'applicazione:

    * Nella pagina **Measurements** (Misure) per il dispositivo reale è possibile vedere i dati di telemetria inviati dal dispositivo Raspberry Pi.
    * Nella pagina **Proprietà** è possibile visualizzare la proprietà del dispositivo **numero di matrici** .
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

| Nome visualizzato | Nome campo | Unità | Cifre decimali | Minima | Massima | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Tensione      | setVoltage | Volt | 0              | 0       | 240     | 0       |
| Current      | setCurrent | Amp  | 0              | 0       | 100     | 0       |
| Velocità della ventola    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Impostazioni attivazione/disattivazione

| Nome visualizzato | Nome campo | Testo attivato | Testo disattivato | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ATTIVA      | DISATTIVA      | Off     |

### <a name="properties"></a>properties

| Type            | Nome visualizzato | Nome campo | Tipo di dati |
| --------------- | ------------ | ---------- | --------- |
| Proprietà dispositivo | Numero stampo   | dieNumber  | d'acquisto    |
| Testo            | Località     | location   | N/D       |

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come connettere un dispositivo Raspberry Pi all'applicazione IoT Central di Azure, il passaggio successivo suggerito consiste nell'apprendere come [configurare un modello di dispositivo personalizzato](howto-set-up-template.md) per il proprio dispositivo.
