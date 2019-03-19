---
title: Connettere un dispositivo Windows IoT Core all'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni su come connettere un dispositivo MXChip IoT DevKit all'applicazione Azure IoT Central in qualità di sviluppatore di dispositivi.
author: miriambrus
ms.author: miriamb
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 85fba27c856561eb1270e719dcf24b88d2d5a01f
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57309911"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Connettere un dispositivo Windows IoT Core all'applicazione Azure IoT Central

Questo articolo descrive come connettere un dispositivo Windows IoT Core all'applicazione Microsoft Azure IoT Central in qualità di sviluppatore di dispositivi.

## <a name="before-you-begin"></a>Prima di iniziare

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

1. Un'applicazione Azure IoT Central creata dal modello di applicazione **Sample Devkits**. Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).
2. Un dispositivo che esegue il sistema operativo Windows 10 IoT Core. Per questa procedura dettagliata si userà un dispositivo Raspberry Pi.


## <a name="sample-devkits-application"></a>Applicazione **Sample Devkits**

Un'applicazione creata dal modello di applicazione **Sample Devkits** include un modello di dispositivo **Windows IoT Core** con le caratteristiche seguenti: 

- I dati di telemetria che contengono le misurazioni del dispositivo **umidità**, **temperatura** e **pressione**. 
- Impostazioni che mostrano la **velocità della ventola**.
- Le proprietà contengono la proprietà del dispositivo **numero stampo** e la proprietà del cloud **posizione**.


Per i dettagli completi sulla configurazione del modello del dispositivo, vedere [Dettagli del modello del dispositivo di Windows IoT Core](howto-connect-windowsiotcore.md#windows-iot-core-device-template-details)

## <a name="add-a-real-device"></a>Aggiungere un dispositivo reale

Nell'applicazione Azure IoT Central aggiungere un dispositivo reale dal modello di dispositivo **Windows IoT Core** e prendere nota della stringa di connessione del dispositivo. Per altre informazioni, vedere [Aggiungere un dispositivo reale all'applicazione Azure IoT Central](tutorial-add-device.md).

### <a name="prepare-the-windows-iot-core-device"></a>Preparare il dispositivo Windows IoT Core

Per configurare un dispositivo Windows IoT Core, seguire la guida dettagliata in [Dettagli del modello del dispositivo Windows IoT Core](https://github.com/Azure/iot-central-firmware/tree/master/WindowsIoT#setup-a-physical-device).

### <a name="add-a-real-device"></a>Aggiungere un dispositivo reale

Nell'applicazione Azure IoT Central aggiungere un dispositivo reale dal modello di dispositivo **Windows IoT Core** e prendere nota dei dettagli relativi alla connessione del dispositivo (**ID ambito, ID dispositivo, chiave primaria**). Per altre informazioni, vedere [Aggiungere un dispositivo reale all'applicazione Azure IoT Central](tutorial-add-device.md).

 > [!NOTE]
   > Azure IoT Central ha eseguito la transizione all'utilizzo del servizio Azure IoT Hub Device Provisioning (DPS) per tutte le connessioni del dispositivo, seguire queste istruzioni per [ottenere la stringa di connessione dispositivo](concepts-connectivity.md#get-a-connection-string) e continuare con il resto dell'esercitazione.

## <a name="prepare-the-windows-10-iot-core-device"></a>Preparare il dispositivo Windows 10 IoT Core

### <a name="what-youll-need"></a>Elementi necessari

Per configurare un dispositivo reale Windows 10 IoT Core, è necessario innanzitutto avere un dispositivo che esegue Windows 10 IoT Core. Informazioni su come configurare un dispositivo Windows 10 IoT Core sono disponibili [qui](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

È necessaria anche un'applicazione client in grado di comunicare con Azure IoT Central. È possibile compilare un'applicazione personalizzata con Azure SDK e distribuirla in un dispositivo con Visual Studio oppure è possibile scaricare un [esempio precompilato](https://developer.microsoft.com/windows/iot/samples) e semplicemente distribuirlo ed eseguirlo sul dispositivo. 

### <a name="deploying-the-sample-client-application"></a>Distribuzione dell'applicazione client di esempio

Per distribuire l'applicazione client del passaggio precedente sul dispositivo Windows 10 IoT, prepararla completando le operazioni seguenti:

**Verificare che la stringa di connessione sia archiviata nel dispositivo in modo che l'applicazione client possa usarla**
* Sul desktop salvare la stringa di connessione in un file di testo denominato connection.string.iothub.
* Copiare il file di testo nella cartella documenti del dispositivo: `[device-IP-address]\C$\Data\Users\DefaultAccount\Documents\connection.string.iothub`

Dopo averlo fatto, sarà necessario aprire il [portale dei dispositivi Windows](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) digitando http://[indirizzo-IP-del-dispositivo]:8080 in qualsiasi browser.

Da questo momento, come indicato sotto, sarà necessario:
1. Espandere la **app** nodo a sinistra.
2. Selezionare **esempi di rapida esecuzione**.
3. Selezionare **Client dell'Hub IoT Azure**.
4. Selezionare **distribuire ed eseguire**.

![GIF del client hub IoT nel portale dei dispositivi Windows](./media/howto-connect-windowsiotcore/iothubapp.gif)

Al termine l'applicazione sarà avviata sul dispositivo e avrà un aspetto simile al seguente:

![Schermata dell'app client hub IoT](./media/howto-connect-windowsiotcore/IoTHubForegroundClientScreenshot.png)

In Azure IoT Central si può vedere in che modo il codice eseguito sul dispositivo Raspberry Pi interagisce con l'applicazione:

* Nella pagina **Measurements** (Misure) per il dispositivo reale è possibile vedere dati di telemetria.
* Nella pagina **Properties** (Proprietà) è possibile vedere il valore dichiarato per la proprietà Die Number (Numero modello).
* Nella pagina **Settings** (Impostazioni) è possibile modificare diverse impostazioni nel dispositivo Raspberry Pi, ad esempio la tensione e la velocità della ventola.

## <a name="download-the-source-code"></a>Scaricare il codice sorgente

Se si vuole esplorare e modificare il codice sorgente per l'applicazione client, è possibile scaricarlo da GitHub [qui](https://github.com/Microsoft/Windows-iotcore-samples/tree/develop/Samples/Azure/IoTHubClients). Se si prevede di modificare il codice, è opportuno seguire queste istruzioni nel file Leggimi [qui](https://github.com/Microsoft/Windows-iotcore-samples) per il sistema operativo desktop.

> [!NOTE]
> Se **git** non è installato nell'ambiente di sviluppo, è possibile scaricarlo da [https://git-scm.com/download](https://git-scm.com/download).

## <a name="windows-iot-core-device-template-details"></a>Dettagli del modello del dispositivo Windows IoT Core

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
