---
title: Configura le risorse di tutto il necessario per Plug and Play Microsoft Docs
description: Creare un'istanza del servizio Device provisioning e dell'hub Internet per l'uso con le guide introduttive e le esercitazioni Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 08/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 3b7c9b51bad45bb348f70c8b0e433404b49b5aac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761363"
---
# <a name="set-up-your-environment-for-the-iot-plug-and-play-quickstarts-and-tutorials"></a>Configurare l'ambiente per le guide introduttive e le esercitazioni Plug and Play

Prima di poter completare qualsiasi cosa Plug and Play guide introduttive e le esercitazioni, è necessario configurare un hub e il servizio Device provisioning (DPS) nella sottoscrizione di Azure. Sono inoltre necessarie copie locali dei file di modello usati dalle applicazioni di esempio e dallo strumento Azure Internet Explorer.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Per evitare la necessità di installare l'interfaccia della riga di comando di Azure in locale, è possibile usare la Azure Cloud Shell per configurare i servizi cloud.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resources"></a>Creare le risorse

Creare un gruppo di risorse di Azure per le risorse:

```azurecli-interactive
az group create --name my-pnp-resourcegroup --location centralus
```

Creare un hub IoT. Il comando seguente usa il nome `my-pnp-hub` come esempio per il nome dell'hub Internet delle cose da creare. Scegliere un nome univoco per l'hub delle cose da usare al posto di `my-pnp-hub` :

```azurecli-interactive
az iot hub create --name my-pnp-hub --resource-group my-pnp-resourcegroup --sku F1 --partition-count 2
```

Creare un'istanza di DPS. Il comando seguente usa il nome `my-pnp-dps` come esempio per il nome dell'istanza di DPS da creare. Scegliere un nome univoco per l'istanza di DPS da usare al posto di `my-pnp-dps` :

```azurecli-interactive
az iot dps create --name my-pnp-dps --resource-group my-pnp-resourcegroup
```

Per collegare l'istanza di DPS all'hub Internet, usare i comandi seguenti. Sostituire `my-pnp-dps` e `my-pnp-hub` con i nomi univoci scelti in precedenza:

```azurecli-interactive
hubConnectionString=$(az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString -o tsv)
az iot dps linked-hub create --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --location centralus --connection-string $hubConnectionString
```

## <a name="retrieve-the-settings"></a>Recuperare le impostazioni

Per alcune guide introduttive ed esercitazioni viene usata la stringa di connessione per l'hub. È necessaria anche la stringa di connessione quando si configura lo strumento Azure Internet Explorer. Recuperare la stringa di connessione e annotarla ora. Sostituire `my-pnp-hub` con il nome univoco scelto per l'hub Internet delle cose:

```azurecli-interactive
az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString
```

La maggior parte delle guide introduttive ed esercitazioni usano l' *ambito ID* della configurazione DPS. Recuperare l'ambito ID e annotarlo ora. Sostituire `my-pnp-dps` con il nome univoco scelto per l'istanza di DPS:

```azurecli-interactive
az iot dps show --name my-pnp-dps --query properties.idScope
```

Tutte le guide introduttive e le esercitazioni usano una registrazione del dispositivo DPS. Usare il comando seguente per creare una `my-pnp-device` *registrazione del singolo dispositivo* nell'istanza di DPS. Sostituire `my-pnp-dps` con il nome univoco scelto per l'istanza di DPS. Prendere nota dell'ID registrazione e dei valori di chiave primaria da usare nelle guide introduttive ed esercitazioni:

```azurecli-interactive
az iot dps enrollment create --attestation-type symmetrickey --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --enrollment-id my-pnp-device --device-id my-pnp-device --query '{registrationID:registrationId,primaryKey:attestation.symmetricKey.primaryKey}'
```

## <a name="create-environment-variables"></a>Creare variabili di ambiente

Creare cinque variabili di ambiente per configurare gli esempi nelle guide introduttive e le esercitazioni per usare il servizio Device provisioning (DPS) per connettersi all'hub Internet:

* **IOTHUB_DEVICE_SECURITY_TYPE**: valore `DPS` .
* **IOTHUB_DEVICE_DPS_ID_SCOPE**: l'ambito dell'ID DPS è stato annotato in precedenza.
* **IOTHUB_DEVICE_DPS_DEVICE_ID**: valore `my-pnp-device` .
* **IOTHUB_DEVICE_DPS_DEVICE_KEY**: la chiave primaria di registrazione di cui si è preso nota in precedenza.
* **IOTHUB_DEVICE_DPS_ENDPOINT**: valore `global.azure-devices-provisioning.net`

Gli esempi di servizio richiedono le seguenti variabili di ambiente per identificare l'hub e il dispositivo a cui connettersi:

* **IOTHUB_CONNECTION_STRING**: stringa di connessione dell'hub IoT annotata in precedenza.
* **IOTHUB_DEVICE_ID**: `my-pnp-device`.

Ad esempio, in una shell bash Linux:

```bash
export IOTHUB_DEVICE_SECURITY_TYPE="DPS"
export IOTHUB_DEVICE_DPS_ID_SCOPE="<Your ID scope>"
export IOTHUB_DEVICE_DPS_DEVICE_ID="my-pnp-device"
export IOTHUB_DEVICE_DPS_DEVICE_KEY="<Your enrolment primary key>"
export IOTHUB_DEVICE_DPS_ENDPOINT="global.azure-devices-provisioning.net"
export IOTHUB_CONNECTION_STRING="<Your IoT hub connection string>"
export IOTHUB_DEVICE_ID="my-pnp-device"
```

Ad esempio, nella riga di comando di Windows:

```cmd
set IOTHUB_DEVICE_SECURITY_TYPE=DPS
set IOTHUB_DEVICE_DPS_ID_SCOPE=<Your ID scope>
set IOTHUB_DEVICE_DPS_DEVICE_ID=my-pnp-device
set IOTHUB_DEVICE_DPS_DEVICE_KEY=<Your enrolment primary key>
set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
set IOTHUB_CONNECTION_STRING=<Your IoT hub connection string>
set IOTHUB_DEVICE_ID=my-pnp-device
```

## <a name="download-the-model-files"></a>Scaricare i file del modello

Le guide introduttive e le esercitazioni usano file di modello di esempio per il controller di temperatura e i dispositivi del termostato. Per scaricare i file del modello di esempio:

1. Nel computer locale creare una cartella denominata *models*.

1. Fare clic con il pulsante destro del mouse su [TemperatureController.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json) e salvare il file JSON nella cartella *models*.

1. Fare clic con il pulsante destro del mouse su [Thermostat.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json) e salvare il file JSON nella cartella *models*.

## <a name="install-the-azure-iot-explorer"></a>Installare Azure IoT Explorer

Le guide introduttive e le esercitazioni usano lo strumento **Azure Internet Explorer** . Passare ad [Azure Internet Explorer releases](https://github.com/Azure/azure-iot-explorer/releases) ed espandere l'elenco degli asset per la versione più recente. Scaricare e installare la versione più recente dell'applicazione per il sistema operativo in uso.

La prima volta che si esegue lo strumento, viene richiesta la stringa di connessione dell'hub Internet. Usare la stringa di connessione di cui si è preso nota in precedenza.

Configurare lo strumento per l'uso dei file di modello scaricati in precedenza. Dal home page nello strumento selezionare **impostazioni plug and Play**Internet e quindi **+ Aggiungi > cartella locale**. Selezionare la cartella *Models* creata in precedenza. Quindi selezionare **Save (Salva** ) per salvare le impostazioni.

Per altre informazioni, vedere [Installare e usare Azure IoT Explorer](howto-use-iot-explorer.md).

## <a name="remove-the-resources"></a>Rimuovere le risorse

È possibile usare l'hub e l'istanza di DPS per tutte le guide introduttive e le esercitazioni di Plug and Play, quindi è necessario completare la procedura in questo articolo una sola volta. Al termine, è possibile rimuoverli dalla sottoscrizione con il comando seguente:

```azurecli-interactive
az group delete --name my-pnp-resourcegroup
```

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato configurato l'ambiente, è possibile provare una delle guide introduttive o le esercitazioni, ad esempio:

> [!div class="nextstepaction"]
> [Connettere un'app di esempio Plug and Play dispositivo all'hub Internet (Node.js)](quickstart-connect-device-node.md)
