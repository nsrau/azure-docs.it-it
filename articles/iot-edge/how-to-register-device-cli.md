---
title: Registrare un nuovo dispositivo Azure IoT Edge (interfaccia della riga di comando) | Microsoft Docs
description: Usare l'estensione IoT per l'interfaccia della riga di comando di Azure per registrare un nuovo dispositivo IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/27/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 737a2dfe5c3b3382db00785b3465147143b17e9e
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51569251"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli"></a>Registrare un nuovo dispositivo Azure IoT Edge con l'interfaccia della riga di comando di Azure

Per poter usare i dispositivi IoT con Azure IoT Edge, è necessario registrarli nell'hub IoT. Dopo la registrazione di un dispositivo, si riceve una stringa di connessione che può essere usata per configurare il dispositivo per i carichi di lavoro Edge. 

L'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) è uno strumento da riga di comando multipiattaforma e open source per la gestione di risorse di Azure come IoT Edge. Consente di gestire le risorse dell'hub IoT di Azure, le istanze del servizio di provisioning di dispositivi e gli hub collegati predefiniti. La nuova estensione IoT arricchisce l'interfaccia della riga di comando di Azure con funzionalità quali la gestione dei dispositivi e le funzionalità complete di IoT Edge.

Questo articolo illustra come registrare un nuovo dispositivo IoT Edge usando l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) nella sottoscrizione di Azure. 
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) nell'ambiente in uso. La versione dell'interfaccia della riga di comando di Azure deve essere 2.0.24 o successiva. Usare il comando `az –-version` per verificare. Questa versione supporta i comandi dell'estensione az e introduce il framework dei comandi Knack. 
* [Estensione IoT per l'interfaccia della riga di comando di Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>Creare un dispositivo

Usare il comando seguente per creare una nuova identità del dispositivo nell'hub IoT: 

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Questo comando include tre parametri:
* **device-id**: specificare un nome descrittivo univoco per l'hub IoT.
* **hub-name**: specificare il nome dell'hub IoT.
* **edge-enabled**: questo parametro dichiara che il dispositivo è destinato all'uso con IoT Edge.

   ![Creare un dispositivo IoT Edge](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>Visualizzare tutti i dispositivi

Usare il comando seguente per visualizzare tutti i dispositivi nell'hub IoT:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Per qualsiasi dispositivo registrato come dispositivo IoT Edge, la proprietà **capabilities.iotEdge** sarà impostata su **true**. 

## <a name="retrieve-the-connection-string"></a>Recuperare la stringa di connessione

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT. Usare il comando seguente per restituire la stringa di connessione per un singolo dispositivo:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name] 
   ```

Per il parametro device-id viene fatta distinzione tra maiuscole e minuscole. Non copiare le virgolette alle estremità della stringa di connessione. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [distribuire moduli in un dispositivo con l'interfaccia della riga di comando di Azure](how-to-deploy-modules-cli.md)
