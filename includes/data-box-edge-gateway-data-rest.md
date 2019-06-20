---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8a6ceee0f7db4b7b368dce1a28c8edc0b2b11180
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180789"
---
Per i dati inattivi:

- Crittografia di BitLocker XTS-AES a 256 bit viene usata per proteggere i dati locali.
- Accesso ai dati archiviati nelle condivisioni è limitato.

    - I client SMB che accedono ai dati di condivisione necessitino le credenziali utente associate alla condivisione. Queste credenziali vengono definite quando viene creata la condivisione.
    - Gli indirizzi IP dei client NFS che accedono a una condivisione devono essere aggiunti quando viene creata la condivisione.