---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/20/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 241a2be834d7828cdb56f59313c0c1a9c478ff77
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94986802"
---
- Questa funzionalità è attualmente supportata solo per le unità SSD Premium.
- È necessario deallocare la VM o scollegare il disco da una macchina virtuale in esecuzione prima di poter modificare il livello del disco.
- I livelli di prestazioni P60, p70 e P80 possono essere usati solo da dischi di dimensioni superiori a 4.096 GiB.
- Il downgrade del livello di prestazioni di un disco può essere eseguito solo una volta ogni 12 ore.