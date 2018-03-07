---
title: 'DevKit di IoT al cloud: Connettere DevKit di IoT AZ3166 all''hub IoT di Azure | Microsoft Docs'
description: In questa esercitazione viene descritto come inviare lo stato dei sensori nel dispositivo IoT DevKit AZ3166 ad Azure IoT Suite per il monitoraggio e la visualizzazione.
services: iot-hub
documentationcenter: 
author: liydu
manager: timlt
tags: 
keywords: 
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: b43061f5af3e836ba3f0b37eb11b351a769890be
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-suite-for-remote-monitoring"></a>Connettere il dispositivo IoT DevKit AZ3166 ad Azure IoT Suite per il monitoraggio remoto

In questa esercitazione viene descritto come eseguire un'app di esempio nel dispositivo DevKit per inviare i dati dei sensori ad Azure IoT Suite.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) è una scheda all-in-one compatibile con Arduino con sensori e periferiche avanzati. È possibile sviluppare per questa scheda tramite l'[estensione di Visual Studio Code per Arduino](https://aka.ms/arduino). Per la scheda esiste un [catalogo di progetti](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) in crescita che rappresenta una guida per sviluppare e creare prototipi di soluzioni di Internet delle cose (IoT) che usano i servizi di Microsoft Azure.

## <a name="what-you-need"></a>Elementi necessari

Completare la [Guida introduttiva](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) per:

* Connettere il dispositivo DevKit alla rete Wi-Fi
* Preparare l'ambiente di sviluppo

Una sottoscrizione di Azure attiva. Se non è disponibile, è possibile registrarsi tramite uno di questi metodi:

* Attivare una [versione di prova gratuita di 30 giorni dell'account di Microsoft Azure](https://azureinfo.microsoft.com/us-freetrial.html)
* Richiedere il [credito Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se si ha un abbonamento a MSDN o una sottoscrizione di Visual Studio

## <a name="create-an-azure-iot-suite"></a>Creare un'istanza di Azure IoT Suite

1. Passare al sito [Azure IoT Suite](https://www.azureiotsuite.com/) e fare clic su **Creare una nuova soluzione**.
  ![Selezionare il tipo di Azure IoT Suite](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)
  > [!NOTE]
  > Per impostazione predefinita, questo esempio crea un hub IoT S2 dopo aver creato un'istanza di IoT Suite. Se questo hub IoT non viene usato con un numero elevato di dispositivi, è consigliabile effettuare il downgrade da S2 a S1 ed eliminare l'istanza di IoT Suite in modo da poter eliminare anche l'hub IoT correlato quando non sarà più necessario. 

2. Selezionare **Monitoraggio remoto**.

3. Immettere il nome della soluzione, selezionare una sottoscrizione e un'area e quindi fare clic su **Crea soluzione**. Il provisioning della soluzione può richiedere tempo.
  ![Creare la soluzione](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. Al termine del provisioning, fare clic su **Avvia**. Durante il processo di provisioning, vengono creati alcuni dispositivi simulati per la soluzione. Fare clic su **DISPOSITIVI** per estrarli. ![Dashboard](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  ![Console](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Fare clic su **AGGIUNGI UN DISPOSITIVO**.

6. Fare clic su **Aggiungi nuovo** per **Dispositivo personalizzato**.
  ![Aggiungere un nuovo dispositivo](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Fare clic su **Desidero definire il mio ID dispositivo**, immettere `AZ3166` e quindi fare clic su **Crea**.
  ![Creare il dispositivo con l'ID](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Annotare il valore di **Nome host hub IoT** e fare clic su **Fine**.

## <a name="open-the-remotemonitoring-sample"></a>Aprire l'esempio RemoteMonitoring

1. Disconnettere il dispositivo DevKit dal computer, se è connesso.

2. Avviare Visual Studio Code.

3. Connettere il dispositivo DevKit al computer. Visual Studio Code rileva automaticamente il dispositivo DevKit e apre le pagine seguenti:
  * Pagina introduttiva di DevKit.
  * Esempi Arduino: esempi pratici per iniziare con il dispositivo DevKit.

4. Espandere la sezione **ARDUINO EXAMPLES** (ESEMPI ARDUINO) a sinistra, passare a **Examples for MXCHIP AZ3166 > AzureIoT** (Esempi per MXCHIP AZ3166 > AzureIoT) e selezionare **RemoteMonitoring**. Verrà visualizzata una nuova finestra di Visual Studio Code con una cartella di progetto all'interno.
  > [!NOTE]
  > Se si chiude il riquadro involontariamente, è possibile riaprirlo. Premere `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) per aprire il riquadro comandi, digitare **Arduino** e quindi cercare e selezionare **Arduino: Examples**.

## <a name="provision-required-azure-services"></a>Eseguire il provisioning dei servizi di Azure necessari

Nella finestra della soluzione eseguire l'attività premendo `Ctrl+P` (macOS: `Cmd+P`) e immettendo `task cloud-provision` nella casella di testo visualizzata:

Nel terminale di Visual Studio Code una riga di comando interattiva guiderà nel processo di provisioning dei servizi di Azure necessari:

![Effettuare il provisioning delle risorse di Azure](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Compilare e caricare il codice del dispositivo

1. Usare `Ctrl+P` (macOS: `Cmd + P`) e digitare **task config-device-connection**.

2. Il terminale chiede se si vuole usare la stringa di connessione per il recupero dal passaggio `task cloud-provision`. È anche possibile immettere una stringa di connessione del dispositivo personalizzata facendo clic su "Crea nuovo".

3. Il terminale richiederà di passare alla modalità di configurazione. A tale scopo, tenere premuto il pulsante A, quindi premere e rilasciare il pulsante di reimpostazione. La schermata visualizza l'ID DevKit e la voce 'Configuration' (Configurazione).
  ![Immettere la stringa di connessione](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Al termine di `task config-device-connection`, fare clic su `F1` per caricare i comandi di Visual Studio Code e selezionare `Arduino: Upload`. Visual Studio Code inizia quindi a verificare e a caricare lo sketch Arduino: ![Verifica e caricamento dello sketch Arduino](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

Il DevKit viene riavviato e inizia a eseguire il codice.

## <a name="test-the-project"></a>Verificare il progetto

Quando viene eseguita l'app di esempio, il dispositivo DevKit invia i dati dei sensori tramite Wi-Fi ad Azure IoT Suite. Per visualizzare il risultato, seguire questi passaggi:

1. Passare ad Azure IoT Suite e fare clic su **DASHBOARD**.

2. Nella console della soluzione Azure IoT Suite verrà visualizzato lo stato dei sensori del dispositivo DevKit.

![Dati dei sensori in Azure IoT Suite](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Modificare l'ID dispositivo

È possibile modificare l'ID dispositivo nell'hub IoT seguendo le istruzioni contenute in [questa guida](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/). Se si vuole modificare il dispositivo **AZ3166** hardcoded in base all'ID dispositivo personalizzato nel codice, [qui](https://github.com/Microsoft/devkit-sdk/blob/master/AZ3166/src/libraries/AzureIoT/examples/RemoteMonitoring/RemoteMonitoring.ino#L23) è disponibile la riga di codice che è possibile modificare.

## <a name="problems-and-feedback"></a>Problemi e commenti

In caso di problemi, fare riferimento alle [domande frequenti](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) o contattare Microsoft tramite i canali seguenti:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come connettere un dispositivo DevKit ad Azure IoT Suite e visualizzare i dati dei sensori, ecco i passaggi successivi consigliati:

* [Panoramica di Azure IoT Suite](https://docs.microsoft.com/azure/iot-suite/)
* [Connect an MXChip IoT DevKit device to your Microsoft IoT Central application](https://docs.microsoft.com/en-us/microsoft-iot-central/howto-connect-devkit) (Connettere un dispositivo MXChip IoT DevKit all'applicazione Microsoft IoT Central)
