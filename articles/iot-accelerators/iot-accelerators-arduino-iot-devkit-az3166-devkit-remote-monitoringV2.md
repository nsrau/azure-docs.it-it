---
title: "DevKit di IoT al cloud: connettere DevKit di IoT AZ3166 all'acceleratore di soluzioni IoT di monitoraggio remoto | Microsoft Docs"
description: In questa esercitazione viene descritto come inviare lo stato dei sensori nel DevKit di IoT AZ3166 all'acceleratore di soluzioni IoT di monitoraggio remoto per il monitoraggio e la visualizzazione.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: isacabe
ms.openlocfilehash: 88ce34125009665d346cf034f5d02ba7e21f48fc
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2018
ms.locfileid: "42143370"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Connettere MXChip IoT DevKit AZ3166 all'acceleratore di soluzioni di monitoraggio remoto IoT


[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

In questa esercitazione viene descritto come eseguire un'app di esempio nel DevKit per inviare i dati dei sensori all'acceleratore di soluzioni.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) è una scheda all-in-one compatibile con Arduino con sensori e periferiche avanzati. È possibile sviluppare per questa scheda tramite l'[estensione di Visual Studio Code per Arduino](https://aka.ms/arduino). Per la scheda esiste un [catalogo di progetti](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) in crescita che rappresenta una guida per sviluppare e creare prototipi di soluzioni di Internet delle cose (IoT) che usano i servizi di Microsoft Azure.

## <a name="what-you-need"></a>Elementi necessari

Completare la [Guida introduttiva](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) per:

* Connettere il dispositivo DevKit alla rete Wi-Fi
* Preparare l'ambiente di sviluppo


## <a name="open-the-remotemonitoring-sample"></a>Aprire l'esempio RemoteMonitoring

1. Disconnettere il dispositivo DevKit dal computer, se è connesso.

2. Avviare Visual Studio Code.

3. Connettere il dispositivo DevKit al computer. Visual Studio Code rileva automaticamente il dispositivo DevKit e apre le pagine seguenti:
  * Pagina introduttiva di DevKit.
  * Esempi Arduino: esempi pratici per iniziare con il dispositivo DevKit.

4. Espandere la sezione **ARDUINO EXAMPLES** (ESEMPI ARDUINO) a sinistra, passare a **Examples for MXCHIP AZ3166 > AzureIoT** (Esempi per MXCHIP AZ3166 > AzureIoT) e selezionare **RemoteMonitoringv2**. Verrà visualizzata una nuova finestra di Visual Studio Code con una cartella di progetto all'interno.

  ![Aprire il progetto di monitoraggio remoto](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-arduino-examples.png)


  > [!NOTE]
  > Se si chiude il riquadro involontariamente, è possibile riaprirlo. Premere `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) per aprire il riquadro comandi, digitare **Arduino** e quindi cercare e selezionare **Arduino: Examples**.

## <a name="add-a-new-physical-device"></a>Aggiungere un nuovo dispositivo fisico

Nel portale passare alla sezione **Dispositivi** e fare clic sul pulsante **+New Device** (+Nuovo dispositivo). 

![Aggiungere un nuovo dispositivo](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device.png)

Compilare il modulo *new device form* (modulo nuovo dispositivo).
1. Fare clic su **Fisico** nella sezione *Tipo di dispositivo*.
2. Definire il proprio ID dispositivo (ad esempio *MXChip* oppure *AZ3166*).
3. Scegliere **Genera chiavi automaticamente** nella sezione *Chiave di autenticazione*.
4. Fare clic sul pulsante *Applica*.

![Modulo di aggiunta di un nuovo dispositivo](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-new-device-form.png)

Attendere che il portale termini il provisioning del nuovo dispositivo.

![Provisioning di un nuovo dispositivo ](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device-provisioning.png)


Verrà quindi visualizzata la configurazione del nuovo dispositivo.
Copiare la **stringa di connessione** generata.

![Stringa di connessione del dispositivo](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-new-device-connstring.png)


Questa stringa di connessione sarà usata nella sezione seguente.





## <a name="build-and-upload-the-device-code"></a>Compilare e caricare il codice del dispositivo

Tornare a Visual Studio Code: 

1. Usare `Ctrl+P` (macOS: `Cmd + P`) e digitare **task config-device-connection**.

  ![Scegliere la sottoscrizione di Azure e l'Hub IoT](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion.png)

2. Il terminale chiede quale stringa di connessione del dispositivo IoT si vuole usare. Selezionare *Crea nuova* e incollarla.

  ![Incollare la stringa di connessione](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion-choose-iot-hub-press-button-A.png)

3. Il terminale richiede a volte di passare alla modalità di configurazione. In tal caso tenere premuto il pulsante A, premere e rilasciare il pulsante di reimpostazione, quindi rilasciare il pulsante A. Sullo schermo verrà visualizzato l'ID di DevKit e 'Configuration'.

  ![Schermo del dispositivo DevKit](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-screen.png)

  > [!NOTE]
  > Se sono state seguite le istruzioni nell'ultima sezione di questa esercitazione, la stringa di connessione sarà salvata negli Appunti. In caso contrario, passare al portale di Azure e cercare l'hub IoT del gruppo di risorse di monitoraggio remoto, dove è possibile vedere i dispositivi connessi all'hub IoT e copiare la stringa di connessione del dispositivo.

  ![cercare la stringa di connessione](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-connection-string-of-a-device.png)


A questo punto il nuovo dispositivo fisico sarà visualizzato nella sezione di Visual Studio Code "Azure IoT Hub Devices" (Dispositivi dell'Hub IoT di Azure):

![Notare il nuovo dispositivo dell'Hub IoT](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-new-iot-hub-device.png)

## <a name="test-the-project"></a>Verificare il progetto

Quando viene eseguita l'app di esempio, DevKit invia i dati dei sensori tramite Wi-Fi agli acceleratori di soluzioni IoT. Per visualizzare il risultato, seguire questi passaggi:

1. Passare all'acceleratore di soluzioni IoT e fare clic su **DASHBOARD**.

2. Nella console dell'acceleratore di soluzioni IoT verrà visualizzato lo stato dei sensori del dispositivo DevKit. 

![Dati dei sensori negli acceleratori di soluzioni di IoT](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-dashboard.png)

Se si fa clic sul nome del sensore (AZ3166), a destra del dashboard verrà visualizzata una scheda con il grafico dei sensori del chip MX in tempo reale.


## <a name="send-a-c2d-message"></a>Inviare un messaggio C2D
Monitoraggio remoto v2 consente di richiamare il metodo remoto sul dispositivo.
Il codice di esempio del chip MX pubblica tre metodi, visualizzati nella sezione Metodo quando si seleziona il sensore.

![Metodi del chip MX](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-methods.png)

È possibile cambiare il colore di uno dei LED del chip MX usando il metodo "LedColor". A tal fine, selezionare la casella di controllo del dispositivo e fare clic sul pulsante Pianificazione. 

![Metodi del chip MX](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-schedule.png)

Scegliere il metodo chiamato ChangeColor nell'elenco a discesa dove sono visualizzati tutti i metodi, scrivere un nome e fare clic su Applica.

![Elenco a discesa del chip MX](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-change-color.png)

Dopo qualche secondo, il chip MX fisico cambierà il colore del LED RGB (sotto al pulsante A)

![LED del chip MX](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-led.png)

## <a name="problems-and-feedback"></a>Problemi e commenti

In caso di problemi, fare riferimento alle [domande frequenti](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) o contattare Microsoft tramite i canali seguenti:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come connettere un dispositivo DevKit agli acceleratori di soluzioni IoT e visualizzare i dati dei sensori, ecco i passaggi successivi consigliati:

* [Panoramica degli acceleratori di soluzioni IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Connect an MXChip IoT DevKit device to your Microsoft IoT Central application](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit) (Connettere un dispositivo MXChip IoT DevKit all'applicazione Microsoft IoT Central)
* [Kit per sviluppatori IoT](https://microsoft.github.io/azure-iot-developer-kit/)
