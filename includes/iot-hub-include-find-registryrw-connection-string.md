---
title: File di inclusione
description: File di inclusione
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a752427d1e5873f0a27fd231872e3a13b234d9ed
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402302"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Per ottenere la stringa di connessione dell'hub Internet per il criterio **registryReadWrite** , seguire questa procedura:

1. Aprire l'hub Internet delle cose nel [portale di Azure](https://portal.azure.com).  Il modo più semplice per ottenere l'hub Internet è quello di selezionare i **gruppi di risorse**, selezionare il gruppo di risorse in cui si trova l'hub Internet e quindi selezionare l'hub delle cose dall'elenco di risorse.

2. Nel riquadro sinistro dell'hub Internet, selezionare **criteri di accesso condiviso**.

3. Nell'elenco dei criteri selezionare il criterio **registryReadWrite** .

4. In **chiavi di accesso condivise**selezionare l'icona di copia per la **stringa di connessione--chiave primaria** e salvare il valore.

    ![Mostrare come recuperare la stringa di connessione](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-string.png)

Per altre informazioni sui criteri di accesso condiviso e sulle autorizzazioni dell'hub Internet, vedere [controllo di accesso e autorizzazioni](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
