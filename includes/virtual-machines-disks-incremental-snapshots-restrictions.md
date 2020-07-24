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
ms.openlocfilehash: f4af8a150b062526f08c1d15581ec26e2fe12d8c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102751"
---
- Non è attualmente possibile spostare gli snapshot incrementali tra le sottoscrizioni.
- Attualmente, è possibile generare solo URI SAS di un massimo di cinque snapshot di una particolare famiglia di snapshot in un determinato momento.
- Non è possibile creare uno snapshot incrementale per un disco specifico al di fuori della sottoscrizione del disco.
- È possibile creare fino a sette snapshot incrementali per ogni disco ogni cinque minuti.
- Per un singolo disco è possibile creare un totale di 200 snapshot incrementali.
- Non è possibile ottenere le modifiche tra gli snapshot effettuati prima e dopo aver modificato le dimensioni del disco padre tra i limiti di 4 TB. Ad esempio, è stato eseguito uno snapshot incrementale snapshot-a quando le dimensioni di un disco erano 2 TB. A questo punto le dimensioni del disco sono aumentate a 6 TB, quindi è stato eseguito un altro snapshot di snapshot incrementale-b. Non è possibile ottenere le modifiche tra snapshot-a e snapshot-b. È necessario scaricare di nuovo la copia completa di snapshot-b creata dopo il ridimensionamento. Successivamente, è possibile ottenere le modifiche tra snapshot-b e snapshot creati dopo lo snapshot-b. 
