---
title: Supporto dell'aggiunta di macchine virtuali di Azure a un set di disponibilità esistente | Microsoft Docs
description: Questo articolo fornisce una matrice di supporto per la serie di macchine virtuali che è possibile combinare nello stesso set di disponibilità
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: cb0034f2b353284e94d6f1508541b31040a5b076
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028415"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Supporto dell'aggiunta di macchine virtuali di Azure a un set di disponibilità esistente

È possibile riscontrare occasionalmente limitazioni quando si aggiungono nuove macchine virtuali (VM) in un set di disponibilità esistente. Il grafico seguente illustra in dettaglio quali serie di macchine virtuali è possibile combinare nello stesso set di disponibilità.

Di seguito si riporta la matrice di supporto per combinare diversi tipi di macchine virtuali:

Serie e set di disponibilità|Seconda macchina virtuale|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Prima macchina virtuale|||||||
|A||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Tutte le altre serie potrebbero non trovarsi nello stesso set di disponibilità perché richiedono un hardware specifico.

Le dimensioni della macchina virtuale A8/A9 non possono essere combinate a causa del requisito sulla rete back-end RDMA dedicata.
