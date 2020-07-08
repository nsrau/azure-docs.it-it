---
title: includere file
description: includere file
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 68260bf8aafbbe5afd46ec7dfb763eb88ee2123e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78892797"
---
In questa sezione si usa l'interfaccia della riga di comando di Azure per creare un'identità del dispositivo per questo articolo. Gli ID dispositivo fanno distinzione tra maiuscole e minuscole.

1. Aprire [Azure Cloud Shell](https://shell.azure.com/).

1. In Azure Cloud Shell, eseguire questo comando per aggiungere l'estensione Microsoft Azure IoT per l'interfaccia della riga di comando di Azure:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. Creare una nuova identità del dispositivo denominata `myDeviceId` e recuperare la stringa di connessione con questi comandi:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Annotare la stringa di connessione del dispositivo visualizzata nei risultati. Questa stringa di connessione del dispositivo viene usata dall'app del dispositivo per la connessione all'hub IoT come dispositivo.

<!-- images and links -->
