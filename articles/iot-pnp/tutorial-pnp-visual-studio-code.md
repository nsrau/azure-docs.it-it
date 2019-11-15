---
title: Creare e testare un dispositivo di anteprima di Plug and Play IoT | Microsoft Docs
description: Uno sviluppatore di dispositivi può apprendere come usare VS Code per creare e testare un nuovo modello di funzionalità di dispositivo per un dispositivo di anteprima Plug and Play IoT.
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: b7b9cd1040accda4d39af4d0a18940b56a45f929
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73569894"
---
# <a name="tutorial-create-and-test-a-device-capability-model-using-visual-studio-code"></a>Esercitazione: Creare e testare un modello di funzionalità di dispositivo usando Visual Studio Code

Questa esercitazione illustra come uno sviluppatore di dispositivi deve usare Visual Studio Code per creare un _modello di funzionalità di dispositivo_. È possibile usare il modello per generare una bozza di codice da eseguire in un dispositivo che si connette a un'istanza dell'hub IoT nel cloud.

La sezione di questa esercitazione che descrive come compilare la bozza di codice generata presuppone che si stia usando Windows.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un modello di funzionalità di dispositivo
> * Generare la bozza di codice del dispositivo dal modello
> * Implementare gli stub nel codice generato
> * Eseguire il codice per testare le interazioni con un hub IoT

## <a name="prerequisites"></a>Prerequisiti

Per usare il modello di funzionalità di dispositivo in questa esercitazione, è necessario:

* [Visual Studio Code](https://code.visualstudio.com/download): VS Code è disponibile per più piattaforme
* Pacchetto di estensione [Azure IoT Tools per VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools). Usare la procedura seguente per installare il pacchetto di estensione in VS Code:

    1. In VS Code selezionare **Estensioni**.
    1. Cercare **Azure IoT Tools**.
    1. Selezionare **Installa**.

Per compilare il codice C generato in Windows in questa esercitazione, è necessario quanto segue:

* [Visual Studio Build Tools](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) con **Strumenti di compilazione C++** e carichi di lavoro del **componente Gestione pacchetti NuGet**. In alternativa, se si dispone già di [Visual Studio (Community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 o 2015 con gli stessi carichi di lavoro installati.
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/)

Per testare il codice del dispositivo in questa esercitazione, è necessario:

* [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases).
* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="model-your-device"></a>Modellare il dispositivo

Per creare un modello di funzionalità di dispositivo, usare il _linguaggio di definizione dei gemelli digitali_. Un modello è costituito in genere da più file di definizione dell'_interfaccia_ e da un unico file di modello. **Azure IoT Tools per VS Code** include strumenti che consentono di creare e modificare questi file JSON.

### <a name="create-the-interface-file"></a>Creare il file di interfaccia

Per creare un file di interfaccia che definisce le funzionalità del dispositivo IoT in VS Code:

1. Creare una cartella denominata **devicemodel**.

1. Avviare VS Code e usare **CTRL+MAIUSC+P** per aprire il riquadro comandi.

1. Immettere **Plug and Play** e quindi selezionare il comando **IoT Plug & Play: Create Interface** (Crea interfaccia).

1. Individuare e selezionare la cartella **devicemodel** creata.

1. Immettere quindi **EnvironmentalSensor** come nome dell'interfaccia e premere **INVIO**. VS Code crea un file di interfaccia di esempio denominato **EnvironmentalSensor.interface.json**.

1. Sostituire il contenuto del file con il codice JSON seguente e sostituire `{your name}` nel campo `@id` con un valore univoco. Usare solo i caratteri a-z, A-Z, 0-9 e il carattere di sottolineatura. Per altre informazioni, vedere il [formato dell'identificatore gemello digitale](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format). L'ID di interfaccia deve essere univoco per salvare l'interfaccia nel repository:

    ```json
    {
      "@id": "urn:{your name}:EnvironmentalSensor:1",
      "@type": "Interface",
      "displayName": "Environmental Sensor",
      "description": "Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
      "comment": "Requires temperature and humidity sensors.",
      "contents": [
        {
          "@type": "Property",
          "displayName": "Device State",
          "description": "The state of the device. Two states online/offline are available.",
          "name": "state",
          "schema": "boolean"
        },
        {
          "@type": "Property",
          "displayName": "Customer Name",
          "description": "The name of the customer currently operating the device.",
          "name": "name",
          "schema": "string",
          "writable": true
        },
        {
          "@type": "Property",
          "displayName": "Brightness Level",
          "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
          "name": "brightness",
          "writable": true,
          "schema": "long"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Temperature"
          ],
          "description": "Current temperature on the device",
          "displayName": "Temperature",
          "name": "temp",
          "schema": "double",
          "unit": "Units/Temperature/fahrenheit"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Humidity"
          ],
          "description": "Current humidity on the device",
          "displayName": "Humidity",
          "name": "humid",
          "schema": "double",
          "unit": "Units/Humidity/percent"
        },
        {
          "@type": "Telemetry",
          "name": "magnetometer",
          "displayName": "Magnetometer",
          "comment": "This shows a complex telemetry that contains a magnetometer reading.",
          "schema": {
            "@type": "Object",
            "fields": [
              {
                "name": "x",
                "schema": "integer"
              },
              {
                "name": "y",
                "schema": "integer"
              },
              {
                "name": "z",
                "schema": "integer"
              }
            ]
          }
        },
        {
          "@type": "Command",
          "name": "turnon",
          "response": {
            "name": "turnon",
            "schema": "string"
          },
          "comment": "This Commands will turn-on the LED light on the device.",
          "commandType": "synchronous"
        },
        {
          "@type": "Command",
          "name": "turnoff",
          "comment": "This Commands will turn-off the LED light on the device.",
          "response": {
            "name": "turnoff",
            "schema": "string"
          },
          "commandType": "synchronous"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    Questa interfaccia definisce le proprietà del dispositivo, ad esempio **Nome del cliente**, i tipi di telemetria come la **Temperatura** e i comandi come **turnon**.

1. Aggiungere una funzionalità di comando denominata **blink** alla fine del file di interfaccia. Assicurarsi di aggiungere una virgola prima di aggiungere il comando. Provare a digitare la definizione per vedere come IntelliSense, il completamento automatico e la convalida consentono di modificare la definizione di un'interfaccia:

    ```json
    {
      "@type": "Command",
      "description": "This command will begin blinking the LED for given time interval.",
      "name": "blink",
      "request": {
        "name": "blinkRequest",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "interval",
              "schema": "long"
            }
          ]
        }
      },
      "response": {
        "name": "blinkResponse",
        "schema": "string"
      },
      "commandType": "synchronous"
    }
    ```

1. Salvare il file.

### <a name="create-the-model-file"></a>Creare il file di modello

Il file del modello specifica le interfacce implementate dal dispositivo Plug and Play IoT. In un modello sono in genere presenti almeno due interfacce, una o più che definiscono le funzionalità specifiche del dispositivo e un'interfaccia standard che tutti i dispositivi Plug and Play IoT devono implementare.

Per creare un file di modello che specifichi le interfacce implementate dal dispositivo Plug and Play IoT in VS Code:

1. Usare **CTRL+MAIUSC+P** per aprire il riquadro comandi.

1. Immettere **Plug and Play** e quindi selezionare il comando **IoT Plug & Play: Create Capability Model** (Crea modello di funzionalità). Quindi immettere **SensorboxModel** come nome del modello. VS Code crea un file di interfaccia di esempio denominato **SensorboxModel.capabilitymodel.json**.

1. Sostituire il contenuto del file con il codice JSON seguente e sostituire `{your name}` nel campo `@id` e nell'interfaccia `EnvironmentalSensor` con lo stesso valore usato nel file **EnvironmentalSensor.interface.json**. L'ID di interfaccia deve essere univoco per salvare l'interfaccia nel repository:

    ```json
    {
      "@id": "urn:{your name}:SensorboxModel:1",
      "@type": "CapabilityModel",
      "displayName": "Environmental Sensorbox Model",
      "implements": [
        {
          "schema": "urn:{your name}:EnvironmentalSensor:1",
          "name": "environmentalSensor"
        },
        {
          "schema": "urn:azureiot:DeviceManagement:DeviceInformation:1",
          "name": "deviceinfo"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
    }
    ```

    Il modello definisce un dispositivo che implementa l'interfaccia **EnvironmentalSensor** e l'interfaccia **DeviceInformation** standard.

1. Salvare il file.

### <a name="download-the-deviceinformation-interface"></a>Scaricare l'interfaccia DeviceInformation

Prima di poter generare la bozza di codice dal modello, è necessario creare una copia locale di **DeviceInformation** dal *repository del modello pubblico*. Il repository del modello pubblico contiene già l'interfaccia **DeviceInformation**.

Per scaricare l'interfaccia **DeviceInformation** dal repository del modello pubblico usando VS Code:

1. Usare **CTRL+MAIUSC+P** per aprire il riquadro comandi.

1. Immettere **Plug and Play**, selezionare il comando **Open Model Repository**, quindi selezionare **Open Public Model Repository**.

1. Selezionare **Interfaces** (Interfacce), quindi selezionare l'interfaccia con le informazioni sul dispositivo con ID `urn:azureiot:DeviceManagement:DeviceInformation:1`, infine selezionare **Download** (Scarica).

Sono ora disponibili i tre file che costituiscono il modello di funzionalità del dispositivo:

* urn_azureiot_DeviceManagement_DeviceInformation_1.interface.json
* EnvironmentalSensor.interface.json
* SensorboxModel.capabilitymodel.json

## <a name="publish-the-model"></a>Pubblicare il modello

Affinché lo strumento Azure IoT Explorer possa leggere il modello di funzionalità del dispositivo, è necessario pubblicarlo nel repository aziendale. Per pubblicare da VS Code, è necessaria la stringa di connessione per il repository aziendale:

1. Passare al [portale Microsoft Azure Certified per IoT](https://aka.ms/ACFI).

1. Usare il proprio _account aziendale_ Microsoft per accedere al portale.

1. Select **Company repository** (Repository aziendale) e quindi **Connection strings** (Stringhe di connessione).

1. Copiare la stringa di connessione.

Per aprire il repository aziendale in VS Code:

1. Usare **CTRL+MAIUSC+P** per aprire il riquadro comandi.

1. Immettere **Plug and Play** e quindi selezionare il comando **IoT Plug & Play: Open Model Repository** (Apri repository modello).

1. Selezionare **Open Organizational Model Repository** e incollare la stringa di connessione.

1. Per aprire il repository aziendale premere **INVIO**.

Per pubblicare il modello di funzionalità e le interfacce del dispositivo nel repository aziendale:

1. Usare **CTRL+MAIUSC+P** per aprire il riquadro comandi.

1. Immettere **Plug and Play** e quindi selezionare il comando **IoT Plug & Play: Submit files to Model Repository** (Invia file al repository modello).

1. Selezionare i file **EnvironmentalSensor.interface.json** e **SensorboxModel.capabilitymodel.json**, quindi selezionare **OK**.

I file vengono ora archiviati nel repository aziendale.

## <a name="generate-code"></a>Generare il codice

È possibile usare **Azure IoT Tools per VS Code** per generare una bozza di codice C dal modello. Per generare la bozza di codice in VS Code:

1. Usare **CTRL+MAIUSC+P** per aprire il riquadro comandi.

1. Immettere **Plug and Play** e quindi selezionare il comando **IoT Plug & Play: Generate Device Code Stub** (Genera stub codice dispositivo).

1. Selezionare il file del modello di funzionalità **SensorboxModel.capabilitymodel.json**.

1. Immettere **sensorbox_app** come nome del progetto.

1. Scegliere **ANSI C** come linguaggio.

1. Scegliere **Via IoT Hub device connection string** (Tramite stringa di connessione del dispositivo hub IoT) come modalità di connessione.

1. Scegliere **progetto CMake in Windows** come modello di progetto.

1. Scegliere la modalità **Via Vcpkg** (Tramite vcpkg) per includere l'SDK per dispositivi.

VS code genera la bozza di codice C e salva i file nella cartella **sensorbox_app** nella cartella **modelcode**. VS Code apre una nuova finestra contenente i file di codice generati.

## <a name="update-the-generated-code"></a>Aggiornare il codice generato

Prima di poter compilare ed eseguire il codice, è necessario implementare le proprietà, i dati di telemetria e i comandi sottoposti a stub.

Per fornire implementazioni per il codice sottoposto a stub in VS Code:

1. Aprire il file **SensorboxModel_impl.c**.

1. Aggiungere il codice per implementare le funzioni sottoposte a stub.

1. Salvare le modifiche.

## <a name="build-the-code"></a>Compilare il codice

Prima di eseguire il codice per testare il dispositivo Plug and Play IoT con un hub IoT di Azure, è necessario compilare il codice.

Seguire le istruzioni nel file **Readme.MD** nella cartella **sensorbox_app** per compilare ed eseguire il codice in Windows. La sezione seguente include le istruzioni per recuperare una stringa di connessione del dispositivo da usare quando si esegue il codice del dispositivo.

## <a name="test-the-code"></a>Testare il codice

Quando si esegue il codice, questo si connette all'hub IoT e inizia a inviare i dati di telemetria di esempio e i valori delle proprietà. Il dispositivo risponde anche ai comandi inviati dall'hub IoT. Per verificare questo comportamento:

1. Per creare un hub IoT:

    ```azurecli-interactive
    az group create --name environmentalsensorresources --location centralus
    az iot hub create --name {your iot hub name} \
      --resource-group environmentalsensorresources --sku F1
    ```

1. Aggiungere un dispositivo all'hub IoT e recuperare la relativa stringa di connessione:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {your iot hub name} --device-id MyPnPDevice
    az iot hub device-identity show-connection-string --hub-name {your iot hub name} --device-id MyPnPDevice --output table
    ```

    Prendere nota della stringa di connessione.

1. Al prompt dei comandi, passare alla cartella **azure-iot-sdk-c** in cui sono stati compilati l'SDK e gli esempi. Passare quindi alla cartella **cmake\\sensorbox_app\\Release**.

1. Eseguire il comando seguente:

    ```cmd
    sensorbox_app.exe {your device connection string}
    ```

1. Usare lo strumento Azure IoT Explorer per interagire con il dispositivo Plug and Play IoT connesso all'hub IoT. Per altre informazioni, vedere [Install and use Azure IoT explorer](./howto-install-iot-explorer.md) (Installare e usare Azure IoT Explorer).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un dispositivo Plug and Play IoT pronto per la certificazione, è possibile passare agli argomenti seguenti:

> [!div class="nextstepaction"]
> [Creare un dispositivo pronto per la certificazione](tutorial-build-device-certification.md)
