---
title: Connettere il codice del dispositivo di esempio Plug and Play IoT (anteprima) all'hub IoT (Linux) | Microsoft Docs
description: Compilare ed eseguire il codice del dispositivo di esempio Plug and Play IoT (anteprima) su Linux per la connessione a un hub IoT. Usare l'interfaccia della riga di comando di Azure per visualizzare le informazioni inviate dal dispositivo all'hub.
author: dominicbetts
ms.author: dobett
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 38a6deb8d021c5e6a20d765cc7aee5b86042f557
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585977"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub"></a>Guida introduttiva: Connettere un'applicazione per dispositivi Plug and Play IoT (anteprima) in esecuzione in Linux nell'hub IoT

Questa guida di avvio rapido illustra come creare in Linux un'applicazione per dispositivi Plug and Play IoT di esempio, come connetterla all'hub IoT e come usare l'interfaccia della riga di comando di Azure per visualizzare le informazioni inviate all'hub. L'applicazione di esempio è scritta in C ed è inclusa in Azure IoT SDK per dispositivi per C. Uno sviluppatore di soluzioni può usare l'interfaccia della riga di comando di Azure per conoscere le funzionalità di un dispositivo Plug and Play IoT senza visualizzare il codice del dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti

Questa guida di avvio rapido presuppone l'uso di Ubuntu Linux. I passaggi in questa esercitazione sono stati testati con Ubuntu 18.04.

Per completare questa guida di avvio rapido, è necessario installare il software seguente nel computer Linux locale:

Installare **GCC**, **Git**, **cmake** e tutte le dipendenze tramite il comando `apt-get`:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Verificare che la versione di `cmake` sia superiore alla **2.8.12** e che la versione di **GCC** sia superiore alla **4.4.7**.

```sh
cmake --version
gcc --version
```

## <a name="prepare-an-iot-hub"></a>Preparare un hub IoT

Per completare questa guida di avvio rapido, la sottoscrizione di Azure deve includere anche un hub IoT di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!IMPORTANT]
> Durante la fase di anteprima pubblica, le funzionalità Plug and Play IoT sono disponibili solo negli hub IoT creati nelle aree **Stati Uniti centrali**, **Europa settentrionale** e **Giappone orientale**.

Se si usa l'interfaccia della riga di comando di Azure in locale, è necessario che la versione di `az` sia la **2.0.73** o successiva. Azure Cloud Shell usa la versione più recente. Usare il comando `az --version` per verificare la versione installata nel computer.

Aggiungere l'estensione IoT di Microsoft Azure per l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

I passaggi in questa guida di avvio rapido richiedono la versione **0.8.5** o successiva dell'estensione. Usare il comando `az extension list` per verificare la versione installata e il comando `az extension update` per eseguire l'aggiornamento, se necessario.

Se si usa l'interfaccia della riga di comando in locale, accedere alla sottoscrizione di Azure con il comando seguente:

```bash
az login
```

Se si usa Azure Cloud Shell, l'accesso è automatico.

Se non è disponibile alcun hub IoT, seguire [queste istruzioni](../iot-hub/iot-hub-create-using-cli.md) per crearne uno: Nella fase di anteprima pubblica Plug and Play IoT è disponibile nelle aree Europa settentrionale, Stati Uniti centrali e Giappone orientale. Assicurarsi quindi che l'hub venga creato in una di queste aree.

Eseguire il comando seguente per creare l'identità del dispositivo nell'hub IoT. Sostituire il segnaposto **NomeHubIoTpersonale** con il nome dell'hub IoT effettivo:

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id mydevice
```

Eseguire i comandi seguenti per ottenere la _stringa di connessione del dispositivo_ per il dispositivo appena registrato:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id mydevice --output table
```

## <a name="prepare-the-development-environment"></a>Preparare l'ambiente di sviluppo

In questa guida di avvio rapido verrà preparato un ambiente di sviluppo che è possibile usare per clonare e compilare Azure IoT SDK per dispositivi per C.

Aprire un prompt dei comandi. Eseguire il comando seguente per clonare il repository GitHub [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c):

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Il completamento di questo comando richiede diversi minuti.

## <a name="build-the-code"></a>Compilare il codice

Usare l'SDK del dispositivo per compilare il codice di esempio incluso. L'applicazione compilata simula un dispositivo che si connette a un hub IoT. L'applicazione invia proprietà e dati di telemetria e riceve comandi.

1. Creare una sottodirectory `cmake` nella directory radice dell'SDK del dispositivo e passare a tale cartella:

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Eseguire i comandi seguenti per compilare l'SDK del dispositivo e lo stub del codice generato:

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>Aggiornare il repository del modello

Prima di eseguire l'esempio, aggiungere il modello di funzionalità del dispositivo e le definizioni di interfaccia al repository del modello aziendale:

1. Accedere al [portale di Microsoft Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com) con l'account Microsoft aziendale o dell'istituto di istruzione o con l'ID partner Microsoft, se disponibile.

1. Selezionare **Company repository** (Repository aziendale) e quindi **Capability models** (Modelli di funzionalità).

1. Selezionare **Nuovo** e quindi **Carica**.

1. Selezionare il file `SampleDevice.capabilitymodel.json` nella cartella `digitaltwin_client/samples` nella cartella radice dell'SDK del dispositivo. Selezionare **Apri** e quindi **Salva** per caricare il file del modello nel repository.

1. Selezionare **Company repository** (Repository aziendale) e quindi **Interfaces** (Interfacce).

1. Selezionare **Nuovo** e quindi **Carica**.

1. Selezionare il file `EnvironmentalSensor.interface.json` nella cartella `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` nella cartella radice dell'SDK del dispositivo. Selezionare **Apri** e quindi **Salva** per caricare il file di interfaccia nel repository.

1. Select **Company repository** (Repository aziendale) e quindi **Connection strings** (Stringhe di connessione). Prendere nota della prima stringa di connessione del repository del modello aziendale, che verrà usata più avanti nella presente Guida di avvio rapido.

## <a name="run-the-sample"></a>Eseguire l'esempio

Eseguire un'applicazione di esempio nell'SDK per simulare un dispositivo Plug and Play IoT che invia dati di telemetria all'hub IoT. Per eseguire l'applicazione di esempio:

1. Dalla cartella `cmake` passare alla cartella che contiene il file eseguibile:

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Eseguire il file eseguibile:

    ```bash
    ./digitaltwin_sample_device "{your device connection string}"
    ```

Il dispositivo simulato inizia a inviare dati di telemetria e ad ascoltare i comandi e gli aggiornamenti delle proprietà.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Usare l'interfaccia della riga di comando di Azure IoT per convalidare il codice

Dopo l'avvio dell'esempio client del dispositivo, verificare che funzioni con l'interfaccia della riga di comando di Azure.

Usare il comando seguente per visualizzare i dati di telemetria inviati dal dispositivo di esempio. Potrebbe essere necessario attendere un minuto o due prima che i dati di telemetria vengano visualizzati nell'output:

```azurecli-interactive
az iot dt monitor-events --hub-name {your IoT hub} --device-id mydevice
```

Usare il comando seguente per visualizzare le proprietà inviate dal dispositivo:

```azurecli-interactive
az iot dt list-properties --hub-name {your IoT hub} --device-id mydevice --interface sensor --source private --repo-login "{your company model repository connection string}"
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come connettere un dispositivo Plug and Play IoT a un hub IoT. Per altre informazioni su come creare una soluzione che interagisce con i dispositivi Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Procedura: Connettersi a un dispositivo e interagire con esso](howto-develop-solution.md)
