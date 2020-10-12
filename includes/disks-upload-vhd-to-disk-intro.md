---
title: includere file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e96d205ef1a8f94baa3a0cfe6c5127b6cf570e5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80420974"
---
Questo articolo illustra come caricare un disco rigido virtuale dal computer locale in un disco gestito di Azure o copiare un disco gestito in un'altra area, usando AzCopy. Questo processo, caricamento diretto, consente anche di caricare un disco rigido virtuale con dimensioni fino a 32 TiB direttamente in un disco gestito. Attualmente, il caricamento diretto è supportato per HDD standard, unità SSD standard e Managed disks SSD Premium. Non è ancora supportato per i dischi Ultra.

Se si fornisce una soluzione di backup per le macchine virtuali IaaS in Azure, è consigliabile usare il caricamento diretto per ripristinare i backup dei clienti in Managed Disks. Quando si carica un disco rigido virtuale da un'origine esterna ad Azure, le velocità variano a seconda della larghezza di banda locale. Quando si carica o si esegue la copia da una macchina virtuale di Azure, la larghezza di banda sarà identica a quella degli HDD standard.