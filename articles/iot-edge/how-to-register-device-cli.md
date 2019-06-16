---
title: Registrare un nuovo dispositivo dalla riga di comando - Azure IoT Edge | Microsoft Docs
description: Usare l'estensione IoT per l'interfaccia della riga di comando di Azure per registrare un nuovo dispositivo IoT Edge e recuperare la stringa di connessione
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: de00c317483da9bcd93bb2e2505350d787385925
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66495385"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli"></a>Registrare un nuovo dispositivo Azure IoT Edge con l'interfaccia della riga di comando di Azure

Per poter usare i dispositivi IoT con Azure IoT Edge, è necessario registrarli nell'hub IoT. Dopo avere registrato un dispositivo, si riceve una stringa di connessione che può essere utilizzata per configurare il dispositivo per i carichi di lavoro di IoT Edge.

L'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) è uno strumento da riga di comando multipiattaforma e open source per la gestione di risorse di Azure come IoT Edge. Consente di gestire le risorse dell'hub IoT di Azure, le istanze del servizio di provisioning di dispositivi e gli hub collegati predefiniti. La nuova estensione IoT arricchisce l'interfaccia della riga di comando di Azure con funzionalità quali la gestione dei dispositivi e le funzionalità complete di IoT Edge.

Questo articolo illustra come registrare un nuovo dispositivo IoT Edge usando l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) nella sottoscrizione di Azure.
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) nell'ambiente in uso. La versione dell'interfaccia della riga di comando di Azure deve essere 2.0.24 o successiva. Usare il comando `az –-version` per verificare. Questa versione supporta i comandi dell'estensione az e introduce il framework dei comandi Knack.
* [Estensione IoT per l'interfaccia della riga di comando di Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>Creare un dispositivo

Usare la [az iot hub device-identity creare](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) comando per creare una nuova identità del dispositivo nell'hub IoT. Ad esempio:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Questo comando include tre parametri:

* **device-id**: specificare un nome descrittivo univoco per l'hub IoT.

* **hub-name**: specificare il nome dell'hub IoT.

* **edge-enabled**: questo parametro dichiara che il dispositivo è destinato all'uso con IoT Edge.

   ![az iot hub device-identity create output](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>Visualizzare tutti i dispositivi

Usare la [elenco di az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) comando per visualizzare tutti i dispositivi nell'hub IoT. Ad esempio:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Per qualsiasi dispositivo registrato come dispositivo IoT Edge, la proprietà **capabilities.iotEdge** sarà impostata su **true**.

## <a name="retrieve-the-connection-string"></a>Recuperare la stringa di connessione

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT. Usare la [az iot hub device-identity show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) comando per restituire la stringa di connessione per un singolo dispositivo:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

Per il parametro `device-id` viene fatta distinzione tra maiuscole e minuscole. Non copiare le virgolette alle estremità della stringa di connessione.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [distribuire moduli in un dispositivo con Azure CLI](how-to-deploy-modules-cli.md).
