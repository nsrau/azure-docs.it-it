---
title: includere file
description: includere file
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "68883813"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Per ottenere la stringa di connessione dell'hub IoT per i criteri **registryReadWrite**, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Gruppi di risorse**. Selezionare il gruppo di risorse in cui si trova l'hub e quindi selezionare l'hub dall'elenco di risorse.

2. Nel riquadro sinistro dell'hub selezionare **Criteri di accesso condiviso**.

3. Dall'elenco dei criteri selezionare il criterio **registryReadWrite**.

4. In **Chiavi di accesso condivise** selezionare l'icona di copia per **Stringa di connessione -- Chiave primaria** e salvare il valore.

    ![Mostrare come recuperare la stringa di connessione](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

Per altre informazioni sui criteri di accesso condiviso e sulle autorizzazioni dell'hub IoT, vedere [Controllo dell'accesso e autorizzazioni](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
