---
title: File di inclusione
description: File di inclusione
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 68260bf8aafbbe5afd46ec7dfb763eb88ee2123e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "78892797"
---
In questa sezione si usa l'interfaccia della riga di comando di Azure per creare un'identità del dispositivo per questo articolo. Gli ID dispositivo fanno distinzione tra maiuscole e minuscole.

1. Aprire [Azure Cloud Shell](https://shell.azure.com/).

1. In Azure Cloud Shell, run the following command to install the Microsoft Azure IoT Extension for Azure CLI:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. Creare una nuova `myDeviceId` identità del dispositivo denominata e recuperare la stringa di connessione del dispositivo con questi comandi:Create a new device identity called and retrieve the device connection string with these commands:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Annotare la stringa di connessione del dispositivo visualizzata nei risultati. Questa stringa di connessione del dispositivo viene usata dall'app del dispositivo per la connessione all'hub IoT come dispositivo.

<!-- images and links -->
