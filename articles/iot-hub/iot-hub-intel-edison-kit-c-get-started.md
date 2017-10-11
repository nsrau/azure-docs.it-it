---
title: Da Intel Edison al cloud (C) - Connettere Intel Edison all'Hub IoT di Azure | Microsoft Docs
description: "Informazioni su come configurare e connettere Intel Edison all'hub IoT di Azure perché Intel Edison invii i dati alla piattaforma cloud di Azure in questa esercitazione."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: azure iot intel edison, intel edison hub iot, intel edison invio dati al cloud, da intel edison al cloud
ms.assetid: 4885fa2c-c2ee-4253-b37f-ccd55f92b006
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/17/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: edbdbe0230f742cd7228f04a4a83c9bd567527e8
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="connect-intel-edison-to-azure-iot-hub-c"></a>Connettere Intel Edison all'Hub IoT di Azure (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Questa esercitazione illustra le nozioni base per l'uso di Intel Edison. Viene poi illustrato come connettere i dispositivi al cloud usando l'[hub IoT di Azure](iot-hub-what-is-iot-hub.md).

Se non si ha ancora un kit, iniziare da [qui](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="what-you-do"></a>Operazioni da fare

* Configurare i moduli Intel Edison e Grove.
* Creare un hub IoT.
* Registrare un dispositivo per Edison nel proprio hub IoT.
* Eseguire un'applicazione di esempio in Edison per inviare i dati del sensore all'hub IoT.

Connettere Intel Edison a un hub IoT creato dall'utente. Eseguire un'applicazione di esempio in Edison per raccogliere dati di temperatura e umidità da un sensore temperatura Grove. Infine inviare i dati del sensore all'hub IoT.

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

* Come creare un hub IoT di Azure e ottenere la stringa di connessione del nuovo dispositivo.
* Come connettere Edison a un sensore temperatura Grove.
* Come raccogliere i dati del sensore eseguendo un'applicazione di esempio in Edison.
* Come inviare i dati del sensore all'hub IoT.

## <a name="what-you-need"></a>Elementi necessari

![Elementi necessari](media/iot-hub-intel-edison-kit-c-get-started/0_kit.png)

* Scheda Intel Edison
* Scheda di espansione Arduino
* Una sottoscrizione di Azure attiva. Se non si ha un account Azure, [creare un account Azure gratuito](https://azure.microsoft.com/free/) in pochi minuti.
* Un Mac o PC che esegue Windows o Linux.
* Una connessione Internet.
* Cavo USB Micro B/Tipo A
* Alimentatore in corrente continua (CC). I valori nominali dell'alimentatore devono essere i seguenti:
  - 7-15 V CC
  - Almeno 1500 mA
  - Il pin centrale deve essere il polo positivo dell'alimentatore

Gli elementi seguenti sono opzionali:

* Grove Base Shield V2
* Sensore temperatura Grove
* Cavo Grove
* Elementi distanziali o viti contenuti nel pacchetto, inclusi quattro set di viti ed elementi distanziali in plastica e due viti per fissare il modulo alla scheda di espansione.

> [!NOTE] 
Questi elementi sono opzionali, poiché l'esempio di codice supporta i dati del sensore simulato.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-intel-edison"></a>Configurare Intel Edison

### <a name="assemble-your-board"></a>Assemblare la scheda

Questa sezione contiene i passaggi per connettere il modulo Intel® Edison alla scheda di espansione.

1. Posizionare il modulo Intel® Edison all'interno del bordo bianco della scheda di espansione, allineando i fori del modulo con le viti della scheda di espansione.

2. Premere verso il basso il modulo sotto le parole `What will you make?` fino a quando non si sente uno scatto.

   ![assemblare la scheda 2](media/iot-hub-intel-edison-kit-c-get-started/1_assemble_board2.jpg)

3. Usare i due dadi esagonali (inclusi nel pacchetto) per fissare il modulo alla scheda di espansione.

   ![assemblare la scheda 3](media/iot-hub-intel-edison-kit-c-get-started/2_assemble_board3.jpg)

4. Inserire una vite in uno dei quattro fori negli angoli della scheda di espansione. Avvitare e fissare uno dei due elementi distanziali in plastica alla vite.

   ![assemblare la scheda 4](media/iot-hub-intel-edison-kit-c-get-started/3_assemble_board4.jpg)

5. Ripetere l'operazione per gli altri tre elementi distanziali angolari.

   ![assemblare la scheda 5](media/iot-hub-intel-edison-kit-c-get-started/4_assemble_board5.jpg)

A questo punto la scheda è assemblata.

   ![scheda assemblata](media/iot-hub-intel-edison-kit-c-get-started/5_assembled_board.jpg)

### <a name="connect-the-grove-base-shield-and-the-temperature-sensor"></a>Connettere Grove Base Shield e il sensore temperatura

1. Posizionare Grove Base Shield sulla scheda e assicurarsi che sia inserito correttamente nella scheda.
   
   ![Grove Base Shield](media/iot-hub-intel-edison-kit-c-get-started/6_grove_base_sheild.jpg)

2. Usare il cavo Grove Cable per connettere il sensore temperatura Grove alla porta **A0** di Grove Base Shield.

   ![Connettersi al sensore temperatura](media/iot-hub-intel-edison-kit-c-get-started/7_temperature_sensor.jpg)
   
   ![Edison e connessione al sensore](media/iot-hub-intel-edison-kit-c-get-started/16_edion_sensor.png)

Il sensore è pronto.

### <a name="power-up-edison"></a>Alimentare Edison

1. Collegare l'alimentatore.

   ![Collegare l'alimentatore](media/iot-hub-intel-edison-kit-c-get-started/8_plug_power.jpg)

2. Un LED verde (con etichetta DS1 sulla scheda di espansione Arduino*) dovrebbe accendersi e rimanere acceso.

3. Attendere un minuto per il completamento dell'avvio della scheda.

   > [!NOTE]
   > Se non si dispone di un alimentatore CC, è comunque possibile alimentare la scheda tramite una porta USB. Per informazioni dettagliate, vedere la sezione `Connect Edison to your computer`. Questo modo di alimentare la scheda può causarne un comportamento non prevedibile, soprattutto quando si usa il Wi-Fi o si comandano motori.

### <a name="connect-edison-to-your-computer"></a>Connettere Edison al computer

1. Spostare i microinterruttori verso le due porte micro-USB in modo che Edison si trovi in modalità dispositivo. Per informazioni sulle differenze tra la modalità dispositivo e la modalità host, vedere [qui](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode).

   ![Spostare verso il basso il microinterruttore](media/iot-hub-intel-edison-kit-c-get-started/9_toggle_down_microswitch.jpg)

2. Collegare il cavo micro-USB alla porta micro-USB superiore.

   ![Porta micro-USB superiore](media/iot-hub-intel-edison-kit-c-get-started/10_top_usbport.jpg)

3. Collegare l'altra estremità del cavo USB al computer.

   ![USB computer](media/iot-hub-intel-edison-kit-c-get-started/11_computer_usb.jpg)

4. La scheda è completamente inizializzata quando il computer consente di montare una nuova unità (ad esempio inserendo una scheda SD).

## <a name="download-and-run-the-configuration-tool"></a>Scaricare ed eseguire lo strumento di configurazione
Scaricare lo strumento di configurazione più recente presente in [questo collegamento](https://software.intel.com/en-us/iot/hardware/edison/downloads) sotto l'intestazione `Installers`. Eseguire lo strumento e attenersi alle istruzioni visualizzate. Fare clic su Avanti quando necessario.

### <a name="flash-firmware"></a>Eseguire il flashing del firmware
1. Nella pagina `Set up options` fare clic su `Flash Firmware`.
2. Selezionare l'immagine per eseguire il flashing sulla scheda tramite una delle operazioni seguenti:
   - Per scaricare ed eseguire il flashing della scheda con l'immagine del firmware più recente disponibile in Intel, selezionare `Download the latest image version xxxx`.
   - Per eseguire il flashing della scheda con un'immagine già salvata nel computer, selezionare `Select the local image`. Individuare e selezionare l'immagine di cui si desidera eseguire il flashing nella scheda.
3. Lo strumento di installazione tenterà di eseguire il flashing della scheda. L'intero processo di esecuzione del flashing può richiedere fino a 10 minuti.

### <a name="set-password"></a>Impostare la password
1. Nella pagina `Set up options` fare clic su `Enable Security`.
2. Per la scheda Intel® Edison è possibile impostare un nome personalizzato. Facoltativo.
3. Digitare una password per la scheda e quindi fare clic su `Set password`.
4. Annotare la password per usarla in seguito.

### <a name="connect-wi-fi"></a>Connettere il Wi-Fi
1. Nella pagina `Set up options` fare clic su `Connect Wi-Fi`. Attendere fino a un minuto per la ricerca delle reti Wi-Fi disponibili.
2. Nell'elenco a discesa `Detected Networks` selezionare la rete.
3. Nell'elenco a discesa `Security` selezionare il tipo di sicurezza della rete.
4. Specificare le informazioni di accesso e la password, quindi fare clic su `Configure Wi-Fi`.
5. Annotare l'indirizzo IP per usarlo in seguito.

> [!NOTE]
> Verificare che Edison sia connesso alla stessa rete del computer. Il computer si connette a Edison tramite l'indirizzo IP.

   ![Connettersi al sensore temperatura](media/iot-hub-intel-edison-kit-c-get-started/12_configuration_tool.png)

Congratulazioni. Edison è stato configurato.

## <a name="run-a-sample-application-on-intel-edison"></a>Eseguire un'applicazione di esempio in Intel Edison

### <a name="prepare-the-azure-iot-device-sdk"></a>Preparare Azure IoT SDK per dispositivi

1. Per connettersi a Intel Edison, usare uno dei client SSH seguenti dal computer host. L'indirizzo IP è relativo allo strumento di configurazione e la password corrisponde a quella impostata nello strumento.
    - [PuTTY](http://www.putty.org/) per Windows.
    - Il client SSH incorporato in Ubuntu o macOS (eseguire `ssh root@"the IP address"`).

2. Clonare l'app client di esempio nel dispositivo. 
   
   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-intel-edison-client-app.git
   ```

3. Passare quindi alla cartella del repository per eseguire il comando seguente per compilare Azure IoT SDK

   ```bash
   cd iot-hub-c-intel-edison-client-app
   sed -i -e 's/\r$//' buildSDK.sh
   chmod 755 buildSDK.sh
   ./buildSDK.sh
   ```

### <a name="configure-the-sample-application"></a>Configurare l'applicazione di esempio

1. Aprire il file di configurazione eseguendo i comandi seguenti:

   ```bash
   nano config.h
   ```

   ![File di configurazione](media/iot-hub-intel-edison-kit-c-get-started/13_configure_file.png)

   Questo file contiene due macro configurabili. La prima è `INTERVAL`, che definisce l'intervallo di tempo tra due messaggi inviati al cloud. La seconda è `SIMULATED_DATA`, ossia un valore booleano che indica se usare o no i dati del sensore simulato.

   Se **non si dispone del sensore**, impostare il valore `SIMULATED_DATA` su `1` per permettere all'applicazione di esempio di creare e usare i dati del sensore simulati.

2. Salvare e uscire premendo CTRL-O > Invio > CTRL-X.

### <a name="build-and-run-the-sample-application"></a>Compilare ed eseguire l'applicazione di esempio

1. Compilare l'applicazione di esempio eseguendo il comando seguente:

   ```bash
   cmake . && make
   ```
   ![Output della compilazione](media/iot-hub-intel-edison-kit-c-get-started/14_build_output.png)

1. Eseguire l'applicazione di esempio tramite il comando seguente:

   ```bash
   sudo ./app '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   Assicurarsi di copiare e incollare la stringa di connessione del dispositivo tra virgolette singole.

Dovrebbe essere visibile l'output seguente che mostra i dati del sensore e i messaggi inviati all'hub IoT.

![Output - dati del sensore inviati da Intel Edison all'hub IoT](media/iot-hub-intel-edison-kit-c-get-started/15_message_sent.png)

## <a name="next-steps"></a>Passaggi successivi

È stata eseguita un'applicazione di esempio per raccogliere i dati del sensore da inviare all'hub IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
