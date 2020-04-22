---
title: Connettere un dispositivo DevKit all'applicazione Azure IoT Central | Microsoft Docs
description: Gli sviluppatori di dispositivi, scoprino come connettere un dispositivo MXChip IoT DevKit all'applicazione Azure IoT Central usando Plug and Play IoT (anteprima).
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: bcf1dd2f89cf049d7da5b56170b2c13874c83ba4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756800"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Connettere un dispositivo MXChip IoT DevKit all'applicazione Azure IoT Central

*Questo articolo si applica agli sviluppatori di dispositivi.*

Questo articolo illustra come connettere un dispositivo MXChip IoT DevKit (DevKit) a un'applicazione Azure IoT Central.This article shows you how to connect an MXChip IoT DevKit (DevKit) device to an Azure IoT Central application. Il dispositivo utilizza il modello IoT Plug and Play (anteprima) certificato per il dispositivo DevKit per configurare la connessione a IoT Central.

In questo articolo how-to:

- Ottenere i dettagli di connessione dall'applicazione IoT Central.Get the connection details from your IoT Central application.
- Preparare il dispositivo e connetterlo all'applicazione IoT Central.
- Visualizzare i dati di telemetria e le proprietà dal dispositivo in IoT Central.View the telemetry and properties from the device in IoT Central.

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, sono necessarie le risorse seguenti:

- Un [dispositivo DevKit](https://aka.ms/iot-devkit-purchase).
- Un'applicazione IoT Central. È possibile seguire i passaggi descritti in [Creare un'applicazione IoT Central](./quick-deploy-iot-central.md).

## <a name="get-device-connection-details"></a>Ottenere i dettagli di connessione del dispositivoGet device connection details

1. Nell'applicazione Azure IoT Central selezionare la scheda Modelli di **dispositivo** e fare clic su **Nuovo**. Nella sezione Utilizzare un modello di **dispositivo preconfigurato**selezionare **MXChip IoT DevKit**.

    ![Modello di dispositivo per MXChip IoT DevKit](media/howto-connect-devkit/device-template.png)

1. Selezionare **Avanti: Personalizza** e quindi **Crea**.

1. Selezionare la scheda **Dispositivi.Select Devices** tab. Nell'elenco dei dispositivi, seleziona **MXChip IoT DevKit** e seleziona **- Nuovo** per creare un nuovo dispositivo dal modello.

    ![Nuovo dispositivo](media/howto-connect-devkit/new-device.png)

1. Nella finestra pop-up, immettere `SampleDevKit` l'ID **dispositivo** come e **il nome del dispositivo** come `MXChip IoT DevKit - Sample`. Assicurarsi che l'opzione Simulato sia **disattivata.** Selezionare quindi **Crea**.

    ![ID e nome del dispositivo](media/howto-connect-devkit/device-id-name.png)

1. Selezionare il dispositivo creato e quindi selezionare **Connetti**. Prendere nota **dell'ambito ID**, **dell'ID dispositivo**e della **chiave primaria**. Questi valori sono necessari più avanti in questo articolo delle modalità di e-a.

    ![Informazioni di connessione del dispositivo](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>Preparare il dispositivo

1. Scarica il firmware più recente di [Azure IoT Central Plug and Play (anteprima)](https://github.com/Azure-Samples/mxchip-iot-devkit-pnp/raw/master/bin/iotc_devkit.bin) per il dispositivo DevKit da GitHub.

1. Connettere il dispositivo DevKit al computer di sviluppo usando un cavo USB. In Windows viene visualizzata una finestra di Esplora file per un'unità mappata all'archiviazione nel dispositivo DevKit. Ad esempio, l'unità potrebbe chiamarsi **AZ3166 (D:)**.

1. Trascinare il file **iotc_devkit.bin** nella finestra dell'unità. Al termine del processo di copia il dispositivo viene riavviato con il nuovo firmware.

    > [!NOTE]
    > Se vedi errori sullo schermo come **No Wi-Fi**, questo è perché DevKit non è ancora stato collegato al WiFi.

1. Nel DevKit tenere premuto **il pulsante B**, premere e rilasciare il pulsante **Reimposta,** quindi rilasciare **il pulsante B**. Il dispositivo è ora in modalità punto di accesso. Per confermare, nella schermata vengono visualizzati "IoT DevKit - AP" e l'indirizzo IP del portale di configurazione.

1. Sul computer o tablet, connettersi al nome della rete WiFi visualizzato sullo schermo del dispositivo. La rete WiFi inizia con **l'A- seguito** dall'indirizzo MAC. Quando ci si connette a questa rete, non si dispone dell'accesso a Internet. Questo stato è previsto e ci si connette a questa rete solo per un breve periodo di tempo durante la configurazione del dispositivo.

1. Aprire il browser Web [http://192.168.0.1/](http://192.168.0.1/)e passare a . Viene visualizzata la pagina Web seguente:

    ![Interfaccia utente di configurazioneConfig UI](media/howto-connect-devkit/config-ui.png)

    Nella pagina Web immettere:

    - Il nome della rete WiFi (SSID).
    - La password della rete WiFi.
    - I dettagli della connessione: immettere **l'ID dispositivo**, **l'ambito ID**e la chiave primaria **sAS** di cui si è preso nota in precedenza.

    > [!NOTE]
    > Attualmente, l'IoT DevKit può solo connettersi a 2,4 GHz Wi-Fi, 5 GHz non è supportato a causa di restrizioni hardware.

1. Scegliere **Configura dispositivo**, il dispositivo DevKit riavvia ed esegue l'applicazione:

    ![Interfaccia utente di riavvio](media/howto-connect-devkit/reboot-ui.png)

    La schermata DevKit visualizza una conferma che l'applicazione è in esecuzione:The DevKit screen displays a confirmation that the application is running:

    ![DevKit in esecuzione](media/howto-connect-devkit/devkit-running.png)

DevKit registra innanzitutto un nuovo dispositivo nell'applicazione IoT Central e quindi avvia l'invio di dati.

## <a name="view-the-telemetry"></a>Visualizzare i dati di telemetria

In questo passaggio vengono visualizzate i dati di telemetria nell'applicazione Azure IoT Central.In this step, you view the telemetry in your Azure IoT Central application.

Nell'applicazione IoT Central selezionare la scheda **Dispositivi** e selezionare il dispositivo aggiunto. Nella scheda **Panoramica** è possibile visualizzare i dati di telemetria dal dispositivo DevKit:In the Overview tab, you can see the telemetry from the DevKit device:

![Panoramica del dispositivo IoT Central](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>Esaminare il codice

Per esaminare il codice o modificarlo e compilarlo, passare a [Esempi di codice](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/).

## <a name="next-steps"></a>Passaggi successivi

Se sei uno sviluppatore di dispositivi, alcuni passaggi successivi suggeriti sono:If you're a device developer, some suggested next steps are to:

- Informazioni sulla connettività dei dispositivi [in Azure IoT CentralRead about Device connectivity in Azure IoT Central](./concepts-get-connected.md)
- Informazioni su come [monitorare la connettività dei dispositivi usando l'interfaccia della riga di comando di AzureLearn](./howto-monitor-devices-azure-cli.md) how to Monitor device connectivity using Azure CLI
