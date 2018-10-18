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
ms.openlocfilehash: ffd5da239f8e271a8c9b2aaf3f6d5fd9f885c79c
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49400320"
---
## <a name="create-a-device-identity"></a>Creare un'identità del dispositivo

In questa sezione si usa il [portale di Azure](https://portal.azure.com) per creare un'identità del dispositivo nel registro delle identità dell'hub IoT. Un dispositivo non può connettersi all'hub IoT a meno che non abbia una voce nel registro di identità. Per altre informazioni, consultare la sezione “registro delle identità” della [Guida per gli sviluppatori dell'hub IoT](../articles/iot-hub/iot-hub-devguide-identity-registry.md). Usare il pannello **Dispositivi IoT** nel portale per generare un ID dispositivo e una chiave univoci per il dispositivo per consentirne l'identificazione nell'hub IoT. Gli ID dispositivo fanno distinzione tra maiuscole e minuscole.

1. Accedere al [portale di Azure](https://portal.azure.com)

2. Selezionare **Tutte le risorse** e trovare la risorsa hub IoT.

3. Dopo aver aperto la risorsa hub IoT, fare clic sullo strumento **Dispositivi IoT** e quindi su **Aggiungi** nella parte superiore. 

    ![Creare l'identità del dispositivo nel portale](./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png)

4. Specificare un nome per il nuovo dispositivo, ad esempio **myDeviceId** e fare clic su **Salva**. In questo modo viene creata una nuova identità del dispositivo per l'hub IoT.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![Aggiungere un nuovo dispositivo](./media/iot-hub-get-started-create-device-identity-portal/create-a-device.png)

5. Nell'elenco dei dispositivi fare clic sul dispositivo appena creato e copiare la **Stringa di connessione - chiave primaria**.

    ![Stringa di connessione del dispositivo](./media/iot-hub-get-started-create-device-identity-portal/device-details.png)

> [!NOTE]
> Il registro di identità dell'hub IoT archivia solo le identità del dispositivo per abilitare l'accesso sicuro all'hub. Archivia gli ID dispositivo e le chiavi da usare come credenziali di sicurezza e un flag di attivazione/disattivazione che consente di disabilitare l'accesso per un singolo dispositivo. Se l'applicazione deve archiviare altri metadati specifici del dispositivo, dovrà usare un archivio specifico dell'applicazione. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'hub IoT](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
