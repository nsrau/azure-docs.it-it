---
title: includere file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7700b1118c4f04643607f44a474338ffdd5c09e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86231028"
---
- Non supporta dischi Ultra.
- Non può essere abilitata se crittografia dischi di Azure (crittografia guest-VM con BitLocker/VM-Decrypt) è abilitata nelle VM o nei set di scalabilità di macchine virtuali.
- Crittografia dischi di Azure non può essere abilitata nei dischi in cui è abilitata la crittografia in host.
- La crittografia può essere abilitata nel set di scalabilità di macchine virtuali esistente. Tuttavia, solo le nuove macchine virtuali create dopo l'abilitazione della crittografia vengono crittografate automaticamente.
- Per poter essere crittografate, è necessario deallocare e riallocare le macchine virtuali esistenti.