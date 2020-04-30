---
title: Connettere un dispositivo DevKit all'applicazione Azure IoT Central | Microsoft Docs
description: In qualità di sviluppatore di dispositivi, informazioni su come connettere un dispositivo MXChip DevKit di Azure all'applicazione IoT Central di Azure usando gli Internet delle cose Plug and Play (anteprima).
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: bcf1dd2f89cf049d7da5b56170b2c13874c83ba4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81756800"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Connettere un dispositivo MXChip IoT DevKit all'applicazione Azure IoT Central

*Questo articolo si applica agli sviluppatori di dispositivi.*

Questo articolo illustra come connettere un dispositivo MXChip DevKit (DevKit) a un'applicazione IoT Central di Azure. Per configurare la connessione ai IoT Central, il dispositivo usa il modello di anteprima Plug and Play (anteprima) certificato per il dispositivo DevKit.

Questo articolo illustra come eseguire le operazioni seguenti:

- Ottenere i dettagli della connessione dall'applicazione IoT Central.
- Preparare il dispositivo e connetterlo all'applicazione IoT Central.
- Visualizzare i dati di telemetria e le proprietà dal dispositivo in IoT Central.

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, sono necessarie le risorse seguenti:

- Un [dispositivo DevKit](https://aka.ms/iot-devkit-purchase).
- Applicazione IoT Central. È possibile seguire la procedura descritta in [creare un'applicazione IoT Central](./quick-deploy-iot-central.md).

## <a name="get-device-connection-details"></a>Ottenere i dettagli della connessione del dispositivo

1. Nell'applicazione IoT Central di Azure selezionare la scheda **modelli di dispositivo** e selezionare **+ nuovo**. Nella sezione **usare un modello di dispositivo preconfigurato**selezionare **MXChip DevKit**.

    ![Modello di dispositivo per MXChip Internet DevKit](media/howto-connect-devkit/device-template.png)

1. Selezionare **Avanti: Personalizza** e quindi **Crea**.

1. Selezionare la scheda **dispositivi** . Nell'elenco dispositivi selezionare **MXChip DevKit** e selezionare **+ nuovo** per creare un nuovo dispositivo dal modello.

    ![Nuovo dispositivo](media/howto-connect-devkit/new-device.png)

1. Nella finestra popup immettere l' **ID del dispositivo** `SampleDevKit` e il **nome del dispositivo** come `MXChip IoT DevKit - Sample`. Assicurarsi che l'opzione **simulata** sia disattivata. Selezionare quindi **Crea**.

    ![ID e nome del dispositivo](media/howto-connect-devkit/device-id-name.png)

1. Selezionare il dispositivo creato, quindi selezionare **Connetti**. Prendere nota dell' **ambito ID**, dell' **ID dispositivo**e della **chiave primaria**. Questi valori sono necessari più avanti in questo articolo.

    ![Informazioni di connessione del dispositivo](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>Preparare il dispositivo

1. Scaricare la versione più recente del [firmware di Azure IoT Central plug and Play (anteprima)](https://github.com/Azure-Samples/mxchip-iot-devkit-pnp/raw/master/bin/iotc_devkit.bin) per il dispositivo DevKit da GitHub.

1. Connettere il dispositivo DevKit al computer di sviluppo usando un cavo USB. In Windows viene visualizzata una finestra di Esplora file per un'unità mappata all'archiviazione nel dispositivo DevKit. Ad esempio, l'unità potrebbe chiamarsi **AZ3166 (D:)**.

1. Trascinare il file **iotc_devkit. bin** nella finestra dell'unità. Al termine del processo di copia il dispositivo viene riavviato con il nuovo firmware.

    > [!NOTE]
    > Se vengono visualizzati errori sullo schermo, ad esempio **Nessun Wi-Fi**, questo è dovuto al fatto che il DevKit non è ancora stato connesso al Wi-Fi.

1. In DevKit, tenendo premuto il **pulsante b**, premere e rilasciare il pulsante di **reimpostazione** e quindi rilasciare il **pulsante b**. Il dispositivo è ora in modalità punto di accesso. Per confermare, nella schermata vengono visualizzati "DevKit-e-AP" e l'indirizzo IP del portale di configurazione.

1. Sul computer o sul tablet, connettersi al nome della rete Wi-Fi visualizzato sullo schermo del dispositivo. La rete Wi-Fi inizia con **AZ-** seguita dall'indirizzo Mac. Quando ci si connette a questa rete, non si ha accesso a Internet. Questo stato è previsto ed è possibile connettersi a questa rete solo per un breve periodo di tempo durante la configurazione del dispositivo.

1. Aprire il Web browser e passare a [http://192.168.0.1/](http://192.168.0.1/). Viene visualizzata la pagina Web seguente:

    ![Interfaccia utente di configurazione](media/howto-connect-devkit/config-ui.png)

    Nella pagina Web immettere:

    - Nome della rete Wi-Fi (SSID).
    - Password della rete Wi-Fi.
    - I dettagli della connessione: immettere l' **ID del dispositivo**, l' **ambito ID**e la **chiave primaria SAS** di cui si è preso nota in precedenza.

    > [!NOTE]
    > Attualmente, il DevKit Internet delle cose può connettersi solo a 2,4 GHz Wi-Fi, a 5 GHz non è supportato a causa di limitazioni hardware.

1. Scegliere **Configura dispositivo**. il dispositivo DevKit viene riavviato ed esegue l'applicazione:

    ![Riavvia interfaccia utente](media/howto-connect-devkit/reboot-ui.png)

    La schermata DevKit Visualizza un avviso per confermare che l'applicazione è in esecuzione:

    ![DevKit in esecuzione](media/howto-connect-devkit/devkit-running.png)

Il DevKit prima registra un nuovo dispositivo nell'applicazione IoT Central e quindi inizia a inviare i dati.

## <a name="view-the-telemetry"></a>Visualizzare i dati di telemetria

In questo passaggio si visualizzano i dati di telemetria nell'applicazione IoT Central di Azure.

Nell'applicazione IoT Central selezionare scheda **dispositivi** , selezionare il dispositivo aggiunto. Nella scheda **Panoramica** è possibile visualizzare i dati di telemetria dal dispositivo DevKit:

![Panoramica del dispositivo IoT Central](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>Esaminare il codice

Per esaminare il codice o modificarlo e compilarlo, vedere gli [esempi di codice](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/).

## <a name="next-steps"></a>Passaggi successivi

Se si è uno sviluppatore di dispositivi, alcuni passaggi successivi suggeriti sono:

- Scopri di più sulla [connettività dei dispositivi in Azure IOT Central](./concepts-get-connected.md)
- Informazioni su come [monitorare la connettività dei dispositivi usando l'interfaccia](./howto-monitor-devices-azure-cli.md) della riga di comando
