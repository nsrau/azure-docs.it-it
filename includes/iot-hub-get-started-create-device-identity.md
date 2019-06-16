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
ms.openlocfilehash: b2bce9788006a564def9bd8c1375a85dc4184b67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66814734"
---
## <a name="create-a-device-identity"></a>Creare un'identità del dispositivo

In questa sezione si usa l'interfaccia della riga di comando di Azure per creare un'identità del dispositivo per questa esercitazione. Il comando di Azure è preinstallato nel [Azure Cloud Shell](~/articles/cloud-shell/overview.md), oppure è possibile [installare CLI Azure localmente](/cli/azure/install-azure-cli). Gli ID dispositivo fanno distinzione tra maiuscole e minuscole.

1. Eseguire il seguente comando nell'ambiente della riga di comando in cui si usa l'interfaccia della riga di comando di Azure per installare l'estensione IoT:

    ```cmd/sh
    az extension add --name azure-cli-iot-ext
    ```

1. Se si sta eseguendo l'interfaccia della riga di comando di Azure localmente, usare il seguente comando per accedere al proprio account Azure (se si usa Cloud Shell, è stato effettuato automaticamente l'accesso e non è necessario eseguire questo comando):

    ```cmd/sh
    az login
    ```

1. Infine, creare una nuova identità del dispositivo denominata `myDeviceId` e recuperare la stringa di connessione con questi comandi:

    ```cmd/sh
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Annotare la stringa di connessione del dispositivo visualizzata nei risultati. Questa stringa di connessione del dispositivo viene usata dall'app del dispositivo per la connessione all'hub IoT come dispositivo.

<!-- images and links -->
