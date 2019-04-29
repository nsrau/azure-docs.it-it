---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754289"
---
Per i dati in fase di trasferimento:

- Viene usato TLS 1.2 standard per i dati scambiati tra il dispositivo e Azure. Non vi è Nessun fallback a TLS 1.1 e versioni precedenti. La comunicazione dell'agente verrà bloccata se TLS 1.2 non è supportato. È necessario per il portale e gestione SDK anche TLS 1.2.
- Quando i client di accedono al dispositivo tramite l'interfaccia utente di un browser web locale, come il protocollo di protezione predefinito viene usato standard TLS 1.2.

    - La procedura consigliata consiste nel configurare il browser per usare TLS 1.2.
    - Se il browser non supporta TLS 1.2, è possibile usare TLS 1.1 o TLS 1.0.
- È consigliabile usare SMB 3.0 con crittografia per proteggere i dati quando lo si copia dal server di dati.
