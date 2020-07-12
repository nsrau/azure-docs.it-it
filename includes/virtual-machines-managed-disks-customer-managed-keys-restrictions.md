---
title: includere il file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c303527c7411ead585e70fc2e31db4dd2d35e996
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259814"
---
- Sono supportate solo [le chiavi RSA software e HSM](../articles/key-vault/keys/about-keys.md) di dimensioni 2.048 bit, 3.072 bit e 4.096 bit, senza altre chiavi o dimensioni.
    - Le chiavi [HSM](../articles/key-vault/keys/hsm-protected-keys.md) richiedono il livello **Premium** di Azure Key Vault.
- I dischi creati da immagini personalizzate crittografati con crittografia lato server e chiavi gestite dal cliente devono essere crittografati usando le stesse chiavi gestite dal cliente e devono trovarsi nella stessa sottoscrizione.
- Gli snapshot creati da dischi crittografati con crittografia lato server e chiavi gestite dal cliente devono essere crittografati con le stesse chiavi gestite dal cliente.
- Tutte le risorse correlate alle chiavi gestite dal cliente (istanze di Azure Key Vault, set di crittografia dischi, macchine virtuali, dischi e snapshot) devono trovarsi nella stessa sottoscrizione e nella stessa area.
- I dischi, gli snapshot e le immagini crittografati con chiavi gestite dal cliente non possono passare a un'altra sottoscrizione.
- I dischi gestiti crittografati con chiavi gestite dal cliente non possono anche essere crittografati con Crittografia dischi di Azure.
- Può creare solo fino a 50 set di crittografia del disco per area per sottoscrizione.
- Per informazioni sull'uso delle chiavi gestite dal cliente con le raccolte immagini condivise, vedere [Anteprima: Usare le chiavi gestite dal cliente per crittografare le immagini](../articles/virtual-machines/image-version-encryption.md).
