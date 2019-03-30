---
title: File di inclusione
description: File di inclusione
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: d70544866b9e321d98acd3978da145276e036025
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58671759"
---
<!-- put the ## header in the file that includes this file -->

In questa sezione si creerà un'identità del dispositivo nel registro delle identità dell'hub IoT. Un dispositivo non può connettersi all'hub IoT a meno che non abbia una voce nel registro di identità. Per altre informazioni, vedere la sezione "Registro delle identità" della [Guida per gli sviluppatori dell'hub IoT](../articles/iot-hub/iot-hub-devguide-identity-registry.md) 

1. Nel menu di spostamento dell'hub IoT, aprire **dispositivi IoT**, quindi selezionare **Add** per registrare un nuovo dispositivo nell'hub IoT.

    ![Creare l'identità del dispositivo nel portale](./media/iot-hub-include-create-device/create-identity-portal.png)

1. Specificare un nome per il nuovo dispositivo, ad esempio **myDeviceId**e selezionare **salvare**. In questo modo viene creata una nuova identità del dispositivo per l'hub IoT.

   ![Aggiungere un nuovo dispositivo](./media/iot-hub-include-create-device/create-a-device.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]


1. Dopo la creazione del dispositivo, aprirlo nel riquadro **Dispositivi IoT**. Copiare il valore di **Stringa di connessione---Chiave primaria** dell'hub IoT per usarlo in seguito.

    ![Stringa di connessione del dispositivo](./media/iot-hub-include-create-device/device-details.png)

> [!NOTE]
> Il registro di identità dell'hub IoT archivia solo le identità del dispositivo per abilitare l'accesso sicuro all'hub. Archivia gli ID dispositivo e le chiavi da usare come credenziali di sicurezza e un flag di attivazione/disattivazione che consente di disabilitare l'accesso per un singolo dispositivo. Se l'applicazione deve archiviare altri metadati specifici del dispositivo, dovrà usare un archivio specifico dell'applicazione. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'hub IoT](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
