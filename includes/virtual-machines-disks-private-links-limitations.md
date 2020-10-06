---
title: includere il file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 293f0f459e1f1e464fdec16b76eaf08336c92e93
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377218"
---
- A un oggetto accesso al disco è possibile collegare una sola rete virtuale.
- Per il corretto collegamento, la rete virtuale e l'oggetto accesso al disco devono trovarsi nella stessa sottoscrizione.
- Con lo stesso oggetto accesso al disco è possibile importare o esportare fino a 10 dischi o snapshot contemporaneamente.
- Non è possibile richiedere l'approvazione manuale per collegare una rete virtuale a un oggetto accesso al disco.
- Gli snapshot incrementali non possono essere esportati quando sono associati a un oggetto accesso al disco.
- Non è possibile usare AzCopy per scaricare il file VHD di un disco o di uno snapshot protetto tramite collegamenti privati in un account di archiviazione. Si può invece usare AzCopy per scaricare il file VHD nelle VM.
