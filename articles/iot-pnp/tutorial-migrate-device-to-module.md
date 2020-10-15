---
title: Convertire un dispositivo Plug and Play IoT in un modulo generico | Microsoft Docs
description: Usare il codice C# del dispositivo Plug and Play e convertirlo in un modulo.
author: ericmitt
ms.author: ericmitt
ms.date: 9/22/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ccc450242c50f82d4215f6b172f72d8eceab7c52
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046337"
---
# <a name="tutorial-how-to-convert-an-iot-plug-and-play-device-to-a-module-c"></a>Esercitazione: Come convertire un dispositivo Plug and Play IoT in un modulo (C#)

Questa esercitazione illustra come convertire il codice di un dispositivo Plug and Play IoT per eseguirlo come modulo generico.

Un dispositivo si definisce di tipo Plug and Play IoT se pubblica il proprio ID modello quando si connette a un hub IoT e implementa le proprietà e i metodi descritti nel modello DTDL (Digital Twin Definition Language) identificato dall'ID modello. Per altre informazioni su come vengono usati i modelli DTDL e gli ID modello nei dispositivi, vedere [Guida per sviluppatori di Plug and Play IoT](./concepts-developer-guide-device-csharp.md). I moduli usano gli ID modello e i modelli DTDL allo stesso modo.

Per illustrare come implementare un modulo Plug and Play IoT, questa esercitazione descrive come convertire l'esempio del dispositivo termostato in C# in un modulo generico.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Per completare questa esercitazione in Windows, installare il software seguente nell'ambiente Windows locale:

* [Visual Studio (Community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

Usare lo strumento Azure IoT Explorer per aggiungere un nuovo dispositivo denominato **my-module-device** all'hub IoT.

Aggiungere un modulo denominato **my-module** a **my-module-device**:

1. Nello strumento Azure IoT Explorer passare al dispositivo **my-module-device**.

1. Selezionare **Identità del modulo**, quindi selezionare **+ Aggiungi**.

1. Immettere **my-module** come nome dell'identità del modulo e selezionare **Salva**.

1. Nell'elenco di identità di moduli selezionare **my-module**. Quindi copiare la stringa di connessione primaria. Questa stringa di connessione del modulo verrà usata più avanti nell'esercitazione.

1. Selezionare la scheda **Modulo gemello** e notare che non sono presenti proprietà desiderate o segnalate:

    ```json
    {
      "deviceId": "my-module-device",
      "moduleId": "my-module",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "NjgzMzQ1MzQ1",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "",
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
          },
          "reported": {
            "$metadata": {
              "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
        }
      }
    }
    ```

## <a name="download-the-code"></a>Scaricare il codice

Se non è già stato fatto, clonare il repository GitHub Azure IoT Hub SDK per dispositivi in C# nel computer locale:

Aprire un prompt dei comandi in una cartella di propria scelta. Usare il comando seguente per clonare il repository GitHub di [esempi di Azure IoT in C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) in questa posizione:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="prepare-the-project"></a>Preparare il progetto

Per aprire e preparare il progetto di esempio:

1. Aprire il file di progetto *azure-iot-sdk-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* in Visual Studio 2019.

1. In Visual Studio passare a **Progetto > Thermostat Properties (Proprietà termostato) > Debug**. Aggiungere quindi al progetto le variabili di ambiente seguenti:

    | Nome | Valore |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | connectionString |
    | IOTHUB_MODULE_CONNECTION_STRING | La stringa di connessione del modulo annotata in precedenza |

    Per altre informazioni sulla configurazione di esempio, vedere il file [Leggimi dell'esempio](https://github.com/Azure-Samples/azure-iot-samples-csharp/blob/master/iot-hub/Samples/device/PnpDeviceSamples/readme.md).

## <a name="modify-the-code"></a>Modificare il codice

Per modificare il codice in modo che funzioni come modulo invece che come dispositivo:

1. In Visual Studio aprire *Parameter.cs* e modificare la riga che imposta la variabile **PrimaryConnectionString** come indicato di seguito:

    ```csharp
    public string PrimaryConnectionString { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_MODULE_CONNECTION_STRING");
    ```

1. In Visual Studio aprire *Program.cs* e sostituire le sette istanze della classe `DeviceClient` con la classe `ModuleClient`.

    > [!TIP]
    > Usare la funzionalità di ricerca e sostituzione di Visual Studio con le opzioni **Maiuscole e minuscole** e **Parola intera** abilitate per sostituire `DeviceClient` con `ModuleClient`.

1. In Visual Studio aprire *Thermostat.cs* e sostituire entrambe le istanze della classe `DeviceClient` con la classe `ModuleClient` come segue.

1. Salvare le modifiche apportate ai file.

Se si esegue il codice e quindi si usa Azure IoT Explorer per visualizzare il modulo gemello aggiornato, viene visualizzato il dispositivo gemello aggiornato con la proprietà segnalata dall'ID modello e dal modulo:

```json
{
  "deviceId": "my-module-device",
  "moduleId": "my-mod",
  "etag": "AAAAAAAAAAE=",
  "deviceEtag": "NjgzMzQ1MzQ1",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "modelId": "dtmi:com:example:Thermostat;1",
  "version": 3,
  "properties": {
    "desired": {
      "$metadata": {
        "$lastUpdated": "0001-01-01T00:00:00Z"
      },
      "$version": 1
    },
    "reported": {
      "maxTempSinceLastReboot": 5,
      "$metadata": {
        "$lastUpdated": "2020-09-28T08:53:45.9956637Z",
        "maxTempSinceLastReboot": {
          "$lastUpdated": "2020-09-28T08:53:45.9956637Z"
        }
      },
      "$version": 2
    }
  }
}
```

## <a name="interact-with-a-device-module"></a>Interagire con un modulo di dispositivo

Gli SDK di servizio consentono di recuperare l'ID modello dei dispositivi e dei moduli Plug and Play IoT connessi. È possibile usare gli SDK di servizio per impostare proprietà scrivibili e chiamare i comandi:

1. In un'altra istanza di Visual Studio aprire il progetto *azure-iot-sdk-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj*.

1. In Visual Studio passare a **Progetto > Thermostat Properties (Proprietà termostato) > Debug**. Aggiungere quindi al progetto le variabili di ambiente seguenti:

    | Nome | valore |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | my-module-device |
    | IOTHUB_CONNECTION_STRING | Valore di cui si è preso nota quando è stato completato il passaggio [Configurare l'ambiente](set-up-environment.md) |

    > [!TIP]
    > La stringa di connessione dell'hub IoT si può trovare anche nello strumento Azure IoT Explorer.

1. Aprire il file *Program.cs* e modificare la riga che chiama un comando come indicato di seguito:

    ```csharp
    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, "my-module", commandInvocation);
    ```

1. Nel file *Program.cs* modificare la riga che recupera il dispositivo gemello come indicato di seguito:

    ```csharp
    Twin twin = await s_registryManager.GetTwinAsync(s_deviceId, "my-module");
    ```

1. Verificare che l'esempio di client del modulo sia ancora in esecuzione, quindi eseguire questo esempio di servizio. L'output dell'esempio di servizio mostra l'ID modello del dispositivo gemello e la chiamata al comando:

    ```cmd
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Initialize the service client.
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Get Twin model Id and Update Twin
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Invoke a command
    [09/28/2020 10:53:00]dbug: Thermostat.Program[0]
      Command getMaxMinReport invocation result status is: 200
    ```

    L'output del client del modulo mostra la risposta del gestore del comando:

    ```cmd
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 28/09/2020 10:52:55.
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 28/09/2020 10:52:55: maxTemp=25.4, minTemp=25.4, avgTemp=25.4, startTime=28/09/2020 10:52:56, endTime=28/09/2020 10:52:56
    ```

## <a name="convert-to-an-iot-edge-module"></a>Conversione in un modulo IoT Edge

Per convertire questo esempio in modo che funzioni come modulo IoT Edge di Plug and Play IoT, è necessario inserire l'applicazione in un contenitore. Non è necessario apportare altre modifiche al codice. La variabile di ambiente della stringa di connessione viene inserita dal runtime di IoT Edge all'avvio. Per altre informazioni, vedere [Usare Visual Studio 2019 per sviluppare ed eseguire il debug di moduli per Azure IoT Edge](../iot-edge/how-to-visual-studio-develop-module.md).

Per informazioni su come distribuire il modulo in contenitori, vedere:

* [Eseguire Azure IoT Edge in macchine virtuali Ubuntu](../iot-edge/how-to-install-iot-edge-ubuntuvm.md).
* [Installare il runtime di Azure IoT Edge in sistemi Linux basati su Debian](../iot-edge/how-to-install-iot-edge.md).

È possibile usare lo strumento Azure IoT Explorer per vedere:

* L'ID modello del dispositivo IoT Edge nel modulo gemello.
* I dati di telemetria inviati dal dispositivo IoT Edge.
* Gli aggiornamenti delle proprietà del modulo gemello IoT Edge che attivano le notifiche di Plug and Play IoT.
* La reazione del modulo IoT Edge ai comandi di Plug and Play IoT.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come connettere un dispositivo Plug and Play IoT con moduli a un hub IoT. Per altre informazioni sui modelli di dispositivo Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Guida per sviluppatori alla modellazione di Plug and Play IoT](./concepts-developer-guide-device-csharp.md)