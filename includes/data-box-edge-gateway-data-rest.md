---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: d8570c0be6b64d4e289575ce3f3f1721c4a65074
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684471"
---
Per i dati inattivi:

- Per i dati inattivi, viene utilizzata la crittografia BitLocker XTS AES-256 per proteggere i dati locali.
- Per i dati che risiede nelle condivisioni, l'accesso alle condivisioni è limitato.

    - Per i client SMB che accedono ai dati di condivisione, necessarie le credenziali utente associate alla condivisione. Queste credenziali vengono definite al momento della creazione della condivisione.
    - Per i client NFS che accedono a condivisioni, gli indirizzi IP dei client devono essere aggiunti al momento della creazione della condivisione.