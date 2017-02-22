---
title: 'Connettere Arduino (C) ad Azure IoT: lezione 1: Configurare il dispositivo | Documentazione Microsoft'
description: Configurare Adafruit Feather M0 WiFi per il primo utilizzo.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: configurazione di arduino, connessione di arduino al pc, impostazione di arduino, scheda arduino
ms.assetid: f5b334f0-a148-41aa-b374-ce7b9f5b305a
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 3789c07cd6142ba5b6a58a67813c0ead6b87bd76


---
# <a name="configure-your-device"></a>Configurare il dispositivo
## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
Configurare la scheda Arduino Adafruit Feather M0 WiFi per il primo utilizzo assemblando la scheda e fornendo l'alimentazione. In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md).

## <a name="what-you-need"></a>Elementi necessari
Per completare questa operazione, tenere a portata di mano i componenti seguenti inclusi nello starter kit di Adafruit Feather M0 WiFi:

* Scheda Adafruit Feather M0 WiFi
* Cavo USB Micro B/tipo A

![kit][kit]

Altri elementi necessari:

* Computer Windows, Mac o Linux.
* Connessione wireless a cui connettere la scheda Arduino.
* Connessione Internet per scaricare lo strumento di configurazione.

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione
Contenuto dell'articolo:

* Come assemblare la scheda Arduino e fornire l'alimentazione per le lezioni successive.
* Come aggiungere autorizzazioni per la porta seriale in Ubuntu.

## <a name="connect-your-arduino-board-to-your-computer"></a>Connettere la scheda Arduino al computer

1. Collegare il cavo micro-USB alla porta micro-USB superiore.

   ![Porta micro-USB superiore][top-micro-usb-port]

2. Collegare l'altra estremità del cavo USB al computer.

   ![USB computer][computer-usb]

## <a name="add-serial-port-permissions-on-ubuntu"></a>Aggiungere autorizzazioni per la porta seriale in Ubuntu

Se si usa Windows o macOS, è possibile ignorare questa sezione. Per Ubuntu, è necessario attenersi alla seguente procedura per assicurarsi che il normale utente Linux disponga delle autorizzazioni per operare sulla porta USB della scheda Arduino.

1. Come utente normale dal terminale:

   ```bash
   ls -l /dev/ttyUSB*
   # Or
   ls -l /dev/ttyACM*
   ```

   Verrà visualizzata una schermata simile alla seguente:

   ```bash
   crw-rw---- 1 root uucp 188, 0 5 apr 23.01 ttyUSB0
   # Or
   crw-rw---- 1 root dialout 188, 0 5 apr 23.01 ttyACM0
   ```

   Il valore "0" potrebbe essere un numero diverso o potrebbero essere restituite più voci. Nel primo caso i dati necessari sono `uucp`, nel secondo `dialout`, che è il proprietario del gruppo del file.

2. Aggiungere l'utente al gruppo:

   ```bash
   sudo usermod -a -G group-name username
   ```

   Dove `group-name` indica i dati trovati nel primo passaggio e `username` è il nome utente Linux.

3. È necessario eseguire la disconnessione e accedere nuovamente perché questa modifica abbia effetto e per completare la configurazione.

## <a name="summary"></a>Riepilogo
In questo articolo si è appreso come configurare la scheda Arduino. L'attività successiva consiste nell'installazione del software e degli strumenti necessari per preparare l'esecuzione di un'applicazione di esempio nella scheda Arduino.

![L'hardware è pronto][hardware-is-ready]

## <a name="next-steps"></a>Passaggi successivi
[Ottenere gli strumenti][get-the-tools]
<!-- Images and links -->

[kit]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/kit.png
[top-micro-usb-port]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/top_usbport.jpg
[computer-usb]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/computer_usb.jpg
[hardware-is-ready]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/hardware_ready.jpg
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md


<!--HONumber=Jan17_HO4-->


