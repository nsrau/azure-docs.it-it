---
title: includere file
description: includere file
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/16/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 55cdd864d73ce084d994c64233e79d5a58b17def
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "69558478"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

Per ottenere la stringa di connessione dell'hub IoT per i criteri del **servizio**, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Gruppi di risorse**. Selezionare il gruppo di risorse in cui si trova l'hub e quindi selezionare l'hub dall'elenco di risorse.

1. Nel riquadro sinistro dell'hub IoT selezionare **Criteri di accesso condiviso**.

1. Dall'elenco dei criteri selezionare i criteri del **servizio**.

1. In **Chiavi di accesso condivise** selezionare l'icona di copia per **Stringa di connessione -- Chiave primaria** e salvare il valore.

    ![Mostrare come recuperare la stringa di connessione](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

Per altre informazioni sui criteri di accesso condiviso e sulle autorizzazioni dell'hub IoT, vedere [Controllo dell'accesso e autorizzazioni](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
