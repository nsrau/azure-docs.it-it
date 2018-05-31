---
title: File di inclusione
description: File di inclusione
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: f8cd78e63099f864c5fc54b6268f6e558d738626
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371372"
---
## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Ora che è stato creato un hub IoT, individuare le informazioni importanti che consentono di connettere dispositivi e applicazioni all'hub IoT. 

Nel menu di spostamento dell'hub IoT, aprire i **criteri di accesso condiviso**.
Selezionare il criterio **iothubowner**, quindi copiare la **stringa di connessione---chiave primaria** dell'hub IoT. Per altre informazioni, vedere [Controllare l'accesso all'hub IoT](../articles/iot-hub/iot-hub-devguide-security.md).

   > [!NOTE] 
   > Per questa esercitazione di configurazione non è necessaria la stringa di connessione iothubowner. Potrebbe essere tuttavia necessaria per alcune delle esercitazioni o per altri scenari IoT, dopo aver completato questa configurazione.

   ![Ottenere la stringa di connessione dell'hub IoT](./media/iot-hub-get-started-create-hub-and-device/create-iot-hub5.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>Registrare un dispositivo nell'hub IoT per il dispositivo

1. Nel menu di spostamento dell'hub IoT, aprire **Dispositivi IoT**, quindi fare clic su **Aggiungi** per registrare un dispositivo nell'hub IoT.

   ![Aggiungere un dispositivo a Dispositivi IoT nell'hub IoT](./media/iot-hub-get-started-create-hub-and-device/create-identity-portal.png)

2. Immettere un **ID dispositivo** per il nuovo dispositivo. Gli ID dispositivo fanno distinzione tra maiuscole e minuscole.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Fare clic su **Save**.
5. Dopo la creazione del dispositivo, aprirlo nel riquadro **Dispositivi IoT**.
6. Copiare il valore di **Stringa di connessione---Chiave primaria** dell'hub IoT per usarlo in seguito.

   ![Ottenere la stringa di connessione del dispositivo](./media/iot-hub-get-started-create-hub-and-device/device-connection-string.png)
