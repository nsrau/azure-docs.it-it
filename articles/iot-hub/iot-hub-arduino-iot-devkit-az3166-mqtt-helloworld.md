---
title: Inviare messaggi a un server MQTT usando la libreria client di Azure MQTT
description: Informazioni su come usare la libreria client MQTT per inviare messaggi a un broker MQTT. Informazioni su come configurare il DevKit di mXChip in modo che sia un client MQTT.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.custom: mqtt
ms.openlocfilehash: 09ea3ceb15c9b175b8366bf3ac57a61e468ab72a
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91356027"
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

![Screenshot mostra una finestra del prompt dei comandi che consente di caricare ed eseguire lo sketch Arduino.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> È possibile che venga visualizzato il messaggio "Errore: AZ3166: Pacchetto sconosciuto". Questo errore si verifica quando l'indice del pacchetto della scheda non è aggiornato correttamente. Per risolvere questo errore, vedere la [sezione sullo sviluppo delle domande frequenti di IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testare il progetto

In Visual Studio Code seguire questa procedura per aprire e configurare il monitor seriale:

1. Fare clic sulla `COM[X]` parola sulla barra di stato per impostare la porta com corretta con `STMicroelectronics` : ![ screenshot mostra Visual Studio Code con COM8 S T Micro Electronics selezionato.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Fare clic sull'icona della spina di alimentazione sulla barra di stato per aprire il monitor seriale: ![ screenshot che mostra il riepilogo della versione e l'icona della spina di alimentazione sulla barra di stato.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Sulla barra di stato fare clic sul numero che rappresenta la velocità in baud e impostarlo su `115200` : ![ screenshot mostra impostazione della velocità in baud in Visual Studio Code.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

Il monitor seriale mostra tutti i messaggi inviati dallo sketch di esempio. Lo sketch connette il DevKit al Wi-Fi. Se la connessione Wi-Fi ha esito positivo, lo sketch invia un messaggio al broker MQTT. Successivamente, l'esempio inviati ripetutamente due messaggi "iot.eclipse.org" usando rispettivamente QoS 0 e QoS 1.

![Screenshot mostra il monitor seriale che Visualizza i messaggi inviati dallo sketch.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problemi e commenti

In caso di problemi, vedere le [domande frequenti di IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) o mettersi in contatto usando i canali seguenti:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Vedere anche

* [Connettere DevKit di IoT AZ3166 all'hub IoT di Azure nel cloud](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Usare lo scuotimento per recuperare un messaggio di Twitter con Funzioni di Azure](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso come configurare il MXChip Internet DevKit come client MQTT e come usare la libreria client MQTT per inviare messaggi a un broker di MQTT, ecco il passaggio successivo suggerito: [Panoramica dell'acceleratore della soluzione di monitoraggio remoto di Azure](https://docs.microsoft.com/azure/iot-suite/) .
