---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: f79081d506db6225b9ebef25674aad136e342ecf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467789"
---
Per i dati in corso:

- Il Transport Layer Security Standard (TLS) 1,2 viene usato per i dati che si spostano tra il dispositivo e Azure. Non esiste alcun fallback a TLS 1,1 e versioni precedenti. La comunicazione dell'agente verrà bloccata se TLS 1,2 non è supportato. TLS 1,2 è necessario anche per la gestione del portale e dell'SDK.
- Quando i client accedono al dispositivo tramite l'interfaccia utente Web locale di un browser, viene usato standard TLS 1,2 come protocollo di sicurezza predefinito.

  - La procedura consigliata consiste nel configurare il browser per l'uso di TLS 1,2.
  - Il dispositivo supporta solo TLS 1,2 e non supporta le versioni precedenti di TLS 1,1 e TLS 1,0.
- Si consiglia di usare SMB 3,0 con la crittografia per proteggere i dati quando lo si copia dai server dati.
