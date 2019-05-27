---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: cb160a140b5c0cb184a5172da10ade0de37c4fed
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159193"
---
<!--v-sharos 10/13/2105 virtual device security-->

Quando si utilizza il dispositivo virtuale StorSimple, tenere presenti le seguenti considerazioni sulla sicurezza:

* La sottoscrizione a Microsoft Azure consente di proteggere il dispositivo virtuale. Ciò significa che se l'utente utilizza il dispositivo virtuale in presenza di una sottoscrizione ad Azure compromessa, anche i dati memorizzati nel dispositivo virtuale possono essere vulnerabili.
* La chiave pubblica del certificato usato per crittografare i dati archiviati in Azure StorSimple viene resa disponibile per il portale di Azure classico e la chiave privata viene conservata nel dispositivo StorSimple. Nel dispositivo virtuale StorSimple le chiavi pubbliche e private vengono archiviate entrambe in Azure.
* Il dispositivo virtuale è ospitato nel data center di Microsoft Azure.

