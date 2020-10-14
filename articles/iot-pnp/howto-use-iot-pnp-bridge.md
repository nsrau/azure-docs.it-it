---
title: Come connettere un esempio Plug and Play Bridge in esecuzione su Linux o Windows a un hub Internet. Microsoft Docs
description: Consente di compilare ed eseguire l'Plug and Play Bridge in Linux o Windows che si connette a un hub Internet. Usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate dal dispositivo all'hub.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6670f654685f8d5cdcaf55d2b1679738a57ecab4
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042797"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Come connettere un esempio Plug and Play Bridge in esecuzione in Linux o Windows all'hub Internet

In questa procedura viene illustrato come creare la scheda dell'ambiente di esempio Plug and Play del Bridge, connetterla all'hub Internet e usare lo strumento Azure Internet Explorer per visualizzare i dati di telemetria inviati. Il Bridge Plug and Play è scritto in C e include l'SDK per dispositivi Azure per dispositivi per C. Al termine di questa esercitazione, si dovrebbe essere in grado di eseguire il Plug and Play Bridge e visualizzare i dati di telemetria del report in Azure Internet Explorer: :::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="uno screenshot che mostra Azure Internet Explorer con una tabella di dati di telemetria segnalati (umidità, temperatura) del bridge plug and Play.":::

## <a name="prerequisites"></a>Prerequisiti

Per questo argomento di avvio rapido è possibile usare Linux o Windows. I comandi della shell in questa guida alle procedure seguono la convenzione di Windows per i separatori di percorso ' `\` '. Se si segue in Linux, assicurarsi di scambiare questi separatori per ' `/` '.

I prerequisiti variano in base al sistema operativo:

### <a name="linux"></a>Linux

Questa guida di avvio rapido presuppone l'uso di Ubuntu Linux. I passaggi in questo argomento di avvio rapido sono stati testati con Ubuntu 18.04.

Per completare questo argomento di avvio rapido, installare il software seguente nell'ambiente Linux locale:

Installare **GCC**, **Git**, **cmake** e tutte le dipendenze necessarie usando il comando `apt-get`:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Verificare che la versione di `cmake` sia superiore alla **2.8.12** e che la versione di **GCC** sia superiore alla **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Per completare questo argomento di avvio rapido in Windows, installare il software seguente nell'ambiente Windows locale:

* [Visual Studio (Community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/): assicurarsi di includere il carico di lavoro **Sviluppo di applicazioni desktop con C++** quando si [installa](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Per interagire con il dispositivo di esempio nella seconda parte di questo argomento di avvio rapido, usare lo strumento**Azure IoT Explorer**. [Scaricare e installare la versione di Azure IoT Explorer più recente](./howto-use-iot-explorer.md) per il sistema operativo in uso.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Eseguire il comando seguente per ottenere la _stringa di connessione dell'hub Internet_ per l'hub. Prendere nota della stringa di connessione, poiché verrà usata più avanti in questo argomento di avvio rapido:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> È anche possibile usare lo strumento Azure IoT Explorer per trovare la stringa di connessione dell'hub IoT.

Eseguire il comando seguente per ottenere la _stringa di connessione del dispositivo_ aggiunto all'hub. Prendere nota della stringa di connessione, poiché verrà usata più avanti in questo argomento di avvio rapido:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="view-the-model"></a>Visualizzare il modello

Azure Internet Explorer verrà usato nei passaggi successivi per visualizzare il dispositivo quando si connette all'hub Internet delle cose. Azure Internet Explorer richiede una copia locale del file del modello corrispondente all' **ID modello** inviato dal dispositivo. Il file del modello consente a Internet Explorer di visualizzare i dati di telemetria, le proprietà e i comandi implementati dal dispositivo.

Quando si Scarica il codice nel passaggio seguente, vengono inclusi i file del modello di esempio nella `pnpbridge/docs/schema` cartella. Per preparare Azure Internet Explorer:

1. Nel computer locale creare una cartella denominata *models*.
1. Visualizza [EnvironmentalSensor.js](https://aka.ms/iot-pnp-bridge-env-model) e salva il file JSON nella cartella *Models*
1. Visualizzare [RootBridgeSampleDevice.js](https://aka.ms/iot-pnp-bridge-root-model) e salvare il file JSON nella cartella *Models* .

## <a name="download-the-code"></a>Scaricare il codice

Aprire un prompt dei comandi nella directory di propria scelta. Eseguire il comando seguente per clonare il repository GitHub [plug and Play Bridge](https://aka.ms/iotplugandplaybridge) in questa posizione:

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

Dopo la clonazione del repository Plug and Play Bridge nel computer, aprire un prompt dei comandi amministrativi e passare alla directory del repository clonato:

```cmd
cd pnpbridge
git submodule update --init --recursive
```

Il completamento di questa operazione richiederà alcuni minuti.

>[!NOTE]
> Se si verificano problemi con l'aggiornamento del modulo secondario del clone git, si tratta di un problema noto relativo ai percorsi dei file di Windows e a git: https://github.com/msysgit/git/pull/110 . Per risolvere il problema, è possibile provare il comando seguente: `git config --system core.longpaths true`

## <a name="setting-up-the-configuration-json"></a>Impostazione della configurazione JSON

Dopo la clonazione del repository Plug and Play Bridge nel computer, passare alla `pnpbridge/docs/schema` directory del repository clonato in cui è possibile trovare il file JSON di [configurazione](https://aka.ms/iot-pnp-bridge-env-config) o `config.json` per l'esempio di sensore ambientale del Bridge. Per ulteriori informazioni sui file di configurazione, vedere il documento relativo ai [concetti relativi a plug and Play Bridge](concepts-iot-pnp-bridge.md).

Per il `root-_interface_model_id` campo è necessario copiare il plug and Play ID modello che identifica il modello per il dispositivo. In questo esempio si tratta di `dtmi:com:example:SampleDevice;1`. Modificare i seguenti parametri sotto **pnp_bridge_parameters** nodo del `config.json` file in ':

  Uso della stringa di connessione (Nota: il symmetric_key deve corrispondere alla chiave SAS nella stringa di connessione):

  ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "[To fill in]",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
  ```

 Una volta compilato, il `config.json` file dovrebbe essere simile al seguente:

   ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "dtmi:com:example:SampleDevice;1",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
```

 Una volta compilato il Bridge, sarà necessario posizionarlo `config.json` nella stessa directory del Bridge o specificare il percorso in cui viene eseguito.

## <a name="build-the-iot-plug-and-play-bridge"></a>Crea il Bridge Plug and Play

Passare alla cartella *pnpbridge* nella directory del repository.

Per Windows eseguire quanto segue in una [prompt dei comandi per gli sviluppatori per Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs):

```cmd
cd scripts\windows
build.cmd
```

Analogamente per Linux eseguire quanto segue:

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>In Windows è possibile aprire la soluzione generata dal comando cmake in Visual Studio 2019. Aprire il file di progetto *azure_iot_pnp_bridge. sln* nella directory CMake e impostare il progetto *pnpbridge_bin* come progetto di avvio nella soluzione. È ora possibile compilare l'esempio in Visual Studio ed eseguirlo in modalità di debug.

## <a name="start-the-iot-plug-and-play-bridge"></a>Avvia il Bridge Plug and Play

 Avviare l'esempio Plug and Play Bridge per i sensori ambientali passando alla cartella *pnpbridge* ed eseguendo il comando seguente in un prompt dei comandi:

```bash
 cd cmake/pnpbridge_x86/src/adaptors/samples/environmental_sensor/
./pnpbridge_environmentalsensor

```

```cmd
REM Windows
cd cmake\pnpbridge_x86\src\adaptors\samples\environmental_sensor
Debug\pnpbridge_environmentalsensor.exe
```

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usare Azure IoT Explorer per convalidare il codice

Dopo l'avvio dell'esempio client del dispositivo, usare lo strumento Azure IoT Explorer per verificarne il funzionamento.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come connettere un dispositivo Plug and Play IoT a un hub IoT. Per altre informazioni su come creare una soluzione che interagisce con i dispositivi Plug and Play IoT, vedere:

* [Informazioni sul Bridge Plug and Play](./concepts-iot-pnp-bridge.md)
* [Vedere le informazioni di riferimento per gli sviluppatori di GitHub per Internet Plug and Play Bridge](https://aka.ms/iot-pnp-bridge-dev-doc)
* [Plug and Play Bridge su GitHub](https://aka.ms/iotplugandplaybridge)