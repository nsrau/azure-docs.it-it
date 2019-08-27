---
title: Connettere un dispositivo SensorTile. box all'applicazione IoT Central di Azure | Microsoft Docs
description: In qualità di sviluppatore di dispositivi, informazioni su come connettere un dispositivo SensorTile. box all'applicazione IoT Central di Azure.
author: sarahhubbard
ms.author: sahubbar
ms.date: 08/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: 3d804b1e14d1b79266a74340e8682f1bf03d8f30
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050548"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>Connettere il dispositivo SensorTile. box all'applicazione IoT Central di Azure

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Questo articolo descrive come lo sviluppatore di dispositivi deve connettere un dispositivo SensorTile. box all'applicazione Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare i passaggi descritti in questo articolo, sono necessarie le risorse seguenti:

* Un dispositivo SensorTile. box. Per ulteriori informazioni, vedere [SensorTile. Box](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html).
* L'app ST BLE Sensor installata nel dispositivo Android può [essere scaricata da qui](https://play.google.com/store/apps/details?id=com.st.bluems). Per ulteriori informazioni, visitare: [Sensore ST BLE](https://www.st.com/stblesensor)
* Un'applicazione IoT Central di Azure creata dal modello di applicazione **DevKits** . Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).
* Aggiungere il modello di dispositivo **SensorTile. Box** nell'applicazione IoT Central visitando la pagina **modelli di dispositivo** , facendo clic su **+ nuovo**e selezionando il modello **SensorTile. Box** .

### <a name="get-your-device-connection-details"></a>Ottenere i dettagli della connessione del dispositivo

Nell'applicazione IoT Central di Azure aggiungere un dispositivo reale dal modello di dispositivo **SensorTile. Box** e prendere nota dei dettagli della connessione del dispositivo: **ID ambito**, **ID dispositivo**e **chiave primaria**:

1. Aggiungere un dispositivo dai dispositivi. Selezionare **+ nuovo > reale** per aggiungere un dispositivo reale.

    * Immettere un **ID dispositivo**minuscolo o usare l' **ID dispositivo**suggerito.
    * Immettere un **nome di dispositivo**o usare il nome suggerito

    ![Aggiungi dispositivo](media/howto-connect-sensortile/real-device.png)

1. Per ottenere i dettagli di connessione del dispositivo, **ID ambito**, **ID dispositivo**e **chiave primaria**, selezionare **Connetti** nella pagina dispositivo.

    ![Dettagli connessione](media/howto-connect-sensortile/connect-device.png)

1. Prendere nota dei dettagli della connessione. Si è temporaneamente disconnessi da Internet quando si prepara il dispositivo DevKit nel passaggio successivo.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>Configurare SensorTile. Box con l'applicazione per dispositivi mobili

Questa sezione illustra come eseguire il push del firmware dell'applicazione nel dispositivo. È quindi possibile inviare i dati del dispositivo a IoT Central tramite l'app per dispositivi mobili sensore di ST BLE usando la connettività Bluetooth a basso consumo (BLE).

1. Aprire l'app ST BLE Sensor e premere il pulsante **Crea una nuova app** .

    ![Crea app](media/howto-connect-sensortile/create-app.png)

1. Selezionare l'applicazione **barometro** .
1. Premere il pulsante carica.

    ![Caricamento barometro](media/howto-connect-sensortile/barometer-upload.png)

1. Premere il pulsante Play associato a SensorTile. box.
1. Al termine del processo, SensorTile. Box trasmette la temperatura, la pressione e l'umidità rispetto a BLE.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>Connettere SensorTile. box al cloud

In questa sezione viene illustrato come connettere SensorTile. box all'applicazione per dispositivi mobili e come connettere l'applicazione per dispositivi mobili al cloud.

1. Utilizzando il menu a sinistra, selezionare il pulsante **registrazione cloud** .

    ![Registrazione cloud](media/howto-connect-sensortile/cloud-logging.png)

1. Selezionare **Azure IOT Central** come provider di servizi cloud.
1. Inserire l'ID del dispositivo e l'ID ambito che sono stati annotati in precedenza.

    ![Credenziali](media/howto-connect-sensortile/credentials.png)

1. Selezionare il pulsante di opzione **chiave applicazione** .
1. Fare clic su **Connetti** e selezionare i dati di telemetria che si desidera caricare.
1. Dopo alcuni secondi, i dati vengono visualizzati nel dashboard dell'applicazione IoT Central.

## <a name="sensortilebox-device-template-details"></a>Dettagli del modello di dispositivo SensorTile. Box

Applicazione creata dal modello di dispositivo SensorTile. Box con le caratteristiche seguenti:

### <a name="telemetry"></a>Telemetria

| Nome campo     | Unità  | Minima | Massima | Cifre decimali |
| -------------- | ------ | ------- | ------- | -------------- |
| umidità       | %      | 30       | 90     | 1              |
| temp           | °C     | 0     | 40     | 1              |
| pressure       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | DPS   | -3276   | 3276    | 1              |
| gyroscopeY     | DPS   | -3276   | 3276    | 1              |
| gyroscopeZ     | DPS   | -3276   | 3276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come connettere un SensorTile. box all'applicazione IoT Central di Azure, il passaggio successivo suggerito consiste nell'apprendere [come configurare un modello di dispositivo personalizzato](howto-set-up-template.md) per il proprio dispositivo.
