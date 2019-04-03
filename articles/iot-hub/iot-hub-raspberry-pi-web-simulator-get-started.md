---
title: Raspberry Pi simulato al cloud (Node.js) - Connettere il simulatore Web Raspberry Pi all'hub IoT di Azure | Microsoft Docs
description: Connettere il simulatore Web Raspberry Pi all'hub IoT di Azure per permettere a Raspberry Pi di inviare dati al cloud di Azure.
author: rangv
manager: ''
keywords: simulatore raspberry pi, azure iot raspberry pi, raspberry pi hub iot, raspberry pi invia dati al cloud, raspberry pi al cloud
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 3f8fbf571efafe22d7d0c5ccfd71dded381ba6fb
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58847208"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Connettore il simulatore online Raspberry Pi all'hub IoT di Azure (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Questa esercitazione illustra le nozioni di base relative all'uso del simulatore online Raspberry Pi. In seguito illustra come connettere in modo trasparente il simulatore al cloud usando l'[hub IoT di Azure](about-iot-hub.md). 

Se si dispone di dispositivi fisici, per iniziare visitare [Connettere Raspberry Pi all'hub IoT di Azure](iot-hub-raspberry-pi-kit-node-get-started.md). 

<p>
<div id="diag" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/3_banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6_button_default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5_button_click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6_button_default.png';">
</div>

## <a name="what-you-do"></a>Operazioni da fare

* Apprendere le nozioni base del simulatore online Raspberry Pi.
* Creare un hub IoT.
* Registrare un dispositivo per Pi nel proprio hub IoT.
* Eseguire un'applicazione di esempio in Pi per inviare i dati del sensore simulato all'hub IoT.

Connettere il connettore Raspberry Pi simulato a un hub IoT creato. Eseguire un'applicazione di esempio con il simulatore per generare i dati del sensore. Infine inviare i dati del sensore all'hub IoT.

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

* Come creare un hub IoT di Azure e ottenere la stringa di connessione del nuovo dispositivo. Se non si ha un account di Azure, [creare un account di Azure gratuito](https://azure.microsoft.com/free/) in pochi minuti.
* Come utilizzare il simulatore online Raspberry Pi.
* Come inviare i dati del sensore all'hub IoT.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Panoramica del simulatore Web Raspberry Pi

Fare clic sul pulsante per avviare il simulatore online Raspberry Pi.

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Avviare il simulatore Raspberry Pi</a>

Esistono tre aree nel simulatore Web.

1. Area dell'assembly: nel circuito predefinito un Pi si connette a un sensore BME280 e a un LED. L'area è bloccata nella versione di anteprima, pertanto non è attualmente possibile eseguire la personalizzazione.

2. Area della codifica: un editor di codice online da usare con Raspberry Pi. L'applicazione di esempio predefinita consente di raccogliere i dati del sensore BME280 e di inviarli all'hub IoT di Azure. L'applicazione è completamente compatibile con dispositivi Pi effettivi. 

3. Finestra della console integrata: mostra l'output del codice. Nella parte superiore di questa finestra sono disponibili tre pulsanti.

   * **Esegui**: eseguire l'applicazione nell'area di codifica.
   * **Reimposta**: reimposta l'area di codifica sull'applicazione di esempio predefinita.
   * **Comprimi/Espandi**: sul lato destro è disponibile un pulsante per comprimere/espandere la finestra della console.

> [!NOTE]
> Il simulatore Web Raspberry Pi è ora disponibile nella versione di anteprima. Vorremmo sentire la tua voce nella [chat Gitter](https://gitter.im/Microsoft/raspberry-pi-web-simulator). Il codice sorgente è pubblico in [GitHub](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Panoramica del simulatore online Pi](media/iot-hub-raspberry-pi-web-simulator/0_overview.png)

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Ottenere la stringa di connessione per l'hub IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrare un nuovo dispositivo nell'hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>Eseguire un'applicazione di esempio nel simulatore Web Pi

1. Nell'area di codifica assicurarsi di lavorare nell'applicazione di esempio predefinita. Sostituire il segnaposto nella riga 15 con la stringa di connessione del dispositivo hub IoT di Azure.
   ![Sostituire la stringa di connessione del dispositivo](media/iot-hub-raspberry-pi-web-simulator/1_connectionstring.png)

2. Fare clic su **Esegui** o digita `npm start` per eseguire l'applicazione.

Dovrebbe venire visualizzato l'output seguente che mostra i dati del sensore e i messaggi inviati all'hub IoT ![Output: dati del sensore inviati da Raspberry Pi all'hub IoT](media/iot-hub-raspberry-pi-web-simulator/2_run_application.png)

## <a name="read-the-messages-received-by-your-hub"></a>Leggere i messaggi ricevuti dall'hub

Un modo per monitorare i messaggi ricevuti dall'hub IoT dal dispositivo simulato è usare gli strumenti di IoT di Azure per Visual Studio Code. Per altre informazioni, vedere [utilizza gli strumenti di IoT di Azure per Visual Studio Code inviare e ricevere messaggi tra il dispositivo e l'IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Per altre modalità elaborare i dati inviati dal dispositivo, continuare con la sezione successiva.

## <a name="next-steps"></a>Passaggi successivi

È stata eseguita un'applicazione di esempio per raccogliere i dati del sensore da inviare all'hub IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
