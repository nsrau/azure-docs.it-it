---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e96d205ef1a8f94baa3a0cfe6c5127b6cf570e5a
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420974"
---
Questo articolo illustra come caricare un disco rigido virtuale dal computer locale a un disco gestito di Azure o copiare un disco gestito in un'altra area, usando AzCopy.This article explains how to either upload a VHD from your local machine to an Azure managed disk or copy a managed disk to another region, using AzCopy. Questo processo, il caricamento diretto, consente anche di caricare un disco rigido virtuale con dimensioni fino a 32 TiB direttamente in un disco gestito. Attualmente, il caricamento diretto è supportato per i dischi gestiti HDD standard, SSD standard e SSD premium. Non è ancora supportato per i dischi ultra.

Se si fornisce una soluzione di backup per le macchine virtuali IaaS in Azure, è consigliabile usare il caricamento diretto per ripristinare i backup dei clienti nei dischi gestiti. Quando si carica un disco rigido virtuale da un'origine esterna ad Azure, le velocità dipendono dalla larghezza di banda locale. Quando si carica o si copia da una macchina virtuale di Azure, la larghezza di banda corrisponde ai dischi rigidi standard.