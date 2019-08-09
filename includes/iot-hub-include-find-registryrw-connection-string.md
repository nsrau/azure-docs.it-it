---
title: File di inclusione
description: File di inclusione
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883813"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Per ottenere la stringa di connessione dell'hub Internet per il criterio **registryReadWrite** , seguire questa procedura:

1. Nella [portale di Azure](https://portal.azure.com)selezionare gruppi di **risorse**. Selezionare il gruppo di risorse in cui si trova l'hub e quindi selezionare l'hub dall'elenco di risorse.

2. Nel riquadro a sinistra dell'hub selezionare **criteri di accesso condiviso**.

3. Nell'elenco dei criteri selezionare il criterio **registryReadWrite** .

4. In **chiavi di accesso condivise**selezionare l'icona di copia per la **stringa di connessione--chiave primaria** e salvare il valore.

    ![Mostrare come recuperare la stringa di connessione](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

Per altre informazioni sui criteri di accesso condiviso e sulle autorizzazioni dell'hub Internet, vedere [controllo di accesso e autorizzazioni](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
