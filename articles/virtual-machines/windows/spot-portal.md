---
title: Usare il portale per distribuire le VM di Azure spot
description: Informazioni su come usare Azure PowerShell per distribuire macchine virtuali spot per risparmiare sui costi.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/25/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 6e7723a437e90807063e3c3b7af2bf068dca5b9f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82100652"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>Distribuire le VM spot usando il portale di Azure

L'uso di [macchine virtuali con spot](spot-vms.md) consente di sfruttare la capacità inutilizzata con un notevole risparmio sui costi. Quando, in qualsiasi momento, Azure avrà di nuovo bisogno di quella capacità, l'infrastruttura di Azure rimuoverà le macchine virtuali spot. Le macchine virtuali spot sono pertanto ideali per i carichi di lavoro in grado di gestire le interruzioni, come i processi di elaborazione batch, gli ambienti di sviluppo/test, i carichi di lavoro di calcolo di grandi dimensioni e altro ancora.

I prezzi per le macchine virtuali spot variano in base all'area e allo SKU. Per altre informazioni, vedere i prezzi delle macchine virtuali per [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Per altre informazioni sull'impostazione del prezzo massimo, vedere [spot VM-prezzi](spot-vms.md#pricing).

È possibile impostare un prezzo massimo che si è disposti a pagare, per ora, per la macchina virtuale. Il prezzo massimo per una VM spot può essere impostato in dollari USA (USD), usando un massimo di 5 cifre decimali. Ad esempio, il valore `0.05701` è un prezzo massimo di $0,05701 USD all'ora. Se si imposta il prezzo massimo su `-1`, la macchina virtuale non verrà eliminata in base al prezzo. Il prezzo della macchina virtuale corrisponderà al prezzo corrente per spot o al prezzo di una macchina virtuale standard, a seconda di quale sia il più basso, purché siano disponibili capacità e quota.


## <a name="create-the-vm"></a>Creare la VM

Il processo di creazione di una macchina virtuale che usa le VM spot è identico a quello illustrato in dettaglio nella [Guida introduttiva](quick-create-portal.md). Quando si distribuisce una macchina virtuale, è possibile scegliere di usare un'istanza di Azure spot.


Nella scheda **nozioni di base** , nella sezione **Dettagli istanza** , **non** è il valore predefinito per l'uso di un'istanza di Azure spot.

![Acquisizione schermo per la scelta di no, non usare un'istanza di Azure spot](media/spot-portal/no.png)

Selezionando **Sì**, la sezione si espande ed è possibile scegliere il [tipo di rimozione e i criteri di rimozione](spot-vms.md#eviction-policy). 

![Acquisizione schermo per la scelta di sì, usare un'istanza di Azure spot](media/spot-portal/yes.png)


## <a name="next-steps"></a>Passaggi successivi

È anche possibile creare macchine virtuali spot usando [PowerShell](spot-powershell.md).