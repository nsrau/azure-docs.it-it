---
title: includere il file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4ae190b55df70753f8f4dd081e8077c00e4c688a
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231031"
---
- Sono supportate solo [le chiavi RSA software e HSM](../articles/key-vault/keys/about-keys.md) di dimensioni 2.048 bit, 3.072 bit e 4.096 bit, senza altre chiavi o dimensioni.
    - Le chiavi [HSM](../articles/key-vault/keys/hsm-protected-keys.md) richiedono il livello **Premium** di Azure Key Vault.
- I dischi creati da immagini personalizzate crittografati con crittografia lato server e chiavi gestite dal cliente devono essere crittografati usando le stesse chiavi gestite dal cliente e devono trovarsi nella stessa sottoscrizione.
- Gli snapshot creati da dischi crittografati con crittografia lato server e chiavi gestite dal cliente devono essere crittografati con le stesse chiavi gestite dal cliente.
- Tutte le risorse correlate alle chiavi gestite dal cliente (istanze di Azure Key Vault, set di crittografia dischi, macchine virtuali, dischi e snapshot) devono trovarsi nella stessa sottoscrizione e nella stessa area.
- I dischi, gli snapshot e le immagini crittografati con chiavi gestite dal cliente non possono passare a un'altra sottoscrizione.
- I dischi gestiti crittografati con chiavi gestite dal cliente non possono anche essere crittografati con Crittografia dischi di Azure.
- Per informazioni sull'uso delle chiavi gestite dal cliente con le raccolte immagini condivise, vedere [Anteprima: usare le chiavi gestite dal cliente per crittografare immagini](../articles/virtual-machines/image-version-encryption.md).
