---
title: Connettere un dispositivo SensorTile.box all'applicazione Azure IoT Central | Microsoft Docs
description: Uno sviluppatore di dispositivi, informazioni su come connettere un dispositivo SensorTile.box all'applicazione Azure IoT Central.
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: 8c1b4a4ab834b2203a7e0b6e4e9e366c3fc38774
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472209"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>Connettere dispositivo SensorTile.box all'applicazione Azure IoT Central

Questo articolo descrive come, come uno sviluppatore di dispositivi, connettere un dispositivo SensorTile.box all'applicazione di Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare la procedura descritta in questo articolo, sono necessarie le risorse seguenti:

* Un dispositivo SensorTile.box. Per altre informazioni, vedere [SensorTile.box](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html).
* ST BLE sensore installata l'app nel dispositivo Android, è possibile [scaricarlo da qui](https://play.google.com/store/apps/details?id=com.st.bluems). Per altre informazioni, visitare: [Sensore BLE ST](https://www.st.com/stblesensor)
* Un'applicazione Azure IoT Central creata mediante il **DevKits** modello dell'applicazione. Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).
* Aggiungere il **SensorTile.box** modello del dispositivo nell'applicazione IoT Central, visitare il **dei modelli di dispositivo** pagina, fare clic su **+ nuovo**e selezionando il **SensorTile.box** modello.

### <a name="get-your-device-connection-details"></a>Ottenere i dettagli della connessione del dispositivo

Nell'applicazione Azure IoT Central, aggiungere un dispositivo reale dal **SensorTile.box** modello del dispositivo e prendere nota dei dettagli della connessione dispositivo: **Definire l'ambito ID**, **ID dispositivo**, e **chiave primaria**:

1. Aggiungere un dispositivo da Device Explorer. Selezionare **+ nuovo > reale** per aggiungere un dispositivo reale.

    * Immettere una minuscola **ID dispositivo**, oppure usare suggeriti **ID dispositivo**.
    * Immettere un **nome dispositivo**, oppure usare il nome suggerito

    ![Aggiungi dispositivo](media/howto-connect-sensortile/real-device.png)

1. Per ottenere i dettagli di connessione, il dispositivo **ambito ID**, **ID dispositivo**, e **Primary key**, selezionare **Connect** nella pagina del dispositivo.

    ![Dettagli connessione](media/howto-connect-sensortile/connect-device.png)

1. Prendere nota dei dettagli della connessione. Si è temporaneamente disconnessi da internet quando si prepara il dispositivo DevKit nel passaggio successivo.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>Configurare il SensorTile.box con l'applicazione per dispositivi mobili

In questa sezione descrive come eseguire il push del firmware di applicazione nel dispositivo. È quindi come inviare i dati del dispositivo a IoT Central tramite l'app per dispositivi mobili ST BLE sensore connettività Bluetooth Low Energy (BLE).

1. Aprire l'app ST BLE sensore e premere il **creare una nuova app** pulsante.

    ![Crea app](media/howto-connect-sensortile/create-app.png)

1. Selezionare il **barometro della rigidità** dell'applicazione.
1. Premere il pulsante di caricamento.

    ![Caricamento barometro della rigidità](media/howto-connect-sensortile/barometer-upload.png)

1. Premere il pulsante play associato il SensorTile.box.
1. Una volta completato il processo, il SensorTile.box trasmette la temperatura, umidità e pressione su BLE.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>Connettere il SensorTile.box al cloud

In questa sezione descrive come connettere il SensorTile.box all'applicazione per dispositivi mobili, quindi connettere l'applicazione per dispositivi mobili nel cloud.

1. Utilizzando il menu a sinistra, selezionare la **Cloud registrazione** pulsante.

    ![Registrazione di cloud](media/howto-connect-sensortile/cloud-logging.png)

1. Selezionare **Azure IoT Central** come provider di servizi cloud.
1. Inserire l'ID dispositivo e l'ID ambito che sono stati indicati in precedenza.

    ![Credenziali](media/howto-connect-sensortile/credentials.png)

1. Selezionare il **chiave dell'applicazione** pulsante di opzione.
1. Fare clic su **Connect** e selezionare i dati di telemetria da caricare.
1. Dopo alcuni secondi, i dati vengono visualizzati nel dashboard dell'applicazione IoT Central.

## <a name="sensortilebox-device-template-details"></a>Dettagli modello dispositivo SensorTile.box

Un'applicazione creata dal modello di dispositivo SensorTile.box con le caratteristiche seguenti:

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
| gyroscopeX     | dps   | -3276   | 3276    | 1              |
| gyroscopeY     | dps   | -3276   | 3276    | 1              |
| gyroscopeZ     | dps   | -3276   | 3276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come connettere un SensorTile.box all'applicazione Azure IoT Central, il passaggio successivo consigliato consiste Scopri [come configurare un modello di dispositivo personalizzata](howto-set-up-template.md) per dispositivo IoT.
