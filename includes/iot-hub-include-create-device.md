---
title: includere file
description: includere file
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7b022f71e197c5695876f2049ee376c3616afc6d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "70049072"
---
<!-- put the ## header in the file that includes this file -->

In questa sezione si creerà un'identità del dispositivo nel registro delle identità dell'hub IoT. Un dispositivo non può connettersi all'hub a meno che non abbia una voce nel registro di identità. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'hub IoT](../articles/iot-hub/iot-hub-devguide-identity-registry.md#identity-registry-operations).

1. Nel menu di spostamento dell'hub IoT, aprire **Dispositivi IoT**, quindi selezionare **Nuovo** per aggiungere un dispositivo nell'hub IoT.

    ![Creare l'identità del dispositivo nel portale](./media/iot-hub-include-create-device/create-identity-portal-vs2019.png)

1. In **Crea un dispositivo**, specificare un nome per il nuovo dispositivo, ad esempio **myDeviceId**, quindi selezionare **Salva**. In questo modo viene creata un’identità del dispositivo per l'hub IoT.

   ![Aggiungere un nuovo dispositivo](./media/iot-hub-include-create-device/create-a-device-vs2019.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Dopo la creazione del dispositivo, aprirlo nel riquadro **Dispositivi IoT**. Copiare la **Stringa di connessione primaria** da usare in un secondo momento.

    ![Stringa di connessione del dispositivo](./media/iot-hub-include-create-device/device-details-vs2019.png)

> [!NOTE]
> Il registro di identità dell'hub IoT archivia solo le identità del dispositivo per abilitare l'accesso sicuro all'hub. Archivia gli ID dispositivo e le chiavi da usare come credenziali di sicurezza e un flag di attivazione/disattivazione che consente di disabilitare l'accesso per un singolo dispositivo. Se l'applicazione deve archiviare altri metadati specifici del dispositivo, dovrà usare un archivio specifico dell'applicazione. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'hub IoT](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
