---
title: Raspberry Pi simulato al cloud (Node.js) - Connettere il simulatore Web Raspberry Pi all'hub IoT di Azure | Microsoft Docs
description: Connettere il simulatore Web Raspberry Pi all'hub IoT di Azure per permettere a Raspberry Pi di inviare dati al cloud di Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: simulatore raspberry pi, azure iot raspberry pi, raspberry pi hub iot, raspberry pi invia dati al cloud, raspberry pi al cloud
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/7/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 920486ece1ce93f689687d35088e39bac853f50d
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Connettore il simulatore online Raspberry Pi all'hub IoT di Azure (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Questa esercitazione illustra le nozioni di base relative all'uso del simulatore online Raspberry Pi. In seguito illustra come connettere in modo trasparente il simulatore al cloud usando l'[hub IoT di Azure](iot-hub-what-is-iot-hub.md). 

Se si dispone di dispositivi fisici, per iniziare visitare [Connettere Raspberry Pi all'hub IoT di Azure](iot-hub-raspberry-pi-kit-node-get-started.md). 

<p>
<div id="diag" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted">
<img src="media/iot-hub-raspberry-pi-web-simulator/3_banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted">
<img src="media/iot-hub-raspberry-pi-web-simulator/6_button_default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5_button_click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6_button_default.png';">
</div>

## <a name="what-you-do"></a>Operazioni da fare

* Apprendere le nozioni base del simulatore online Raspberry Pi.
* Creare un hub IoT.
* Registrare un dispositivo per Pi nel proprio hub IoT.
* Eseguire un'applicazione di esempio in Pi per inviare i dati del sensore simulato all'hub IoT.

Connettere il connettore Raspberry Pi simulato a un hub IoT creato. Eseguire un'applicazione di esempio con il simulatore per generare i dati del sensore. Infine inviare i dati del sensore all'hub IoT.

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

* Come creare un hub IoT di Azure e ottenere la stringa di connessione del nuovo dispositivo. Se non si ha un account Azure, [creare un account Azure gratuito](https://azure.microsoft.com/free/) in pochi minuti.
* Come utilizzare il simulatore online Raspberry Pi.
* Come inviare i dati del sensore all'hub IoT.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Panoramica del simulatore Web Raspberry Pi

Fare clic sul pulsante per avviare il simulatore online Raspberry Pi.

> [!div class="button"]
[Avviare il simulatore Raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted)

Esistono tre aree nel simulatore Web.
* Area dell'assembly: nel circuito predefinito un Pi si connette a un sensore BME280 e a un LED. L'area è bloccata nella versione di anteprima, pertanto non è attualmente possibile eseguire la personalizzazione.
* Area della codifica: un editor di codice online da usare con Raspberry Pi. L'applicazione di esempio predefinita consente di raccogliere i dati del sensore BME280 e di inviarli all'hub IoT di Azure. L'applicazione è completamente compatibile con dispositivi Pi effettivi. 
* Finestra della console integrata: mostra l'output del codice. Nella parte superiore di questa finestra sono disponibili tre pulsanti.
   * **Esegui**: eseguire l'applicazione nell'area di codifica.
   * **Reimposta**: reimposta l'area di codifica sull'applicazione di esempio predefinita.
   * **Comprimi/Espandi**: sul lato destro è disponibile un pulsante per comprimere/espandere la finestra della console.

> [!NOTE] 
Il simulatore Web Raspberry Pi è ora disponibile nella versione di anteprima. Vorremmo sentire la tua voce nella [chat Gitter](https://gitter.im/Microsoft/raspberry-pi-web-simulator). Il codice sorgente è pubblico in [GitHub](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Panoramica del simulatore online Pi](media/iot-hub-raspberry-pi-web-simulator/0_overview.png)

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]


## <a name="run-a-sample-application-on-pi-web-simulator"></a>Eseguire un'applicazione di esempio nel simulatore Web Pi

1. Nell'area di codifica assicurarsi di lavorare nell'applicazione di esempio predefinita. Sostituire il segnaposto nella riga 15 con la stringa di connessione del dispositivo hub IoT di Azure.
   ![Sostituire la stringa di connessione del dispositivo](media/iot-hub-raspberry-pi-web-simulator/1_connectionstring.png)

2. Fare clic su **Esegui** o digita `npm start` per eseguire l'applicazione.


Dovrebbe venire visualizzato l'output seguente che mostra i dati del sensore e i messaggi inviati all'hub IoT ![Output: dati del sensore inviati da Raspberry Pi all'hub IoT](media/iot-hub-raspberry-pi-web-simulator/2_run_application.png)


## <a name="next-steps"></a>Passaggi successivi

È stata eseguita un'applicazione di esempio per raccogliere i dati del sensore da inviare all'hub IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

