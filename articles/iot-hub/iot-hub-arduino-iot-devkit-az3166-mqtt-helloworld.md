---
title: Inviare messaggi a un server MQTT usando la libreria client MQTT di Azure| Microsoft Docs
description: Usare il dispositivo DevKit come client per inviare messaggi a un server MQTT
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.openlocfilehash: 60520f5a72fd7e27d4ea64ac76511a00a727426e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552011"
---
# <a name="send-messages-to-an-mqtt-server"></a>Inviare messaggi a un server MQTT

I sistemi Internet delle cose (IoT) hanno spesso problemi associati a connessioni Internet lente, intermittenti o di qualità scadente. MQTT è un protocollo di connettività macchina-macchina (M2M) sviluppato pensando a queste problematiche. 

La libreria client MQTT usata in questo argomento fa parte del progetto [Eclipse Paho](https://www.eclipse.org/paho/), che fornisce API per l'uso di MQTT con più sistemi di trasporto.

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

In questo progetto viene illustrato:
- Come usare la libreria Client MQTT per inviare messaggi a un broker MQTT.
- Come configurare il dispositivo MXChip Iot DevKit come client MQTT.

## <a name="what-you-need"></a>Elementi necessari

Completare la [Guida introduttiva](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) per:

* Connettere il dispositivo DevKit alla rete Wi-Fi
* Preparare l'ambiente di sviluppo

## <a name="open-the-project-folder"></a>Aprire la cartella del progetto

1. Disconnettere il dispositivo DevKit dal computer, se è già connesso.

2. Avviare Visual Studio Code.

3. Connettere il dispositivo DevKit al computer.

## <a name="open-the-mqttclient-sample"></a>Aprire l'esempio MQTTClient

Espandere la sezione **ARDUINO EXAMPLES** (ESEMPI ARDUINO) a sinistra, passare a **Examples for MXCHIP AZ3166 > MQTT** (Esempi per MXCHIP AZ3166 > MQTT) e selezionare **MQTTClient**. Si apre una nuova finestra di Visual Studio Code con una cartella di progetto all'interno.

> [!NOTE]
> È anche possibile aprire l'esempio dal riquadro comandi. Premere `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) per aprire il riquadro comandi, digitare **Arduino** e quindi cercare e selezionare **Arduino: Examples**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Compilare e caricare lo sketch Arduino nel DevKit

Digitare `Ctrl+P` (macOS: `Cmd+P`) per eseguire `task device-upload`. Una volta completato il caricamento, il DevKit si riavvia ed esegue lo sketch.

![device-upload](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> È possibile che venga visualizzato il messaggio "Errore: AZ3166: Pacchetto sconosciuto". Questo errore si verifica quando l'indice del pacchetto della scheda non è aggiornato correttamente. Per risolvere questo errore, vedere la [sezione sullo sviluppo delle domande frequenti di IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Verificare il progetto

In Visual Studio Code seguire questa procedura per aprire e configurare il monitor seriale:

1. Fare clic sulla parola `COM[X]` sulla barra di stato per impostare la porta COM corretta con `STMicroelectronics`: ![set-com-port](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Fare clic sull'icona della spina di alimentazione sulla barra di stato per aprire il monitor seriale: ![serial-monitor](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Sulla barra di stato fare clic sul numero che rappresenta la velocità di trasmissione in baud e impostarlo su `115200`: ![set-baud-rate](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

Il monitor seriale mostra tutti i messaggi inviati dallo sketch di esempio. Lo sketch connette il DevKit al Wi-Fi. Se la connessione Wi-Fi ha esito positivo, lo sketch invia un messaggio al broker MQTT. Successivamente, l'esempio inviati ripetutamente due messaggi "iot.eclipse.org" usando rispettivamente QoS 0 e QoS 1.

![serial-output](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problemi e commenti

In caso di problemi, vedere le [domande frequenti di IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) o mettersi in contatto usando i canali seguenti:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Vedere anche 

* [Connettere DevKit di IoT AZ3166 all'hub IoT di Azure nel cloud](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Usare lo scuotimento per recuperare un messaggio di Twitter con Funzioni di Azure](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come configurare il dispositivo MXChip Iot DevKit come client MQTT e come usare la libreria client MQTT per inviare messaggi a un broker MQTT, ecco i passaggi successivi consigliati:

* [Panoramica dell'acceleratore di soluzioni di monitoraggio remoto di Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Connect an MXChip IoT DevKit device to your Azure IoT Central application](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit) (Connettere un dispositivo MXChip IoT DevKit all'applicazione Azure IoT Central)
