---
title: Gestire in blocco i dispositivi connessi alla soluzione Monitoraggio remoto - Azure | Microsoft Docs
description: Questa esercitazione illustra come gestire in blocco i dispositivi connessi a una soluzione Monitoraggio remoto.
author: aditidugar
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: adugar
ms.openlocfilehash: 640eb8800f9593aef510d99713595bdd0c844263
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52683429"
---
# <a name="tutorial-manage-your-connected-devices-in-bulk"></a>Esercitazione: Gestire i dispositivi connessi in blocco

In questa esercitazione si usa l'acceleratore di soluzione Monitoraggio remoto per gestire in blocco la configurazione dei dispositivi connessi.

In qualità di operatore di Contoso, è necessario configurare un gruppo di dispositivi con una nuova versione del firmware. Non si vuole aggiornare il firmware su ogni singolo dispositivo. Per aggiornare il firmware in un gruppo di dispositivi, è possibile usare i gruppi di dispositivi e la gestione automatica dei dispositivi nell'acceleratore di soluzione Monitoraggio remoto. Tutti i dispositivi aggiunti al gruppo di dispositivi ottengono il firmware più recente non appena il dispositivo torna online.

In questa esercitazione:

>[!div class="checklist"]
> * Creare un gruppo di dispositivi
> * Preparare e ospitare il firmware
> * Creare una configurazione del dispositivo nel portale di Azure
> * Importare una configurazione del dispositivo nella soluzione Monitoraggio remoto
> * Distribuire la configurazione ai dispositivi nel gruppo di dispositivi
> * Monitorare la distribuzione

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

<!--
If this is going to be a tutorial - we need to split this include into two so that we can accommodate the additional prerequisites:

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]
-->

## <a name="prerequisites"></a>Prerequisiti

Per seguire questa esercitazione, è necessaria un'istanza distribuita dell'acceleratore della soluzione di monitoraggio remoto nella sottoscrizione di Azure.

Se l'acceleratore della soluzione di monitoraggio remoto non è stato ancora distribuito, completare la guida introduttiva [Distribuire la soluzione di monitoraggio remoto basata su cloud](quickstart-remote-monitoring-deploy.md).

Per archiviare i file del firmware è necessario un account di archiviazione di Azure. È possibile usare un account di archiviazione esistente oppure [creare un nuovo account di archiviazione](../storage/common/storage-quickstart-create-account.md) nella sottoscrizione.

L'esercitazione usa un dispositivo [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) come dispositivo di esempio.

È necessario che nel computer locale sia installato il software seguente:

* [Visual Studio Code (VS Code)](https://code.visualstudio.com/).
* Estensione di Visual Studio Code [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench).

Prima di iniziare:

* Assicurarsi che il [bootloader del dispositivo IoT DevKit sia della versione 1.4.0 o successiva](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).
* Assicurarsi che la versione di IoT DevKit SDK sia la stessa del bootloader. È possibile aggiornare IoT DevKit SDK usando l'estensione Azure IoT Workbench in VS Code. Aprire il riquadro comandi e immettere **Arduino: Board Manager** (Arduino: Gestione scheda). Per altre informazioni, vedere [Preparare l'ambiente di sviluppo](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment).

È anche necessario connettere almeno un dispositivo IoT DevKit all'acceleratore di soluzione Monitoraggio remoto. Se non si è connesso un dispositivo IoT DevKit, vedere [Connettere MXChip IoT DevKit AZ3166 all'acceleratore di soluzione Monitoraggio remoto IoT](iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md).

## <a name="navigate-to-the-dashboard"></a>Passare al dashboard

Per visualizzare il dashboard della soluzione di monitoraggio remoto nel browser, passare a [Acceleratori di soluzione Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#dashboard). Potrebbe essere necessario accedere con le credenziali della sottoscrizione di Azure.

Quindi fare clic su **Avvia** nel riquadro per l'acceleratore di soluzioni di monitoraggio remoto distribuito nella [Guida introduttiva](quickstart-remote-monitoring-deploy.md).

## <a name="create-a-device-group"></a>Creare un gruppo di dispositivi

Per aggiornare automaticamente il firmware in un gruppo di dispositivi, i dispositivi devono essere membri di un gruppo di dispositivi nella soluzione Monitoraggio remoto:

1. Nella pagina **Dispositivi** selezionare tutti i dispositivi **IoT DevKit** connessi all'acceleratore di soluzione. Quindi fare clic su **Processi**.

1. Nella pannello **Processi** selezionare **Tag**, impostare il nome del processo su **AddDevKitTag**, quindi aggiungere un tag di testo denominato **IsDevKitDevice** con il valore **Y**. Fare quindi clic su **Applica**.

1. Ora è possibile usare i valori dei tag per creare un gruppo di dispositivi. Nella pagina **Dispositivi** fare clic su **Manage device groups** (Gestisci gruppi di dispositivi).

1. Creare un filtro di testo che usi il nome di tag **IsDevKitDevice** e il valore **Y** nella condizione. Salvare il gruppo di dispositivi con il nome **Dispositivi IoT DevKit**.

Più avanti in questa esercitazione si userà questo gruppo di dispositivi per applicare una configurazione del dispositivo che aggiorna il firmware di tutti i membri.

## <a name="prepare-and-host-the-firmware"></a>Preparare e ospitare il firmware

L'estensione di Visual Studio Code [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) include il codice del dispositivo di esempio per l'aggiornamento del firmware.

### <a name="open-the-firmware-ota-sample-in-vs-code"></a>Aprire l'esempio Firmware OTA in Visual Studio Code

1. Assicurarsi che il dispositivo IoT DevKit non sia connesso al computer. Avviare VS Code e quindi connettere il DevKit al computer.

1. Premere **F1** per aprire il riquadro comandi, digitare e selezionare **IoT Workbench: Examples** (Workbench IoT: Esempi). Quindi selezionare **IoT DevKit** come scheda.

1. Trovare **Firmware OTA** e fare clic su **Open Sample** (Apri esempio). Si apre una nuova finestra di Visual Studio Code che visualizza la cartella di progetto **firmware_ota**:

    ![IoT Workbench, selezionare l'esempio Firmware OTA](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-firmware-example.png)

### <a name="build-the-new-firmware"></a>Compilare il nuovo firmware

La versione iniziale del firmware del dispositivo è 1.0.0. Il nuovo firmware deve avere un numero di versione superiore.

1. In Visual Studio Code aprire il file **FirmwareOTA.ino** e modificare `currentFirmwareVersion` da `1.0.0` a `1.0.1`:

    ![Modificare la versione del firmware](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Aprire il riquadro comandi, quindi digitare e selezionare **IoT Workbench: Device** (Workbench IoT: Dispositivo). Selezionare quindi **Device Compile** (Compilazione dispositivo) per compilare il codice:

    ![Compilazione dispositivo](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-compile.png)

    Visual Studio Code salva il file compilato nella cartella `.build` del progetto. A seconda delle impostazioni, Visual Studio Code può nascondere la cartella `.build` nella visualizzazione Esplora risorse.

### <a name="generate-the-crc-value-and-calculate-the-firmware-file-size"></a>Generare il valore CRC e calcolare le dimensioni del file del firmware

1. Aprire il riquadro comandi, quindi digitare e selezionare **IoT Workbench: Device** (Workbench IoT: Dispositivo). Selezionare quindi **Generate CRC**(Generazione CRC):

    ![Generazione CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-crc.png)

1. Visual Studio Code genera e stampa il valore CRC, il nome e il percorso del file del firmware e le dimensioni del file nella finestra di output. Prendere nota di questi valori per usarli più avanti:

    ![Informazioni CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/crc-info.png)

### <a name="upload-the-firmware-to-the-cloud"></a>Caricare il firmware nel cloud

Usare l'account di archiviazione di Azure per ospitare il nuovo file del firmware nel cloud.

1. Passare all'account di archiviazione nel portale di Azure. Nella sezione Servizi selezionare **BLOB**. Creare un contenitore pubblico denominato **firmware** per archiviare i file del firmware:

    ![Creazione cartella](media/iot-accelerators-remote-monitoring-bulk-configuration-update/blob-folder.png)

1. Per caricare il file del firmware nel contenitore, selezionare il contenitore **firmware** e fare clic su **Carica**.

1. Selezionare **FirmwareOTA.ino.bin**. Prendere nota del percorso completo del file nella sezione precedente.

1. Una volta completato il caricamento del file del firmware, prendere nota dell'URL del file.

### <a name="build-and-upload-the-original-firmware-to-the-iot-devkit-device"></a>Compilare e caricare il firmware originale nel dispositivo IoT DevKit

1. In Visual Studio Code aprire il file **FirmwareOTA.ino** e modificare `currentFirmwareVersion` in `1.0.0`:

    ![Versione 1.0.0](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Aprire il riquadro comandi, quindi digitare e selezionare **IoT Workbench: Device** (Workbench IoT: Dispositivo). Selezionare quindi **Device Upload** (Caricamento dispositivo):

    ![Caricamento dispositivo](media/iot-accelerators-remote-monitoring-bulk-configuration-update/device-upload.png)

1. Visual Studio Code verifica e carica il codice nel dispositivo IoT DevKit.

1. Al termine del caricamento, il dispositivo IoT DevKit viene riavviato. Dopo il riavvio, la schermata del dispositivo IoT DevKit visualizza **FW version: 1.0.0** (Versione firmware: 1.0.0) e l'indicazione che sta verificando la disponibilità di nuovo firmware:

    ![ota-1](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-1.jpg)

## <a name="create-a-device-configuration"></a>Creare una configurazione del dispositivo

Una configurazione del dispositivo specifica lo stato desiderato dei dispositivi. In genere, uno sviluppatore [crea la configurazione](../iot-hub/iot-hub-auto-device-config.md#create-a-configuration) nella pagina **Configurazione del dispositivo IoT** del portale di Azure. Una configurazione del dispositivo è un documento JSON che specifica lo stato desiderato dei dispositivi e un set di metriche.

Salvare la configurazione seguente come file denominato **firmware update.json** nel computer locale. Sostituire i segnaposto `YOURSTRORAGEACCOUNTNAME`, `YOURCHECKSUM` e `YOURPACKAGESIZE` con i valori annotati in precedenza:

```json
{
  "id": "firmware-update",
  "schemaVersion": null,
  "labels": {
    "Version": "1.0.1",
    "Devices": "IoT DevKit"
  },
  "content": {
    "deviceContent": {
      "properties.desired.firmware": {
        "fwVersion": "1.0.1",
        "fwPackageURI": "https://YOURSTRORAGEACCOUNTNAME.blob.core.windows.net/firmware/FirmwareOTA.ino.bin",
        "fwPackageCheckValue": "YOURCHECKSUM",
        "fwSize": YOURPACKAGESIZE
      }
    }
  },
  "targetCondition": "",
  "priority": 10,
  "systemMetrics": {
    "results": {
      "targetedCount": 0,
      "appliedCount": 0
    },
    "queries": {
      "targetedCount": "",
      "appliedCount": "select deviceId from devices where configurations.[[firmware-update]].status = 'Applied'"
    }
  },
  "metrics": {
    "results": {
      "Current": 1
    },
    "queries": {
      "Current": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Current' AND properties.reported.firmware.type='IoTDevKit'",
      "Downloading": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Downloading' AND properties.reported.firmware.type='IoTDevKit'",
      "Verifying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Verifying' AND properties.reported.firmware.type='IoTDevKit'",
      "Applying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Applying' AND properties.reported.firmware.type='IoTDevKit'",
      "Error": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Error' AND properties.reported.firmware.type='IoTDevKit'"
    }
  }
}
```

Questo file di configurazione si usa nella sezione seguente.

## <a name="import-a-configuration"></a>Importare una configurazione

In questa sezione si importerà la configurazione del dispositivo come pacchetto nell'acceleratore di soluzione Monitoraggio remoto. In genere, un operatore completa questa attività.

1. Nell'interfaccia utente Web di monitoraggio remoto passare alla pagina **Pacchetti** e fare clic su **+ Nuovo pacchetto**:

    ![Nuovo pacchetto](media/iot-accelerators-remote-monitoring-bulk-configuration-update/packagepage.png)

1. Nel pannello **Nuovo pacchetto** scegliere **Configurazione del dispositivo** come tipo di pacchetto e **Firmware** come tipo di configurazione. Fare clic su **Sfoglia** per trovare il file **firmware update.json** nel computer locale e quindi fare clic su **Carica**:

    ![Caricare il pacchetto](media/iot-accelerators-remote-monitoring-bulk-configuration-update/uploadpackage.png)

1. L'elenco dei pacchetti include ora il pacchetto **firmware-update**.

## <a name="deploy-the-configuration-to-your-devices"></a>Distribuire la configurazione ai dispositivi

In questa sezione si crea e si esegue una distribuzione che applica la configurazione del dispositivo ai dispositivi IoT DevKit.

1. Nell'interfaccia utente Web di monitoraggio remoto passare alla pagina **Distribuzioni** e fare clic su **+ Nuova distribuzione**:

    ![New deployment](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentpage.png)

1. Nel pannello **Nuova distribuzione** creare una distribuzione con le impostazioni seguenti:

    |Opzione|Valore|
    |---|---|
    |NOME|Distribuzione aggiornamento firmware|
    |Tipo di pacchetto|Configurazione del dispositivo|
    |Tipo configurazione|Firmware|
    |Pacchetto|firmware-update.json|
    |Gruppo di dispositivi|Dispositivi IoT DevKit|
    |Priorità|10|

    ![Creare una distribuzione](media/iot-accelerators-remote-monitoring-bulk-configuration-update/newdeployment.png)

    Fare clic su **Apply**. Nella pagina **Distribuzioni** sarà visibile una nuova distribuzione che mostra le metriche seguenti:

    * **Assegnato** indica il numero di dispositivi nel gruppo.
    * **Applicato** indica il numero di dispositivi che sono stati aggiornati con il contenuto della configurazione.
    * **Riuscito** indica il numero di dispositivi nella distribuzione che segnalano l'esito positivo.
    * **Non riuscito** indica il numero di dispositivi nella distribuzione che segnalano l'esito negativo.

## <a name="monitor-the-deployment"></a>Monitorare la distribuzione

Dopo alcuni minuti, IoT DevKit recupera le informazioni sul nuovo firmware e inizia scaricarle nel dispositivo:

![ota-2](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-2.jpg)

A seconda della velocità della rete, il download potrebbe richiedere un paio di minuti. Dopo aver scaricato il firmware, il dispositivo verifica le dimensioni del file e il valore CRC. La schermata del dispositivo MXChip indica **passed** se la verifica ha esito positivo.

![ota-3](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-3.jpg)

Se il controllo ha esito positivo, il dispositivo viene riavviato. Prima del riavvio viene visualizzato un conto alla rovescia da **5** a **0**.

![ota-4](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-4.jpg)

Dopo il riavvio, il bootloader di IoT DevKit aggiorna il firmware alla nuova versione. L'aggiornamento può richiedere alcuni secondi. Durante questa fase, il LED RGB del dispositivo è rosso e la schermata è vuota.

![ota-5](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-5.jpg)

Al termine del riavvio,il dispositivo IoT DevKit esegue la versione 1.0.1 del firmware.

![ota-6](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-6.jpg)

Nella pagina **Distribuzioni** fare clic su una distribuzione per visualizzare lo stato dei dispositivi man mano che vengono aggiornati. È possibile visualizzare lo stato di tutti i dispositivi nel gruppo di dispositivi e le metriche personalizzate definite.

![Dettagli della distribuzione](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentstatus.png)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come aggiornare il firmware di un gruppo di dispositivi connessi alla soluzione. Per aggiornare i dispositivi, la soluzione usa la gestione automatica dei dispositivi. Per altre informazioni sulla funzionalità di gestione automatica dei dispositivi nell'hub IoT sottostante della soluzione, vedere [Configurare e monitorare i dispositivi IoT su larga scala tramite il portale di Azure](../iot-hub/iot-hub-auto-device-config.md).