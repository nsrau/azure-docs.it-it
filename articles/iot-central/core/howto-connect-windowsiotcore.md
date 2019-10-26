---
title: Connettere un dispositivo Windows IoT Core all'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni su come connettere un dispositivo MXChip IoT DevKit all'applicazione Azure IoT Central in qualità di sviluppatore di dispositivi.
author: miriambrus
ms.author: miriamb
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d880130e114b2bd3e4f978c2ae3fc7bacf0648c4
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953902"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Connettere un dispositivo Windows IoT Core all'applicazione Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Questo articolo descrive come connettere un dispositivo Windows IoT Core all'applicazione Microsoft Azure IoT Central in qualità di sviluppatore di dispositivi.

## <a name="before-you-begin"></a>Prima di iniziare

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

- Un'applicazione Azure IoT Central creata dal modello di applicazione **Sample Devkits**. Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).

- Un dispositivo che esegue il sistema operativo Windows 10 IoT Core. Per ulteriori informazioni, vedere [la pagina relativa alla configurazione del dispositivo Core Windows 10](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

- Un computer di sviluppo in cui è installato [node. js](https://nodejs.org/) versione 8.0.0 o successiva. Per controllare la versione, è possibile eseguire `node --version` nella riga di comando. Node.js è disponibile per un'ampia gamma di sistemi operativi.

## <a name="the-sample-devkits-application"></a>Applicazione devkits di esempio

Un'applicazione creata dal modello di applicazione **Sample Devkits** include un modello di dispositivo **Windows IoT Core** con le caratteristiche seguenti:

- Misurazioni di telemetria per il dispositivo: **umidità**, **temperatura**e **pressione**.
- Impostazione per controllare la **velocità della ventola**.
- **Numero** delle proprietà del dispositivo e **posizione**della proprietà del cloud.

Per informazioni dettagliate sulla configurazione del modello di dispositivo, vedere la pagina relativa ai [Dettagli del modello di dispositivo principale di Windows](#device-template-details).

## <a name="add-a-real-device"></a>Aggiungere un dispositivo reale

Nell'applicazione IoT Central di Azure, usare la pagina **Device Explorer** per aggiungere un dispositivo reale dal modello di dispositivo **Core di Windows 10** . Prendere nota dei dettagli della connessione del dispositivo (**ID ambito**, **ID dispositivo**e **chiave primaria**).

## <a name="prepare-the-device"></a>Preparare il dispositivo

Per la connessione del dispositivo a IoT Central, è necessaria una stringa di connessione:

1. Usare l'utilità della riga di comando `dps-keygen` per generare una stringa di connessione:

    Per installare l'[utilità generatore di chiavi](https://github.com/Azure/dps-keygen), eseguire il comando seguente:

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. Per generare una stringa di connessione, eseguire il comando seguente usando i dettagli della connessione annotati in precedenza:

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. Copiare la stringa di connessione dall'output di `dps-keygen` per usarla nel codice del dispositivo.

Per il codice del dispositivo per accedere alla stringa di connessione, salvarla in un file denominato **Connection. String. iothub** nella cartella `C:\Data\Users\DefaultAccount\Documents\` sul dispositivo Windows 10.

Per copiare il file **Connection. String. iothub** dal computer desktop alla cartella `C:\Data\Users\DefaultAccount\Documents\` del dispositivo, è possibile usare il [portale per dispositivi Windows](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Usare il Web browser per passare al portale per dispositivi Windows nel dispositivo.
1. Per esplorare i file nel dispositivo, scegliere **app > Esplora file**.
1. Passare a **User Folders\Documents**. Caricare quindi il file **Connection. String. iothub** :

    ![Carica stringa di connessione](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>Distribuire ed eseguire

Per distribuire ed eseguire l'applicazione di esempio nel dispositivo, è possibile usare il [portale per dispositivi Windows](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Usare il Web browser per passare al portale per dispositivi Windows nel dispositivo.
1. Per distribuire ed eseguire l'applicazione **client dell'hub Azure** , scegliere **app > esempi di esecuzione rapida**. Scegliere quindi **client Hub Azure**.
1. Quindi scegliere **Distribuisci ed Esegui**.

    ![Distribuire ed eseguire](media/howto-connect-windowsiotcore/quick-run.png)

Dopo alcuni minuti, è possibile visualizzare i dati di telemetria dal dispositivo nell'applicazione IoT Central.

Il [portale per dispositivi Windows](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) include gli strumenti che è possibile usare per risolvere i problemi relativi al dispositivo:

- La pagina **App Manager** consente di controllare le app in esecuzione nel dispositivo.
- Se non si dispone di un monitoraggio connesso al dispositivo, è possibile usare la pagina **Impostazioni dispositivo** per acquisire screenshot dal dispositivo. ad esempio:

    ![Screenshot dell'app](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>Scaricare il codice sorgente

Se si vuole esplorare e modificare il codice sorgente per l'applicazione client, è possibile scaricarlo dal [repository GitHub Windows-iotcore-Samples](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients).

## <a name="device-template-details"></a>Dettagli del modello di dispositivo

Un'applicazione creata dal modello di applicazione **Sample Devkits** include un modello di dispositivo **Windows IoT Core** con le caratteristiche seguenti:

### <a name="telemetry-measurements"></a>Misure di telemetria

| Nome campo     | Unità  | Minima | Massima | Cifre decimali |
| -------------- | ------ | ------- | ------- | -------------- |
| umidità       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Impostazioni

Impostazioni numeriche

| Nome visualizzato | Nome campo | Unità | Cifre decimali | Minima | Massima | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Velocità della ventola    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>properties

| Type            | Nome visualizzato | Nome campo | Tipo di dati |
| --------------- | ------------ | ---------- | --------- |
| Proprietà dispositivo | Numero stampo   | dieNumber  | d'acquisto    |
| Testo            | Località     | location   | N/D       |

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come connettere un dispositivo principale di Windows per l'applicazione IoT Central di Azure, il passaggio successivo suggerito consiste nell'apprendere come [configurare un modello di dispositivo personalizzato](howto-set-up-template.md) per il proprio dispositivo.
