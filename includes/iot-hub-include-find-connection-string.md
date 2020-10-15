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
ms.openlocfilehash: 8d7ac457041474f4e774414b1d5e6f9ed09dc856
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67180244"
---
<!-- this tells how to get the connection string for your hub -->
<!-- This assumes the user is looking at his hub in the portal. -->

Dopo aver creato l'hub, recuperare la stringa di connessione per l'hub che verrà usata per la connessione di dispositivi e applicazioni all'hub. 

1. Fare clic sull'hub per visualizzare il riquadro Hub IoT con Impostazioni e così via. Fare clic su **Criteri di accesso condivisi**.
   
2. In **Criteri di accesso condivisi** selezionare il criterio **iothubowner**. 

3. In **Chiavi di accesso condiviso** copiare il valore di **Stringa di connessione -- Chiave primaria** da usare in seguito.

    ![Mostrare come recuperare la stringa di connessione](./media/iot-hub-include-find-connection-string/iot-hub-get-connection-string.png)

    Per altre informazioni, vedere [Controllo di accesso](../articles/iot-hub/iot-hub-devguide-security.md) nella "Guida per gli sviluppatori dell'hub IoT".
