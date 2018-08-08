---
title: Creare un hub IoT di Azure usando Azure IoT Toolkit per Visual Studio Code | Microsoft Docs
description: Come usare Azure IoT Toolkit per Visual Studio Code per creare un hub IoT.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: junhan
ms.openlocfilehash: 71b188443ee48d742b14753cd5526edac493d9e3
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368619"
---
# <a name="create-an-iot-hub-using-the-azure-iot-toolkit-for-visual-studio-code"></a>Creare un hub IoT con Azure IoT Toolkit per Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

È possibile usare [Azure IoT Toolkit per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) per creare hub IoT di Azure. Questo articolo illustra come creare un hub IoT con Azure IoT Toolkit.

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* Un account Azure attivo.
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

## <a name="create-an-iot-hub"></a>Creare un hub IoT

1. In Visual Studio Code aprire la **finestra di esplorazione**.

2. Nella parte inferiore della finestra di esplorazione espandere la sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure). 

   ![Espandere i dispositivi dell'hub IoT di Azure](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Fare clic sui puntini di sospensione (**...**) nell'intestazione della sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure). Se i puntini di sospensione non sono visibili, passare il puntatore sull'intestazione. 

4. Scegliere **Create IoT Hub** (Crea hub IoT).

5. Verrà visualizzata una finestra popup nell'angolo in basso a destra che consentirà di accedere ad Azure per la prima volta.

6. Selezionare la sottoscrizione di Azure. 

7. Selezionare il gruppo di risorse.

8. Selezionare la località.

9. Selezionare il piano tariffario.

10. Immettere un nome univoco globale per l'hub IoT.

11. Attendere alcuni minuti fino a quando non viene creato l'hub IoT.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito un hub IoT usando Azure IoT Toolkit per Visual Studio Code, è possibile:

* [Usare l'estensione Azure IoT Toolkit per Visual Studio Code per inviare e ricevere messaggi tra il dispositivo e l'hub IoT](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).
* Vedere la [pagina Wiki](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki) per Azure IoT Toolkit.
