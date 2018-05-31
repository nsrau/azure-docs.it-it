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
ms.openlocfilehash: 1df3e188b71b8fa2d5223bad8bc5914513e26286
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371202"
---
## <a name="create-a-device-identity"></a>Creare un'identità del dispositivo

In questa sezione si usa il [portale di Azure][lnk-azure-portal] per creare un'identità del dispositivo nel registro delle identità dell'hub IoT. Un dispositivo non può connettersi all'hub IoT a meno che non abbia una voce nel registro di identità. Per altre informazioni, vedere la sezione "Registro di identità" della [Guida per gli sviluppatori dell'hub IoT][lnk-devguide-identity]. Usare il pannello **Dispositivi IoT** nel portale per generare un ID dispositivo e una chiave univoci per il dispositivo per consentirne l'identificazione nell'hub IoT. Gli ID dispositivo fanno distinzione tra maiuscole e minuscole.

1. Accedere al [portale di Azure][lnk-azure-portal].

1. Selezionare **Tutte le risorse** e trovare la risorsa hub IoT.

1. Dopo aver aperto la risorsa hub IoT, fare clic sullo strumento **Dispositivi IoT** e quindi su **Aggiungi** nella parte superiore. 

    ![Creare l'identità del dispositivo nel portale][img-add-device]

1. Specificare un nome per il nuovo dispositivo, ad esempio **myDeviceId** e fare clic su **Salva**. In questo modo viene creata una nuova identità del dispositivo per l'hub IoT.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![Aggiungere un nuovo dispositivo][img-create-device]

1. Nell'elenco dei dispositivi fare clic sul dispositivo appena creato e copiare la **Stringa di connessione - chiave primaria**.

    ![Stringa di connessione del dispositivo][img-connection-string]

> [!NOTE]
> Il registro di identità dell'hub IoT archivia solo le identità del dispositivo per abilitare l'accesso sicuro all'hub. Archivia gli ID dispositivo e le chiavi da usare come credenziali di sicurezza e un flag di attivazione/disattivazione che consente di disabilitare l'accesso per un singolo dispositivo. Se l'applicazione deve archiviare altri metadati specifici del dispositivo, dovrà usare un archivio specifico dell'applicazione. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'hub IoT][lnk-devguide-identity].

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-add-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png
[img-create-device]:./media/iot-hub-get-started-create-device-identity-portal/add-device.png

<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

