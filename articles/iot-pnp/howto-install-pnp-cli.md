---
title: Usare l'estensione IoT di Azure per l'interfaccia della riga di comando di Azure per interagire con i dispositivi Plug and Play IoT di anteprima | Microsoft Docs
description: Installazione dell'estensione IoT di Azure per l'interfaccia della riga di comando di Azure e suo utilizzo per l'interazione con i dispositivi Plug and Play IoT connessi all'hub IoT.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 1ccb32996cd8f15805a810dd5b5985aeb5f87c26
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770450"
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
- Usare l'estensione per gestire le interfacce nel repository di modelli.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Installare l'estensione IoT di Azure per l'interfaccia della riga di comando di Azure

### <a name="step-1---install-the-azure-cli"></a>Passaggio 1 - Installare l'interfaccia della riga di comando di Azure

Seguire le [istruzioni di installazione](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) per configurare l'interfaccia della riga di comando di Azure nell'ambiente. Per usare tutti i comandi seguenti, la versione dell'interfaccia della riga di comando di Azure deve essere 2.0.73 o versione successiva. Usare il comando `az -–version` per verificare.

### <a name="step-2---install-iot-extension"></a>Passaggio 2 - Installare l'estensione IoT

Il [file Leggimi dell'estensione IoT](https://github.com/Azure/azure-iot-cli-extension) illustra diverse modalità per installare l'estensione. Il modo più semplice è quello di eseguire `az extension add --name azure-iot`. Dopo l'installazione, è possibile usare `az extension list` per convalidare le estensioni attualmente installate o `az extension show --name azure-iot` per visualizzare informazioni dettagliate sull'estensione IoT. Per rimuovere l'estensione, è possibile usare `az extension remove --name azure-iot`.

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

- Per alcuni comandi è necessaria la stringa di connessione per un repository di modelli aziendale. Il repository di modelli per l'azienda viene creato quando si esegue l'[onboarding per la prima volta nel portale Microsoft Azure Certified per IoT](howto-onboard-portal.md). Una terza parte può condividere la stringa di connessione del repository di modelli con l'utente per consentire l'accesso alle interfacce e ai modelli.

### <a name="interact-with-a-device"></a>Interagire con un dispositivo

È possibile usare l'estensione per visualizzare e interagire con i dispositivi Plug and Play IoT connessi a un hub IoT. L'estensione funziona con il dispositivo gemello digitale che rappresenta il dispositivo Plug and Play IoT.

#### <a name="list-devices-and-interfaces"></a>Elencare i dispositivi e le interfacce

Elencare tutti i dispositivi in un hub IoT:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

Elencare tutte le interfacce registrate da un dispositivo Plug and Play IoT:

```azurecli
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>Proprietà

Elencare tutte le proprietà e i valori delle proprietà per un'interfaccia in un dispositivo:

```azurecli
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

Impostare il valore di una proprietà di lettura/scrittura:

```azurecli
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

Un file di payload di esempio per impostare la proprietà **name** sull'interfaccia del **sensore** di un dispositivo per **Contoso** è simile a quanto riportato di seguito:

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

#### <a name="commands"></a>Comandi

Elencare tutti i comandi per un'interfaccia in un dispositivo:

```azurecli
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

In assenza del parametro `--repo-login`, questo comando usa il repository di modelli pubblico.

Richiamare un comando:

```azurecli
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>Eventi gemelli digitali

Monitorare tutti gli eventi gemelli digitali Plug and Play IoT da un dispositivo e un'interfaccia specifici passando al gruppo di consumer dell'hub eventi **$Default**:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

Monitorare tutti gli eventi gemelli digitali Plug and Play IoT da un dispositivo e un'interfaccia specifici passando a un gruppo di consumer specifico:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>Gestire le interfacce in un repository di modelli

I comandi seguenti usano il repository di modelli Plug and Play IoT pubblico. Per usare un repository di modelli aziendale, aggiungere l'argomento `--login` con la stringa di connessione del repository di modelli.

Elencare le interfacce nel repository di modelli Plug and Play IoT pubblico:

```azurecli
az iot pnp interface list
```

Mostrare un'interfaccia nel repository di modelli Plug and Play IoT pubblico:

```azurecli
az iot pnp interface show --interface {YourInterfaceId}
```

Creare un'interfaccia nel repository di modelli Plug and Play IoT pubblico:

```azurecli
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Non è possibile creare direttamente un'interfaccia nel repository di modelli pubblico.

Aggiornare un'interfaccia nel repository di modelli Plug and Play IoT aziendale:

```azurecli
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Non è possibile aggiornare direttamente un'interfaccia nel repository di modelli pubblico.

Pubblicare un'interfaccia dal repository di modelli Plug and Play IoT aziendale nel repository di modelli pubblico. Questa operazione rende l'interfaccia non modificabile:

```azurecli
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Solo i partner Microsoft possono pubblicare le interfacce nel repository di modelli pubblico.

### <a name="manage-device-capability-models-in-a-model-repository"></a>Gestire i modelli di funzionalità di dispositivo in un repository di modelli

I comandi seguenti usano il repository di modelli Plug and Play IoT pubblico. Per usare un repository di modelli aziendale, aggiungere l'argomento `--login` con la stringa di connessione del repository di modelli.

Elencare i modelli di funzionalità di dispositivo nel repository di modelli Plug and Play IoT pubblico:

```azurecli
az iot pnp capability-model list
```

Mostrare un modello di funzionalità di dispositivo nel repository di modelli Plug and Play IoT pubblico:

```azurecli
az iot pnp capability-model show --model {YourModelID}
```

Creare un modello di funzionalità di dispositivo in un repository di modelli Plug and Play IoT aziendale:

```azurecli
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Non è possibile creare direttamente un modello nel repository di modelli pubblico.

Aggiornare un modello di funzionalità di dispositivo nel repository di modelli Plug and Play IoT aziendale:

```azurecli
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Non è possibile aggiornare direttamente un modello nel repository di modelli pubblico.

Pubblicare un modello di funzionalità di dispositivo dal repository di modelli Plug and Play IoT aziendale nel repository di modelli pubblico. Questa operazione rende il modello non modificabile:

```azurecli
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Solo i partner Microsoft possono pubblicare i modelli nel repository di modelli pubblico.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sulle procedure si è appreso come installare e usare l'estensione IoT di Azure per l'interfaccia della riga di comando di Azure per interagire con i dispositivi Plug and Play. Un passaggio successivo suggerito consiste nell'apprendere come [gestire i modelli](./howto-manage-models.md).
