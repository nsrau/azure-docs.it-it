---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: f77310ebf10812c1bf15a2555520f46c10c8c182
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165656"
---
<!--alkohli 02/21/2017 cloud appliance security-->

Durante l'uso dell'appliance cloud StorSimple, tenere presenti le considerazioni sulla sicurezza riportate di seguito:

* L'appliance cloud è protetta mediante la sottoscrizione di Microsoft Azure. Ciò significa che se si usa l'appliance cloud in presenza di una sottoscrizione di Azure compromessa, anche i dati memorizzati nell'appliance cloud possono essere vulnerabili.
* La chiave pubblica del certificato usato per crittografare i dati archiviati in StorSimple viene resa disponibile in modo sicuro per il portale di Azure, mentre la chiave privata viene conservata nell'appliance cloud StorSimple. Nell'appliance cloud StorSimple le chiavi pubbliche e private vengono archiviate entrambe in Azure.
* L'appliance cloud è ospitata nel data center di Microsoft Azure.

