---
title: File di inclusione
description: File di inclusione
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/05/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: b08bfcd4cb9e85f9e682efe0f599b6dd88897962
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
## <a name="create-a-device-identity"></a>Creare un'identità del dispositivo

In questa sezione si usa il [portale di Azure][lnk-azure-portal] per creare un'identità del dispositivo nel registro delle identità dell'hub IoT. Un dispositivo non può connettersi all'hub IoT a meno che non abbia una voce nel registro di identità. Per altre informazioni, vedere la sezione "Registro di identità" della [Guida per gli sviluppatori dell'hub IoT][lnk-devguide-identity]. Usare il pannello **Dispositivi IoT** nel portale per generare un ID dispositivo e una chiave univoci per il dispositivo per consentirne l'identificazione nell'hub IoT. Gli ID dispositivo fanno distinzione tra maiuscole e minuscole.

1. Assicurarsi di aver eseguito l'accesso al [portale di Azure][lnk-azure-portal].

1. Nell'indice fare clic su **Tutte le risorse** e trovare la risorsa hub IoT.

    ![Passare all'hub IoT][img-find-iothub]

1. Dopo aver aperto la risorsa hub IoT, fare clic sullo strumento **Dispositivi IoT** e quindi su **Aggiungi** nella parte superiore. Specificare un nome per il nuovo dispositivo, ad esempio **myDeviceId** e fare clic su **Salva**.

    ![Creare l'identità del dispositivo nel portale][img-create-device]

   In questo modo viene creata una nuova identità del dispositivo per l'hub IoT.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Nell'elenco dei dispositivi in **Dispositivi IoT** fare clic sul dispositivo appena creato e prendere nota del valore in **Stringa di connessione - chiave primaria**.

    ![Stringa di connessione del dispositivo][img-connection-string]

> [!NOTE]
> Il registro di identità dell'hub IoT archivia solo le identità del dispositivo per abilitare l'accesso sicuro all'hub. Archivia gli ID dispositivo e le chiavi da usare come credenziali di sicurezza e un flag di attivazione/disattivazione che consente di disabilitare l'accesso per un singolo dispositivo. Se l'applicazione deve archiviare altri metadati specifici del dispositivo, dovrà usare un archivio specifico dell'applicazione. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'hub IoT][lnk-devguide-identity].

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-create-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png


<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

