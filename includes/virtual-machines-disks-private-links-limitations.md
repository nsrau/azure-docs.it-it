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
ms.openlocfilehash: 138e7efd33ee36a77fd56deda155a29017432302
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420287"
---
- A un oggetto accesso al disco è possibile collegare una sola rete virtuale.
- Per il corretto collegamento, la rete virtuale e l'oggetto accesso al disco devono trovarsi nella stessa sottoscrizione.
- Con lo stesso oggetto accesso al disco è possibile importare o esportare fino a 10 dischi o snapshot contemporaneamente.
- Non è possibile richiedere l'approvazione manuale per collegare una rete virtuale a un oggetto accesso al disco.
- La funzionalità differenziale non è supportata per gli snapshot incrementali associati a un oggetto accesso al disco.
- Non è possibile usare AzCopy per scaricare il file VHD di un disco o di uno snapshot protetto tramite collegamenti privati in un account di archiviazione. Si può invece usare AzCopy per scaricare il file VHD nelle VM.
