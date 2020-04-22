---
title: Usare l'estensione IoT di Azure per l'interfaccia della riga di comando di Azure per interagire con i dispositivi IoT Plug and Play Preview. Documenti Microsoft
description: Installare l'estensione IoT di Azure per l'interfaccia della riga di comando di Azure e usarla per interagire con i dispositivi Plug and Play IoT connessi all'hub IoT.Install the Azure IoT extension for Azure CLI and use it to interact with the IoT Plug and Play devices connected to my IoT hub.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 1ccb32996cd8f15805a810dd5b5985aeb5f87c26
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770450"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Installare e usare l'estensione IoT di Azure per l'interfaccia della riga di comando di AzureInstall and use the Azure IoT extension for the Azure CLI

[L'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) di Azure è un'interfaccia di comando open source per la gestione delle risorse di Azure, ad esempio Hub IoT.The Azure CLI is an open-source cross platform command-line tool for managing Azure resources such as IoT Hub. L'interfaccia della riga di comando di Azure è disponibile in Windows, Linux e MacOS.The Azure CLI is available on Windows, Linux, and MacOS. L'interfaccia della riga di comando di Azure è preinstallata anche in [Azure Cloud Shell.](https://shell.azure.com) L'interfaccia della riga di comando di Azure consente di gestire le risorse dell'hub IoT di Azure, le istanze del servizio di provisioning dei dispositivi e gli hub collegati senza installare estensioni.

L'estensione IoT di Azure per l'interfaccia della riga di comando di Azure è uno strumento della riga di comando per interagire con i dispositivi Plug and Play Preview IoT.The Azure IoT extension for the Azure CLI is a command-line tool for interacting with, and testing IoT Plug and Play Preview devices. È possibile utilizzare l'estensione per:

- Connettersi a un dispositivo.
- Visualizzare i dati di telemetria inviati dal dispositivo.
- Utilizzare le proprietà del dispositivo.
- Chiamare i comandi del dispositivo.

Questo articolo illustra come:

- Installare e configurare l'estensione IoT di Azure per l'interfaccia della riga di comando di Azure.Install and configure the Azure IoT extension for the Azure CLI.
- Usa l'estensione per interagire con i tuoi dispositivi e testarli.
- Utilizzare l'estensione per gestire le interfacce nel repository dei modelli.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Installare l'estensione IoT di Azure per l'interfaccia della riga di comando di AzureInstall Azure IoT extension for the Azure CLI

### <a name="step-1---install-the-azure-cli"></a>Passaggio 1 - Installare l'interfaccia della riga di comando di AzureStep 1 - Install the Azure CLI

Seguire le istruzioni di [installazione](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) per configurare l'interfaccia della riga di comando di Azure nell'ambiente. Per usare tutti i comandi seguenti, la versione dell'interfaccia della riga di comando di Azure deve essere versione 2.0.73 o successiva. Usare il comando `az -–version` per verificare.

### <a name="step-2---install-iot-extension"></a>Passaggio 2 - Installare l'estensione IoTStep 2 - Install IoT extension

Il [file Leggimi dell'estensione IoT](https://github.com/Azure/azure-iot-cli-extension) illustra diverse modalità per installare l'estensione. Il modo più semplice è quello di eseguire `az extension add --name azure-iot`. Dopo l'installazione, è possibile usare `az extension list` per convalidare le estensioni attualmente installate o `az extension show --name azure-iot` per visualizzare informazioni dettagliate sull'estensione IoT. Per rimuovere l'estensione, è possibile usare `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Usare l'estensione IoT di Azure per l'interfaccia della riga di comando di AzureUse Azure IoT extension for the Azure CLI

### <a name="prerequisites"></a>Prerequisiti

Per accedere alla sottoscrizione di Azure, eseguire il comando seguente:To sign in to your Azure subscription, run the following command:

```azurecli
az login
```

> [!NOTE]
> Se si usa la shell cloud di Azure, l'accesso viene eseguito automaticamente e non è necessario eseguire il comando precedente.

Per usare l'estensione IoT di Azure per l'interfaccia della riga di comando di Azure, è necessario:To use the Azure IoT extension for the Azure CLI, you need:

- Un hub IoT di Azure. Esistono molti modi per aggiungere un hub IoT alla sottoscrizione di Azure, ad esempio [Creare un hub IoT usando l'interfaccia della riga](../iot-hub/iot-hub-create-using-cli.md)di comando di Azure.There are many ways to add an IoT hub to your Azure subscription, such as Create an IoT hub using the Azure CLI . È necessaria la stringa di connessione dell'hub IoT per eseguire i comandi di estensione IoT di Azure.You need the IoT hub's connection string to run the Azure IoT extension commands. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Un dispositivo registrato nell'hub IoT. È possibile usare il comando cli di Azure seguente `{YourIoTHubName}` per `{YourDeviceID}` registrare un dispositivo, assicurarsi di sostituire i segnaposto e con i valori:

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- Alcuni comandi richiedono la stringa di connessione per un repository di modelli aziendali. Un repository di modelli per l'azienda viene creato quando si esegue [l'onboarding al portale Azure Certified for IoT](howto-onboard-portal.md). Una terza parte potrebbe condividere la stringa di connessione del repository dei modelli con l'utente per accedere alle relative interfacce e ai modelli.

### <a name="interact-with-a-device"></a>Interagire con un dispositivo

È possibile usare l'estensione per visualizzare e interagire con i dispositivi Plug and Play IoT connessi a un hub IoT.You can use the extension to view and interact with IoT Plug and Play devices that are connected to an IoT hub. L'estensione funziona con il gemello digitale che rappresenta il dispositivo IoT Plug and Play.

#### <a name="list-devices-and-interfaces"></a>Elencare dispositivi e interfacce

Elencare tutti i dispositivi in un hub IoT:List all devices on an IoT Hub:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

Elencare tutte le interfacce registrate da un dispositivo Plug and Play IoT:

```azurecli
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>Proprietà

Elencare tutte le proprietà e i valori delle proprietà per un'interfaccia in un dispositivo:List all properties and property values for an interface on a device:

```azurecli
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

Impostare il valore di una proprietà di lettura/scrittura:Set the value of a read-write property:

```azurecli
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

Un file di payload di esempio per impostare la proprietà **name** nell'interfaccia **del sensore** di un dispositivo su Contoso è simile al seguente:An example payload file to set the name property on the sensor interface of a device to **Contoso** looks like the following:

```json
{
  "sensor": {
    "properties": {
      "name": {
        "desired": {
          "value": "Contoso"
        }
      }
    }
  }
}
```

#### <a name="commands"></a>Comandi:

Elencare tutti i comandi per un'interfaccia su un dispositivo:List all commands for an interface on a device:

```azurecli
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

Senza `--repo-login` il parametro, questo comando utilizza il repository dei modelli pubblici.

Richiamare un comando:

```azurecli
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>Eventi gemelli digitali

Monitorare tutti gli eventi ioT Plug and Play digital twin da un dispositivo e un'interfaccia specifici che vanno al gruppo di consumer dell'hub **$Default** degli eventi:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

Monitorare tutti gli eventi ioT Plug and Play digitali gemelli da un dispositivo e un'interfaccia specifici che vanno a un gruppo di consumatori specifico:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>Gestire le interfacce in un repository di modelliManage interfaces in a model repository

I comandi seguenti usano il repository pubblico dei modelli Plug and Play dell'IoT. Per usare un repository di `--login` modelli aziendali, aggiungere l'argomento con la stringa di connessione del repository dei modelli.

Elencare le interfacce nel repository pubblico dei modelli Plug and Play dell'IoT:

```azurecli
az iot pnp interface list
```

Mostra un'interfaccia nel repository pubblico dei modelli Plug and Play dell'IoT:

```azurecli
az iot pnp interface show --interface {YourInterfaceId}
```

Creare un'interfaccia nel repository di modelli aziendali IoT Plug and Play:Create an interface in your IoT Plug and Play company model repository:

```azurecli
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Non è possibile creare direttamente un'interfaccia nel repository dei modelli pubblici.

Aggiornare un'interfaccia nel repository di modelli aziendali IoT Plug and Play:

```azurecli
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Non è possibile aggiornare direttamente un'interfaccia nel repository dei modelli pubblici.

Pubblicare un'interfaccia dal repository di modelli aziendali IoT Plug and Play nel repository dei modelli pubblici. Questa operazione rende l'interfaccia non modificabile:This operation makes the interface immutable:

```azurecli
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Solo i partner Microsoft possono pubblicare interfacce nel repository dei modelli pubblici.

### <a name="manage-device-capability-models-in-a-model-repository"></a>Gestire i modelli di funzionalità del dispositivo in un repository di modelliManage device capability models in a model repository

I comandi seguenti usano il repository pubblico dei modelli Plug and Play dell'IoT. Per usare un repository di `--login` modelli aziendali, aggiungere l'argomento con la stringa di connessione del repository dei modelli.

Elencare i modelli di funzionalità dei dispositivi nel repository dei modelli pubblici IoT Plug and Play:

```azurecli
az iot pnp capability-model list
```

Mostra un modello di funzionalità del dispositivo nel repository dei modelli pubblici IoT Plug and Play:

```azurecli
az iot pnp capability-model show --model {YourModelID}
```

Creare un modello di funzionalità del dispositivo in un repository di modelli aziendali Plug and Play IoT:Create a device capability model in an IoT Plug and Play company model repository:

```azurecli
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Non è possibile creare direttamente un modello nel repository dei modelli pubblici.

Aggiornare un modello di funzionalità del dispositivo nel repository di modelli aziendali Plug and Play IoT:Update a device capability model in the IoT Plug and Play company model repository:

```azurecli
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Non è possibile aggiornare direttamente un modello nel repository dei modelli pubblici.

Pubblicare un modello di funzionalità del dispositivo dal repository di modelli aziendali IoT Plug and Play nel repository dei modelli pubblici. Questa operazione rende il modello immutabile:This operation makes the model immutable:

```azurecli
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Solo i partner Microsoft possono pubblicare modelli nel repository dei modelli pubblici.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo delle procedure è stato illustrato come installare e usare l'estensione IoT di Azure affinché l'interfaccia della riga di comando di Azure interagisca con i dispositivi Plug and Play.In this how-to article, you've learned how to install and use the Azure IoT extension for the Azure CLI to interact with your Plug and Play devices. Un passaggio successivo suggerito consiste nell'imparare a [gestire i modelli](./howto-manage-models.md).
