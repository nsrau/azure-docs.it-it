---
title: Connettere un dispositivo DevKit all'applicazione Azure IoT Central | Microsoft Docs
description: In qualità di sviluppatore di dispositivi, informazioni su come connettere un dispositivo MXChip DevKit di Azure all'applicazione IoT Central di Azure usando le Plug and Play.
author: liydu
ms.author: liydu
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: b7d2e1b08653cb8023ef6a5190ab53ecc3d568a6
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72297030"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Connettere un dispositivo MXChip IoT DevKit all'applicazione Azure IoT Central

Questo articolo illustra come connettere un dispositivo MXChip DevKit (DevKit) a un'applicazione IoT Central di Azure. Il dispositivo usa il modello di Plug and Play delle cose certificate per il dispositivo DevKit per configurare la connessione a IoT Central.

Questo articolo illustra come eseguire le operazioni seguenti:

- Ottenere i dettagli della connessione dall'applicazione IoT Central.
- Preparare il dispositivo e connetterlo all'applicazione IoT Central.
- Visualizzare i dati di telemetria e le proprietà dal dispositivo in IoT Central.

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi descritti in questo articolo, sono necessarie le risorse seguenti:

1. Un [dispositivo DevKit](https://aka.ms/iot-devkit-purchase).
1. Applicazione IoT Central creata dal modello di **applicazione di anteprima** . È possibile seguire la procedura descritta in [creare un'applicazione plug and Play](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="get-device-connection-details"></a>Ottenere i dettagli della connessione del dispositivo

Nell'applicazione IoT Central di Azure selezionare la scheda **Amministrazione** e selezionare **connessione del dispositivo**. Prendere nota dell' **ambito ID** e della **chiave primaria**.

![Dettagli connessione gruppo di dispositivi](media/howto-connect-devkit-pnp/device-group-connection-details.png)

## <a name="prepare-the-device"></a>Preparare il dispositivo

1. Scaricare la versione più recente del [firmware Plug and Play IOT Central di Azure](https://github.com/MXCHIP/IoTDevKit/raw/master/pnp/iotc_devkit/bin/iotc_devkit.bin) predefinito per il dispositivo DevKit da GitHub.

1. Connettere il dispositivo DevKit al computer di sviluppo usando un cavo USB. In Windows viene visualizzata una finestra di Esplora file per un'unità mappata all'archiviazione nel dispositivo DevKit. Ad esempio, l'unità potrebbe chiamarsi **AZ3166 (D:)** .

1. Trascinare il file **iotc_devkit. bin** nella finestra dell'unità. Al termine del processo di copia il dispositivo viene riavviato con il nuovo firmware.

    > [!NOTE]
    > Se vengono visualizzati errori sullo schermo, ad esempio **Nessun Wi-Fi**, questo è dovuto al fatto che il DevKit non è ancora stato connesso al Wi-Fi.

1. In DevKit, tenendo premuto il **pulsante b**, premere e rilasciare il pulsante di **reimpostazione** e quindi rilasciare il **pulsante b**. Il dispositivo è ora in modalità punto di accesso. Per confermare, nella schermata vengono visualizzati "DevKit-e-AP" e l'indirizzo IP del portale di configurazione.

1. Sul computer o sul tablet, connettersi al nome della rete Wi-Fi visualizzato sullo schermo del dispositivo. La rete Wi-Fi inizia con **AZ-** seguita dall'indirizzo Mac. Quando ci si connette a questa rete, non si ha accesso a Internet. Questo stato è previsto ed è possibile connettersi a questa rete solo per un breve periodo di tempo durante la configurazione del dispositivo.

1. Aprire un Web browser e passare a [http://192.168.0.1/](http://192.168.0.1/). Viene visualizzata la pagina Web seguente:

    ![Interfaccia utente di configurazione](media/howto-connect-devkit-pnp/config-ui.png)

    Nella pagina Web immettere:

    - Nome della rete Wi-Fi (SSID).
    - Password della rete Wi-Fi.
    - I dettagli della connessione: l' **ID del dispositivo** che è possibile scegliere se stessi e la **chiave primaria** dell' **ambito ID** e del gruppo di cui si è preso nota in precedenza.

    > [!NOTE]
    > Attualmente, il DevKit Internet delle cose può connettersi solo a 2,4 GHz Wi-Fi, a 5 GHz non è supportato a causa di limitazioni hardware.

1. Scegliere **Configura dispositivo**. il dispositivo DevKit viene riavviato ed esegue l'applicazione:

    ![Riavvia interfaccia utente](media/howto-connect-devkit-pnp/reboot-ui.png)

    La schermata DevKit Visualizza un avviso per confermare che l'applicazione è in esecuzione:

    ![DevKit in esecuzione](media/howto-connect-devkit-pnp/devkit-running.png)

Il DevKit prima registra un nuovo dispositivo nell'applicazione IoT Central e quindi inizia a inviare i dati.

## <a name="view-the-telemetry"></a>Visualizzare i dati di telemetria

In questo passaggio si visualizzano i dati di telemetria nell'applicazione IoT Central di Azure.

Nell'applicazione IoT Central selezionare scheda **dispositivi** , selezionare il dispositivo aggiunto. Nella scheda **Panoramica** è possibile visualizzare i dati di telemetria dal dispositivo DevKit:

   ![Panoramica del dispositivo IoT Central](media/howto-connect-devkit-pnp/mxchip-overview-page.png)

## <a name="review-the-code"></a>Esaminare il codice

Per esaminare il codice o modificarlo e compilarlo, vedere gli [esempi di codice](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/).

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come connettere un dispositivo DevKit all'applicazione IoT Central di Azure, il passaggio successivo suggerito consiste nell'apprendere come [configurare un modello di dispositivo personalizzato](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) per il proprio dispositivo.
