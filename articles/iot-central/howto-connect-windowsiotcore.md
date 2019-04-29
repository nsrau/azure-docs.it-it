---
title: Connettere un dispositivo Windows IoT Core all'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni su come connettere un dispositivo MXChip IoT DevKit all'applicazione Azure IoT Central in qualità di sviluppatore di dispositivi.
author: miriambrus
ms.author: miriamb
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: af6d66d2e3eae80477a151323578b930dcd7727a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60886642"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Connettere un dispositivo Windows IoT Core all'applicazione Azure IoT Central

Questo articolo descrive come connettere un dispositivo Windows IoT Core all'applicazione Microsoft Azure IoT Central in qualità di sviluppatore di dispositivi.

## <a name="before-you-begin"></a>Prima di iniziare

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

- Un'applicazione Azure IoT Central creata dal modello di applicazione **Sample Devkits**. Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).

- Un dispositivo che esegue il sistema operativo Windows 10 IoT Core. Per altre informazioni, vedere [configurazione del dispositivo Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

- Un computer di sviluppo con [Node. js](https://nodejs.org/) version 8.0.0 installato o versione successiva. Per controllare la versione, è possibile eseguire `node --version` nella riga di comando. Node.js è disponibile per un'ampia gamma di sistemi operativi.

## <a name="the-sample-devkits-application"></a>L'applicazione di esempio Devkits

Un'applicazione creata dal modello di applicazione **Sample Devkits** include un modello di dispositivo **Windows IoT Core** con le caratteristiche seguenti:

- Misurazioni di dati di telemetria per il dispositivo: **Umidità**, **temperatura**, e **pressione**.
- Impostazione per controllare **velocità della ventola**.
- Una proprietà del dispositivo **morire numeri** e una proprietà cloud **posizione**.

Per informazioni dettagliate sulla configurazione del modello del dispositivo, vedere [dettagli del modello per dispositivi Windows IoT Core](#device-template-details).

## <a name="add-a-real-device"></a>Aggiungere un dispositivo reale

Nell'applicazione Azure IoT Central, usare il **Device Explorer** pagina per aggiungere un dispositivo reale dalle **Windows 10 IoT Core** modello del dispositivo. Prendere nota del dispositivo i dettagli della connessione (**ambito ID**, **ID dispositivo**, e **chiave primaria**). Per altre informazioni, vedere [ottenere le informazioni di connessione](howto-generate-connection-string.md#get-connection-information).

## <a name="prepare-the-device"></a>Preparare il dispositivo

Per il dispositivo per la connessione a IoT Central, è necessario una stringa di connessione.

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

Per il codice del dispositivo per accedere alla stringa di connessione, salvarlo in un file denominato **connection.string.iothub** nella cartella `C:\Data\Users\DefaultAccount\Documents\` nel dispositivo Windows 10 IoT Core.

Per copiare il **connection.string.iothub** file dal computer desktop per il `C:\Data\Users\DefaultAccount\Documents\` cartella nel dispositivo, è possibile usare i [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Usare il web browser per passare alla finestra di di Windows Device Portal nel dispositivo.
1. Per esaminare i file nel dispositivo, scegliere **le app > Esplora File**.
1. Passare a **Folders\Documents utente**. Caricare quindi il **connection.string.iothub** file:

    ![Caricamento stringa di connessione](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>Distribuire ed eseguire

Per distribuire ed eseguire l'applicazione di esempio nel dispositivo, è possibile usare la [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Usare il web browser per passare alla finestra di di Windows Device Portal nel dispositivo.
1. Distribuire ed eseguire la **Client dell'Hub IoT di Azure** dell'applicazione, scegliere **le app > esempi di rapida esecuzione**. Quindi scegliere **Client dell'Hub IoT di Azure**.
1. Quindi scegliere **distribuire ed eseguire**.

    ![Distribuire ed eseguire](media/howto-connect-windowsiotcore/quick-run.png)

Dopo un paio di minuti, è possibile visualizzare i dati di telemetria dal dispositivo nell'applicazione IoT Central.

Il [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) include strumenti che è possibile usare per risolvere i problemi del dispositivo:

- Il **Apps manager** pagina consente di controllare le App in esecuzione nel dispositivo.
- Se non si dispone di un monitor connesso al dispositivo, è possibile usare la **le impostazioni del dispositivo** pagina per acquisire schermate da un dispositivo. Ad esempio: 

    ![Schermata di App](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>Scaricare il codice sorgente

Se si desidera esplorare e modificare il codice sorgente per l'applicazione client, è possibile scaricarlo dal [repository GitHub degli esempi Windows-iotcore](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients).

## <a name="device-template-details"></a>Dettagli modello dispositivo

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

### <a name="properties"></a>Properties

| Type            | Nome visualizzato | Nome campo | Tipo di dati |
| --------------- | ------------ | ---------- | --------- |
| Proprietà dispositivo | Numero stampo   | dieNumber  | number    |
| Text            | Località     | location   | N/D       |

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come connettere Raspberry Pi all'applicazione Azure IoT Central, il passaggio successivo consigliato sia per informazioni su come [configurare un modello di dispositivo personalizzata](howto-set-up-template.md) per dispositivo IoT.