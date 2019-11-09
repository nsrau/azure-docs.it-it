---
title: Creare un hub IoT di Azure usando Azure IoT Tools per Visual Studio Code | Microsoft Docs
description: Informazioni su come usare gli strumenti di Azure per la Visual Studio Code per creare un hub Azure per le risorse in un gruppo di risorse.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 3fe4dfae2e86d549056805e87c0fd3927697fe24
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890559"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>Creare un hub IoT con Azure IoT Tools per Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Questo articolo illustra come usare [Azure IoT Tools per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) per creare un hub IoT di Azure. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- [Visual Studio Code](https://code.visualstudio.com/)

- [Strumenti di Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) per Visual Studio Code.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

1. In Visual Studio Code aprire la **finestra di esplorazione**.

2. Nella parte inferiore della finestra di esplorazione espandere la sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure). 

   ![Espandere i dispositivi dell'hub IoT di Azure](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Fare clic sui puntini di sospensione ( **...** ) nell'intestazione della sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure). Se i puntini di sospensione non sono visibili, passare il puntatore sull'intestazione. 

4. Scegliere **Create IoT Hub** (Crea hub IoT).

5. Verrà visualizzata una finestra popup nell'angolo in basso a destra che consentirà di accedere ad Azure per la prima volta.

6. Selezionare la sottoscrizione di Azure. 

7. Selezionare il gruppo di risorse.

8. Selezionare la località.

9. Selezionare il piano tariffario.

10. Immettere un nome univoco globale per l'hub IoT.

11. Attendere alcuni minuti fino a quando non viene creato l'hub IoT.

## <a name="next-steps"></a>Passaggi successivi

Si è ora implementato un hub IoT usando Azure IoT Tools per Visual Studio Code. Per altre informazioni, vedere gli articoli seguenti:

* [Usare Azure IoT Tools per Visual Studio Code per inviare e ricevere messaggi tra il dispositivo e un hub IoT](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Usare Azure IoT Tools per Visual Studio Code per la gestione dei dispositivi dell'hub IoT di Azure](iot-hub-device-management-iot-toolkit.md)

* [Pagina Wiki per Azure IoT Hub Toolkit](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
