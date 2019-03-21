---
title: Supporto dell'aggiunta di macchine virtuali di Azure a un set di disponibilità esistente | Microsoft Docs
description: Supporto dell'aggiunta di macchine virtuali di Azure a un set di disponibilità esistente.
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 7a5e97b66fec040b4ec32caa8d58cf9b50169a33
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433145"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Supporto dell'aggiunta di macchine virtuali di Azure a un set di disponibilità esistente

È possibile riscontrare occasionalmente limitazioni quando si aggiungono nuove macchine virtuali (VM) in un set di disponibilità esistente. Il grafico seguente illustra in dettaglio quali serie di macchine virtuali è possibile combinare nello stesso set di disponibilità.

Di seguito si riporta la matrice di supporto per combinare diversi tipi di macchine virtuali:

Serie e set di disponibilità|Seconda macchina virtuale|Una |Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Prima macchina virtuale|||||||
|Una ||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Tutte le altre serie potrebbero non trovarsi nello stesso set di disponibilità perché richiedono un hardware specifico.

Dimensioni della macchina virtuale a8 e A9 non possono essere combinate a causa di un requisito in rete di back-end RDMA dedicata.
