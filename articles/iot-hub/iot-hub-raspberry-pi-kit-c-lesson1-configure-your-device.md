---
title: 'Connettere Raspberry Pi (C) ad Azure IoT: lezione 1: Configurare il dispositivo | Documentazione Microsoft'
description: Questa lezione illustra come configurare Raspberry Pi 3 per il primo uso e installare Raspbian, un sistema operativo gratuito ottimizzato per l&quot;hardware Raspberry Pi.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "installare raspbian, scaricare raspbian, come installare raspbian, configurazione di raspbian, raspberry pi - installare raspbian, raspberry pi - installare il sistema operativo, raspberry pi - installazione della scheda sd, connessione raspberry pi, connettersi a raspberry pi, connettività di raspberry pi"
ms.assetid: 8ee9b23c-93f7-43ff-8ea1-e7761eb87a6f
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 5b80d471c78973f8c2f6b1ad4e1240c66f0505ef
ms.openlocfilehash: 3e34b8202794bc969bf41765ce519d083037ab46
ms.lasthandoff: 02/21/2017


---
# <a name="configure-your-device"></a>Configurare il dispositivo
## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
Configurare Pi per il primo uso e installare il sistema operativo Raspbian. Raspbian è un sistema operativo gratuito ottimizzato per l'hardware Raspberry Pi. In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione
Contenuto dell'articolo:

* Come installare Raspbian in Pi.
* Come accendere Pi tramite un cavo USB.
* Come connettere Pi alla rete tramite un cavo Ethernet o la rete wireless.
* Come aggiungere un LED alla basetta sperimentale e connetterla a Pi.

## <a name="what-you-need"></a>Elementi necessari
Per completare questa operazione, tenere a portata di mano i componenti seguenti inclusi nello starter kit di Raspberry Pi 3:

* Scheda di Raspberry Pi 3
* Scheda microSD da 16 GB
* Alimentatore da 5 V/2 A con cavo micro USB da 1,8 metri
* Basetta sperimentale
* Cavi del connettore
* Resistore da 560 Ohm
* LED da 10 mm a luce diffusa
* Cavo Ethernet

![Contenuto dello starter kit](media/iot-hub-raspberry-pi-lessons/lesson1/starter_kit.jpg)

Altri elementi necessari:

* Connessione cablata o wireless a cui connettere Pi.
* Scheda USB-SD o mini-SD con cui masterizzare l'immagine del sistema operativo nella scheda microSD.
* Computer Windows, Mac o Linux. Il computer viene usato per installare Raspbian nella scheda microSD.
* Connessione Internet per scaricare gli strumenti e il software necessari.

## <a name="install-raspbian-on-the-microsd-card"></a>Installare Raspbian nella scheda microSD
Preparare la scheda microSD per l'installazione dell'immagine di Raspbian.

1. Scaricare Raspbian.
   1. [Scaricare](https://www.raspberrypi.org/downloads/raspbian/) il file ZIP per Raspbian Jessie with Pixel.
   2. Estrarre l'immagine di Raspbian in una cartella nel computer.
2. Installare Raspbian nella scheda microSD.
   1. [Scaricare](https://www.etcher.io) e installare l'utilità di masterizzazione Etcher per schede SD.
   2. Eseguire Etcher e selezionare l'immagine di Raspbian estratta nel passaggio 1.
   3. Selezionare l'unità della scheda microSD.
      Si noti che Etcher potrebbe avere già selezionato l'unità corretta.
   4. Fare clic su **Flash** per installare Raspbian nella scheda microSD.
   5. Rimuovere la scheda microSD dal computer al termine dell'installazione.
      È possibile rimuovere direttamente la scheda microSD perché viene espulsa o smontata automaticamente da Etcher al termine dell'operazione.
   6. Inserire la scheda microSD in Pi.

![Inserire la scheda SD](media/iot-hub-raspberry-pi-lessons/lesson1/insert_sdcard.jpg)

## <a name="turn-on-pi"></a>Accendere Pi
Accendere Pi usando il cavo micro USB e l'alimentatore.

![Accendere](media/iot-hub-raspberry-pi-lessons/lesson1/micro_usb_power_on.jpg)

> [!NOTE]
> È importante usare l'alimentatore da almeno 2 A incluso nel kit per garantire un'alimentazione sufficiente al corretto funzionamento del dispositivo Raspberry.

## <a name="enable-ssh"></a>Abilitare SSH
A partire dalla versione di novembre 2016, Raspbian ha il server SSH disabilitato per impostazione predefinita. È necessario abilitarlo manualmente. A tale scopo, fare riferimento alle [istruzioni ufficiali](https://www.raspberrypi.org/documentation/remote-access/ssh/) o collegare un monitor e passare a **Preferences -> Raspberry Pi Configuration** (Preferenze-> Configurazione Raspberry Pi).

## <a name="connect-raspberry-pi-3-to-the-network"></a>Connettere Raspberry Pi 3 alla rete
È possibile connettere Pi a una rete cablata oppure a una rete wireless. Verificare che Pi sia connesso alla stessa rete del computer. È ad esempio possibile connettere Pi allo stesso commutatore a cui è connesso il computer.

### <a name="connect-to-a-wired-network"></a>Connettersi a una rete cablata
Per connettere Pi alla rete cablata, usare il cavo Ethernet. Quando viene stabilita la connessione, i due LED del dispositivo si illuminano.

![Connettersi tramite cavo Ethernet](media/iot-hub-raspberry-pi-lessons/lesson1/connect_ethernet.jpg)

### <a name="connect-to-a-wireless-network"></a>Connettersi a una rete wireless
Per connettere il dispositivo alla rete wireless, vedere le [istruzioni](https://www.raspberrypi.org/learning/software-guide/wifi/) riportate nella Guida di Raspberry Pi. Per seguire tali istruzioni è prima necessario connettere un monitor e una tastiera al dispositivo.

## <a name="connect-the-led-to-pi"></a>Collegare il LED a Raspberry Pi
Per completare questa attività, usare la [basetta sperimentale](https://learn.sparkfun.com/tutorials/how-to-use-a-breadboard), i cavi del connettore, il LED e il resistore. Collegarli alle porte GPIO ([General-Purpose Input/Output](https://www.raspberrypi.org/documentation/usage/gpio/)) di Pi.

![Basetta sperimentale, LED e resistore](media/iot-hub-raspberry-pi-lessons/lesson1/breadboard_led_resistor.jpg)

1. Collegare il piedino corto del LED alla porta **GPIO GND (pin 6)**.
2. Collegare il piedino lungo del LED a un piedino del resistore.
3. Collegare l'altro piedino del resistore alla porta **GPIO 4 (pin 7)**.

È importante rispettare la polarità del LED, generalmente impostata come attiva bassa.

![Piedinatura](media/iot-hub-raspberry-pi-lessons/lesson1/pinout_breadboard.png)

Congratulazioni. Il dispositivo Raspberry Pi è stato configurato.

## <a name="summary"></a>Riepilogo
In questo articolo si è appreso come configurare il dispositivo Raspberry Pi installando Raspbian, connettendo il dispositivo alla rete e collegando un LED al dispositivo. Si noti che il LED non si illumina ancora. L'attività successiva consiste nell'installazione del software e degli strumenti necessari per preparare l'esecuzione di un'applicazione di esempio in Pi.

![L'hardware è pronto](media/iot-hub-raspberry-pi-lessons/lesson1/hardware_ready.jpg)

## <a name="next-steps"></a>Passaggi successivi
[Ottenere gli strumenti](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-win32.md)


