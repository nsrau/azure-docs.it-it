---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67180784"
---
Per i dati in volo:

- Standard TLS 1.2 is used for data that travels between the device and Azure. Non esiste alcun fallback a TLS 1.1 e versioni precedenti. La comunicazione dell'agente verrà bloccata se TLS 1.2 non è supportato. TLS 1.2 è necessario anche per la gestione del portale e dell'SDK.
- Quando i client accedono al dispositivo tramite l'interfaccia utente Web locale di un browser, TLS 1.2 standard viene utilizzato come protocollo sicuro predefinito.

    - La procedura consigliata consiste nel configurare il browser per l'utilizzo di TLS 1.2.
    - Se il browser non supporta TLS 1.2, è possibile utilizzare TLS 1.1 o TLS 1.0.
- Si consiglia di utilizzare SMB 3.0 con crittografia per proteggere i dati quando vengono copiati dai server di dati.
