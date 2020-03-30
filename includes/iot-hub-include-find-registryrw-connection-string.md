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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883813"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Per ottenere la stringa di connessione dell'hub IoT per il criterio **registryReadWrite,** attenersi alla seguente procedura:

1. Nel [portale](https://portal.azure.com)di Azure selezionare **Gruppi di risorse**. Selezionare il gruppo di risorse in cui si trova l'hub e quindi selezionare l'hub dall'elenco delle risorse.

2. Nel riquadro sinistro dell'hub selezionare **Criteri di accesso condiviso.**

3. Nell'elenco dei criteri selezionare il criterio **registryReadWrite.**

4. In Chiavi di **accesso condivise**selezionare l'icona di copia per la stringa di connessione, ovvero la chiave **primaria,** quindi salvare il valore.

    ![Mostrare come recuperare la stringa di connessione](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

Per ulteriori informazioni sulle autorizzazioni e sui criteri di accesso condiviso dell'hub IoT, vedere [Controllo di accesso e autorizzazioni](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
