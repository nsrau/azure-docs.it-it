---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: bea14544949f90290bf3878295b49f1e08be9eea
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684434"
---
Per i dati in volo:

- Per i dati destinati tra il dispositivo e Azure, viene usato TLS 1.2 standard. Non vi è Nessun fallback a TLS 1.1 e versioni precedenti. La comunicazione dell'agente verrà bloccata se TLS 1.2 non è supportato. È necessario per le operazioni di gestione SDK e portale anche TLS 1.2.
- Quando i client di accedere al dispositivo tramite l'interfaccia utente web locale in un browser, viene usato TLS 1.2 standard come il protocollo di protezione predefinito.

    - La procedura consigliata consiste nel configurare il browser per usare TLS 1.2.
    - Se il browser non supporta TLS 1.2, è possibile usare TLS 1.1 o TLS 1.0.
- Per proteggere i dati quando lo si copia dal server di dati, è consigliabile usare SMB 3.0 con crittografia.
