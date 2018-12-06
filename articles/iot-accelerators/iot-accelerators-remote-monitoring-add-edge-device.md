---
title: Aggiunta di un dispositivo Edge alla soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questo articolo descrive come aggiungere un dispositivo IoT Edge a un acceleratore della soluzione di monitoraggio remoto
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: 67bfde828287d9892ad404f3d950dbe373503a56
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2018
ms.locfileid: "51827540"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>Aggiungere un dispositivo IoT Edge all'acceleratore della soluzione di monitoraggio remoto

Per aggiungere un dispositivo [IoT Edge](../iot-edge/about-iot-edge.md) all'acceleratore della soluzione, completare i due passaggi seguenti:

1. Aggiungere il dispositivo Edge nella pagina **Dispositivi** nell'interfaccia utente Web dell'acceleratore della soluzione di monitoraggio remoto.
1. Installare il runtime IoT Edge nel dispositivo Edge.

## <a name="add-the-iot-edge-device"></a>Aggiungere il dispositivo IoT Edge

Per aggiungere un dispositivo IoT Edge all'acceleratore di soluzione di monitoraggio remoto, passare alla pagina **Dispositivi** nell'interfaccia utente Web e fare clic su **+ Nuovo dispositivo**.

Nel pannello **Nuovo dispositivo** scegliere **Dispositivo IoT Edge**. È possibile lasciare i valori predefiniti per le altre impostazioni. Fare quindi clic su **Applica**:

![Aggiungere il dispositivo IoT Edge](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>Modi alternativi per aggiungere un dispositivo IoT Edge

È anche possibile registrare un dispositivo IoT Edge direttamente nell'istanza dell'hub IoT nell'acceleratore della soluzione. Prima di seguire queste guide procedurali, tuttavia, è necessario conoscere il nome dell'hub IoT nell'acceleratore della soluzione:

- [Registrare un nuovo dispositivo Azure IoT Edge dal portale di Azure](../iot-edge/how-to-register-device-portal.md)
- [Registrare un nuovo dispositivo Azure IoT Edge con l'interfaccia della riga di comando di Azure](../iot-edge/how-to-register-device-cli.md)
- [Registrare un nuovo dispositivo Azure IoT Edge da Visual Studio Code](../iot-edge/how-to-register-device-vscode.md)

Quando si registra un dispositivo direttamente nell'hub IoT nell'acceleratore della soluzione di monitoraggio remoto, il dispositivo viene elencato nella pagina **Dispositivi** dell'interfaccia utente Web.

## <a name="install-the-iot-edge-runtime"></a>Installare il runtime IoT Edge.

Prima di poter distribuire i moduli nel dispositivo Edge, è necessario installare il runtime IoT Edge nel dispositivo fisico. Le seguenti guide procedurali illustrano come installare il runtime su piattaforme per dispositivi comuni:

- [Installare il runtime di Azure IoT Edge in Linux (x64)](../iot-edge/how-to-install-iot-edge-linux.md)
- [Installare il runtime di Azure IoT Edge in Linux (ARM32v7/armhf)](../iot-edge/how-to-install-iot-edge-linux-arm.md)
- [Installare il runtime Azure IoT Edge in Windows per l'uso con contenitori Windows](../iot-edge/how-to-install-iot-edge-windows-with-windows.md)
- [Installare il runtime Azure IoT Edge in Windows per l'uso con contenitori Linux](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [Installare il runtime IoT Edge su Windows IoT Core](../iot-edge/how-to-install-iot-core.md)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver preparato il dispositivo IoT Edge, è ora possibile distribuire i moduli nel dispositivo. Vedere [Importare un pacchetto IoT Edge nell'acceleratore della soluzione di monitoraggio remoto](iot-accelerators-remote-monitoring-import-edge-package.md)
