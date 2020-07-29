---
title: Usare l'estensione IoT di Azure per l'interfaccia della riga di comando di Azure per interagire con i dispositivi Plug and Play IoT di anteprima | Microsoft Docs
description: Installazione dell'estensione IoT di Azure per l'interfaccia della riga di comando di Azure e suo utilizzo per l'interazione con i dispositivi Plug and Play IoT connessi all'hub IoT.
author: dominicbetts
ms.author: dobett
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 3699213fe61c64d7677ba026a8df54ccbbfe4b33
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352229"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Installare e usare l'estensione Azure IoT per l'interfaccia della riga di comando di Azure

L'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) è uno strumento da riga di comando multipiattaforma e open source per la gestione di risorse di Azure come l'hub IoT. L'interfaccia della riga di comando di Azure è disponibile su Windows, Linux e MacOS. L'interfaccia della riga di comando di Azure è preinstallata anche in [Azure Cloud Shell](https://shell.azure.com). L'interfaccia della riga di comando di Azure consente di gestire le risorse dell'hub IoT di Azure, le istanze del servizio di provisioning dei dispositivi e gli hub collegati senza installare alcuna estensione.

L'estensione Azure IoT per l'interfaccia della riga di comando di Azure è uno strumento da riga di comando che consente l'interazione e il test dei dispositivi Plug and Play IoT di anteprima. È possibile usare l'estensione a questi scopi:

- Connettersi a un dispositivo.
- Visualizzare i dati di telemetria inviati dal dispositivo.
- Gestire le proprietà del dispositivo.
- Chiamare i comandi dispositivo.

Questo articolo illustra come:

- Installare e configurare l'estensione IoT di Azure per l'interfaccia della riga di comando di Azure.
- Usare l'estensione per l'interazione e il test dei dispositivi.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Installare l'estensione IoT di Azure per l'interfaccia della riga di comando di Azure

### <a name="step-1---install-the-azure-cli"></a>Passaggio 1 - Installare l'interfaccia della riga di comando di Azure

Seguire le [istruzioni di installazione](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) per configurare l'interfaccia della riga di comando di Azure nell'ambiente. Per un'esperienza ottimale, la versione dell'interfaccia della riga di comando di Azure deve essere 2.9.1 o versione successiva. Usare il comando `az -–version` per verificare.

### <a name="step-2---install-iot-extension"></a>Passaggio 2 - Installare l'estensione IoT

Il [file Leggimi dell'estensione IoT](https://github.com/Azure/azure-iot-cli-extension) illustra diverse modalità per installare l'estensione. Il modo più semplice è quello di eseguire `az extension add --name azure-iot`. Dopo l'installazione, è possibile usare `az extension list` per convalidare le estensioni attualmente installate o `az extension show --name azure-iot` per visualizzare informazioni dettagliate sull'estensione IoT. Al momento della stesura del documento, il numero di versione dell'estensione è `0.9.7` .

Per rimuovere l'estensione, è possibile usare `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Installare l'estensione Azure IoT per l'interfaccia della riga di comando di Azure

### <a name="prerequisites"></a>Prerequisiti

Per accedere alla sottoscrizione di Azure eseguire questo comando:

```azurecli
az login
```

> [!NOTE]
> Se si usa Azure Cloud Shell, l'accesso viene eseguito automaticamente e non è necessario il comando precedente.

Per usare l'estensione IoT di Azure per l'interfaccia della riga di comando di Azure, è necessario:

- Un hub IoT di Azure. Esistono diversi modi per aggiungere un hub IoT alla sottoscrizione di Azure, ad esempio [creando un hub IoT con l'interfaccia della riga di comando di Azure](../iot-hub/iot-hub-create-using-cli.md). La stringa di connessione dell'hub IoT è necessaria per l'esecuzione dei comandi dell'estensione IoT di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Un dispositivo registrato nell'hub IoT. È possibile usare il comando dell'interfaccia della riga di comando di Azure seguente per registrare un dispositivo, assicurarsi di sostituire i segnaposto `{YourIoTHubName}` e `{YourDeviceID}` con i valori:

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

### <a name="interact-with-a-device"></a>Interagire con un dispositivo

È possibile usare l'estensione per visualizzare e interagire con i dispositivi Plug and Play IoT connessi a un hub IoT. L'estensione funziona con il dispositivo gemello digitale che rappresenta il dispositivo Plug and Play IoT.

#### <a name="list-devices"></a>Elencare i dispositivi

Elencare tutti i dispositivi in un hub IoT:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

#### <a name="view-digital-twin"></a>Visualizzazione del dispositivo gemello digitale

Visualizzare il gemello digitale di un dispositivo:

```azurecli
az iot pnp twin show -n {iothub_name} -d {device_id}
```

#### <a name="properties"></a>Proprietà

Impostare il valore di una proprietà di lettura/scrittura:

```azurecli
az iot pnp twin update --hub-name {iothub_name} --device-id {device_id} --json-patch '{"op":"add", "path":"/thermostat1/targetTemperature", "value": 54}'
```

#### <a name="commands"></a>Comandi:

Richiamare un comando:

```azurecli
az iot pnp twin invoke-command --cn getMaxMinReport -n {iothub_name} -d {device_id} --component-path thermostat1
```

#### <a name="digital-twin-events"></a>Eventi gemelli digitali

Monitorare tutti gli eventi gemelli digitali Plug and Play IoT da un dispositivo e un'interfaccia specifici passando al gruppo di consumer dell'hub eventi **$Default**:

```azurecli
az iot hub monitor-events -n {iothub_name} -d {device_id} -i {interface_id}
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sulle procedure si è appreso come installare e usare l'estensione IoT di Azure per l'interfaccia della riga di comando di Azure per interagire con i dispositivi Plug and Play. Un passaggio successivo suggerito consiste nell'imparare a usare Azure Internet [Explorer con i dispositivi](./howto-use-iot-explorer.md).
